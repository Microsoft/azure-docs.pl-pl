---
title: Seria migracji Contoso | Dokumenty firmy Microsoft
description: Łącza do przykładowych scenariuszy migracji contoso w celu migracji na platformę Azure.
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: c57a9f85e8b12bd4e1e66a4fcd5d08ab5f7b9118
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676323"
---
# <a name="contoso-migration-series"></a>Seria dotycząca migracji w firmie Contoso


Mamy serię artykułów, które pokazują, jak fikcyjna organizacja Contoso migruje swoją infrastrukturę lokalną do chmury [platformy Microsoft Azure.](https://azure.microsoft.com/overview/what-is-azure/) 

Seria zawiera scenariusze, które ilustrują sposób konfigurowania migracji infrastruktury i jak uruchamiać różne typy migracji. Scenariusze rosną w złożoności w miarę ich postępu. Artykuły pokazują, jak firma Contoso obsługuje migracji, ale ogólne instrukcje i wskaźniki są dostarczane w całym.

## <a name="migration-articles"></a>Artykuły dotyczące migracji

Artykuły z serii są podsumowane w poniższej tabeli.  

- Każdy scenariusz migracji jest napędzany przez nieco inne wymagania biznesowe, które określają strategię migracji.
- Dla każdego scenariusza wdrażania udostępniamy informacje o sterownikach biznesowych i celach, proponowanej architekturze, krokach do przeprowadzenia migracji oraz zaleceniach dotyczących oczyszczania i kolejnych krokach po zakończeniu migracji.


**Artykułu** | **Szczegóły** 
--- | --- 
[Artykuł 1: Przegląd](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Omówienie serii artykułów, strategii migracji firmy Contoso i przykładowych aplikacji, które są używane w serii. 
[Artykuł 2: Wdrażanie infrastruktury platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso przygotowuje swoją infrastrukturę lokalną i infrastrukturę platformy Azure do migracji. Ta sama infrastruktura jest używana dla wszystkich artykułów z serii. 
[Artykuł 3: Ocena zasobów lokalnych do migracji na platformę Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Firma Contoso przeprowadza ocenę lokalnej aplikacji SmartHotel360 działającej na vmware. Contoso ocenia maszyny wirtualne aplikacji przy użyciu usługi Azure Migrate i bazy danych programu SQL Server aplikacji przy użyciu Asystenta migracji danych.
[Artykuł 4: Ponowne hostyj aplikacji na maszynie Wirtualnej Platformy Azure i wystąpieniu zarządzanym bazy danych SQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Firma Contoso uruchamia migrację typu "lift-and-shift" na platformę Azure dla lokalnej aplikacji SmartHotel360. Contoso migruje maszynę wirtualną front-end aplikacji przy użyciu [usługi Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-services-overview). Contoso migruje bazę danych aplikacji do wystąpienia zarządzanego usługi Azure SQL Database przy użyciu [usługi migracji bazy danych Azure](https://docs.microsoft.com/azure/dms/dms-overview).
[Artykuł 5: Ponowne hostyj aplikacji na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso migruje swoje maszyny wirtualne aplikacji SmartHotel360 do maszyn wirtualnych platformy Azure przy użyciu usługi Azure Migrate. 
[Artykuł 6: Ponowne hostowanie aplikacji na maszynach wirtualnych platformy Azure i w grupie dostępności zawsze dostępnego programu SQL Server](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Contoso przeprowadza migrację aplikacji SmartHotel360. Contoso używa usługi Azure Migrate do migracji maszyn wirtualnych aplikacji. Usługa migracji bazy danych służy do migracji bazy danych aplikacji do klastra programu SQL Server chronionego przez grupę dostępności AlwaysOn. 
[Artykuł 7: Ponowne hosty aplikacji systemu Linux na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso kończy migrację lift-and-shift swojej aplikacji osTicket systemu Linux do maszyn wirtualnych platformy Azure przy użyciu usługi Azure Migrate.
[Artykuł 8: Ponowne hostowania aplikacji systemu Linux na maszynach wirtualnych platformy Azure i bazie danych platformy Azure dla mysql](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso migruje swoją aplikację osTicket systemu Linux do maszyn wirtualnych platformy Azure przy użyciu usługi Azure Migrate. Migruje bazę danych aplikacji do bazy danych usługi Azure Database dla mysq przy użyciu usługi migracji bazy danych Azure (zawiera alternatywną opcję przy użyciu mysql workbench).
[Artykuł 9: Refaktoryzator aplikacji w aplikacji sieci Web platformy Azure i bazie danych SQL usługi Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso migruje swoją aplikację SmartHotel360 do aplikacji sieci Web platformy Azure i migruje bazę danych aplikacji do wystąpienia programu Azure SQL Server przy użyciu usługi migracji bazy danych azure.
[Artykuł 10: Refaktoryzator aplikacji systemu Windows przy użyciu usług azure app services i wystąpienia zarządzanego SQL](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Contoso migruje lokalną aplikację systemu Windows do aplikacji sieci Web platformy Azure i migruje bazę danych aplikacji do wystąpienia zarządzanego sql azure przy użyciu usługi migracji bazy danych azure.
[Artykuł 11: Refaktoryzator aplikacji systemu Linux w aplikacji sieci Web platformy Azure i usługi Azure Database dla mysql](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso migruje swoją aplikację osTicket systemu Linux do aplikacji sieci Web platformy Azure w wielu regionach platformy Azure przy użyciu usługi Azure Traffic Manager, zintegrowanej z usługą GitHub w celu ciągłego dostarczania. Contoso migruje bazę danych aplikacji do usługi Azure Database dla wystąpienia MySQL. 
[Artykuł 12: Serwer regeneracji team foundation w usługach DevOps platformy Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso migruje swoje lokalne wdrożenie programu Team Foundation Server do usług Azure DevOps na platformie Azure.
[Artykuł 13: Odbuduj aplikację na platformie Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso odbudowuje swoją aplikację SmartHotel przy użyciu szeregu funkcji i usług platformy Azure, w tym usługi Azure App Service, usługi Azure Kubernetes Service (AKS), usługi Azure Functions, usługi Azure Cognitive Services i usługi Azure Cosmos DB.
[Artykuł 14: Skalowanie migracji na platformę Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Po wypróbowaniu kombinacji migracji contoso przygotowuje się do skalowania do pełnej migracji na platformę Azure.



## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) migracji do chmury.
- Dowiedz się więcej o strategiach migracji dla innych scenariuszy (par źródłowych/docelowych) w [Przewodniku po migracji bazy danych](https://datamigration.microsoft.com/).
