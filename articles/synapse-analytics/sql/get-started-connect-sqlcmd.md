---
title: Nawiązywanie połączenia z Synapse SQL za pomocą narzędzia sqlcmd
description: Użyj narzędzia wiersza polecenia sqlcmd, aby nawiązać połączenie z bezserową pulą SQL i dedykowaną pulą SQL i odpytywać pulę bez serwera.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 3abdd44f0684282e92da147dff996ff54f0ef23f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565478"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>Nawiązywanie połączenia z Synapse SQL za pomocą narzędzia sqlcmd

> [!div class="op_single_selector"]
> * [Azure Data Studio)](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Narzędzie wiersza polecenia [sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true) umożliwia nawiązywanie połączeń z bez użycia serwera i dedykowaną pulą SQL w ramach Synapse SQL.  

## <a name="1-connect"></a>1. Połącz
Aby rozpocząć pracę z [programem sqlcmd,](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true)otwórz wiersz polecenia i wprowadź **ciąg sqlcmd,** a następnie podaj Synapse SQL bazy danych. Wymagane parametry połączenia to:

* **Serwer (-S):** serwer w postaci `<`nazwa_serwera`>`.database.windows.net
* **Baza danych (-d):** Nazwa bazy danych
* **Włącz identyfikatory w cudzysłowy (-I):** Identyfikatory w cudzysłowy muszą być włączone, aby nawiązać połączenie z Synapse SQL wystąpieniem

Aby użyć SQL Server uwierzytelniania, należy dodać parametry nazwy użytkownika i hasła:

* **Użytkownik (-U):** użytkownik serwera w formie `<`Użytkownik`>`
* **Hasło (-P):** Hasło skojarzone z użytkownikiem

Ciąg połączenia może wyglądać podobnie do poniższego przykładu:

**Bez serwera pula SQL**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**Dedykowana pula SQL**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Aby użyć zintegrowanego uwierzytelniania usługi Azure Active Directory, należy dodać parametry usługi Azure Active Directory:

* **Uwierzytelnianie usługi Azure Active Directory (-G):** używaj usługi Azure Active Directory do uwierzytelniania

Ciąg połączenia może wyglądać podobnie do następujących przykładów:

**Bez serwera pula SQL**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**Dedykowana pula SQL**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Musisz [włączyć uwierzytelnianie usługi Azure Active Directory](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), aby uwierzytelniać przy użyciu usługi Active Directory.

## <a name="2-query"></a>2. Zapytanie

### <a name="use-dedicated-sql-pool"></a>Korzystanie z dedykowanej puli SQL

Po nawiązaniu połączenia możesz wydać dowolne obsługiwane instrukcje [języka Transact-SQL](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (T-SQL) względem wystąpienia . W tym przykładzie zapytania są przesyłane w trybie interaktywnym:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

W przypadku dedykowanej puli SQL w poniższych przykładach podano sposób uruchamiania zapytań w trybie wsadowym przy użyciu opcji -Q lub przekierowania kodu SQL do narzędzia sqlcmd:

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-serverless-sql-pool"></a>Korzystanie z bezserwerowej puli SQL

Po nałączeniu można wydać dowolne obsługiwane instrukcje [języka Transact-SQL](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (T-SQL) względem wystąpienia.  W poniższym przykładzie zapytania są przesyłane w trybie interaktywnym:

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

W przypadku bez serwera puli SQL w przykładach poniżej podano sposób uruchamiania zapytań w trybie wsadowym przy użyciu opcji -Q lub przekierowania kodu SQL do narzędzia sqlcmd:

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat opcji narzędzia sqlcmd, zobacz [dokumentację narzędzia sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true).
