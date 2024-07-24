---
title: 'Set Windows Recovery to Front of Volume'
collection: posts
date: 2024-07-24
permalink: /posts/2024-07-24-Set-Windows-Recovery-to-Front-of-Volume
tags:
  - Windows
  - Server
  - 2022
  - Recovery
---

This post shows how to set the recovery partition during OS deployment

1. Use [Shift]+[F10] to open command prompt while in the windows pre-boot enviroment
2. Open Disk Part by typing `diskpart`
     ![image](https://github.com/user-attachments/assets/34a9a886-e584-4827-bd5f-ec900cad754e)

3. Use `List Disk` to show all attached disks
     ![image](https://github.com/user-attachments/assets/0c966fc4-91f6-45b7-98e4-384906ac51c8)

4. Use `Select disk` to select your disk, in this case `select disk 0`
     ![image](https://github.com/user-attachments/assets/bf979bcb-4f5a-4082-82a6-32316448a696)

5. Use `list vol` and `list part` to show the disks details
     ![image](https://github.com/user-attachments/assets/9c646892-736d-45cc-a1c6-f089cd740bb7)

6. Use `create part primary size=1025` to create a 1GB partition at the start of the disk
     ![image](https://github.com/user-attachments/assets/9e0a66d6-ee0a-4f9f-a876-78f995cefe9e)

7. Use `format quick fs=ntfs label="Recovery"` to format and label the partition
     ![image](https://github.com/user-attachments/assets/c1adc523-3c1e-437a-adf2-7a296eab6201)




```
diskpart
select disk 0
list vol
list part

rem == 2. Recovery partition =====================
rem ==    a. Create the Recovery partition =======
create part primary size=1024
format quick fs=ntfs label="Windows"

rem == 2. Windows partition =====================
rem ==    a. Create the Windows partition =======
create partition primary
rem ==    c. Prepare the Windows partition ====== 
format quick fs=ntfs label="Windows"
assign letter="W"
list volume
exit
```
