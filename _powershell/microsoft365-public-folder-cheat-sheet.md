---
title: 'Microsoft 365 : Public Folder Cheat Sheet'
collection: powershell
date: 2024-03-05
permalink: /powershell/microsoft365-public-folder-cheat-sheet
tags:
  - Microsoft 365
  - Powershell
  - Office 365
---

This blog contains powershell snippits that I find useful


# Public Folders
## Public Folder Creation
- In this example we are setting up an Exchange Online folder called <ins>"Professional Services"</ins> with the path of  <ins>"\Finance Docs"
- Replace <ins>"Professional Services"</ins> with the name you would like your public folder to have
- Replace <ins>"\Finance Docs"</ins> with the path you would like for your public folder
```
New-PublicFolder -"Professional Services" -path "\Finance Docs";
```
	
## Public Folder Deletion
- In this example we are deleting an Exchange Online folder with the path of  <ins>"\Finance Docs"
- Replace <ins>"\Finance Docs"</ins> with the path you would like for your public folder
```
Remove-PublicFolder -Identity "\Finance Docs" -Recurse;
```
	
## Public Folder Renaming
- In this example we are renaming an Exchange Online public folder with the path of  <ins>"\Finance Docs"</ins> to the name of <ins>"Professional Services"
- Replace <ins>"Professional Services"</ins> with the name you would like your public folder to have
- Replace <ins>"\Finance Docs"</ins> with the path of your public folder
```
set-publicfolder "\Finance Docs" -Name "Professional Services";
```

## Public Folder Apply Permissions
- In this example we are adding <ins>"matthew.kolakowski@kolakowski.us"</ins> to the Reviewer  called <ins>"Professional Services"</ins> With the path of  <ins>"\Finance Docs"
- Replace <ins>"matthew.kolakowski@kolakowski.us"</ins> with the email you would like to have permissions
- Replace <ins>"\Finance Docs"</ins> with the path you would like for your public folder
- Replace Reviewer with the permission level of your choosing [Owner, PublishingEditor, Reviewer, Author, Author, NonEditingAuthor, Contributor]
```
Add-PublicFolderClientPermission -Identity "\Finance Docs" -User matthew.kolakowski@kolakowski.us -AccessRights Reviewer;
```

## Public Folder Check Permission
- In this example we are getting the permissions for an Exchange Online folder with the path of  <ins>"\Finance Docs"
- Replace <ins>"\Finance Docs"</ins> with the path of you public folder
```
Get-PublicFolderClientPermission -Identity "\Finance Docs";
```
