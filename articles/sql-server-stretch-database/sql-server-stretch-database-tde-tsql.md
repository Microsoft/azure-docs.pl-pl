---
title: Włącz Transparent Data Encryption dla Stretch Database (T-SQL)
description: Włącz Transparent Data Encryption (TDE) dla SQL Server Stretch Database na platformie Azure TSQL
services: sql-server-stretch-database
documentationcenter: ''
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
author: blazem-msft
ms.author: blazem
ms.reviewer: jroth
manager: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 6f1f5f55348069dbfe11b4d5857d93f8ba8c9b19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74033950"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Włączanie Transparent Data Encryption (TDE) dla Stretch Database na platformie Azure (Transact-SQL)
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparent Data Encryption (TDE) pomaga chronić przed zagrożeniem złośliwym działaniem, wykonując w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych i plików dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian w aplikacji.

Technologia TDE szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego nazywanego kluczem szyfrowania bazy danych. Klucz szyfrowania bazy danych jest chroniony przez wbudowany certyfikat serwera. Wbudowany certyfikat serwera jest unikatowy dla każdego serwera platformy Azure. Firma Microsoft automatycznie obraca te certyfikaty co najmniej co 90 dni. Ogólny opis TDE można znaleźć w temacie [transparent Data Encryption (TDE)].

## <a name="enabling-encryption"></a>Włączanie szyfrowania
Aby włączyć TDE dla bazy danych platformy Azure, która przechowuje dane migrowane z SQL Server bazy danych z obsługą rozciągnięcia, wykonaj następujące czynności:

1. Nawiąż połączenie z bazą danych *Master* na serwerze platformy Azure hostującym bazę danych przy użyciu nazwy logowania będącej administratorem lub członkiem roli **DBManager** w bazie danych Master.
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Wyłączanie szyfrowania
Aby wyłączyć TDE dla bazy danych platformy Azure, która przechowuje dane migrowane z SQL Server bazy danych z obsługą rozciągnięcia, wykonaj następujące czynności:

1. Łączenie się z bazą danych *Master* przy użyciu nazwy logowania będącej administratorem lub członkiem roli **DBManager** w bazie danych Master
2. Wykonaj następującą instrukcję, aby zaszyfrować bazę danych.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>Weryfikowanie szyfrowania
Aby sprawdzić stan szyfrowania bazy danych platformy Azure, która przechowuje dane migrowane z SQL Serverej bazy danych z obsługą rozciągnięcia, wykonaj następujące czynności:

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

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
