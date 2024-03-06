# Storage Spaces - Slow Parity Performance

Collected from my experience, here is how to deal with slow parity virtual disk performance in Microsoft Storage Spaces. In most cases, recreation will be required, but in some cases, you might get away with not having to dump all your data elsewhere. This article presumes a Storage Spaces array with no SSDs and no cache. The problem being solved here is HDDs in SS parity volumes providing 20 to 40 MB/s writes instead of multiples of disk’s rated write speeds (100 to 300 MB/s).

*Update 5/2022: This is by far the most popular article on this website. I have fixed up some typos, cleared up formatting and added some clarifications I get most commonly asked about. Also, the formula for calculating right AUS and interleave values and also the PowerShell command are now written in RED color, so you can find them on the page easier.*

If you do a Google search for Storage Spaces parity performance, you will find both enthusiasts and professionals complaining about it. No wonder – the UI is very limited in what it shows to the user and sometimes doesn’t even work properly. Today, we will talk about Interleave sizes, Allocation Unit sizes and how they work together.

# Columns
To understand all this, we have to know what columns are in Storage Spaces, since the user interface doesn’t communicate the NumberOfColumns parameter at all. SS gives users the opportunity to decide how many physical targets will be used for each write. This is more important than you might think. For parity spaces, this argument decides what is the storage efficiency going to be and affects performance as well. 

## With 3 columns, we get 66% efficiency:
![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/e48a8f31-4367-4d61-91c6-289f595a9db8)


*Every time anything is written, the data written is 150% of the original data, since there is extra write for parity. Hence, the efficiency of storage is 66%, as we are effectively losing 1 whole disk to parity. Notice, however, that in practice, parity travels across all disks. There is no one dedicated parity disk.*


## With 4 columns, we get 75% efficiency:
![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/18ee26dd-737d-466f-887d-69eee61623cf)


*With 4 columns (4 physical disks minimum) we get higher efficiency. From 66 to 75 percent.*

Here comes the kick:

**Number of columns is not tied with number of physical disks in the pool.
**

Data and parity rotates between physical disks, so you can have 3-column parity virtual disk in a pool with more than 3 physical disks and data will be stacked evenly on all disks in the pool, with efficiency still being 66%, as shown below:

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/bdd09135-abeb-42d7-b23a-c9877e22edaa)


*3 columns on 4 physical disks.*

With parity spaces, the use of columns is quite clear. Same logic applies to other storage strategies as well, however. Consider this two-way mirror space, for example:

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/578942c5-996a-496e-bae4-d2c18e649373)


*A simple 1 column two-way mirror space.*

Simple, right? Everything gets written twice. What’s the big deal? Well, what if we increase amount of disks in the pool?

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/f63d3935-f835-42a6-8452-452a3d36f651)


*Still a simple 1 column two-way mirror space, where everything gets written twice.*

Well, what if we increase number of columns to 2?

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/80b6786d-c4d7-4690-8583-70689493a4df)


*2-column two-way mirror space effectively becomes RAID-10.*

Now that we have done that, we have a mirror space with 4 physical drives, which splits data into two slabs and then creates copies of those two slabs. This brings awesome performance benefits, but decreases storage efficiency to 50%.

# Interleave

This parameter says how much data fits into single cell of a column. In a 3-column parity Space, an interleave size of 16KB means that our row is 48KB long, but 16KB must be reserved for parity, leaving 32KB for data. Hence, every write request will be split into an array of 32KB stripes, and each stripe split into two 16KB slabs. Those two 16KB slabs will be written to individual disks, with third 16KB slab being calculated on the fly as their parity and written to a third disk:

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/c2a2f2ee-8113-4c18-9ed8-ea212e22a932)


*One stripe can handle 32KB of data if Interleave value is set to 16KB.*

What we haven’t been considering all this time is volumes, partitions, allocation units and cluster sizes. Neither the pool, or the space have any care about what it is that they are actually writing into their disks. The volume provider has to take care of that, and above that volume is a partition. So the whole diagram gets even more complicated:

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/5143c5a7-de5b-4e6e-aec8-fe593bbf824d)


*A top down layer infrastructure of how data is stored.*

This is where performance gets hindered the most. Say that we have 4 disks in a pool, we want the data protected by parity, and we create a NTFS partition on this space, doing it all through Storage Spaces UI in Control Panel:

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/5655db3d-686f-4e59-a883-8a84fbbd3ee0)


*Unaligned nonsense created by Windows – this is by default.*

If your Virtual Disk is set to have maximum capacity below 16TB, Windows will create the partition with an Allocation Unit Size or AUS of 4KB. If it’s above 16TB, it will switch up to 8KB. This setting cannot be changed and you have to recreate the partition in order to set it differently. The performance loss here comes from the work that has to be done in order to align the data properly. Each write request has to first allocate the right number of units on the partition, which is 4KB each, and then all this has to translate into 256KB slabs which are propagated to the drives. This is expensive operation and causes the scenario that many Storage Spaces users know by heart – a 1 GB of blast speeds and then slowdown to unusable speeds:

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/d9d6eed1-faea-43ca-be85-261800b7d866)


*Parity write performance dips after 1GB of data written, this is 4 WD REDs in a 4 column parity space.*

The 1 GB rush is a Windows-default 1 GB write cache on system disk, if Storage Spaces considers it worthy. How do we fix this? We align the AUS and Interleave sizes. Since NTFS offers AUS of 4, 8, 16, 32 or 64KB, we have to set our Interleave size so that the following statement is true for our parity space:

```
$ntfs_aus = ($number_of_columns - 1) * $interleave
```

