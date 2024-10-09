---
title: 'Microsoft 365 : Exchange Online : Distrobution List Membership'
collection: powershell
permalink: /posts/microsoft365-exchange-distrobution-list-membership
tags:
  - Microsoft 365
  - Powershell
  - Office 365
---

This script will output a csv of the membership for all Distrobution Lists

```
#Variable : CsvPath : Where the CSV is saved
$CsvPath = "C:\powershell\m365-reports\DistributionListMembers.csv"

# Import the Exchange Online PowerShell module
Import-Module ExchangeOnlineManagement

# Connect to Exchange Online
Connect-ExchangeOnline

# Get all Distribution Groups
$DistributionGroups = Get-DistributionGroup -ResultSize Unlimited

# Prepare an array to hold the results
$Results = @()

foreach ($Group in $DistributionGroups) {
    Write-Host "Processing Distribution List: $($Group.Name)"

    # Get the members of the distribution group
    $Members = Get-DistributionGroupMember -Identity $Group.Identity -ResultSize Unlimited

    # If the group has no members, create an empty entry
    if (!$Members) {
        $Results += [PSCustomObject]@{
            GroupName       = $Group.Name
            GroupEmail      = $Group.PrimarySmtpAddress
            MemberName      = ""
            MemberEmail     = ""
            MemberType      = ""
        }
    }
    else {
        foreach ($Member in $Members) {
            # Add each member to the results
            $Results += [PSCustomObject]@{
                GroupName       = $Group.Name
                GroupEmail      = $Group.PrimarySmtpAddress
                MemberName      = $Member.DisplayName
                MemberEmail     = $Member.PrimarySmtpAddress
                MemberType      = $Member.RecipientType
            }
        }
    }
}

# Export the results to a CSV file
$Results | Export-Csv -Path $CsvPath -NoTypeInformation -Encoding UTF8

Write-Host "Export completed. The CSV file is located at: $CsvPath"

# Disconnect from Exchange Online
Disconnect-ExchangeOnline -Confirm:$false

```
