---
title: Rozwiązania Oracle na platformie Microsoft Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o opcjach wdrażania aplikacji i rozwiązań Oracle na platformie Microsoft Azure, w tym działających w całości na infrastrukturze platformy Azure lub korzystających z łączności między chmurami z Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: borisb
ms.openlocfilehash: c737189650d571fb62a770707e84ed15c5a37a57
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870502"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Omówienie aplikacji i rozwiązań Oracle na platformie Azure

W tym artykule przedstawiono możliwości uruchamiania rozwiązań Oracle przy użyciu infrastruktury platformy Azure. Zobacz także szczegółowe wprowadzenie do dostępnych [obrazów maszyn wirtualnych Oracle](oracle-vm-solutions.md) w portalu Azure Marketplace oraz możliwości [łączenia platformy Azure z infrastrukturą Oracle Cloud Infrastructure (OCI).](oracle-oci-overview.md)

## <a name="oracle-databases-on-azure-infrastructure"></a>Bazy danych Oracle w infrastrukturze platformy Azure

Uruchamianie baz danych Oracle w infrastrukturze platformy Azure przy użyciu bazy danych Oracle Database na obrazach Oracle Linux dostępnych w portalu Azure Marketplace:

* Oracle Database 12.1, 12.2 i 18.3 Enterprise Edition 

* Oracle Database 12.1, 12.2 i 18.3 Standard Edition 

Można również skonfigurować oracle database na obrazie innym niż Oracle Linux dostępny na platformie Azure, oprzeć rozwiązanie na obrazie niestandardowym utworzonym od podstaw na platformie Azure lub przekazać obraz niestandardowy ze środowiska lokalnego.

Opcjonalnie skonfiguruj z wieloma podłączonymi dyskami i zwiększ wydajność bazy danych, instalując oracle automated storage management (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Aplikacje na systemach Oracle Linux i WebLogic Server

Uruchamiaj aplikacje dla przedsiębiorstw na platformie Azure w obsługiwanych systemach operacyjnych Oracle. Następujące obrazy są dostępne w portalu Azure Marketplace:

* Serwer Oracle WebLogic Server 12.1.2

* Oracle Linux z nierozerwalnym jądrem korporacyjnym (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 i 7.6 

## <a name="high-availability-and-disaster-recovery-options"></a>Wysoka dostępność i opcje odzyskiwania po awarii

* Skonfiguruj [oracle data guard,](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956) [active data guard z FSFO,](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)dzielenia na [fragmenty](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) lub [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) na infrastrukturze platformy Azure w połączeniu z [strefami dostępności,](../../../availability-zones/az-overview.md) aby uzyskać wysoką dostępność w regionie. Można również skonfigurować te konfiguracje w wielu regionach platformy Azure dla zwiększenia dostępności i odzyskiwania po awarii.

* Użyj [usługi Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) do organizowania odzyskiwania po awarii i zarządzania nimi dla maszyn wirtualnych Oracle Linux na platformie Azure oraz na serwerach lokalnych lub fizycznych. 

* Włącz oracle real application clusters (RAC) na platformie Azure za pomocą [rozwiązania Azure VMWare lub](https://docs.microsoft.com/azure/vmware-cloudsimple/oracle-real-application-clusters/) [FlashGrid SkyCluster.](https://www.flashgrid.io/oracle-rac-in-azure/)

## <a name="backup-oracle-workloads"></a>Tworzenie kopii zapasowych obciążeń Oracle

* Tworzenie kopii zapasowych maszyn wirtualnych Oracle przy użyciu [usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview)

* Utwórz kopię zapasową bazy danych Oracle Database przy użyciu oracle RMAN i opcjonalnie użyj [usługi Azure Blob Fuse,](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux) aby zainstalować [wysoce ponowne konto usługi Azure Blob Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) i zapisać kopie zapasowe RMAN w celu zwiększenia odporności.

## <a name="integration-of-azure-with-oci"></a>Integracja platformy Azure z OCI

Uruchom aplikacje Oracle w infrastrukturze platformy Azure, połączone z bazami danych zaplecza w oracle cloud infrastructure (OCI). To rozwiązanie wykorzystuje następujące możliwości: 

* **Sieci między chmurami** — użyj bezpośredniego połączenia dostępnego między usługami Azure ExpressRoute i Oracle FastConnect w celu nawiązania połączeń o dużej przepustowości, prywatnych i o małych opóźnieniach między aplikacją a warstwą bazy danych.
* **Zintegrowana tożsamość** — konfigurowanie tożsamości federacyjnej między usługą Azure AD i Oracle IDCS w celu utworzenia jednego źródła tożsamości dla rozwiązań. Włącz logowanie jednokrotne, aby zarządzać zasobami w ramach oci i platformy Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Wdrażanie aplikacji Oracle na platformie Azure

Szablony Terraform umożliwia konfigurowanie infrastruktury platformy Azure i instalowanie aplikacji Oracle Applications. 

> [!IMPORTANT]
> Oracle zaświadczy te aplikacje do uruchomienia na platformie Azure podczas korzystania z rozwiązania Azure / Oracle Cloud interconnect do maja 2020 r.

* Pakiet e-biznesowy
* JD Edwards EnterpriseOne
* Peoplesoft
* Aplikacje Oracle Retail
* Zarządzanie finansami Oracle Hyperion

Wdrażanie również niestandardowych aplikacji na platformie Azure, które łączą się z OCI i innymi usługami platformy Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Konfigurowanie baz danych Oracle w OCI

Korzystaj z usług Oracle Database Cloud Services (Autonomous Database, RAC, Exadata, DBaaS, Single Node) w połączeniu z aplikacjami Oracle działającymi na platformie Azure. Dowiedz się więcej o [opcjach bazy danych OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licencjonowanie

Wdrażanie aplikacji Oracle na platformie Azure opiera się na modelu "przynieś własną licencję". Zakłada się, że masz odpowiednie licencje na korzystanie z oprogramowania Oracle i że masz aktualną umowę pomocy technicznej z Oracle. Oracle ma zagwarantowaną mobilność licencji z lokalnego na platformę Azure. Zapoznaj się z [często zadawanymi pytaniami](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)dotyczącymi platformy Oracle-Azure .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o wdrażaniu [obrazów maszyn wirtualnych Oracle](oracle-vm-solutions.md) w infrastrukturze platformy Azure.

* Dowiedz się więcej o tym, jak [połączyć platformę Azure z OCI](oracle-oci-overview.md).

* Zapoznaj się z [omówioną sesją oracle on Azure](https://myignite.techcommunity.microsoft.com/sessions/82915) z ignite 2019. 
