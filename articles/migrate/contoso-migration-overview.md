---
title: Seria migracji firmy Contoso | Microsoft Docs
description: Linki do przykładowych scenariuszy migracji firmy Contoso na potrzeby migracji na platformę Azure.
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: f9f7b3b64cf91019ed4e40d5d52b859419b74836
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86107635"
---
# <a name="contoso-migration-series"></a>Seria dotycząca migracji w firmie Contoso


Mamy szereg artykułów demonstrujących sposób, w jaki fikcyjna organizacja firmy Contoso migruje swoją infrastrukturę lokalną do chmury [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) . 

Seria zawiera scenariusze, które ilustrują sposób konfigurowania migracji infrastruktury i uruchamiania różnych typów migracji. Scenariusze rosną w miarę postępu. Artykuły pokazują, jak firma Contoso obsługuje migrację, ale ogólne instrukcje i wskaźniki są udostępniane w całym systemie.

## <a name="migration-articles"></a>Artykuły dotyczące migracji

Artykuły z tej serii są podsumowane w poniższej tabeli.  

- Każdy scenariusz migracji ma inne wymagania biznesowe, które określają strategię migracji.
- W każdym scenariuszu wdrażania udostępniamy informacje o sterownikach i celach firmy, proponowanej architekturze, krokach do przeprowadzenia migracji oraz zaleceniach dotyczących czyszczenia i następnych kroków po zakończeniu migracji.


**Artykuł** | **Szczegóły** 
--- | --- 
[Artykuł 1: Omówienie](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Omówienie serii artykułów, strategii migracji firmy Contoso i przykładowych aplikacji, które są używane w tej serii. 
[Artykuł 2. wdrażanie infrastruktury platformy Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Firma Contoso przygotowuje infrastrukturę lokalną i infrastrukturę platformy Azure do migracji. Ta sama infrastruktura jest używana dla wszystkich artykułów w serii. 
[Artykuł 3: Ocena zasobów lokalnych na potrzeby migracji na platformę Azure](/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Firma Contoso wykonuje ocenę swojej lokalnej aplikacji SmartHotel360 działającej w oprogramowaniu VMware. Firma Contoso ocenia maszyny wirtualne aplikacji przy użyciu usługi Azure Migrate, a aplikacja SQL Server bazy danych przy użyciu Data Migration Assistant.
[Artykuł 4: przehostaj aplikację na maszynie wirtualnej platformy Azure i wystąpieniu zarządzanym SQL](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Firma Contoso wykonuje migrację do platformy Azure w celu przeprowadzenia przejścia i przesunięcia do swojej lokalnej aplikacji SmartHotel360. Firma Contoso migruje maszynę wirtualną aplikacji frontonu przy użyciu [Azure Migrate](./migrate-services-overview.md). Firma Contoso migruje bazę danych aplikacji do wystąpienia zarządzanego SQL przy użyciu [Azure Database Migration Service](../dms/dms-overview.md).
[Artykuł 5: Hostowanie aplikacji na maszynach wirtualnych platformy Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Firma Contoso migruje maszyny wirtualne aplikacji SmartHotel360 do maszyn wirtualnych platformy Azure przy użyciu usługi Azure Migrate. 
[Artykuł 6: rehostowanie aplikacji na maszynach wirtualnych platformy Azure i w SQL Server zawsze włączonych grupach dostępności](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Firma Contoso migruje aplikację SmartHotel360. Firma Contoso używa Azure Migrate do migrowania maszyn wirtualnych aplikacji. Używa Database Migration Service do migrowania bazy danych aplikacji do klastra SQL Server, który jest chroniony przez grupę dostępności AlwaysOn. 
[Artykuł 7: przehostaj aplikację systemu Linux na maszynach wirtualnych platformy Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Firma Contoso wykonuje migrację do maszyn wirtualnych platformy Azure w celu podnoszenia i przesunięcia z Azure Migrate funkcji osTicket.
[Artykuł 8: przehostaj aplikację systemu Linux na maszynach wirtualnych platformy Azure i Azure Database for MySQL](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Firma Contoso migruje swoją aplikację osTicket systemu Linux do maszyn wirtualnych platformy Azure przy użyciu Azure Migrate. Migruje bazę danych aplikacji do usługi Azure Database for MySQ przy użyciu Azure Database Migration Service (zawiera alternatywną opcję przy użyciu programu MySQL Workbench).
[Artykuł 9: Refaktoryzacja aplikacji w aplikacji sieci Web platformy Azure i Azure SQL Database](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Firma Contoso migruje swoją aplikację SmartHotel360 do aplikacji sieci Web platformy Azure i migruje bazę danych aplikacji do Azure SQL Database przy użyciu Azure Database Migration Service.
[Artykuł 10: Refaktoryzacja aplikacji systemu Windows przy użyciu usługi Azure App Services i wystąpienia zarządzanego SQL](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Firma Contoso migruje lokalną aplikację opartą na systemie Windows do aplikacji sieci Web platformy Azure i migruje bazę danych aplikacji do wystąpienia zarządzanego usługi Azure SQL przy użyciu Azure Database Migration Service.
[Artykuł 11: Refaktoryzacja aplikacji systemu Linux w aplikacji internetowej platformy Azure i Azure Database for MySQL](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Firma Contoso migruje swoją aplikację z systemem Linux osTicket do aplikacji internetowej platformy Azure w wielu regionach platformy Azure przy użyciu usługi Azure Traffic Manager zintegrowanej z usługą GitHub w celu ciągłego dostarczania. Firma Contoso migruje bazę danych aplikacji do wystąpienia Azure Database for MySQL. 
[Artykuł 12: Refaktoryzacja Team Foundation Server na Azure DevOps Services](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Firma Contoso migruje swoje lokalne wdrożenie Team Foundation Server do Azure DevOps Services na platformie Azure.
[Artykuł 13: ponowne kompilowanie aplikacji na platformie Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Firma Contoso odbudowuje swoją aplikację SmartHotel przy użyciu szeregu możliwości i usług platformy Azure, w tym Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services i Azure Cosmos DB.
[Artykuł 14: skalowanie migracji na platformę Azure](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Po zakończeniu kombinacji migracji firma Contoso przygotowuje skalowanie do pełnej migracji do platformy Azure.



## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o](/azure/architecture/cloud-adoption/migrate/) migracji do chmury.
- Więcej informacji na temat strategii migracji dla innych scenariuszy (par Source/Target) znajduje się w [przewodniku po migracji bazy danych](https://datamigration.microsoft.com/).
