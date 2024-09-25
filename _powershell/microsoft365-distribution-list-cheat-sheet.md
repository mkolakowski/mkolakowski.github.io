---
title: 'Microsoft 365 : Distribution List Cheat Sheet'
collection: powershell
permalink: /powershell/microsoft365-distribution-list-cheat-sheet
tags:
  - Microsoft 365
  - Powershell
  - Office 365
  - Distrobution Lists
---

This blog contains powershell snippits that I find useful

# Distribution Lists

## Distro Creation
- In this example we are creating the <ins>"Kolakowski All"</ins> Distro Group with an alias of allusers
- Place your Distro Groups name in place of <ins>"Kolakowski All"</ins> (needs to be in quotes)
- Change allusers to what you would like the distro groups email alias to be
  - Note: do not enter the domain here, M365 will choose the default domain
```
New-DistributionGroup -Name "Kolakowski All"-Alias allusers;
```
	
## Distro Allow Outside
- In this example we are setting the <ins>"Kolakowski All"</ins> Distro Group to accept emails from inside and outside the Domain
- Place your Distro Groups name in place of <ins>"Kolakowski All"</ins> (needs to be in quotes)
- Change False to True if you do not want outside senders to be able to email the Distro Group
```
Set-DistributionGroup -Identity "Kolakowski All" -RequireSenderAuthenticationEnabled $False
```

## Distro Set SMTP
- In this Example, we are setting Allusers@kolakowski.us as the primary SMTP for the  <ins>"Kolakowski All"</ins> Distro Group
- Place your Distro Groups name in place of <ins>"Kolakowski All"</ins> (needs to be in quotes)
- Replace Allusers@kolakowski.us with the full email you want for the Distro Group
  - This may not be needed if the domain is the primary domain
```
Set-DistributionGroup "Kolakowski All" -PrimarySmtpAddress Allusers@kolakowski.us;
```
	
## Distro Membership
- In this example, we are adding matthew.kolakowski@kolakowski.us to the Allusers@kolakowski.us  Distro Group
- Replace Allusers@kolakowski.us with the email address of your Distro Group
- Replace matthew.kolakowski@kolakowski.us with the UPN (not primary SMTP) of the user you want to be added to the Distro Group
```
Add-DistributionGroupMember Allusers@kolakowski.us -Member matthew.kolakowski@kolakowski.us;
```

## Distribution Group Alias
- In this example, we are adding All@kolakowski.us as an email alias of Allusers@kolakowski.us
- Replace Allusers@kolakowski.us with the email address of your Distro Group
- Replace All@kolakowski.us with the email address you would like to add as a secondary email address
```
Set-DistributionGroup Allusers@kolakowski.us -emailaddresses @{Add=’smtp:All@kolakowski.us’};
```

	
## Delete all Groups
- This will delete all groups showing in Deleted Groups
- https://admin.microsoft.com/Adminportal/Home?#/deletedgroups
```
Get-UnifiedGroup | ForEach-Object { Write-Host "Removing group:" $_.DisplayName; Remove-UnifiedGroup -Identity $_.ExternalDirectoryObjectId -Confirm:$false }
```
	
