---
title: Nawiązywanie połączenia z usługą Synapse SQL przy użyciu narzędzia sqlcmd
description: Użyj narzędzia wiersza polecenia sqlcmd, aby nawiązać połączenie z pulą SQL bezserwerowej i z dedykowaną pulą SQL i wykonać zapytanie.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 7d6675f9584f90b67d8520091dcd4b04dd89e462
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667586"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>Nawiązywanie połączenia z usługą Synapse SQL przy użyciu narzędzia sqlcmd

> [!div class="op_single_selector"]
> * [Azure Data Studio)](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Można użyć narzędzia wiersza polecenia [sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true) , aby nawiązać połączenie z pulą SQL bezserwerowej i dedykowaną pulą SQL w ramach Synapse SQL.  

## <a name="1-connect"></a>1. Połącz
Aby rozpocząć pracę z narzędziem [sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true), Otwórz wiersz polecenia i wprowadź polecenie **sqlcmd** , a następnie parametry połączenia dla bazy danych SQL Synapse. Wymagane parametry połączenia to:

* **Serwer (-S):** serwer w postaci `<`nazwa_serwera`>`.database.windows.net
* **Baza danych (-d):** Nazwa bazy danych
* **Włącz identyfikatory ujęte w cudzysłów (-I):** Aby można było nawiązać połączenie z wystąpieniem programu SQL Synapse, muszą być włączone identyfikatory ujęte w cudzysłów

Aby użyć uwierzytelniania SQL Server, należy dodać parametry nazwy użytkownika i hasła:

* **Użytkownik (-U):** użytkownik serwera w formie `<`Użytkownik`>`
* **Hasło (-P):** Hasło skojarzone z użytkownikiem

Parametry połączenia mogą wyglądać podobnie jak w poniższym przykładzie:

**Pula SQL bezserwerowa**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**Dedykowana pula SQL**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Aby użyć zintegrowanego uwierzytelniania usługi Azure Active Directory, należy dodać parametry usługi Azure Active Directory:

* **Uwierzytelnianie usługi Azure Active Directory (-G):** używaj usługi Azure Active Directory do uwierzytelniania

Parametry połączenia mogą wyglądać następująco:

**Pula SQL bezserwerowa**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**Dedykowana pula SQL**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Musisz [włączyć uwierzytelnianie usługi Azure Active Directory](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), aby uwierzytelniać przy użyciu usługi Active Directory.

## <a name="2-query"></a>2. zapytanie

### <a name="use-dedicated-sql-pool"></a>Użyj dedykowanej puli SQL

Po nawiązaniu połączenia można wydać wszystkie obsługiwane instrukcje [języka Transact-SQL](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (T-SQL) w odniesieniu do wystąpienia. W tym przykładzie zapytania są przesyłane w trybie interaktywnym:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

W przypadku dedykowanej puli SQL w poniższych przykładach pokazano, jak uruchamiać zapytania w trybie wsadowym przy użyciu opcji-Q lub przekazywać instrukcję SQL do narzędzia sqlcmd:

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-serverless-sql-pool"></a>Korzystanie z bezserwerowej puli SQL

Po nawiązaniu połączenia można wydać wszystkie obsługiwane instrukcje [języka Transact-SQL](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (T-SQL) w odniesieniu do wystąpienia.  W poniższym przykładzie zapytania są przesyłane w trybie interaktywnym:

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

W przypadku puli SQL bezserwerowej poniższe przykłady pokazują, jak uruchamiać zapytania w trybie wsadowym przy użyciu opcji-Q lub przekazywać instrukcję SQL do narzędzia sqlcmd:

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat opcji narzędzia sqlcmd, zobacz [dokumentację narzędzia sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true).
