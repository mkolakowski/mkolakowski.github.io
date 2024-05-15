---
title: 'Windows Server : How to check DFSR Status'
collection: posts
date: 2024-05-15
permalink: /posts/2024-05-15-Windows-Server-How-to-check-DFSR-Status
tags:
  - Microsoft
  - Windows Server
  - DFSR
---

The code sample below shows the current status of all replication groups on a host

```
# Import the DFSR module
Import-Module DFSR

# Define the list of replication groups and their member servers
$replicationGroups = Get-DfsReplicationGroup
$servers = Get-DfsrMember

# Function to get the backlog status between two servers
function Get-DfsReplicationStatus {
    param (
        [string]$sourceComputerName,
        [string]$destinationComputerName,
        [string]$replicationGroupName
    )
    
    try {
        $backlog = Get-DfsrBacklog -SourceComputerName $sourceComputerName -DestinationComputerName $destinationComputerName -GroupName $replicationGroupName
        $backlogCount = $backlog.Count
    }
    catch {
        $backlogCount = "Error retrieving backlog"
    }
    
    return $backlogCount
}

# Iterate through each replication group and its members
foreach ($group in $replicationGroups) {
    Write-Host "Replication Group: $($group.GroupName)"
    
    foreach ($server in $servers | Where-Object { $_.GroupName -eq $group.GroupName }) {
        $sourceServer = $server.ComputerName
        $destinationServers = $servers | Where-Object { $_.GroupName -eq $group.GroupName -and $_.ComputerName -ne $sourceServer }

        foreach ($destinationServer in $destinationServers) {
            $destinationServerName = $destinationServer.ComputerName
            $backlogCount = Get-DfsReplicationStatus -sourceComputerName $sourceServer -destinationComputerName $destinationServerName -replicationGroupName $group.GroupName
            Write-Host "Source: $sourceServer -> Destination: $destinationServerName | Backlog: $backlogCount"
        }
    }
    Write-Host "-------------------------------------------"
}
```
