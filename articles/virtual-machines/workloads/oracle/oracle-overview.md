---
title: Rozwiązania firmy Oracle na Microsoft Azure | Microsoft Docs
description: Dowiedz się więcej na temat opcji wdrażania aplikacji i rozwiązań dla oprogramowania Oracle na Microsoft Azure, w tym uruchamiania w całości w infrastrukturze platformy Azure lub łączności między chmurą i usługą Oracle Cloud Infrastructure (OCI).
documentationcenter: ''
author: dbakevlar
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/05/2020
ms.author: kegorman
ms.openlocfilehash: 971f7c919595f915451faf9266ee3bb18b35087e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677182"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Omówienie aplikacji i rozwiązań firmy Oracle na platformie Azure

W tym artykule przedstawiono możliwości uruchamiania rozwiązań firmy Oracle przy użyciu infrastruktury platformy Azure. Zobacz także szczegółowe wprowadzenie do dostępnych [aplikacji platformy Azure WebLogic Server](oracle-weblogic.md), [obrazów maszyn wirtualnych Oracle](oracle-vm-solutions.md) w portalu Azure Marketplace oraz możliwości [łączenia się z platformą Azure przy użyciu infrastruktury chmury firmy Oracle (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Bazy danych Oracle w infrastrukturze platformy Azure

Uruchamiaj bazy danych Oracle w infrastrukturze platformy Azure, korzystając z Oracle Database na Oracle Linux obrazów dostępnych w witrynie Azure Marketplace:

* Oracle Database 12,1, 12,2 i 18,3 Enterprise Edition 

* Oracle Database 12,1, 12,2 i 18,3 Standard Edition

* Oracle Database 19,3

Możesz również skonfigurować Oracle Database na obrazie nieOracle Linux dostępnym na platformie Azure, oprzeć rozwiązanie na obrazie niestandardowym tworzonym od podstaw na platformie Azure lub Przekaż obraz niestandardowy ze środowiska lokalnego.

Opcjonalnie można skonfigurować z wieloma dołączonymi dyskami i zwiększyć wydajność bazy danych, instalując automatyczne zarządzanie magazynem (ASM) firmy Oracle.

## <a name="weblogic-server-with-azure-service-integrations"></a>Serwer WebLogic z integracją usług platformy Azure

Wybieraj z wielu aplikacji platformy Azure WebLogic Server, aby przyspieszyć podróż do chmury.  Dostępne są kilka wstępnie skonfigurowanych integracji usługi platformy Azure, w tym baza danych, usługa Azure App Gateway i Azure Active Directory.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Aplikacje na serwerach Oracle Linux i WebLogic

Uruchamiaj aplikacje dla przedsiębiorstw na platformie Azure na obsługiwanych obrazach Oracle Linux. Następujące obrazy maszyn wirtualnych są dostępne w portalu Azure Marketplace:

* Oracle webWebLogicc Server 12.1.2

* Oracle Linux z nieprzerwanym jądrem przedsiębiorstwa (UEK) 6,8, 6,9, 6,10, 7,3, 7,7, 8,0, 8,1. 

## <a name="high-availability-and-disaster-recovery-options"></a>Opcje wysokiej dostępności i odzyskiwania po awarii

* Skonfiguruj funkcję [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), [aktywną ochronę danych przy użyciu usługi FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [fragmentowania](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) lub [złota bramę](https://www.oracle.com/middleware/technologies/goldengate.html) platformy Azure w połączeniu z [strefy dostępności](../../../availability-zones/az-overview.md) w celu zapewnienia wysokiej dostępności w regionie. Konfiguracje te można również skonfigurować w wielu regionach świadczenia usługi Azure, aby zwiększyć dostępność i odzyskiwanie po awarii.

* Użyj [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) , aby organizować i zarządzać odzyskiwaniem po awarii dla maszyn wirtualnych Oracle Linux na platformie Azure i serwerach fizycznych. 

* Włącz obsługę platformy Oracle dla klastrów (RAC) na platformie Azure przy użyciu [rozwiązania Azure VMware](../../../vmware-cloudsimple/oracle-real-application-clusters.md) lub [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="backup-oracle-workloads"></a>Tworzenie kopii zapasowych obciążeń Oracle

* Tworzenie kopii zapasowych maszyn wirtualnych Oracle przy użyciu [Azure Backup](../../../backup/backup-overview.md)

* Utwórz kopię zapasową Oracle Database przy użyciu programu Oracle RMAN i opcjonalnie Użyj [usługi Azure Blob bezpiecznik](../../../storage/blobs/storage-how-to-mount-container-linux.md) , aby zainstalować [wysoce nadmiarowe konto platformy Azure Blob Storage](../../../storage/common/storage-redundancy.md) i zapisać w nim kopie zapasowe RMAN w celu dodania odporności.

## <a name="integration-of-azure-with-oci"></a>Integracja z platformą Azure za pomocą OCI

Uruchom aplikacje Oracle w infrastrukturze platformy Azure, połączone z bazami danych zaplecza w infrastrukturze chmury firmy Oracle (OCI). To rozwiązanie używa następujących możliwości: 

* **Obsługa sieci** z obsługą wielu chmur — umożliwia korzystanie z bezpośrednich połączeń między platformami Azure ExpressRoute i Oracle FastConnect w celu ustanawiania dużych przepustowości, prywatnych i małych opóźnień między aplikacją a warstwą bazy danych.
* **Zintegrowana tożsamość** — Konfigurowanie tożsamości federacyjnej między usługami Azure AD i Oracle IDCS w celu utworzenia jednego źródła tożsamości dla rozwiązań. Włącz logowanie jednokrotne, aby zarządzać zasobami w systemach OCI i na platformie Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Wdrażanie aplikacji Oracle na platformie Azure

Użyj szablonów Terraform, aby skonfigurować infrastrukturę platformy Azure i zainstalować aplikacje firmy Oracle. 

Firma Oracle potwierdziła, że te aplikacje są uruchamiane na platformie Azure podczas łączenia się z bazą danych Oracle za pośrednictwem rozwiązania Azure Cloud Interconnect:

* Pakiet E-Business
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplikacje sieci sprzedaży firmy Oracle
* Zarządzanie finansowe Oracle Hyperion

Wdrażaj także aplikacje niestandardowe na platformie Azure, które łączą się z użyciem OCI i innych usług platformy Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Konfigurowanie baz danych Oracle w systemie OCI

Użyj Oracle Database Cloud Services (autonomiczna baza danych, RAC, Exadata, DBaaS, pojedynczy węzeł) w połączeniu z oprogramowaniem Oracle działającym na platformie Azure. Dowiedz się więcej na temat [opcji bazy danych OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licencjonowanie

Wdrażanie aplikacji Oracle na platformie Azure jest oparte na modelu "Przenieś własną licencję". Przyjęto założenie, że masz licencję na korzystanie z oprogramowania Oracle i że masz aktualną umowę pomocy technicznej z firmą Oracle. Firma Oracle gwarantuje mobilność licencji ze środowiska lokalnego na platformę Azure. Zapoznaj się z Oracle-Azure [często zadawanych pytań](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [aplikacji platformy Azure WebLogic Server](oracle-weblogic.md) i integracji usługi platformy Azure, które obsługują.

* Dowiedz się więcej o wdrażaniu [obrazów maszyn wirtualnych Oracle](oracle-vm-solutions.md) w infrastrukturze platformy Azure.

* Dowiedz się więcej na temat sposobu [łączenia z platformą Azure za pomocą OCI](oracle-oci-overview.md).

* Zapoznaj się z [sesją omówienia programu Oracle w systemie Azure z platformy](https://www.pluralsight.com/courses/microsoft-ignite-session-57) zapłon 2019. 