Let’s imagine this on a practical example. Say that we have a 3-column parity space of 3 disks, with an interleave of 32KB and a NTFS partition with AUS of 64KB. This means that our stripe size (row length over 3 columns) is 3x32KB, some 96KB. Of this, 1 column is parity, so only 64KB of the stripe are actual data. Now match that with NTFS AUS of 64KB. Perfect alignment. The secret sauce here is, that since Windows 10 build 1809, if you do that, Windows will completely bypass the cache and do full stripe writes, since it doesn’t have to recalculate the allocation. With this, the speeds are great:

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/dfc6b822-24d0-4ea1-b474-4e4b9b51b2c7)


*A steady 300MB/s write.*

In my experience, logical and physical cluster sizes don’t even come into play for mere mortals here, since all disks I was able to get my hands on are 512e. Windows knows this and will create the pool with both logical and physical sector sizes of 4KB. Google this if you want to know more, but I won’t be considering this topic in this article any further.

Incidentally, number of disks in pool has little, if any, performance impact on speeds of the virtual disk. You can have a 3 column parity space on 4 disks, which, if set as above, will perform great. With this in mind, you might have figured out already that there is no viable 4 column configuration for NTFS partition. There is no NTFS AUS that would be divisible by 3 and settable as interleave size for the virtual disk. Unfortunately, this configuration is exactly what Windows will do if given 4 disks in pool, as we have demonstrated above. To gain more parity performance, 5 disks and 5 columns are required. Then you can divide the NTFS AUS by 4 and that would be the interleave size for virtual disk on a pool with at least 5 disks.

As you can see, a lot of thought has to be given to the architecture of Storage Spaces before they are created, because once these values are set, it is hard or impossible to change them. For example, if you opted for permission-less exFAT partition, then you can’t change the partition’s size. That operation simply isn’t supported, so adding new disks into the pool will give no options. Additionally, if you opted for NTFS partition, you might be able to shrink it, create new virtual disk on the pool with correct values of NumberOfColumns and Interleave parameters and keep shrinking the original partition until all of the data is moved over. Of course the best course of action is to move the data off the pool completely, recreate the VirtualDisk and move the data back.

The last step here is to disclose to any potential readers how to actually do all this without the use of Control Panel, since the UI doesn’t give the user ability to change any of these parameters. It’s quite simple actually – but you will need to use PowerShell. Not to worry, all Windows 10 installations come with PowerShell preinstalled, so just run it from your Start menu. Microsoft, in all of it’s wisdom, has included 4 different ways of running PowerShell:

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/a3e3b362-c1e9-4510-b884-abef664e1bac)


*4 options to run PS in Windows 10 x64*

Just run the one highlighted above. The procedure is as follows:

### 1. Create a Pool

Create a Pool as you normally would. You can do this from Control Panel, same as before. If you have a pool already (from past Windows installations, for example), you might need to upgrade the pool. If this is the case, you can launch Storage Spaces from Control Panel and you will see the label “Upgrade pool” by the pool UI area.

### 2. PowerShell

Run PowerShell as Administrator. Right-click it in the Start menu and select Run as Administrator. When opened, you will need to run a command. In PowerShell, these are called cmdlets:

```
New-VirtualDisk -StoragePoolFriendlyName data-pool -FriendlyName data -ResiliencySettingName Parity -ProvisioningType Thin -Interleave 64KB  -NumberOfColumns 5 -Size 43TB
```

In this command, the following placeholders can/have to be replaced:

| Placeholder     | Description                                                                                                                                                                                                                                                                                                                                                      | Example              |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------|
| YourPoolsName   | Name of the pool. This will not be shown anywhere in Explorer, it’s just a name of your pool that holds all of your drives.                                                                                                                                                                                                                                      | POOL1                |
| Interleave      | Length of your stripe excluding the parity slab. Your future AUS must be wholly divisible by interleave value*                                                                                                                                                                                                                                                   | 64KB                 |
| FastParity      | Name of the virtual disk (not the partition). Again, this is not used in Explorer.                                                                                                                                                                                                                                                                               | data |
| Size            | You can enter any size. Virtual disk can be smaller or larger than size of the Pool. Storage Spaces will let your create a 20TB virtual disk on a pool with combined size of 8TB. You can just add more drives to the pool once you start running out of space on the pool. You can also increase the size of the virtual disk in the future, but not shrink it. | 43TB                  |
| NumberOfColumns | Number of columns including parity columns. This is all well explained above. You can’t set this number higher than the amount of physical drives in the pool, but you can set it lower, and in some cases you have to in order to avoid performance issues.                                                                                                     | 5                    |

*Placeholders to go over before executing the PS command.*

## Pay attention to this. 

Interleave value must be lower or equal to AUS value. Interleave value of 32KB and AUS value of 64KB is OK, because 64/32 = 2. However, Interleave value of 64KB and AUS value of 32KB is not OK, because 32/64 = 0.5 which is not a whole number.

Be sure to replace appropriate parameter values before hitting Enter.

Now that your virtual disk (or Space) is created, you can head right into Disk Management console of Windows, and you will find new, empty disk chilling in there:

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/53a4aaac-d672-46c2-8ba3-a5a426306513)


*Newly created parity space is visible in Disk Management window.*

From here, it’s business as usual. Create new volume and partition as you normally would’ve, and do not forget to set the AUS correctly. Now you are able to enjoy full speeds.

Sources:

https://social.technet.microsoft.com/Forums/en-US/64aff15f-2e34-40c6-a873-2e0da5a355d2/parity-storage-space-so-slow-that-its-unusable?forum=winserver8gen

https://social.technet.microsoft.com/wiki/contents/articles/15200.storage-spaces-designing-for-performance.aspx

https://storagespaceswarstories.com/storage-spaces-and-slow-parity-performance/

# Comments










