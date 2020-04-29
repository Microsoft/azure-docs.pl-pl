---
title: Migracja usług SSIS za pomocą Azure SQL Database wystąpienia zarządzanego jako miejsca docelowego obciążenia bazy danych
description: Migracja usług SSIS za pomocą Azure SQL Database wystąpienia zarządzanego jako miejsca docelowego obciążenia bazy danych.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 2e35e4eb750aa2244df920111b201d886599eaf6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81419055"
---
# <a name="ssis-migration-with-azure-sql-database-managed-instance-as-the-database-workload-destination"></a>Migracja usług SSIS za pomocą Azure SQL Database wystąpienia zarządzanego jako miejsca docelowego obciążenia bazy danych

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Podczas migrowania obciążeń bazy danych z SQL Server lokalnie do Azure SQL Database wystąpienia zarządzanego, należy zapoznać się z [usługą Azure Data Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) i [topologiami sieci dla Azure SQL Database migracji wystąpień zarządzanych przy użyciu usługi DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Ten artykuł koncentruje się na migracji pakietów SQL Server Integration Service (SSIS) przechowywanych w katalogu SSIS (SSISDB) i zadaniach agenta SQL Server, które zaplanują wykonywanie pakietów SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrowanie wykazu usług SSIS (SSISDB)

Migrację SSISDB można wykonać za pomocą usługi DMS, zgodnie z opisem w artykule: [Migrowanie pakietów SSIS do wystąpienia zarządzanego Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-azure-sql-database-managed-instance-agent"></a>Zadania SSIS do Azure SQL Database agenta wystąpienia zarządzanego

Azure SQL Database wystąpienie zarządzane ma natywny, pierwszy-klasowy harmonogram, podobnie jak Agent SQL Server lokalnie.  Ponieważ narzędzie migracji dla zadań SSIS nie jest jeszcze dostępne, muszą zostać zmigrowane z SQL Server agenta lokalnie do Azure SQL Database agenta wystąpienia zarządzanego za pośrednictwem skryptów/kopii ręcznej.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Infrastruktura Azure SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Topologie sieci dla Azure SQL Database migracji wystąpień zarządzanych przy użyciu usługi DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrowanie pakietów usług SSIS do wystąpienia zarządzanego usługi Azure SQL Database](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Następne kroki

- [Łączenie się z usługą SSISDB na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Uruchamianie pakietów SSIS wdrożonych na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
