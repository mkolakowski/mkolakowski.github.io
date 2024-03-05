---
title: 'Microsoft 365 PS Cheat Sheet'
date: 2024-03-05
permalink: /posts/2024/03/Microsoft-365-PS-Cheat-Sheet/
tags:
  - Microsoft 365
  - Powershell
  - category2
---

# How to send email via PowerShell
## Direct Send
- Place your Distro Groups name in place of <ins>"Kolakowski All"</ins>  (needs to be in quotes)
- Change allusers to what you would like the distro groups email alias to be
  - Note: do not enter the domain here, M365 will choose the default domain
- In this example we are creating the <ins>"Kolakowski All"</ins> Distro Group with an alias of allusers
```
New-DistributionGroup -Name "Kolakowski All" -Alias allusers;
```

# Distribution Lists
## Distro Creation
- Place your Distro Groups name in place of <ins>"Kolakowski All"</ins> (needs to be in quotes)
- Change allusers to what you would like the distro groups email alias to be
  - Note: do not enter the domain here, M365 will choose the default domain
- In this example we are creating the <ins>"Kolakowski All"</ins> Distro Group with an alias of allusers
```
New-DistributionGroup -Name "Kolakowski All"-Alias allusers;
```
	
## Distro Allow Outside
- Place your Distro Groups name in place of <ins>"Kolakowski All"</ins> (needs to be in quotes)
- Change False to True if you do not want outside senders to be able to email the Distro Group
- In this example we are setting the <ins>"Kolakowski All"</ins> Distro Group to accept emails from inside and outside the Domain
```
Set-DistributionGroup -Identity "Kolakowski All" -RequireSenderAuthenticationEnabled $False
```

## Distro Set SMTP
- Place your Distro Groups name in place of <ins>"Kolakowski All"</ins> (needs to be in quotes)
- Replace Allusers@kolakowski.us with the full email you want for the Distro Group
  - This may not be needed if the domain is the primary domain
- In this Example, we are setting Allusers@kolakowski.us as the primary SMTP for the  <ins>"Kolakowski All"</ins> Distro Group
```
Set-DistributionGroup "Kolakowski All" -PrimarySmtpAddress Allusers@kolakowski.us;
```
	
## Distro Membership
- Replace Allusers@kolakowski.us with the email address of your Distro Group
- Replace matthew.kolakowski@kolakowski.us with the UPN (not primary SMTP) of the user you want to be added to the Distro Group
- In this example, we are adding matthew.kolakowski@kolakowski.us to the Allusers@kolakowski.us  Distro Group
```
Add-DistributionGroupMember Allusers@kolakowski.us -Member matthew.kolakowski@kolakowski.us;
```

## Distribution Group Alias
- Replace Allusers@kolakowski.us with the email address of your Distro Group
- Replace All@kolakowski.us with the email address you would like to add as a secondary email address
- In this example, we are adding All@kolakowski.us as an email alias of Allusers@kolakowski.us
```
Set-DistributionGroup Allusers@kolakowski.us -emailaddresses @{Add=’smtp:All@kolakowski.us’};
```

# Mailbox
## Mailbox Creation (Room)
- Replace matthew.kolakowski@kolakowski.us with the UPN (not primary SMTP) with the email you would like your Room Resource to have
- Replace <ins>"Matthew Kolakowski's Office"</ins> with the name you would like for your Room Resource
- In this example we are setting up a Room Resource with the email of matthew.kolakowski@kolakowski.us and display name of <ins>"Matthew Kolakowski's Office"
```
New-Mailbox matthew.kolakowski@kolakowski.us -room -Displayname "Matthew Kolakowski's Office";
```

## Mailbox Creation (Shared)
- Replace matthew.kolakowski@kolakowski.us with the UPN (not primary SMTP) with the email you would like your Shared Mailbox to have
- Replace <ins>"Matthew Kolakowski's Shared"</ins> with the name you would like for your Shared Mailbox
- In this example we are setting up a Shared Mailbox with the email of matthew.kolakowski@kolakowski.us and display name of <ins>"Matthew Kolakowski's Shared"
```
New-Mailbox matthew.kolakowski@kolakowski.us -Shared -Displayname "Matthew Kolakowski's Shared";
```

## Mailbox Convert to Shared
- Replace matthew.kolakowski@kolakowski.us with the UPN (not primary SMTP) of the mailbox you would like to convert to a shared mailbox
- In this example we are converting matthew.kolakowski@kolakowski.us into a shared mailbox
```
Set-Mailbox matthew.kolakowski@kolakowski.us -Type Shared;
```
	
