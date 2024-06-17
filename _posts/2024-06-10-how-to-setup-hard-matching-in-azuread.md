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
# Import the AzureAD module
Import-Module AzureAD

# Connect to Azure AD
Connect-AzureAD

# Users AD UPN
$UserIdentity = "example"

# Users M365 UPN
$AzureIdentity = "example@example.com"

# Generates ImmutableID from AD
$ImmutableID = [system.convert]::ToBase64String(([guid](get-aduser -identity $UserIdentity ).objectguid).ToByteArray())

#Sets ImmutableID in AzureAD
Set-AzureAdUser -ObjectID $AzureIdentity -ImmutableID $ImmutableID

#Outputs results
Get-AzureADUser -ObjectID $AzureIdentity | select UserPrincipalName,ObjectID,ImmutableID

#Runs Delta Sync, Only if on DC
Start-ADSyncSyncCycle -PolicyType Delta

Pause
```
