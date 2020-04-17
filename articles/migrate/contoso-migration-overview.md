---
title: Seria migracji Contoso | Dokumenty firmy Microsoft
description: Zawiera omówienie strategii migracji i scenariuszy używanych przez firmę Contoso do migracji lokalnego centrum danych na platformę Azure.
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: raynew
ms.openlocfilehash: a1805d2c7568d3104ce499c67c516c0bf94b2db7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460260"
---
# <a name="contoso-migration-series"></a>Seria dotycząca migracji w firmie Contoso


Mamy serię artykułów, które pokazują, jak fikcyjna organizacja Contoso migruje infrastrukturę lokalną do chmury [platformy Microsoft Azure.](https://azure.microsoft.com/overview/what-is-azure/) 

Seria zawiera informacje i scenariusze, które ilustrują sposób konfigurowania migracji infrastruktury i uruchamiania różnych typów migracji. Scenariusze rosną w złożoności w miarę ich postępu. Artykuły pokazują, jak firma Contoso kończy swoją misję migracji, ale wskazówki dotyczące ogólnego odczytu i szczegółowe instrukcje są dostarczane w całym.

## <a name="migration-articles"></a>Artykuły dotyczące migracji

Artykuły z serii są podsumowane w poniższej tabeli.  

- Każdy scenariusz uwzględnia nieco inne cele biznesowe, które determinują strategię migracji.
- W przypadku każdego scenariusza wdrożenia zapewniamy informacje na temat celów biznesowych, proponowanej architektury, czynności do wykonania podczas migracji, zaleceń dotyczących czyszczenia zasobów oraz kolejnych czynności, które należy wykonać po zakończeniu migracji.

**Artykułu** | **Szczegóły** 
--- | --- 
[Artykuł 1: Przegląd](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Omówienie serii artykułów, strategii migracji firmy Contoso i przykładowych aplikacji, które są używane w serii. 
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso przygotowuje swoją infrastrukturę lokalną i infrastrukturę platformy Azure do migracji. Ta sama infrastruktura jest używana dla wszystkich artykułów migracji w serii. 
[Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Firma Contoso przeprowadza ocenę lokalnej aplikacji SmartHotel360 działającej na vmware. Contoso ocenia maszyny wirtualne aplikacji przy użyciu usługi Azure Migrate i bazy danych programu SQL Server aplikacji przy użyciu Asystenta migracji danych.
[Artykuł 4: Ponowne hostyj aplikacji na maszynie Wirtualnej Platformy Azure i wystąpieniu zarządzanym bazy danych SQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Firma Contoso uruchamia migrację typu "lift-and-shift" na platformę Azure dla lokalnej aplikacji SmartHotel360. Contoso migruje maszynę wirtualną front-end aplikacji przy użyciu [usługi Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migruje bazę danych aplikacji do wystąpienia zarządzanego usługi Azure SQL Database przy użyciu [usługi migracji bazy danych Azure](https://docs.microsoft.com/azure/dms/dms-overview).
[Artykuł 5: Ponowne hostyj aplikacji na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso migruje swoje maszyny wirtualne aplikacji SmartHotel360 do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. 
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i w grupie dostępności zawsze dostępnego programu SQL Server](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso przeprowadza migrację aplikacji SmartHotel360. Contoso używa usługi Site Recovery do migracji maszyn wirtualnych aplikacji. Usługa migracji bazy danych służy do migracji bazy danych aplikacji do klastra programu SQL Server chronionego przez grupę dostępności AlwaysOn. 
[Artykuł 7: Ponowne hosty aplikacji systemu Linux na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso kończy migrację lift-and-shift swojej aplikacji osTicket systemu Linux do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery.
[Artykuł 8: Ponowne hostowania aplikacji systemu Linux na maszynach wirtualnych platformy Azure i bazie danych platformy Azure dla mysql](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migruje swoją aplikację osTicket systemu Linux do maszyn wirtualnych platformy Azure przy użyciu usługi Site Recovery. Baza danych aplikacji jest migrowana do usługi Azure Database for MySQL za pomocą programu MySQL Workbench. 
[Artykuł 9: Refaktoryzator aplikacji w aplikacji sieci Web platformy Azure i bazie danych SQL usługi Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migruje swoją aplikację SmartHotel360 do aplikacji sieci Web platformy Azure i migruje bazę danych aplikacji do wystąpienia programu Azure SQL Server za pomocą Asystenta migracji bazy danych.     
[Artykuł 10: Refaktoryzuj aplikację systemu Linux w aplikacji sieci Web platformy Azure i w bazie danych platformy Azure dla mysql](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso migruje swoją aplikację osTicket systemu Linux do aplikacji sieci Web platformy Azure w wielu regionach platformy Azure przy użyciu usługi Azure Traffic Manager, zintegrowanej z usługą GitHub w celu ciągłego dostarczania. Contoso migruje bazę danych aplikacji do usługi Azure Database dla wystąpienia MySQL. 
[Artykuł 11: Serwer regeneracji team foundation w usługach Azure DevOps](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso migruje swoje lokalne wdrożenie programu Team Foundation Server do usług Azure DevOps na platformie Azure.
[Artykuł 12: Odbuduj aplikację na platformie Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso odbudowuje swoją aplikację SmartHotel przy użyciu szeregu możliwości i usług platformy Azure, w tym usługi Azure App Service, usługi Azure Kubernetes Service (AKS), usługi Azure Functions, usługi Azure Cognitive Services i usługi Azure Cosmos DB.
[Artykuł 13: Skalowanie migracji na platformę Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Po wypróbowaniu kombinacji migracji contoso przygotowuje się do skalowania do pełnej migracji na platformę Azure.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) migracji do chmury.
- Dowiedz się więcej o strategiach migracji dla innych scenariuszy (par źródłowych/docelowych) w [Przewodniku po migracji bazy danych](https://datamigration.microsoft.com/).