## Mailbox Alias
- Replace matthew.kolakowski@kolakowski.us with the UPN (not primary SMTP) of the user you want to add the email alias to
- Replace Matthew@kolakowski.us with an email address you would like to set as an email alias
- In this example we are giving matthew.kolakowski@kolakowski.us an email alias of Matthew@kolakowski.us
```
Set-Mailbox matthew.kolakowski@kolakowski.us -EmailAddresses @{add='smtp:Matthew@kolakowski.us'};
```

## Mailbox Access Rights (FullAccess)
- Replace matthew.kolakowski@kolakowski.us with the UPN (not primary SMTP) of the user you want to grant the permissions from
- Replace Kayla.Kolakowski@kolakowski.us with the UPN (not primary SMTP) of the user you want to grant FullAccess Permissions to
- In this example we are giving Kayla.Kolakowski@kolakowski.us the Full Access permission to the matthew.kolakowski@kolakowski.us Mailbox
```
Add-MailboxPermission -Identity matthew.kolakowski@kolakowski.us -User Kayla.Kolakowski@kolakowski.us -AccessRights FullAccess;
```
## Mailbox Access Rights (SendAs)
- Replace matthew.kolakowski@kolakowski.us with the UPN (not primary SMTP) of the user you want to grant the permissions from
- Replace Kayla.Kolakowski@kolakowski.us with the UPN (not primary SMTP) of the user you want to grant Send As Permissions to
- In this example we are giving Kayla.Kolakowski@kolakowski.us the Send As  to the matthew.kolakowski@kolakowski.us Mailbox
```
Add-RecipientPermission matthew.kolakowski@kolakowski.us -AccessRights SendAs -Trustee Kayla.Kolakowski@kolakowski.us -Confirm;
```

## Mailbox Access Rights (SendOnBehalf)
- Replace matthew.kolakowski@kolakowski.us with the UPN (not primary SMTP) of the user you want to grant the permissions from
- Replace Kayla.Kolakowski@kolakowski.us with the UPN (not primary SMTP) of the user you want to grant Send As Permissions to
- In this example we are giving Kayla.Kolakowski@kolakowski.us the Send As  to the matthew.kolakowski@kolakowski.us Mailbox
```
Set-Mailbox -Identity matthew.kolakowski@kolakowski.us -GrantSendonBehalfTo Kayla.Kolakowski@kolakowski.us;
```

# Public Folders
## Public Folder Creation
- Replace <ins>"Professional Services"</ins> with the name you would like your public folder to have
- Replace <ins>"\Finance Docs"</ins> with the path you would like for your public folder
- In this example we are setting up an Exchange Online folder called <ins>"Professional Services"</ins> with the path of  <ins>"\Finance Docs"
```
New-PublicFolder -"Professional Services" -path "\Finance Docs";
```
	
## Public Folder Deletion
- Replace <ins>"\Finance Docs"</ins> with the path you would like for your public folder
- In this example we are deleting an Exchange Online folder with the path of  <ins>"\Finance Docs"
```
Remove-PublicFolder -Identity "\Finance Docs" -Recurse;
```
	
## Public Folder Renaming
- Replace <ins>"Professional Services"</ins> with the name you would like your public folder to have
- Replace <ins>"\Finance Docs"</ins> with the path of your public folder
- In this example we are renaming an Exchange Online public folder with the path of  <ins>"\Finance Docs"</ins> to the name of <ins>"Professional Services"
```
set-publicfolder "\Finance Docs" -Name "Professional Services";
```

## Public Folder Apply Permissions
- Replace <ins>"matthew.kolakowski@kolakowski.us"</ins> with the email you would like to have permissions
- Replace <ins>"\Finance Docs"</ins> with the path you would like for your public folder
- Replace Reviewer with the permission level of your choosing [Owner, PublishingEditor, Reviewer, Author, Author, NonEditingAuthor, Contributor]
- In this example we are adding <ins>"matthew.kolakowski@kolakowski.us"</ins> to the Reviewer  called <ins>"Professional Services"</ins> With the path of  <ins>"\Finance Docs"
```
Add-PublicFolderClientPermission -Identity "\Finance Docs" -User matthew.kolakowski@kolakowski.us -AccessRights Reviewer;
```

## Public Folder Check Permission
- Replace <ins>"\Finance Docs"</ins> with the path of you public folder
- In this example we are getting the permissions for an Exchange Online folder with the path of  <ins>"\Finance Docs"
```
Get-PublicFolderClientPermission -Identity "\Finance Docs";
```

# Teams
## Teams Creation
- Replace <ins>"Kolakowski General"</ins> with the display name you would like for your team
- Replace Kolakowski.General with the alias you would like to give your new Teams Group
- In this example, we are creating a teams group with the display name of <ins>"Kolakowski General"</ins> and the mail alias of Kolakowski.General 
  - Note: do not enter the domain here, M365 will choose the default domain
