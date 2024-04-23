---
title: 'Enable Print Management Console on Server 2022+'
collection: posts
date: 2024-04-23
permalink: /posts/2024-04-23-Enable-Print-Management-Console
tags:
  - Windows Server 2022
  - Powershell
  - Print Management
---


By Default, Windows Server 2022 does NOT include the Print Management Console, even if the Print Server roles are installed. We will need to run some powershell to enable it.

```
dism /Online /add-Capability /CapabilityName:Print.Management.Console~~~~0.0.1.0
```

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/840bbeec-84e9-4375-ac56-aef4e76781ea)


This command will tell DISM to install the Print Management Console.
