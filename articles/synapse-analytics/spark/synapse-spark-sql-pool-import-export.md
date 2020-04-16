---
title: Importowanie i eksportowanie danych między pulami platformy Spark (wersja zapoznawcza) i pulami SQL
description: Ten artykuł zawiera informacje na temat używania łącznika niestandardowego do przenoszenia danych między pulami SQL i pulami platformy Spark (wersja zapoznawcza).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: f92c05476c9e85690fdeacade5463a43d0a4af42
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424293"
---
# <a name="introduction"></a>Wprowadzenie

Łącznik analizy usługi Spark SQL Analytics został zaprojektowany do efektywnego przesyłania danych między pulami platformy Spark (wersja zapoznawcza) i pulami SQL w usłudze Azure Synapse. Łącznik analizy usługi Spark SQL Analytics działa tylko w pulach SQL, nie działa z sql na żądanie.

## <a name="design"></a>Projekt

Przesyłanie danych między pulami platformy Spark i pulami SQL można wykonać za pomocą JDBC. Jednak biorąc pod uwagę dwa systemy rozproszone, takie jak platformy Spark i pule SQL, JDBC wydaje się być wąskim gardłem z transferem danych szeregowych.

Spark puli sql analytics łącznika jest implementacją źródła danych dla Apache Spark. Używa usługi Azure Data Lake Storage Gen 2 i Polybase w pulach SQL do efektywnego przesyłania danych między klastrem platformy Spark a wystąpieniem usługi SQL Analytics.

![Architektura łącznika](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Uwierzytelnianie w usłudze Azure Synapse Analytics

Uwierzytelnianie między systemami jest bezproblemowe w usłudze Azure Synapse Analytics. Istnieje usługa tokenu, która łączy się z usługą Azure Active Directory w celu uzyskania tokenów zabezpieczających do użycia podczas uzyskiwania dostępu do konta magazynu lub serwera magazynu danych. Z tego powodu nie ma potrzeby tworzenia poświadczeń ani określania ich w interfejsie API łącznika, o ile usługa AAD-Auth jest skonfigurowana na koncie magazynu i serwerze magazynu danych. Jeśli nie, można określić Auth SQL. Więcej informacji można znaleźć w sekcji [Użycie.](#usage)

## <a name="constraints"></a>Ograniczenia

- To złącze działa tylko w Scali.

## <a name="prerequisites"></a>Wymagania wstępne

- Mieć **db_exporter** rolę w puli bazy danych/SQL, które chcesz przenieść dane do/z.

Aby utworzyć użytkowników, połącz się z bazą danych i postępuj zgodnie z poniższymi przykładami:

```Sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Aby przypisać rolę:

```Sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Sposób użycia

Instrukcje importu nie muszą być dostarczane, są wstępnie importowane dla środowiska notesu.

### <a name="transferring-data-to-or-from-a-sql-pool-in-the-logical-server-dw-instance-attached-with-the-workspace"></a>Przesyłanie danych do lub z puli SQL w wystąpieniu serwera logicznego (DW) dołączonej do obszaru roboczego

> [!NOTE]
> **Importowanie nie jest potrzebne w notebooku**

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Odczyt interfejsu API

```Scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

Powyższy interfejs API będzie działać zarówno dla wewnętrznych (zarządzanych), jak i tabel zewnętrznych w puli SQL.

#### <a name="write-api"></a>Interfejs API zapisu

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

gdzie TableType może być Constants.INTERNAL lub Constants.EXTERNAL

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

Uwierzytelnianie w magazynie i programie SQL Server jest wykonywane

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-in-a-logical-server-outside-the-workspace"></a>Jeśli przesyłasz dane do lub z puli lub bazy danych SQL na serwerze logicznym poza obszarem roboczym

> [!NOTE]
> Importowanie nie jest potrzebne w notebooku

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Odczyt interfejsu API

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

### <a name="using-sql-auth-instead-of-aad"></a>Korzystanie z Auth SQL zamiast AAD

#### <a name="read-api"></a>Odczyt interfejsu API

Obecnie łącznik nie obsługuje urojenia opartej na tokenach do puli SQL, która znajduje się poza obszarem roboczym. Musisz użyć SQL Auth.

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

### <a name="using-the-pyspark-connector"></a>Korzystanie ze złącza PySpark

> [!NOTE]
> Ten przykład jest podany tylko doświadczenie notesu pamiętać.

Załóżmy, że masz dataframe "pyspark_df", który chcesz zapisać w DW.

Tworzenie tabeli tymczasowej przy użyciu ramki danych w pyspark

```Python
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Uruchamianie komórki Scala w notebooku PySpark przy użyciu magii

```Scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```
Podobnie w scenariuszu odczytu odczytu odczytu odczytu odczytu i zapisać je w tabeli tymczasowej i używać programu Spark SQL w pyspark do kwerendy tabeli tymczasowej do dataframe.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie puli SQL]([Create a new Apache Spark pool for an Azure Synapse Analytics workspace](../../synapse-analytics/quickstart-create-apache-spark-pool.md))
- [Tworzenie nowej puli platformy Spark apache dla obszaru roboczego usługi Azure Synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool.md) 