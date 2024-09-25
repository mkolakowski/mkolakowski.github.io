---
title: 'Microsoft 365 : Teams Cheat Sheet'
collection: powershell
permalink: /powershell/microsoft365-teams-cheat-sheet-index
tags:
  - Microsoft 365
  - Powershell
  - Office 365
  - Teams
---

This blog contains powershell snippits that I find useful in administring teams


# Teams
## Teams Creation
- In this example, we are creating a teams group with the display name of <ins>"Kolakowski General"</ins> and the mail alias of Kolakowski.General 
- Replace <ins>"Kolakowski General"</ins> with the display name you would like for your team
- Replace Kolakowski.General with the alias you would like to give your new Teams Group
  - Note: do not enter the domain here, M365 will choose the default domain
```
New-PnPTeamsTeam -DisplayName "Kolakowski General" -MailNickName Kolakowski.General;
```
	
## Teams Update SMTP
- In this Example, we are setting Kolakowski.General@kolakowski.us as the primary SMTP for the  <ins>"Kolakowski General"</ins> Teams
- Replace <ins>"Kolakowski General"</ins> with the display name you set for your Team
- Replace Kolakowski.General with the alias you gave to your new Teams Group
- Replace Kolakowski.General@kolakowski.us with the email address you would like to set for your teams
```
Set-UnifiedGroup -Identity "Kolakowski General" -Alias Kolakowski.General -PrimarySmtpAddress Kolakowski.General@kolakowski.us;
```
	
## Teams add email alias
- In this Example, we are setting Kolakowski.General.Email@kolakowski.us as an email alias for the  <ins>"Kolakowski General"</ins> Teams
- Replace <ins>"Kolakowski General"</ins> with the display name you set for your Team
- Replace Kolakowski.General with the alias you gave to your new Teams Group
- Replace Kolakowski.General.Email@kolakowski.us with the address you would like to add as an alias for your 
```
Set-UnifiedGroup -Identity "Kolakowski General"  -EmailAddresses @{Add=Kolakowski.General.Email@kolakowski.us};
```
	
## Teams Permissions
- In this example we are adding matthew.kolakowski@kolakowski.us as a Member to the af626252-39ea-4638-ac0d-008901b13bef Team
- Replace the Teams GroupID, af626252-39ea-4638-ac0d-008901b13bef with your Teams GroupID
- Replace matthew.kolakowski@kolakowski.us with the email of the user you would like to apply the permission to
- Member can be changed to Owner to apply Owner level permissions
```
Add-PnPTeamsUser -Team af626252-39ea-4638-ac0d-008901b13bef -User "matthew.kolakowski@kolakowski.us" -Role Member;
```
	
## Teams Channel Creation
- In this example, we are creating a new teams channel with the name of <ins>"Meeting Notes"
- Replace the Teams GroupID, af626252-39ea-4638-ac0d-008901b13bef with your Teams GroupID
- Replace <ins>"Meeting Notes"</ins> with the name of your teams channel
```
New-TeamChannel -GroupId af626252-39ea-4638-ac0d-008901b13bef -DisplayName "Meeting Notes" -MembershipType Private;
```

## Teams Channel Permissions
- In this example we are adding matthew.kolakowski@kolakowski.us as a Member to the <ins>"Discussions, thoughts, ideas"</ins> channel inside of af626252-39ea-4638-ac0d-008901b13bef
- Replace the Teams GroupID, af626252-39ea-4638-ac0d-008901b13bef with your Teams GroupID
- Replace <ins>"Discussions, thoughts, ideas"</ins> with the name of your channel
- Replace matthew.kolakowski@kolakowski.us with the email of the user you would like to apply the permission to
- Member can be changed to Owner to apply Owner level permissions
  - Note: User must ALREADY be a member of the team the channel lives in
```
Add-PnPTeamsUser -Team af626252-39ea-4638-ac0d-008901b13bef -Channel "Discussions, thoughts, ideas" -User "matthew.kolakowski@kolakowski.us" -Role Member;
```
