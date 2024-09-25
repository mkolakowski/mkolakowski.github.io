---
title: 'Microsoft 365 : Mailbox Cheat Sheet'
collection: powershell
date: 2024-03-05
permalink: /powershell/microsoft365-mailbox-cheat-sheet
tags:
  - Microsoft 365
  - Powershell
  - Office 365
---

This blog contains powershell snippits that I find useful

# Mailbox
## Mailbox Creation (Room)
- In this example we are setting up a Room Resource with the email of matthew.kolakowski@kolakowski.us and display name of <ins>"Matthew Kolakowski's Office"
- Replace matthew.kolakowski@kolakowski.us with the UPN (not primary SMTP) with the email you would like your Room Resource to have
- Replace <ins>"Matthew Kolakowski's Office"</ins> with the name you would like for your Room Resource
```
New-Mailbox matthew.kolakowski@kolakowski.us -room -Displayname "Matthew Kolakowski's Office";
```

## Mailbox Creation (Shared)
- In this example we are setting up a Shared Mailbox with the email of matthew.kolakowski@kolakowski.us and display name of <ins>"Matthew Kolakowski's Shared"
- Replace matthew.kolakowski@kolakowski.us with the UPN (not primary SMTP) with the email you would like your Shared Mailbox to have
- Replace <ins>"Matthew Kolakowski's Shared"</ins> with the name you would like for your Shared Mailbox
```
New-Mailbox matthew.kolakowski@kolakowski.us -Shared -Displayname "Matthew Kolakowski's Shared";
```

## Mailbox Convert to Shared
- In this example we are converting matthew.kolakowski@kolakowski.us into a shared mailbox
- Replace matthew.kolakowski@kolakowski.us with the UPN (not primary SMTP) of the mailbox you would like to convert to a shared mailbox
```
Set-Mailbox matthew.kolakowski@kolakowski.us -Type Shared;
```
	
## Mailbox Alias
- In this example we are giving matthew.kolakowski@kolakowski.us an email alias of Matthew@kolakowski.us
- Replace matthew.kolakowski@kolakowski.us with the UPN (not primary SMTP) of the user you want to add the email alias to
- Replace Matthew@kolakowski.us with an email address you would like to set as an email alias
```
Set-Mailbox matthew.kolakowski@kolakowski.us -EmailAddresses @{add='smtp:Matthew@kolakowski.us'};
```

## Mailbox Access Rights (FullAccess)
- In this example we are giving Kayla.Kolakowski@kolakowski.us Full Access permissions to the matthew.kolakowski@kolakowski.us Mailbox
- Replace matthew.kolakowski@kolakowski.us with the UPN (not primary SMTP) of the user you want to grant the permissions from
- Replace Kayla.Kolakowski@kolakowski.us with the UPN (not primary SMTP) of the user you want to grant FullAccess Permissions to
```
Add-MailboxPermission -Identity matthew.kolakowski@kolakowski.us -User Kayla.Kolakowski@kolakowski.us -AccessRights FullAccess;
```
## Mailbox Access Rights (SendAs)
- In this example we are giving Kayla.Kolakowski@kolakowski.us Send As permissions to the matthew.kolakowski@kolakowski.us Mailbox
- Replace matthew.kolakowski@kolakowski.us with the UPN (not primary SMTP) of the user you want to grant the permissions from
- Replace Kayla.Kolakowski@kolakowski.us with the UPN (not primary SMTP) of the user you want to grant Send As Permissions to
```
Add-RecipientPermission matthew.kolakowski@kolakowski.us -AccessRights SendAs -Trustee Kayla.Kolakowski@kolakowski.us -Confirm;
```

## Mailbox Access Rights (SendOnBehalf)
- In this example we are giving Kayla.Kolakowski@kolakowski.us the Send As  to the matthew.kolakowski@kolakowski.us Mailbox
- Replace matthew.kolakowski@kolakowski.us with the UPN (not primary SMTP) of the user you want to grant the permissions from
- Replace Kayla.Kolakowski@kolakowski.us with the UPN (not primary SMTP) of the user you want to grant Send As Permissions to
```
Set-Mailbox -Identity matthew.kolakowski@kolakowski.us -GrantSendonBehalfTo Kayla.Kolakowski@kolakowski.us;
```
