---
title: 'Microsoft Exchange Powershell Cheat Sheet'
collection: posts
date: 2024-03-18
permalink: /posts/2024-03-18-Microsoft-Exchange-Powershell-Cheat-Sheet
tags:
  - Microsoft Exchange
  - Powershell
  - Exchange
---

# Microsoft Exchnage Powershell Cheat Sheet
This blog contains powershell snippits that I find useful when working with Exchange on prem


# 
## Pulls mailbox sizes and exports them to a CSV
- Ensure that "Mailbox Database" is the name of your Exchange servers mail database
```
get-mailboxstatistics -database "Mailbox Database" | Sort-Object TotalItemSize –Descending | Sort-Object TotalItemSize –Descending | export-csv -notypeinformation c:\TEMP\mailbox.csv
```
