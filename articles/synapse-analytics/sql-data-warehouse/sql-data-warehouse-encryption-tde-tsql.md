---
title: Transparent Data Encryption (T-SQL)
description: Transparent Data Encryption (TDE) w usłudze Azure Synapse Analytics (T-SQL)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 80e5da6bb281806afe6bc980e35d70732bcd609c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98676276"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Wprowadzenie do Transparent Data Encryption (TDE)

> [!div class="op_single_selector"]
>
> * [Przegląd zabezpieczeń](sql-data-warehouse-overview-manage-security.md)
> * [Authentication](sql-data-warehouse-authentication.md)
> * [Szyfrowanie (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Szyfrowanie (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby włączyć Transparent Data Encryption (TDE), musisz być administratorem lub członkiem roli dbmanager.

## <a name="enabling-encryption"></a>Włączanie szyfrowania

Wykonaj następujące kroki, aby włączyć TDE:

1. Nawiąż połączenie z bazą danych *Master* na serwerze hostującym bazę danych przy użyciu nazwy logowania będącej administratorem lub członkiem roli **DBManager** w bazie danych Master.
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Wyłączanie szyfrowania

Wykonaj następujące kroki, aby wyłączyć TDE:

1. Łączenie się z bazą danych *Master* przy użyciu nazwy logowania będącej administratorem lub członkiem roli **DBManager** w bazie danych Master
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Przed wprowadzeniem zmian w ustawieniach TDE należy wznowić wstrzymaną pulę SQL.

## <a name="verifying-encryption"></a>Weryfikowanie szyfrowania

Aby sprawdzić stan szyfrowania, wykonaj następujące czynności:

1. Łączenie się z bazą danych *Master* lub wystąpieniem przy użyciu nazwy logowania będącej administratorem lub członkiem roli **DBManager** w bazie danych Master
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Wynikiem ```1``` oznaczania zaszyfrowanej bazy danych ```0``` oznacza, że nie jest zaszyfrowana.

## <a name="encryption-dmvs"></a>Szyfrowanie widoków DMV

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
