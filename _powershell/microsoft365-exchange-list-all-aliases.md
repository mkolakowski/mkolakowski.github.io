---
title: 'Microsoft 365 : Exchange Online : List All Aliases'
collection: powershell
date: 2024-06-28
permalink: /powershell/microsoft365-exchange-list-all-aliases
tags:
  - Microsoft 365
  - Powershell
  - Office 365
---

This script will output a csv of aliases for all Distrobution Lists and Mailboxes (regualr and shared)

```
# Install the ExchangeOnlineManagement module if not already installed
# Install-Module -Name ExchangeOnlineManagement -Scope CurrentUser

# Import the ExchangeOnlineManagement module
Import-Module ExchangeOnlineManagement

# Connect to Exchange Online
Connect-ExchangeOnline

#File Variables
$AliasesCSV = "C:\temp\Aliases.csv"

# Function to list aliases for a mailbox
function List-MailboxAliases {
    param (
        [string]$userPrincipalName
    )

    $mailbox = Get-Mailbox -Identity $userPrincipalName
    $aliases = $mailbox.EmailAddresses | Where-Object { $_ -clike "smtp:*" } | ForEach-Object { $_.ToString().Replace("smtp:", "") }
    return $aliases
}

# Function to list aliases for a distribution list
function List-DistributionListAliases {
    param (
        [string]$groupId
    )

    $distributionList = Get-DistributionGroup -Identity $groupId
    $aliases = $distributionList.EmailAddresses | Where-Object { $_ -clike "smtp:*" } | ForEach-Object { $_.ToString().Replace("smtp:", "") }
    return $aliases
}

# Prepare arrays to store results
$AliasResults = @()

# Get all mailboxes
$mailboxes = Get-Mailbox -ResultSize Unlimited

# List aliases for each mailbox and add to results array
foreach ($mailbox in $mailboxes) {
    $aliases = List-MailboxAliases -userPrincipalName $mailbox.UserPrincipalName

    foreach($alias in $aliases){
        $AliasResults += [PSCustomObject]@{
            Type    = "Mailbox"
            SMTP    = $mailbox.UserPrincipalName
            Aliases = $alias
        }
    }
}

# Get all distribution lists
$distributionLists = Get-DistributionGroup -ResultSize Unlimited

# List aliases for each distribution list and add to results array
foreach ($distributionList in $distributionLists) {
    $aliases = List-DistributionListAliases -groupId $distributionList.Identity
    
    foreach($alias in $aliases){
        $AliasResults += [PSCustomObject]@{
            Type    = "Distro"
            SMTP    = $distributionList.PrimarySmtpAddress
            Aliases = $alias
        }
    }
}

# Export results to CSV files
$AliasResults | Export-Csv -Path $AliasesCSV -NoTypeInformation -Encoding UTF8

# Disconnect from Exchange Online
Disconnect-ExchangeOnline -Confirm:$false

```
