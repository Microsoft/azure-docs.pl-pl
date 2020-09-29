---
title: Raportowanie w skalowanych bazach danych w chmurze
description: Zapytania bazy danych między bazami danych umożliwiają raportowanie wielu baz danych.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 10/10/2019
ms.openlocfilehash: 10be0fc28b53d114a8ff87f01980513ce1759dec
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91443309"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Raportowanie w skalowanych bazach danych w chmurze (wersja zapoznawcza)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Można tworzyć raporty z wielu baz danych z jednego punktu połączenia przy użyciu [zapytania elastycznego](elastic-query-overview.md). Bazy danych muszą być dzielone na partycje w poziomie (znanym także jako "podzielonej na fragmenty").

Jeśli masz istniejącą bazę danych, zobacz [Migrowanie istniejących baz danych do skalowalnych baz danych](elastic-convert-to-use-elastic-tools.md).

Aby zrozumieć obiekty SQL, które są konieczne do wykonywania zapytań, zobacz [zapytania w bazach danych w poziomie partycjonowanym](elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Wymagania wstępne

Pobierz i uruchom [przykład wprowadzenie do narzędzi Elastic Database](elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Tworzenie Menedżera mapy fragmentu za pomocą przykładowej aplikacji
W tym miejscu utworzysz Menedżera mapy fragmentu wraz z kilkoma fragmentów, a następnie wstawiasz dane do fragmentów. Jeśli masz już Instalatora fragmentów z danymi podzielonej na fragmenty, możesz pominąć poniższe kroki i przejść do następnej sekcji.

1. Utwórz i uruchom przykładową aplikację **wprowadzenie do Elastic Database Tools** , wykonując kroki opisane w sekcji artykułu [pobieranie i uruchamianie przykładowej aplikacji](elastic-scale-get-started.md#download-and-run-the-sample-app-1). Po zakończeniu wszystkich kroków zobaczysz następujący wiersz polecenia:

    ![wiersz polecenia][1]
2. W oknie wiersza polecenia wpisz "1" i naciśnij klawisz **Enter**. Spowoduje to utworzenie Menedżera mapy fragmentu i dodanie dwóch fragmentów do serwera. Następnie wpisz "3" i naciśnij klawisz **Enter**; Powtarzaj akcję cztery razy. Spowoduje to wstawienie przykładowych wierszy danych w fragmentów.
3. [Azure Portal](https://portal.azure.com) powinny zawierać trzy nowe bazy danych na serwerze:

   ![Potwierdzenie programu Visual Studio][2]

   W tym momencie zapytania między bazami danych są obsługiwane za pomocą bibliotek klienckich Elastic Database. Na przykład użyj opcji 4 w oknie poleceń. Wyniki zapytania fragmentu są zawsze **Union wszystkie** wyniki ze wszystkich fragmentów.

   W następnej sekcji tworzymy przykładowy punkt końcowy bazy danych, który obsługuje bogatsze zapytania dotyczące danych w fragmentów.

## <a name="create-an-elastic-query-database"></a>Tworzenie elastycznej bazy danych zapytań

1. Otwórz [Azure Portal](https://portal.azure.com) i zaloguj się.
2. Utwórz nową bazę danych w Azure SQL Database na tym samym serwerze, na którym znajduje się konfiguracja fragmentu. Nazwij bazę danych "ElasticDBQuery".

    ![Azure Portal i warstwa cenowa][3]

    > [!NOTE]
    > Możesz użyć istniejącej bazy danych. Jeśli to możliwe, nie może być jednym z fragmentów, w którym chcesz wykonywać zapytania. Ta baza danych będzie używana do tworzenia obiektów metadanych dla zapytania Elastic Database.
    >

## <a name="create-database-objects"></a>Tworzenie obiektów bazy danych
### <a name="database-scoped-master-key-and-credentials"></a>Klucz główny i poświadczenia w zakresie bazy danych
Są one używane do nawiązywania połączenia z menedżerem map fragmentu i fragmentów:

1. Otwórz SQL Server Management Studio lub SQL Server narzędzia danych w programie Visual Studio.
2. Nawiąż połączenie z bazą danych ElasticDBQuery i wykonaj następujące polecenia T-SQL:

    ```tsql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';

    CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
    WITH IDENTITY = '<username>',
    SECRET = '<password>';
    ```

    nazwy "username" i "Password" powinny być takie same jak informacje logowania używane w kroku 3 sekcji [pobieranie i uruchamianie przykładowej aplikacji](elastic-scale-get-started.md#download-and-run-the-sample-app) w artykule **wprowadzenie do narzędzi Elastic Database** .

### <a name="external-data-sources"></a>Zewnętrzne źródła danych
Aby utworzyć zewnętrzne źródło danych, wykonaj następujące polecenie w bazie danych ElasticDBQuery:

```tsql
CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
    (TYPE = SHARD_MAP_MANAGER,
    LOCATION = '<server_name>.database.windows.net',
    DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
    CREDENTIAL = ElasticDBQueryCred,
    SHARD_MAP_NAME = 'CustomerIDShardMap'
) ;
```    

 "CustomerIDShardMap" jest nazwą mapy fragmentu, jeśli utworzono mapę fragmentu i fragmentu mapowanie przy użyciu przykładowych narzędzi Elastic Database. Jeśli jednak użyto konfiguracji niestandardowej dla tego przykładu, powinna to być nazwa mapy fragmentu wybrana w aplikacji.

### <a name="external-tables"></a>Tabele zewnętrzne
Utwórz tabelę zewnętrzną zgodną z tabelą Customers w fragmentów, wykonując następujące polecenie w bazie danych ElasticDBQuery:

```tsql
CREATE EXTERNAL TABLE [dbo].[Customers]
( [CustomerId] [int] NOT NULL,
    [Name] [nvarchar](256) NOT NULL,
    [RegionId] [int] NOT NULL)
WITH
( DATA_SOURCE = MyElasticDBQueryDataSrc,
    DISTRIBUTION = SHARDED([CustomerId])
) ;
```

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Wykonywanie przykładowego zapytania T-SQL Elastic Database
Po zdefiniowaniu zewnętrznego źródła danych i tabel zewnętrznych można teraz używać pełnego języka T-SQL dla tabel zewnętrznych.

Wykonaj to zapytanie w bazie danych ElasticDBQuery:

```tsql
select count(CustomerId) from [dbo].[Customers]
```

Zobaczysz, że zapytanie agreguje wyniki ze wszystkich fragmentów i daje następujące dane wyjściowe:

![Szczegóły danych wyjściowych][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importuj wyniki zapytania Elastic Database do programu Excel
 Wyniki z zapytania można zaimportować do pliku programu Excel.

1. Uruchom program Excel 2013.
2. Przejdź do wstążki **dane** .
3. Kliknij **z innych źródeł** i kliknij pozycję **z SQL Server**.

   ![Import programu Excel z innych źródeł][5]
4. W **Kreatorze połączenia danych** wpisz nazwę serwera i poświadczenia logowania. Następnie kliknij przycisk **Dalej**.
5. W oknie dialogowym **Wybierz bazę danych zawierającą dane, których chcesz użyć**, a następnie wybierz bazę danych **ElasticDBQuery** .
6. Wybierz tabelę **Customers** w widoku listy, a następnie kliknij przycisk **dalej**. Następnie kliknij przycisk **Zakończ**.
7. W formularzu **Importuj dane** w obszarze **Wybierz sposób wyświetlania tych danych w skoroszycie**wybierz pozycję **tabela** , a następnie kliknij przycisk **OK**.

Wszystkie wiersze z tabeli **Customers** , przechowywane w różnych fragmentówach wypełniają arkusz programu Excel.

Teraz można korzystać z zaawansowanych funkcji wizualizacji danych programu Excel. Możesz użyć parametrów połączenia z nazwą serwera, nazwą bazy danych i poświadczeniami, aby połączyć narzędzia do analizy biznesowej i danych z bazą danych zapytań elastycznych. Upewnij się, że SQL Server jest obsługiwane jako źródło danych dla narzędzia. Można odwołać się do bazy danych zapytań elastycznych i tabel zewnętrznych, podobnie jak każda inna baza danych SQL Server i SQL Server tabele, z którymi można nawiązać połączenie za pomocą narzędzia.

### <a name="cost"></a>Cost (Koszt)
Za korzystanie z funkcji zapytania Elastic Database nie ma dodatkowych opłat.

Aby uzyskać informacje o cenach, zobacz [szczegóły cennika SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z omówieniem zapytania elastycznego, zobacz [Omówienie zapytania elastycznego](elastic-query-overview.md).
* Aby zapoznać się z pionowym samouczkiem partycjonowania, zobacz [Rozpoczynanie pracy z kwerendą między bazami danych (partycjonowanie pionowe)](elastic-query-getting-started-vertical.md).
* Aby poznać składnię i przykładowe zapytania dotyczące danych partycjonowanych pionowo, zobacz [wykonywanie zapytań dotyczących partycjonowanych danych w pionie.](elastic-query-vertical-partitioning.md)
* Aby poznać składnię i przykładowe zapytania dla danych z podziałem na partycje, zobacz [wykonywanie zapytań o dane partycjonowane w poziomie.](elastic-query-horizontal-partitioning.md)
* Zapoznaj się z artykułem [SP \_ Execute \_ Remote](https://msdn.microsoft.com/library/mt703714) dla procedury składowanej, która wykonuje instrukcję języka Transact-SQL w ramach jednego zdalnego Azure SQL Database lub zestawu baz danych służących jako fragmentów w poziomym schemacie partycjonowania.


<!--Image references-->
[1]: ./media/elastic-query-getting-started/cmd-prompt.png
[2]: ./media/elastic-query-getting-started/portal.png
[3]: ./media/elastic-query-getting-started/tiers.png
[4]: ./media/elastic-query-getting-started/details.png
[5]: ./media/elastic-query-getting-started/exel-sources.png
<!--anchors-->
