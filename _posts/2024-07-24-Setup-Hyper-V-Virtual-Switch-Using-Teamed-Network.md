---
title: 'Setup Hyper-V Virtual Switch Using Teamed Network'
collection: posts
date: 2024-07-24
permalink: /posts/2024-07-24-Setup-Hyper-V-Virtual-Switch-Using-Teamed-Network
tags:
  - Windows Server
  - Windows
  - Hyper-V
  - Server 2022
---

Post Template
## Create Network Team
1. Open Windows Server Manager and click on Local Server then NIC Teaming
   ![image](https://github.com/user-attachments/assets/c5207d24-4a1b-4554-ac09-95c1dd6f77f9)

2. Click on Tasks then New Team
   ![image](https://github.com/user-attachments/assets/338aae9f-6c62-4b96-a00d-0c69fa893d2b)

3. Name team "VM_TEAM"
4. Select Network Adapters for the team
5. Under Additional Properties, choose a Standby Adapter
   ![image](https://github.com/user-attachments/assets/5c238d33-1c5a-4ea9-87df-d9a2f706c45a)

6. Open PowerShell as an Admin and run the below commands
   ```
   New-VMSwitch -Name "VM_SWITCH" -NetAdapterName "VM_TEAM" -AllowNetLbfoTeams $true -AllowManagementOS $false
   Get-VMSwitch -Name VM_SWITCH | Select-Object *RSC*
   Set-VMSwitch -Name VM_SWITCH -EnableSoftwareRsc $false
   ```
   ![image](https://github.com/user-attachments/assets/d9c1129d-a57a-431c-9373-c968a16b36bc)

7. Open Network and Sharing Center, Change Adapter Settings, Open the network adapter properties for each NIC that is teamed

   ![image](https://github.com/user-attachments/assets/0ad2c58c-a4e9-40ae-b76f-8d48976d10ba)

8. Under Properties, click Configure
   ![image](https://github.com/user-attachments/assets/768d0e67-4cb1-4927-b756-78a38da61110)

9. Under the Advanced Tab, Disable each property with the name of "Virtual Machine Queues"
   ![image](https://github.com/user-attachments/assets/1550058f-76a8-4b1f-94ee-3af9a2ae00e6)
