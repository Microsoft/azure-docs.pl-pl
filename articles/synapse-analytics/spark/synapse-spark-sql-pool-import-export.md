---
title: Importowanie i eksportowanie danych między pulami Spark (wersja zapoznawcza) a pulami SQL
description: Ten artykuł zawiera informacje na temat używania łącznika niestandardowego do przeniesienia danych między pulami SQL i pulami platformy Spark (wersja zapoznawcza).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: f92c05476c9e85690fdeacade5463a43d0a4af42
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424293"
---
# <a name="introduction"></a>Wprowadzenie

Łącznik Spark SQL Analytics został zaprojektowany w celu wydajnego transferu danych między pulą usługi Spark (wersja zapoznawcza) i pulami SQL w usłudze Azure Synapse. Łącznik Spark SQL Analytics działa tylko w pulach SQL, nie działa z SQL na żądanie.

## <a name="design"></a>Projektowanie

Przenoszenie danych między pulami platformy Spark i pulami SQL można wykonać przy użyciu JDBC. Jednak w przypadku dwóch systemów rozproszonych, takich jak platforma Spark i pule SQL, JDBC zachodzi wąskie gardło z transferem danych szeregowych.

Pule platformy Spark do łącznika usługi SQL Analytics to implementacja źródła danych dla Apache Spark. Używa Azure Data Lake Storage Gen 2 i Base w pulach SQL do wydajnego transferu danych między klastrem Spark i wystąpieniem usługi SQL Analytics.

![Architektura łącznika](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Uwierzytelnianie w usłudze Azure Synapse Analytics

Uwierzytelnianie między systemami odbywa się bezproblemowo w usłudze Azure Synapse Analytics. Istnieje usługa tokenów, która łączy się z Azure Active Directory, aby uzyskać tokeny zabezpieczające do użycia podczas uzyskiwania dostępu do konta magazynu lub serwera magazynu danych. Z tego powodu nie ma potrzeby tworzenia poświadczeń ani określania ich w interfejsie API łącznika, o ile uwierzytelnianie AAD jest skonfigurowane na koncie magazynu i na serwerze magazynu danych. W przeciwnym razie można określić uwierzytelnianie SQL. Więcej szczegółów znajduje się w sekcji [użycie](#usage) .

## <a name="constraints"></a>Ograniczenia

- Ten łącznik działa tylko w Scala.

## <a name="prerequisites"></a>Wymagania wstępne

- Mieć rolę **db_exporter** w puli bazy danych/SQL, do której chcesz przenieść dane do/z.

Aby utworzyć użytkowników, nawiąż połączenie z bazą danych i postępuj zgodnie z następującymi przykładami:

```Sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Aby przypisać rolę:

```Sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Sposób użycia

Nie trzeba dostarczać instrukcji importu, które są wstępnie zaimportowane do środowiska notesu.

### <a name="transferring-data-to-or-from-a-sql-pool-in-the-logical-server-dw-instance-attached-with-the-workspace"></a>Transferowanie danych do lub z puli SQL na serwerze logicznym (wystąpienie DW) dołączonym do obszaru roboczego

> [!NOTE]
> **Importy niewymagane w środowisku notesu**

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Odczytaj interfejs API

```Scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

Powyższy interfejs API będzie działał zarówno wewnętrznie (zarządzany), jak i zewnętrzne tabele w puli SQL.

#### <a name="write-api"></a>Interfejs API zapisu

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

gdzie Tabletype może być stałymi. wewnętrzne lub stałe. EXTERNAL

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

Uwierzytelnianie do magazynu i SQL Server są wykonywane

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-in-a-logical-server-outside-the-workspace"></a>W przypadku transferu danych do lub z puli SQL lub bazy danych na serwerze logicznym poza obszarem roboczym

> [!NOTE]
> Importy niewymagane w środowisku notesu

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Odczytaj interfejs API

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Interfejs API zapisu

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>Używanie uwierzytelniania SQL zamiast usługi AAD

#### <a name="read-api"></a>Odczytaj interfejs API

Obecnie łącznik nie obsługuje uwierzytelniania opartego na tokenach w puli SQL, która znajduje się poza obszarem roboczym. Musisz użyć uwierzytelniania SQL.

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Interfejs API zapisu

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-the-pyspark-connector"></a>Korzystanie z łącznika PySpark

> [!NOTE]
> Ten przykład ma na uwadze tylko środowisko notesu.

Załóżmy, że masz element Dataframe "pyspark_df", który chcesz zapisać w magazynie danych.

Tworzenie tabeli tymczasowej przy użyciu ramki Dataframe w PySpark

```Python
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Uruchamianie komórki Scala w notesie PySpark przy użyciu MAGICS

```Scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```
Podobnie w scenariuszu odczytu Odczytaj dane przy użyciu Scala i Zapisz je w tabeli tymczasowej, a następnie użyj platformy Spark SQL w PySpark, aby zbadać tabelę tymczasową w ramce Dataframe.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie puli SQL]([Create a new Apache Spark pool for an Azure Synapse Analytics workspace](../../synapse-analytics/quickstart-create-apache-spark-pool.md))
- [Utwórz nową pulę Apache Spark dla obszaru roboczego analizy usługi Azure Synapse](../../synapse-analytics/quickstart-create-apache-spark-pool.md) 