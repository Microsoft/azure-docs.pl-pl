---
title: Migracja usług SSIS z wystąpieniem zarządzanym usługi Azure SQL Server jako miejscem docelowym obciążenia bazy danych
description: Migracja usług SSIS z wystąpieniem zarządzanym usługi Azure SQL Server jako miejscem docelowym obciążenia bazy danych.
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
ms.openlocfilehash: 6de08faee78deeb86117084b420eb5043153f62d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88186050"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>Migracja usług SSIS z wystąpieniem zarządzanym usługi Azure SQL Server jako miejscem docelowym obciążenia bazy danych

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Podczas migrowania obciążeń bazy danych z wystąpienia SQL Server do wystąpienia zarządzanego usługi Azure SQL należy zapoznać się z [usługą Azure Data Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) i [topologiami sieci dla migracji wystąpienia zarządzanego SQL za pomocą usługi DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Ten artykuł koncentruje się na migracji pakietów SQL Server Integration Service (SSIS) przechowywanych w katalogu SSIS (SSISDB) i zadaniach agenta SQL Server, które zaplanują wykonywanie pakietów SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrowanie wykazu usług SSIS (SSISDB)

Migrację SSISDB można wykonać za pomocą usługi DMS, zgodnie z opisem w artykule: [Migrowanie pakietów SSIS do wystąpienia zarządzanego SQL](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>Zadania SSIS do agenta wystąpienia zarządzanego SQL

Wystąpienie zarządzane SQL ma natywny, pierwszy-klasowy harmonogram, taki jak SQL Server Agent lokalnie.  Pakiety usług [SSIS można uruchamiać za pośrednictwem agenta wystąpienia zarządzanego usługi Azure SQL](how-to-invoke-ssis-package-managed-instance-agent.md).

Ponieważ narzędzie migracji dla zadań SSIS nie jest jeszcze dostępne, muszą zostać zmigrowane z agenta SQL Server lokalnego do agenta wystąpienia zarządzanego SQL za pośrednictwem skryptów/kopii ręcznej.

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Infrastruktura Azure SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Topologie sieci dla migracji wystąpienia zarządzanego SQL za pomocą usługi DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [Migrowanie pakietów usług SSIS do wystąpienia zarządzanego SQL](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Następne kroki

- [Łączenie się z usługą SSISDB na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Uruchamianie pakietów SSIS wdrożonych na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
