---
title: 'Exchange 2016 : Cheat Sheet'
collection: powershell
permalink: /powershell/msexchange-2016-powershell-cheat-sheet
tags:
  - Microsoft 365
  - Powershell
  - Office 365
---

This page will contain a list of Powershell snippits I find useful when working with MS Exchange servers

### Disable local Autodiscover (non-dns)
- Set-ClientAccessService: This cmdlet is used to configure settings for the Client Access services on a Mailbox server.
- AutoDiscoverServiceInternalUri: This parameter specifies the internal URL for the Autodiscover service. The Autodiscover service is used by Outlook and other Exchange clients to automatically find and configure user mailboxes.
- $null: Setting this parameter to $null effectively removes the internal URL for the Autodiscover service.
```
Set-ClientAccessService -AutoDiscoverServiceInternalUri $null
```
