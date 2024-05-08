---
title: 'How to disable SharePoint and OneDrive Sync Buttons'
collection: posts
date: 2024-05-08
permalink: /posts/2024-05-08-SharePoint-Online-Disable-Sync-Button
tags:
  - Microsoft 365
  - SharePoint Online
  - Office 365
  - OneDrive
---

Need to make this go away?
![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/4700f11f-fca5-4606-8db4-6d765abacecc)


## Steps
1. Navigate to your SharePoint Online Admin center
2. Click Settings > OneDrive Sync
3. Un-Check the "Show the Sync button on the OneDrive Website"
4. Click Save

***Note: This change will take some time to replicate***
   
![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/eb242e70-ca40-4fca-a017-b5e2c857fca1)


5. Open SharePoint Sires Legacy Settings Page
6. Open "Search and offline availability"
7. Change "Allow items from this site to be downloaded to offline clients?" from "Yes" to "No"
8. Click "OK" To apply the setting

**Note: This must be done on a site level**
![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/aabac242-71a8-4cae-a62b-3f13ccb4c8d5)


----

# Want to do it in PowerShell?

PowerShell to Remove Sync Button in All Sites of the Site Collection
Let’s hide the sync button for a SharePoint Online Site Collection.

```
#Load SharePoint CSOM Assemblies
Add-Type -Path "C:\Program Files\Common Files\Microsoft Shared\Web Server Extensions\16\ISAPI\Microsoft.SharePoint.Client.dll"
Add-Type -Path "C:\Program Files\Common Files\Microsoft Shared\Web Server Extensions\16\ISAPI\Microsoft.SharePoint.Client.Runtime.dll"
 
Function Disable-SPOSyncButton([String]$SiteURL)
{  
    Try{
        #Setup the context
        $Ctx = New-Object Microsoft.SharePoint.Client.ClientContext($SiteURL)
        $Ctx.Credentials = $Credentials
 
        #Get the Web and Sub Sites
        $Web = $Ctx.Web
        $Ctx.Load($Web)
        $Ctx.Load($Web.Webs)
        $Ctx.ExecuteQuery()
 
        $Web.ExcludeFromOfflineClient=$true
        $Web.Update()
        $Ctx.ExecuteQuery()
        Write-Host -f Green "Sync Button is Disabled for the Site:" $($SiteURL)
 
        #Iterate through each subsite of the current web
        ForEach ($Subweb in $Ctx.Web.Webs)
        {
            #Call the function recursively 
            Disable-SPOSyncButton -SiteURL $Subweb.url
        }
    }
    Catch {
        write-host -f Red "Error Disabling Sync Button!" $_.Exception.Message
    }
}
 
#Set parameter values
$SiteURL="https://crescent.sharepoint.com/"
 
#Get Credentials to connect
$Cred= Get-Credential
$Credentials = New-Object Microsoft.SharePoint.Client.SharePointOnlineCredentials($Cred.Username, $Cred.Password)
 
#Call the function 
Disable-SPOSyncButton -SiteURL $SiteURL
```

Disable the Sync Button for the SharePoint Online Tenant
To completely hide the sync button for all SharePoint Online sites, use the below script:

```
#Connect to SharePoint Online
Connect-SPOService -url https://crescent-admin.sharepoint.com
 
Set-SPOTenant -HideSyncButtonOnTeamSite:$false
```

Thanks to Salaudeen Rajack for the powershel: https://www.sharepointdiary.com/2017/08/disable-sync-button-in-sharepoint-online
