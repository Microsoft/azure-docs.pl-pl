---
title: Wykonywanie zapytań w bazach danych w chmurze z różnymi schematami
description: Jak skonfigurować zapytania obejmujące wiele baz danych w ramach partycji pionowych
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: c507a4c618713ba83d25b9defa918092db1a3c8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792093"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Wykonywanie zapytań w bazach danych w chmurze z różnymi schematami (wersja zapoznawcza)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

![Zapytanie między tabelami w różnych bazach danych][1]

Bazy danych partycjonowane w pionie korzystają z różnych zestawów tabel w różnych bazach danych. Oznacza to, że schemat różni się w różnych bazach danych. Na przykład wszystkie tabele dla spisu znajdują się w jednej bazie danych, podczas gdy wszystkie tabele związane z ewidencjonowanie aktywności znajdują się w drugiej bazie danych.

## <a name="prerequisites"></a>Wymagania wstępne

* Użytkownik musi mieć uprawnienie Zmień każde zewnętrzne źródło danych. To uprawnienie jest dołączone do uprawnienia ALTER DATABASE.
* Aby odwołać się do bazowego źródła danych, należy zmienić wszystkie uprawnienia zewnętrznych źródeł danych.

## <a name="overview"></a>Omówienie

> [!NOTE]
> W przeciwieństwie do partycjonowania poziomego te instrukcje DDL nie zależą od definiowania warstwy danych za pomocą mapy fragmentu za pomocą biblioteki klienta Elastic Database.
>

