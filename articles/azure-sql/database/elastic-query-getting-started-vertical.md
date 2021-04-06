---
title: Wprowadzenie do zapytań między bazami danych
description: Jak używać zapytania Elastic Database z bazami danych z podziałem pionowym
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 90f89e72193c26b71859076b99c448a6e2d4c4c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98060133"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Wprowadzenie do zapytań między bazami danych (partycjonowanie pionowe) (wersja zapoznawcza)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Zapytanie Elastic Database (wersja zapoznawcza) dla Azure SQL Database umożliwia uruchamianie zapytań T-SQL obejmujących wiele baz danych przy użyciu jednego punktu połączenia. Ten artykuł ma zastosowanie do [baz danych partycjonowanych w pionie](elastic-query-vertical-partitioning.md).  

Po zakończeniu: informacje na temat konfigurowania Azure SQL Database i używania ich do wykonywania zapytań obejmujących wiele powiązanych baz danych.

Aby uzyskać więcej informacji na temat funkcji zapytania Elastic Database, zobacz  [Omówienie zapytania Elastic database Azure SQL Database](elastic-query-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Wymagane jest uprawnienie Zmień każde zewnętrzne źródło danych. To uprawnienie jest dołączone do uprawnienia ALTER DATABASE. Aby odwołać się do bazowego źródła danych, należy zmienić wszystkie uprawnienia zewnętrznych źródeł danych.

## <a name="create-the-sample-databases"></a>Tworzenie przykładowych baz danych

Aby rozpocząć pracę z programem, Utwórz dwie bazy danych, **klientów** i **zamówień** w tym samym lub różnych serwerach.

Wykonaj następujące zapytania w bazie danych **Orders** , aby utworzyć tabelę **OrderInformation** i wprowadzić przykładowe dane.

```tsql
CREATE TABLE [dbo].[OrderInformation](
    [OrderID] [int] NOT NULL,
    [CustomerID] [int] NOT NULL
    )
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
```

Teraz wykonaj następujące zapytanie w bazie danych **Customers** , aby utworzyć tabelę **CustomerInformation** i wprowadzić przykładowe dane.

```tsql
CREATE TABLE [dbo].[CustomerInformation](
    [CustomerID] [int] NOT NULL,
    [CustomerName] [varchar](50) NULL,
    [Company] [varchar](50) NULL
    CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
)
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')
```

## <a name="create-database-objects"></a>Tworzenie obiektów bazy danych

### <a name="database-scoped-master-key-and-credentials"></a>Klucz główny i poświadczenia w zakresie bazy danych

1. Otwórz SQL Server Management Studio lub SQL Server narzędzia danych w programie Visual Studio.
2. Nawiąż połączenie z bazą danych Orders i wykonaj następujące polecenia T-SQL:

    ```tsql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
    CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
    WITH IDENTITY = '<username>',
    SECRET = '<password>';  
    ```

    "Master_key_password" jest silnym hasłem używanym do szyfrowania poświadczeń połączenia. 
    "Username" i "Password" powinna być nazwą użytkownika i hasłem używanym do logowania się do bazy danych Customers.
    Uwierzytelnianie przy użyciu Azure Active Directory z elastycznymi zapytaniami nie jest obecnie obsługiwane.

### <a name="external-data-sources"></a>Zewnętrzne źródła danych

Aby utworzyć zewnętrzne źródło danych, wykonaj następujące polecenie w bazie danych Orders:

```tsql
CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
    (TYPE = RDBMS,
    LOCATION = '<server_name>.database.windows.net',
    DATABASE_NAME = 'Customers',
    CREDENTIAL = ElasticDBQueryCred,
) ;
```

### <a name="external-tables"></a>Tabele zewnętrzne

Utwórz zewnętrzną tabelę w bazie danych Orders, która jest zgodna z definicją tabeli CustomerInformation:

```tsql
CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
( [CustomerID] [int] NOT NULL,
    [CustomerName] [varchar](50) NOT NULL,
    [Company] [varchar](50) NOT NULL)
WITH
( DATA_SOURCE = MyElasticDBQueryDataSrc)
```

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Wykonywanie przykładowego zapytania T-SQL Elastic Database

Po zdefiniowaniu zewnętrznego źródła danych i tabel zewnętrznych możesz użyć języka T-SQL do wykonywania zapytań dotyczących tabel zewnętrznych. Wykonaj to zapytanie w bazie danych Orders:

```tsql
SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
FROM OrderInformation
INNER JOIN CustomerInformation
ON CustomerInformation.CustomerID = OrderInformation.CustomerID
```

## <a name="cost"></a>Koszt

Obecnie funkcja zapytania Elastic Database jest uwzględniana w kosztach Azure SQL Database.  

Aby uzyskać informacje o cenach, zobacz [Cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database).

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z omówieniem zapytania elastycznego, zobacz [Omówienie zapytania elastycznego](elastic-query-overview.md).
* Aby poznać składnię i przykładowe zapytania dotyczące danych partycjonowanych pionowo, zobacz [wykonywanie zapytań dotyczących partycjonowanych danych w pionie.](elastic-query-vertical-partitioning.md)
* Aby zapoznać się z samouczkiem dotyczącym partycjonowania poziomego (fragmentowania), zobacz [wprowadzenie do elastycznego zapytania na potrzeby partycjonowania poziomego (fragmentowania)](elastic-query-getting-started.md).
* Aby poznać składnię i przykładowe zapytania dla danych z podziałem na partycje, zobacz [wykonywanie zapytań o dane partycjonowane w poziomie.](elastic-query-horizontal-partitioning.md)
* Zapoznaj się z artykułem [SP \_ Execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) dla procedury składowanej, która wykonuje instrukcję języka Transact-SQL w ramach jednego zdalnego Azure SQL Database lub zestawu baz danych służących jako fragmentów w poziomym schemacie partycjonowania.
