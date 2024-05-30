---
title: "Migrate DHCP scope(s) to windows server 2022"
collection: reposts
permalink: /reposts/2022-06-29-Migrate-DHCP-scopes-to-windows-server-2022
excerpt: 'In this post, I will demonstrate how to change the IP address on a domain controller.
.'
date: 2022-06-29
venue: 'PeteNetLive'
paperurl: 'https://www.petenetlive.com/kb/article/0001792'
citation: Long, P. (2022, June 29). Migrate DHCP scope(s) to windows server 2022. PeteNetLive. https://www.petenetlive.com/kb/article/0001792 
---

If you have a lot of DHCP scopes, options, or reservations, then manually creating them on your new DHCP servers can be a both a time consuming and tedious process. If only there was an easier way?


## Migrate DHCP with Netsh
Below you can see an example, where  I have many scopes and lot’s of settings that would be painful to have to recreate from scratch. This example is on Server 2008 R2,but your source server could be server 2000, (or newer) the export procedure is the same.

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/0dbf0282-b2d3-4a36-aa2c-d8a70c66aa38)

Open an administrative command window, and issue the following  command.

```
netsh dhcp server export C:\dhcp-export.txt all
```

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/6dfdcb59-dacc-4077-8771-2dc966983411)

Now on the source DHCP server I’m stopping and disabling the DHCP SERVER service, you might want to wait until, you are about to authorise the new 2022 DHCP server to minimise downtime, before doing this in production.

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/dd5a71fc-e9f1-4731-a1c4-5c04dbac8bcc)

Copy the exported text file from the old DHCP server, to the new server.

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/309e674e-2930-4781-8ae0-71f26563575f)

## Migrate DHCP: Install DHCP on Server 2022 (via PowerShell)
Open an administrative PoweShell window, and issue the following  command.

```
Install-WindowsFeature DHCP -IncludeManagementTools
```

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/7c69ad99-3c02-47ac-889c-edf3141daf66)

Then import the settings with the following command.

```
netsh dhcp server import C:\dhcp-export.txt all
```

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/4a7715f2-3c98-4f99-a97d-cd6b37fc3985)

Go to Administrative Tools > DHCP > You should see your migrated information in here, the DHCP scopes will be down (because the server has not yet been authorised in AD). Right click the server name, and select **Authorise**__.

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/a9eeccaf-ea2b-4913-92e2-eea912956211)

**Note**: At this point **ENSURE** the old DHCP server has had its DHCP server service stopped and disabled.

Wait a few seconds and then restart the DHCP Server service, (this can be done as shown below).

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/0eb3c64b-bc6f-4cd6-98e0-a5d63cdd2431)

After a few seconds, the new scopes should be up and getting served.

![image](https://github.com/mkolakowski/mkolakowski.github.io/assets/31713098/65a5d8cd-90a6-420c-8338-aafdce89a5f6)


