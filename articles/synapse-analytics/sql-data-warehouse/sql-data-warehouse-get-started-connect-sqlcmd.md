---
title: Nawiązywanie połączenia przy użyciu narzędzia sqlcmd
description: Użyj narzędzia wiersza polecenia sqlcmd, aby nawiązać połączenie z pulą SQL Synapse i wysłać do niej zapytanie.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3d1d8d3ce3afece5a979aadc27cd82dc7ddaf0d5
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676236"
---
# <a name="connect-to-sql-pool-in-azure-synapse-analytics-with-sqlcmd"></a>Nawiązywanie połączenia z pulą SQL w usłudze Azure Synapse Analytics przy użyciu narzędzia sqlcmd

> [!div class="op_single_selector"]
>
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Użyj narzędzia wiersza polecenia [sqlcmd] [sqlcmd], aby nawiązać połączenie z pulą SQL i zbadać ją.  

## <a name="1-connect"></a>1. Połącz

Aby rozpocząć pracę z [sqlcmd] [sqlcmd], Otwórz wiersz polecenia i wprowadź polecenie **sqlcmd** , a następnie parametry połączenia dla puli SQL. Wymagane parametry połączenia to:

* **Serwer (-S):** serwer w postaci `<`nazwa_serwera`>`.database.windows.net
* **Baza danych (-d):** Nazwa puli SQL.
* **Włącz identyfikatory ujęte w cudzysłów (-I):** Identyfikatory w cudzysłowie muszą być włączone, aby można było połączyć się z wystąpieniem puli SQL.

Aby użyć uwierzytelniania programu SQL Server, należy dodać parametry nazwy użytkownika/hasła:

* **Użytkownik (-U):** użytkownik serwera w formie `<`Użytkownik`>`
* **Hasło (-P):** hasło skojarzone z użytkownikiem.

Na przykład parametry połączenia mogą wyglądać następująco:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Aby użyć zintegrowanego uwierzytelniania usługi Azure Active Directory, należy dodać parametry usługi Azure Active Directory:

* **Uwierzytelnianie usługi Azure Active Directory (-G):** używaj usługi Azure Active Directory do uwierzytelniania

Na przykład parametry połączenia mogą wyglądać następująco:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Musisz [włączyć uwierzytelnianie usługi Azure Active Directory](sql-data-warehouse-authentication.md), aby uwierzytelniać przy użyciu usługi Active Directory.

## <a name="2-query"></a>2. zapytanie

Po nawiązaniu połączenia można wydawać dowolne obsługiwane instrukcje języka Transact-SQL dotyczące wystąpienia.  W tym przykładzie zapytania są przesyłane w trybie interaktywnym.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

W następnych przykładach przedstawiono, jak można uruchamiać zapytania w trybie wsadowym przy użyciu opcji -Q lub przesyłania potokiem instrukcji języka SQL do narzędzia sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o opcjach dostępnych w programie sqlcmd, zobacz [dokumentację narzędzia sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).