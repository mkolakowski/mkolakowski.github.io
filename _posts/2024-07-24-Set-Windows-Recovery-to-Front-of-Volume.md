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

6. Use `create part primary size=1024` to create a 1GB partition at the start of the disk
   ![image](https://github.com/user-attachments/assets/9e0a66d6-ee0a-4f9f-a876-78f995cefe9e)

7. Use `format quick fs=ntfs label="Recovery"` to format and label the recovery partition
   ![image](https://github.com/user-attachments/assets/c1adc523-3c1e-437a-adf2-7a296eab6201)

8. Use `assign letter="R"` to assign the partition a drive letter
   ![image](https://github.com/user-attachments/assets/b9b005e3-2d12-4d80-a039-09487a452c79)

9. Use `set id="de94bba4-06d1-4d40-a16a-bfd50179d6ac"` to set the partitions ID
   ![image](https://github.com/user-attachments/assets/7d29c81d-68c5-43a5-9a8a-dea217a452b3)

10. Use `gpt attributes=0x8000000000000001` to set the recovery drives attributes
   ![image](https://github.com/user-attachments/assets/5721acb1-ec0c-4892-b2d6-c0fb2a829f97)

11. Use `list volume` to verify the changes
   ![image](https://github.com/user-attachments/assets/4dce259c-1392-45af-8d40-58c01da5c7cb)

12. Use `Create part primary` to create the partition the OS will reside on. This consumes any remaining space on the disk
    ![image](https://github.com/user-attachments/assets/064cee94-8126-47e6-a643-50704cbad53e)

13. Use `format quick fs=ntfs label="Windows_C"` to format and label the OS partition
    ![image](https://github.com/user-attachments/assets/daeb16bb-c438-4535-87af-8dc03decda2d)

14. Use `list volume` to verify the changes
    ![image](https://github.com/user-attachments/assets/41c11ede-6c39-4252-b3aa-96e25e3649cb)

15. Use `exit` to close DISKPART


Selectable commands
```
diskpart

list disk
select disk 0
list vol
list part

create part primary size=1024
format quick fs=ntfs label="Recovery"
assign letter="R"
set id="de94bba4-06d1-4d40-a16a-bfd50179d6ac"
gpt attributes=0x8000000000000001

list volume

create partition primary
format quick fs=ntfs label="Windows_C"

list volume

exit
```