1. [UTWÓRZ KLUCZ GŁÓWNY](/sql/t-sql/statements/create-master-key-transact-sql)
2. [UTWÓRZ POŚWIADCZENIA W ZAKRESIE BAZY DANYCH](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
3. [UTWÓRZ ZEWNĘTRZNE ŹRÓDŁO DANYCH](/sql/t-sql/statements/create-external-data-source-transact-sql)
4. [TWORZENIE TABELI ZEWNĘTRZNEJ](/sql/t-sql/statements/create-external-table-transact-sql)

## <a name="create-database-scoped-master-key-and-credentials"></a>Utwórz klucz główny i poświadczenia w zakresie bazy danych

To poświadczenie jest używane przez zapytanie elastyczne do łączenia się ze zdalnymi bazami danych.  

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
SECRET = '<password>'
[;]
```

> [!NOTE]
> Upewnij się, że nie `<username>` zawiera żadnego sufiksu **" \@ servername"** .

## <a name="create-external-data-sources"></a>Tworzenie zewnętrznych źródeł danych

Składnia:

<External_Data_Source>:: = Utwórz zewnętrzne źródło danych <data_source_name> z (TYPE = RDBMS, LOCATION = "<fully_qualified_server_name>", DATABASE_NAME = "<remote_database_name>",  
    CREDENTIAL = <credential_name>) [;]

> [!IMPORTANT]
> Parametr typu musi być ustawiony na wartość **RDBMS**.

### <a name="example"></a>Przykład

Poniższy przykład ilustruje użycie instrukcji CREATE dla zewnętrznych źródeł danych.

```sql
CREATE EXTERNAL DATA SOURCE RemoteReferenceData
   WITH
      (
         TYPE=RDBMS,
         LOCATION='myserver.database.windows.net',
         DATABASE_NAME='ReferenceData',
         CREDENTIAL= SqlUser
      );
```

Aby pobrać listę bieżących zewnętrznych źródeł danych:

```sql
select * from sys.external_data_sources;
```

### <a name="external-tables"></a>Tabele zewnętrzne

Składnia:

Utwórz tabelę ZEWNĘTRZną [database_name. [schema_name]. | schema_name. ] table_name  
    ({<column_definition>} [,... n]) {WITH (<rdbms_external_table_options>)}) [;]

<rdbms_external_table_options>:: = DATA_SOURCE = <External_Data_Source>, [SCHEMA_NAME = N "nonescaped_schema_name",] [OBJECT_NAME = N "nonescaped_object_name",]

### <a name="example"></a>Przykład

```sql
CREATE EXTERNAL TABLE [dbo].[customer]
   (
      [c_id] int NOT NULL,
      [c_firstname] nvarchar(256) NULL,
      [c_lastname] nvarchar(256) NOT NULL,
      [street] nvarchar(256) NOT NULL,
      [city] nvarchar(256) NOT NULL,
      [state] nvarchar(20) NULL,
      DATA_SOURCE = RemoteReferenceData
   );
```

Poniższy przykład pokazuje, jak pobrać listę tabel zewnętrznych z bieżącej bazy danych:

```sql
select * from sys.external_tables;
```

### <a name="remarks"></a>Uwagi

Zapytanie elastyczne rozszerza istniejącą składnię tabeli zewnętrznej w celu zdefiniowania tabel zewnętrznych, które używają zewnętrznych źródeł danych typu RDBMS. Definicja tabeli zewnętrznej dla partycjonowania pionowego obejmuje następujące aspekty:

* **Schemat**: tabela zewnętrzna DDL definiuje schemat, którego mogą używać zapytania. Schemat podany w definicji tabeli zewnętrznej musi pasować do schematu tabel w zdalnej bazie danych, w której przechowywane są rzeczywiste dane.
* **Odwołanie do zdalnej bazy danych**: tablica zewnętrzna DDL odwołuje się do zewnętrznego źródła danych. Zewnętrzne źródło danych określa nazwę serwera i nazwę bazy danych, w której przechowywane są rzeczywiste dane tabeli.

Korzystając z zewnętrznego źródła danych, jak opisano w poprzedniej sekcji, Składnia służąca do tworzenia tabel zewnętrznych jest następująca:

Klauzula DATA_SOURCE definiuje zewnętrzne źródło danych (tj. zdalną bazę danych w przypadku partycjonowania pionowego), które jest używane dla tabeli zewnętrznej.  

Klauzule SCHEMA_NAME i OBJECT_NAME umożliwiają mapowanie definicji tabeli zewnętrznej na tabelę w innym schemacie w zdalnej bazie danych lub w tabeli o innej nazwie odpowiednio. Jest to przydatne, jeśli chcesz zdefiniować tabelę zewnętrzną w widoku wykazu lub DMV na zdalnej bazie danych, lub dowolną inną sytuację, w której nazwa tabeli zdalnej jest już pobrana lokalnie.  

Poniższa instrukcja języka DDL powoduje porzucanie istniejącej definicji tabeli zewnętrznej z wykazu lokalnego. Nie ma to wpływu na zdalną bazę danych.

```sql
DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  
```

**Uprawnienia do tworzenia/upuszczania tabeli zewnętrznej**: należy zmienić wszystkie uprawnienia zewnętrznych źródeł danych w przypadku kodu DDL tabeli zewnętrznej, który jest również konieczny do odwoływania się do bazowego źródła danych.  

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa

Użytkownicy z dostępem do tabeli zewnętrznej automatycznie uzyskują dostęp do podstawowych tabel zdalnych w ramach poświadczeń podanych w definicji zewnętrznego źródła danych. Należy dokładnie zarządzać dostępem do tabeli zewnętrznej, aby uniknąć niepożądanego podniesienia uprawnień za pomocą poświadczeń zewnętrznego źródła danych. Regularne uprawnienia SQL mogą służyć do udzielenia lub odwołania dostępu do tabeli zewnętrznej, tak jakby była to zwykła tabela.  

## <a name="example-querying-vertically-partitioned-databases"></a>Przykład: wykonywanie zapytań dotyczących partycjonowanych baz danych

Następujące zapytanie wykonuje sprzężenie trójwymiarowe między dwiema lokalnymi tabelami zamówień i wierszy zamówienia oraz tabelą zdalną dla klientów. Jest to przykład przypadku użycia danych referencyjnych dla zapytania elastycznego:

```sql
    SELECT
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline,
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer
    JOIN orders
    ON c_id = o_c_id
    JOIN  order_line
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Procedura składowana dla zdalnego wykonywania T-SQL: SP \_ execute_remote

W przypadku zapytań elastycznych wprowadzono również procedurę składowaną, która zapewnia bezpośredni dostęp do zdalnej bazy danych. Procedura składowana jest nazywana [SP \_ Execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) i może służyć do wykonywania zdalnych procedur składowanych lub kodu T-SQL w zdalnej bazie danych. Przyjmuje następujące parametry:

* Nazwa źródła danych (nvarchar): Nazwa zewnętrznego źródła danych typu RDBMS.
* Query (nvarchar): zapytanie T-SQL, które ma zostać wykonane na zdalnej bazie danych.
* Deklaracja parametru (nvarchar)-Optional: ciąg z definicjami typu danych dla parametrów używanych w parametrze zapytania (na przykład sp_executesql).
* Lista wartości parametrów — opcjonalne: rozdzielana przecinkami lista wartości parametrów (takich jak sp_executesql).

Funkcja Sp \_ Execute \_ umożliwia zdalne użycie zewnętrznego źródła danych podanego w parametrach wywołania, aby wykonać daną instrukcję T-SQL w zdalnej bazie danych. Używa poświadczeń zewnętrznego źródła danych, aby nawiązać połączenie ze zdalną bazą danych.  

Przykład:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Łączność dla narzędzi

Za pomocą regularnych SQL Server parametrów połączenia można łączyć narzędzia do obsługi analizy biznesowej i danych z bazami danych na serwerze, na którym jest włączona funkcja elastyczne zapytania i zdefiniowane tabele zewnętrzne. Upewnij się, że SQL Server jest obsługiwane jako źródło danych dla narzędzia. Następnie zapoznaj się z bazą danych zapytań elastycznych i jej tabelami zewnętrznymi tak samo jak każda inna baza danych SQL Server, z którą chcesz nawiązać połączenie za pomocą narzędzia.

## <a name="best-practices"></a>Najlepsze rozwiązania

* Upewnij się, że baza danych punktu końcowego zapytania elastycznego ma dostęp do zdalnej bazy danych przez włączenie dostępu do usług platformy Azure w Azure SQL Database konfiguracji zapory. Upewnij się również, że poświadczenie podane w definicji zewnętrznego źródła danych może pomyślnie zalogować się do zdalnej bazy danych i ma uprawnienia dostępu do tabeli zdalnej.  
* Elastyczne zapytanie działa najlepiej w przypadku zapytań, w których można wykonywać większość obliczeń w zdalnych bazach danych. Zwykle uzyskuje się najlepszą wydajność zapytań za pomocą predykatów filtrów selektywnych, które mogą być oceniane w zdalnych bazach danych lub sprzężenia, które można całkowicie wykonać w zdalnej bazie danych. Inne wzorce zapytań mogą wymagać załadowania dużych ilości danych ze zdalnej bazy danych i mogą działać nieprawidłowo.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z omówieniem zapytania elastycznego, zobacz [Omówienie zapytania elastycznego](elastic-query-overview.md).
* Aby zapoznać się z pionowym samouczkiem partycjonowania, zobacz [Rozpoczynanie pracy z kwerendą między bazami danych (partycjonowanie pionowe)](elastic-query-getting-started-vertical.md).
* Aby zapoznać się z samouczkiem dotyczącym partycjonowania poziomego (fragmentowania), zobacz [wprowadzenie do elastycznego zapytania na potrzeby partycjonowania poziomego (fragmentowania)](elastic-query-getting-started.md).
* Aby poznać składnię i przykładowe zapytania dla danych z podziałem na partycje, zobacz [wykonywanie zapytań o dane partycjonowane w poziomie.](elastic-query-horizontal-partitioning.md)
* Zapoznaj się z artykułem [SP \_ Execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) dla procedury składowanej, która wykonuje instrukcję języka Transact-SQL w ramach jednego zdalnego Azure SQL Database lub zestawu baz danych służących jako fragmentów w poziomym schemacie partycjonowania.

<!--Image references-->
[1]: ./media/elastic-query-vertical-partitioning/verticalpartitioning.png

<!--anchors-->