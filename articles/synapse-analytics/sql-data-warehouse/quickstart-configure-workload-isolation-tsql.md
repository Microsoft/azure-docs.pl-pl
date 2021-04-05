---
title: 'Szybki Start: Konfigurowanie izolacji obciążeń — T-SQL'
description: Aby skonfigurować izolację obciążenia, Użyj języka T-SQL.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/27/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9d69c1708e73ad7ce5610a0683835e9f304c3f54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98679758"
---
# <a name="quickstart-configure-workload-isolation-in-a-dedicated-sql-pool-using-t-sql"></a>Szybki Start: Konfigurowanie izolacji obciążeń w dedykowanej puli SQL przy użyciu języka T-SQL

W tym przewodniku szybki start utworzysz grupę obciążeń i klasyfikator w celu zarezerwowania zasobów na potrzeby ładowania danych. Grupa obciążenia przydzieli 20% zasobów systemowych na ładowane dane.  Klasyfikator obciążeń przypisze żądania do grupy obciążeń ładowania danych.  Dzięki obciążeniu 20% w przypadku ładowania danych są one gwarantowane zasobom, które trafią umowy SLA.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

> [!NOTE]
> Utworzenie wystąpienia programu SQL Synapse w usłudze Azure Synapse Analytics może spowodować powstanie nowej usługi do obciążania.  Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku szybki start założono, że masz już wystąpienie SQL Synapse w usłudze Azure Synapse i że masz uprawnienia do kontroli bazy danych. Jeśli trzeba ją utworzyć, użyj instrukcji [Create i Connect-Portal](create-data-warehouse-portal.md) , aby utworzyć dedykowaną pulę SQL o nazwie **mySampleDataWarehouse**.

## <a name="create-login-for-dataloads"></a>Utwórz nazwę logowania dla ładowania danych

Utwórz SQL Server Logowanie do uwierzytelniania w `master` bazie danych przy użyciu polecenia [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) for "ELTLogin'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Tworzenie użytkownika

[Tworzenie użytkownika](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), "ELTLogin" w mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Tworzenie grupy obciążeń

Utwórz [grupę obciążeń](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) dla obciążeń dataloads z izolacją 20%.

```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5)
;
```

## <a name="create-a-workload-classifier"></a>Tworzenie klasyfikatora obciążeń

Utwórz [klasyfikator obciążeń](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , aby zamapować ELTLogin do grupy obciążeń dataloads.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Wyświetlanie istniejących grup obciążeń i klasyfikatorów oraz wartości czasu wykonywania

```sql
--Workload groups
SELECT * FROM
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Opłaty są naliczane za jednostki magazynu danych i dane przechowywane w dedykowanej puli SQL. Opłaty za te zasoby obliczeniowe i magazynowe są naliczane osobno.

- Jeśli chcesz zachować dane w magazynie, możesz wstrzymać obliczenia, gdy nie używasz dedykowanej puli SQL. Przez wstrzymywanie obliczeń opłata jest naliczana tylko za magazyn danych. Gdy wszystko będzie gotowe do pracy z danymi, Wznów obliczenia.
- Jeśli chcesz usunąć przyszłe opłaty, możesz usunąć dedykowaną pulę SQL.

## <a name="next-steps"></a>Następne kroki

- Grupa obciążeń została już utworzona. Uruchom kilka zapytań jako ELTLogin, aby zobaczyć, jak są one wykonywane. Zobacz [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , aby wyświetlić zapytania i przypisaną grupę obciążeń.
- Aby uzyskać więcej informacji na temat Synapse zarządzania obciążeniami SQL, zobacz [zarządzanie obciążeniami](sql-data-warehouse-workload-management.md) i [izolacja obciążeń](sql-data-warehouse-workload-isolation.md).