```
New-PnPTeamsTeam -DisplayName "Kolakowski General" -MailNickName Kolakowski.General;
```
	
## Teams Update SMTP
- Replace <ins>"Kolakowski General"</ins> with the display name you set for your Team
- Replace Kolakowski.General with the alias you gave to your new Teams Group
- Replace Kolakowski.General@kolakowski.us with the email address you would like to set for your teams
- In this Example, we are setting Kolakowski.General@kolakowski.us as the primary SMTP for the  <ins>"Kolakowski General"</ins> Teams
```
Set-UnifiedGroup -Identity "Kolakowski General" -Alias Kolakowski.General -PrimarySmtpAddress Kolakowski.General@kolakowski.us;
```
	
## Teams add email alias
- Replace <ins>"Kolakowski General"</ins> with the display name you set for your Team
- Replace Kolakowski.General with the alias you gave to your new Teams Group
- Replace Kolakowski.General.Email@kolakowski.us with the address you would like to add as an alias for your 
- In this Example, we are setting Kolakowski.General.Email@kolakowski.us as an email alias for the  <ins>"Kolakowski General"</ins> Teams
```
Set-UnifiedGroup -Identity "Kolakowski General"  -EmailAddresses @{Add=Kolakowski.General.Email@kolakowski.us};
```
	
## Teams Permissions
- Replace the Teams GroupID, af626252-39ea-4638-ac0d-008901b13bef with your Teams GroupID
- Replace matthew.kolakowski@kolakowski.us with the email of the user you would like to apply the permission to
- Member can be changed to Owner to apply Owner level permissions
- In this example we are adding matthew.kolakowski@kolakowski.us as a Member to the af626252-39ea-4638-ac0d-008901b13bef Team
```
Add-PnPTeamsUser -Team af626252-39ea-4638-ac0d-008901b13bef -User "matthew.kolakowski@kolakowski.us" -Role Member;
```
	
## Teams Channel Creation
- Replace the Teams GroupID, af626252-39ea-4638-ac0d-008901b13bef with your Teams GroupID
- Replace <ins>"Meeting Notes"</ins> with the name of your teams channel
- In this example, we are creating a new teams channel with the name of <ins>"Meeting Notes"
```
New-TeamChannel -GroupId af626252-39ea-4638-ac0d-008901b13bef -DisplayName "Meeting Notes" -MembershipType Private;
```

## Teams Channel Permissions
- Replace the Teams GroupID, af626252-39ea-4638-ac0d-008901b13bef with your Teams GroupID
- Replace <ins>"Discussions, thoughts, ideas"</ins> with the name of your channel
- Replace matthew.kolakowski@kolakowski.us with the email of the user you would like to apply the permission to
- Member can be changed to Owner to apply Owner level permissions
- In this example we are adding matthew.kolakowski@kolakowski.us as a Member to the <ins>"Discussions, thoughts, ideas"</ins> channel inside of af626252-39ea-4638-ac0d-008901b13bef
  - Note: User must ALREADY be a member of the team the channel lives in
```
Add-PnPTeamsUser -Team af626252-39ea-4638-ac0d-008901b13bef -Channel "Discussions, thoughts, ideas" -User "matthew.kolakowski@kolakowski.us" -Role Member;
```

# Tenant Clean Up RUN WITH CATUTION
## Delete all soft Deleted users
- This will Delete all users showing in the deleted users page
- https://admin.microsoft.com/Adminportal/Home?#/deletedusers
```
Get-MsolUser –ReturnDeletedUsers | Remove-MsolUser –RemoveFromRecycleBin
```
	
## Delete all soft deleted Groups
- This will delete all groups showing in Deleted Groups
- https://admin.microsoft.com/Adminportal/Home?#/deletedgroups
```
Get-AzureADMSDeletedGroup | Remove-AzureADMSDeletedDirectoryObject
```
	
## Delete all Groups
- This will delete all groups showing in Deleted Groups
- https://admin.microsoft.com/Adminportal/Home?#/deletedgroups
```
Get-UnifiedGroup | ForEach-Object { Write-Host "Removing group:" $_.DisplayName; Remove-UnifiedGroup -Identity $_.ExternalDirectoryObjectId -Confirm:$false }
```
	
## Delete all Soft deleted SharePoint sites
- This will delete all groups showing in Deleted Groups
```
Get-SPODeletedSite -IncludePersonalSite | Remove-SPODeletedSite
```

## Delete all Soft deleted SharePoint sites
- This will delete all groups showing in Deleted Groups
```
Get-SPOSite -Limit All | ForEach-Object { Write-Host "Removing SharePoint:" $_.DisplayName; Remove-SPOSite -Identity $_.Url -Confirm:$false }
```

