---
title: A newly promoted Windows 2008 domain controller may fail to advertise after completion of DCpromo
description: Describes an issue where a newly promoted Windows 2008 domain controller fails to advertise after completion of DCpromo.
ms.date: 09/07/2020
author: Deland-Han
ms.author: delhan
manager: dscontentpm
audience: itpro
ms.topic: troubleshooting
ms.prod: windows-server
localization_priority: medium
ms.reviewer: kaushika
ms.prod-support-area-path: DCPromo and the installation of domain controllers
ms.technology: ActiveDirectory
---
# A newly promoted Windows 2008 domain controller may fail to advertise after completion of DCpromo

This article describes an issue where a newly promoted Windows 2008 domain controller fails to advertise after completion of DCpromo.

_Original product version:_ &nbsp;Windows Server 2012 R2  
_Original KB number:_ &nbsp;967336


## Symptoms

A newly promoted Windows 2008 domain controller may fail to advertise after completion of DCpromo and reboot. This issue is specific to domain controllers participating in domains at Windows 2008 functional level where Sysvol is replicated by Distributed Files System Replication (DFSR).

## Cause

There's a name resolution or network connectivity issue.

## Resolution

To resolve this problem, choose one of the following options: 

1. Resolve any possible name resolution or network connectivity issue that would prevent communication with the defined "Parent Computer"

2. Modify the following registry to point to an available source domain controller:

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\DFSR\Parameters\SysVols\Seeding SysVols\\`contoso.com`
    
    "Parent Computer"="`DC1.contoso.com`"

## More information

DFSR is a Multi master replication engine used to replicate files and folders for Distributed files system (DFS) structures and optionally the Domain System Volume in Windows 2008 functional level domains. Windows 2008 domain controllers using DFSR for sysvol will initially synchronize their Sysvol content after the DCpromo wizard has synchronized Active Directory and the computer is rebooted.

A replica domain controller will attempt to source its sysvol content from the same server that it used to source it domain-naming context from during the Active Directory promotion by reading the "Parent Computer" registry key under: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\DFSR\Parameters\SysVols\Seeding SysVols\\`domain.com`

Note: "Parent Computer" may be set automatically or defined by an administrator during DCpromo.

If the Server defined by the "Parent Computer" becomes unavailable after the reboot, initial synchronization of the sysvol content will be delayed until action to correct the server's availability on the network has been restored.

Errors reported in the DFSR event log: 

> Source: DFSR
>
> Event ID: 1202
>
> Level: Error
>
> Description:
>
> The DFS Replication service failed to contact domain controller to access configuration information. Replication is stopped. The service will try again during the next configuration polling cycle, which will occur in 60 minutes. This event can be caused by TCP/IP connectivity, firewall, Active Directory Domain Services, or DNS issues.
>
> Source: DFSR
>
> Event ID: 4612
>
> Level: Error
>
> Description:
>
> The DFS Replication service initialized SYSVOL at local path C:\Windows\SYSVOL\domain and is waiting to perform initial replication. The replicated folder will remain in the initial synchronization state until it has replicated with its partner `WIN-C0T0SC8MCEF.contoso.com`. If the server was in the process of being promoted to a domain controller, the domain controller will not advertise and function as a domain controller until this issue is resolved. This can occur if the specified partner is also in the initial synchronization state, or if sharing violations are encountered on this server or the sync partner. If this event occurred during the migration of SYSVOL from File Replication service (FRS) to DFS Replication, changes will not replicate out until this issue is resolved. This can cause the SYSVOL folder on this server to become out of sync with other domain controllers.
>
> Source: DFSR
>
> Event ID: 4612
>
> Level: Error
>
> Description:
>
> The DFS Replication service initialized SYSVOL at local path C:\Windows\SYSVOL\domain and is waiting to perform initial replication. The replicated folder will remain in the initial synchronization state until it has replicated with its partner DC1.contoso.com. If the server was in the process of being promoted to a domain controller, the domain controller will not advertise and function as a domain controller until this issue is resolved. This can occur if the specified partner is also in the initial synchronization state, or if sharing violations are encountered on this server or the sync partner. If this event occurred during the migration of SYSVOL from File Replication service (FRS) to DFS Replication, changes will not replicate out until this issue is resolved. This can cause the SYSVOL folder on this server to become out of sync with other domain controllers.
>
> Source: DFSR
>
> Event ID: 5008
>
> Level: Error
>
> Description:
>
> The DFS Replication service failed to communicate with partner DC1 for replication group Domain System Volume. This error can occur if the host is unreachable, or if the DFS Replication service is not running on the server. The service will retry the connection periodically.
>
> Additional Information:
>
> Error: 1722 (The RPC server is unavailable.)
>
> Source: DFSR
>
> Event ID: 4614
>
> Level: Warning
>
> Description:
>
> The DFS Replication service initialized SYSVOL at local path C:\Windows\SYSVOL\domain and is waiting to perform initial replication. The replicated folder will remain in the initial synchronization state until it has replicated with its partner DC1.contoso.com. If the server was in the process of being promoted to a domain controller, the domain controller will not advertize and function as a domain controller until this issue is resolved. This can occur if the specified partner is also in the initial synchronization state, or if sharing violations are encountered on this server or the synchronization partner. If this event occurred during the migration of SYSVOL from File Replication service (FRS) to DFS Replication, changes will not replicate out until this issue is resolved. This can cause the SYSVOL folder on this server to become out of sync with other domain controllers.

Additional Information:

Replicated Folder Name: SYSVOL Share

Errors reported in the DFSR debug logs: 

> ERROR: DownstreamTransport: SetupBinding Failed
>
> Error: The RPC server is unavailable.

References: 

[Distributed File System Replication FAQ](https://technet.microsoft.com/library/cc773238.aspx) 

Additional information on DFSR debug Logging

[958893 How to configure DFSR logging](/default.aspx?scid=kb;en-us;958893) 

[DCpromo](https://technet.microsoft.com/library/cc732887.aspx) aspx 

[322756](http://vkbexternal/vkbwebservice/viewcontent.aspx?scid=kb;en-us;322756&portalid=1) How to back up and restore the registry in Windows XP and Windows Vista

[DNS Server Operations Guide](https://technet.microsoft.com/library/cc816603.aspx) 

## Disclaimer

Microsoft and/or its suppliers make no representations or warranties about the suitability, reliability, or accuracy of the information contained in the documents and related graphics published on this website (the "materials") for any purpose. The materials may include technical inaccuracies or typographical errors and may be revised at any time without notice.


To the maximum extent permitted by applicable law, Microsoft and/or its suppliers disclaim and exclude all representations, warranties, and conditions whether express, implied, or statutory, including but not limited to representations, warranties, or conditions of title, non-infringement, satisfactory condition or quality, merchantability and fitness for a particular purpose, with respect to the materials.
