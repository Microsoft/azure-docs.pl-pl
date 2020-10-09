---
title: Obsługiwane jednostki SKU maszyny wirtualnej dla usługi Azure Policy
description: Artykuł opisujący obsługiwane jednostki SKU maszyny wirtualnej (przez wydawcę, ofertę obrazu i jednostkę SKU obrazu), które są obsługiwane dla wbudowanych zasad platformy Azure zapewnianych przez usługę Backup
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 4658d1beffe707682e173491edea1eac0db9c811
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82183623"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Obsługiwane jednostki SKU maszyny wirtualnej dla usługi Azure Policy

Azure Backup udostępnia wbudowane zasady (przy użyciu Azure Policy), które można przypisać do **wszystkich maszyn wirtualnych platformy Azure w określonej lokalizacji w ramach subskrypcji lub grupy zasobów**. Gdy te zasady są przypisane do danego zakresu, wszystkie nowe maszyny wirtualne utworzone w tym zakresie są automatycznie konfigurowane na potrzeby tworzenia kopii zapasowych **w istniejącym magazynie w tej samej lokalizacji i subskrypcji**. W poniższej tabeli wymieniono wszystkie jednostki SKU maszyny wirtualnej obsługiwane przez te zasady.

## <a name="supported-vms"></a>Obsługiwane maszyny wirtualne *

**Nazwa zasad:** Konfigurowanie kopii zapasowych na maszynach wirtualnych lokalizacji do istniejącego magazynu centralnego w tej samej lokalizacji

Wydawca obrazu | Oferta obrazu | Jednostka SKU obrazu
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 z dodatkiem SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 Datacenter (2012 — Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 Datacenter (2012-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Datacenter (2012-R2-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 R2 Datacenter (2012-R2-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter (2016 — Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter — Server Core (2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter-Server Core (2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter (2016-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core z kontenerami (2016-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | Host sesji programu Windows Server 2016 Pulpit zdalny 2016 (2016-Datacenter-with-hosta)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (2019 — Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core z kontenerami (2019-Datacenter-Core-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core z kontenerami (2019-Datacenter-Core-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter (2019-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter z kontenerami (2019-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter z kontenerami (2019-Datacenter-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (zh-CN) (2019-Datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Centrum danych — Core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-with-Containers-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1803-with-Containers-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | Wszystkie jednostki SKU obrazów
MicrosoftSQLServer | SQL2016SP1-WS2016 | Wszystkie jednostki SKU obrazów
MicrosoftSQLServer | SQL2016 — WS2016 | Wszystkie jednostki SKU obrazów
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Wszystkie jednostki SKU obrazów
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | Wszystkie jednostki SKU obrazów
MicrosoftSQLServer | SQL2016 — WS2012R2 | Wszystkie jednostki SKU obrazów
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Wszystkie jednostki SKU obrazów
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | Wszystkie jednostki SKU obrazów
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | Wszystkie jednostki SKU obrazów
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | Wszystkie jednostki SKU obrazów
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | Wszystkie jednostki SKU obrazów
MicrosoftRServer | MLServer-WS2016 | Wszystkie jednostki SKU obrazów
MicrosoftVisualStudio | VisualStudio | Wszystkie jednostki SKU obrazów
MicrosoftVisualStudio | Windows | Wszystkie jednostki SKU obrazów
MicrosoftDynamicsAX | Dynamics | Pre-REQ-AX7-jednopunktowy-U8
Microsoft — ads | Windows-dane-nauka — maszyna wirtualna | Wszystkie jednostki SKU obrazów
MicrosoftWindowsDesktop | System Windows-10 | Wszystkie jednostki SKU obrazów
RedHat | RHEL | 6,7, 6,8, 6,9, 6,10, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7
RedHat | RHEL — SAP-HANA | 6,7, 7,2, 7,3
SUSE | SLES | 12. X
SUSE | SLES — HPC | 12. X
SUSE | SLES-HPC — priorytet | 12. X
SUSE | SLES — SAP | 12. X
SUSE | SLES — SAP-BYOS | 12. X
SUSE | SLES-Priority | 12. X
SUSE | SLES — BYOS | 12. X
SUSE | SLES — SAPCAL | 12. X
SUSE | SLES-Standard | 12. X
Canonical | UbuntuServer | 14.04.0 — LTS
Canonical | UbuntuServer | 14.04.1 — LTS
Canonical | UbuntuServer | 14.04.2-LTS
Canonical | UbuntuServer | 14.04.3 — LTS
Canonical | UbuntuServer | 14.04.4-LTS
Canonical | UbuntuServer | 14.04.5 — CODZIENNIE — LTS
Canonical | UbuntuServer | 14.04.5-LTS
Canonical | UbuntuServer | 16,04 — CODZIENNIE — LTS
Canonical | UbuntuServer | 16.04-LTS
Canonical | UbuntuServer | 16.04.0 — LTS
Canonical | UbuntuServer | 18,04 — CODZIENNIE — LTS
Canonical | UbuntuServer | 18,04 – LTS
Oracle | Oracle-Linux | 6,8, 6,9, 6,10, 7,3, 7,4, 7,5, 7,6
OpenLogic | CentOS | 6. X, 7. X
OpenLogic | CentOS – LVM | 6. X, 7. X
OpenLogic | CentOS – sterownik | 6. X, 7. X
Cloudera | Cloudera — CentOS — OS | 7. X
