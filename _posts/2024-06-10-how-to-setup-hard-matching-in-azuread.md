---
title: 'How to setup "Hard matching in AzureAD"'
collection: posts
date: 2024-06-10
permalink: /posts/2024-06-10-how-to-setup-hard-matching-in-azuread
tags:
  - Microsoft 365
  - Powershell
  - Office 365
---





```
$UserIdentity = mkolakowski
$ImmutableID = [system.convert]::ToBase64String(([guid](get-aduser -identity $UserIdentity ).objectguid).ToByteArray())
Set-AzureAdUser -ObjectID mkolakowski@example.com -ImmutableID $ImmutableID

#Get-AzureADUser -ObjectID mkolakowski | select UserPrincipalName,ObjectID,ImmutableID
```
