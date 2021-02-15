---
title: Migracja usług SSIS z wystąpieniem zarządzanym usługi Azure SQL Server jako miejscem docelowym obciążenia bazy danych
description: Migracja usług SSIS z wystąpieniem zarządzanym usługi Azure SQL Server jako miejscem docelowym obciążenia bazy danych.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 3d2bc60f8ba7120f8d962500c06be50e905c11a5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373593"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>Migracja usług SSIS z wystąpieniem zarządzanym usługi Azure SQL Server jako miejscem docelowym obciążenia bazy danych

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Podczas migrowania obciążeń bazy danych z wystąpienia SQL Server do wystąpienia zarządzanego usługi Azure SQL należy zapoznać się z [usługą Azure Data Migration Service](../dms/dms-overview.md)(DMS) i [topologiami sieci dla migracji wystąpienia zarządzanego SQL za pomocą usługi DMS](../dms/resource-network-topologies.md).

Ten artykuł koncentruje się na migracji pakietów SQL Server Integration Service (SSIS) przechowywanych w katalogu SSIS (SSISDB) i zadaniach agenta SQL Server, które zaplanują wykonywanie pakietów SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Migrowanie wykazu usług SSIS (SSISDB)

Migrację SSISDB można wykonać za pomocą usługi DMS, zgodnie z opisem w artykule: [Migrowanie pakietów SSIS do wystąpienia zarządzanego SQL](../dms/how-to-migrate-ssis-packages-managed-instance.md).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>Zadania SSIS do agenta wystąpienia zarządzanego SQL

Wystąpienie zarządzane SQL ma natywny, pierwszy-klasowy harmonogram, taki jak SQL Server Agent lokalnie.  Pakiety usług [SSIS można uruchamiać za pośrednictwem agenta wystąpienia zarządzanego usługi Azure SQL](how-to-invoke-ssis-package-managed-instance-agent.md).

Ponieważ narzędzie migracji dla zadań SSIS nie jest jeszcze dostępne, muszą zostać zmigrowane z agenta SQL Server lokalnego do agenta wystąpienia zarządzanego SQL za pośrednictwem skryptów/kopii ręcznej.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Azure Data Factory](./introduction.md)
- [Infrastruktura Azure SSIS Integration Runtime](./create-azure-ssis-integration-runtime.md)
- [Azure Database Migration Service](../dms/dms-overview.md)
- [Topologie sieci dla migracji wystąpienia zarządzanego SQL za pomocą usługi DMS](../dms/resource-network-topologies.md)
- [Migrowanie pakietów usług SSIS do wystąpienia zarządzanego SQL](../dms/how-to-migrate-ssis-packages-managed-instance.md)

## <a name="next-steps"></a>Następne kroki

- [Łączenie się z usługą SSISDB na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Uruchamianie pakietów SSIS wdrożonych na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-run-packages)