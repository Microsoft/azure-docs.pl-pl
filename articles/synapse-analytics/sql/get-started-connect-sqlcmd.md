---
title: Połącz się z Synapse SQL za pomocą sqlcmd
description: Użyj narzędzia wiersza polecenia sqlcmd, aby połączyć się z pulą SQL na żądanie i wysyłać zapytania do programu SQL na żądanie (wersja zapoznawcza) i SQL.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 8ff9034e6c31c8d95e862570e3962990dfec8442
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423754"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>Połącz się z Synapse SQL z sqlcmd

> [!div class="op_single_selector"]
>
> * [Usługa Azure Data Studio (wersja zapoznawcza)](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Program Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Za pomocą narzędzia wiersza polecenia [sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) można łączyć się z pulą SQL na żądanie (wersja zapoznawczą) i SQL i wysyłać zapytania do niej .  

## <a name="1-connect"></a>1. Połącz
Aby rozpocząć pracę z [sqlcmd,](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)otwórz wiersz polecenia i wprowadź **sqlcmd,** po którym następuje ciąg połączenia dla bazy danych Synapse SQL. Wymagane parametry połączenia to:

* **Serwer (-S):** serwer w postaci `<`nazwa_serwera`>`.database.windows.net
* **Baza danych (-d):** Nazwa bazy danych
* **Włącz identyfikatory cytowane (-I):** Cytoni identyfikatory muszą być włączone, aby połączyć się z wystąpieniem Synapse SQL

Aby korzystać z uwierzytelniania programu SQL Server, należy dodać parametry nazwy użytkownika i hasła:

* **Użytkownik (-U):** użytkownik serwera w formie `<`Użytkownik`>`
* **Hasło (-P):** Hasło skojarzone z użytkownikiem

Twój parametry połączenia mogą wyglądać następująco:

**SQL na żądanie**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**Pula SQL**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Aby użyć zintegrowanego uwierzytelniania usługi Azure Active Directory, należy dodać parametry usługi Azure Active Directory:

* **Uwierzytelnianie usługi Azure Active Directory (-G):** używaj usługi Azure Active Directory do uwierzytelniania

Twój ciąg połączenia może wyglądać jak w następujących przykładach:

**SQL na żądanie**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**Pula SQL**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Musisz [włączyć uwierzytelnianie usługi Azure Active Directory](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), aby uwierzytelniać przy użyciu usługi Active Directory.

## <a name="2-query"></a>2. Zapytanie

### <a name="use-sql-pool"></a>Korzystanie z puli SQL

Po nawiązaniu połączenia można wydawać dowolne obsługiwane instrukcje języka Transact-SQL dotyczące wystąpienia.  W tym przykładzie kwerendy są przesyłane w trybie interaktywnym:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

W przypadku puli SQL poniższe przykłady pokazują, jak uruchamiać kwerendy w trybie wsadowym przy użyciu opcji -Q lub rurociągów SQL do sqlcmd:

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-sql-on-demand"></a>Korzystanie z języka SQL na żądanie

Po nawiązaniu połączenia można wydać wszystkie obsługiwane instrukcje [Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (T-SQL) względem wystąpienia.  W poniższym przykładzie zapytania są przesyłane w trybie interaktywnym:

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

W przypadku sql na żądanie przykłady, które należy wykonać pokazują, jak uruchamiać kwerendy w trybie wsadowym przy użyciu opcji -Q lub rurociągów SQL do sqlcmd:

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat opcji sqlcmd, zobacz [dokumentację sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).
