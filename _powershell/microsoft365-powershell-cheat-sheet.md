---
title: 'Microsoft 365 : Cleanup Cheat Sheet'
collection: powershell
permalink: /powershell/microsoft365-cleanup-cheat-sheet
tags:
  - Microsoft 365
  - Powershell
  - Office 365
---

This blog contains powershell snippits that I find useful when cleaning up a Microsoft 365 Tenant


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
