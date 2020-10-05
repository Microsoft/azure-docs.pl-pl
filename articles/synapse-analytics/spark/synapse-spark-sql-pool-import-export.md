---
title: Importowanie i eksportowanie danych między pulami Spark (wersja zapoznawcza) a pulami SQL
description: Ten artykuł zawiera informacje na temat używania łącznika niestandardowego do przeniesienia danych między pulami SQL i pulami platformy Spark (wersja zapoznawcza).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: 11f73d2becb40b800c49afe0cd58f56953f8d42d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91259922"
---
# <a name="introduction"></a>Wprowadzenie

Usługa Azure Synapse Apache Spark do Synapse SQL Connector została zaprojektowana w celu wydajnego transferu danych między pulami usługi Spark (wersja zapoznawcza) a pulami SQL na platformie Azure Synapse. Usługa Azure Synapse Apache Spark Synapse łącznik SQL działa tylko w pulach SQL, nie działa z SQL na żądanie.

## <a name="design"></a>Projekt

Przenoszenie danych między pulami platformy Spark i pulami SQL można wykonać przy użyciu JDBC. Jednak w przypadku dwóch systemów rozproszonych, takich jak platforma Spark i pule SQL, JDBC zachodzi wąskie gardło z transferem danych szeregowych.

Pula Apache Spark platformy Azure Synapse do Synapse SQL Connector jest implementacją źródła danych dla Apache Spark. Używa Azure Data Lake Storage Gen2 i Base w pulach SQL do wydajnego transferu danych między klastrem Spark a wystąpieniem programu SQL Synapse.

![Architektura łącznika](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Uwierzytelnianie w usłudze Azure Synapse Analytics

Uwierzytelnianie między systemami odbywa się bezproblemowo w usłudze Azure Synapse Analytics. Usługa tokenów nawiązuje połączenie z Azure Active Directory, aby uzyskać tokeny zabezpieczające, które mają być używane podczas uzyskiwania dostępu do konta magazynu lub serwera magazynu danych.

Z tego powodu nie ma potrzeby tworzenia poświadczeń ani określania ich w interfejsie API łącznika, o ile uwierzytelnianie AAD jest skonfigurowane na koncie magazynu i na serwerze magazynu danych. W przeciwnym razie można określić uwierzytelnianie SQL. Więcej szczegółów znajduje się w sekcji [użycie](#usage) .

## <a name="constraints"></a>Ograniczenia

- Ten łącznik działa tylko w Scala.

## <a name="prerequisites"></a>Wymagania wstępne

- Musi być członkiem roli **db_exporter** w puli bazy danych/SQL, do której chcesz przenieść dane do/z.
- Musi być członkiem roli współautor danych obiektów blob magazynu na domyślnym koncie magazynu.

Aby utworzyć użytkowników, nawiąż połączenie z bazą danych puli SQL i postępuj zgodnie z następującymi przykładami:

```sql
--SQL User
CREATE USER Mary FROM LOGIN Mary;

--Azure Active Directory User
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Aby przypisać rolę:

```sql
--SQL User
EXEC sp_addrolemember 'db_exporter', 'Mary';

--Azure Active Directory User
EXEC sp_addrolemember 'db_exporter',[mike@contoso.com]
```

## <a name="usage"></a>Użycie

Instrukcje import nie są wymagane, ale są wstępnie zaimportowane do środowiska notesu.

### <a name="transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace"></a>Transferowanie danych do lub z puli SQL dołączonej do obszaru roboczego

> [!NOTE]
> **Importy niewymagane w środowisku notesu**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Odczytaj interfejs API

```scala
val df = spark.read.sqlanalytics("<DBName>.<Schema>.<TableName>")
```

Powyższy interfejs API będzie działał zarówno wewnętrznie (zarządzany), jak i zewnętrzne tabele w puli SQL.

#### <a name="write-api"></a>Interfejs API zapisu

```scala
df.write.sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

Interfejs API zapisu tworzy tabelę w puli SQL, a następnie wywołuje bazę do ładowania danych.  Tabela nie może istnieć w puli SQL lub zostanie zwrócony błąd z informacją o tym, że "istnieje już obiekt o nazwie..."

Wartości tabletype

- Stałe. tabela zarządzana wewnętrznie w puli SQL
- Stałe. zewnętrzna — tabela zewnętrzna w puli SQL

Tabela zarządzana przez pulę SQL

```scala
df.write.sqlanalytics("<DBName>.<Schema>.<TableName>", Constants.INTERNAL)
```

Tabela zewnętrzna puli SQL

Aby można było zapisywać w tabeli zewnętrznej puli SQL, zewnętrzne źródło danych i zewnętrzny FORMAT pliku muszą istnieć w puli SQL.  Aby uzyskać więcej informacji, przeczytaj artykuł [Tworzenie zewnętrznych źródeł danych](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) i [zewnętrznych formatów plików](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) w puli SQL.  Poniżej znajdują się przykłady tworzenia zewnętrznego źródła danych i zewnętrznych formatów plików w puli SQL.

```sql
--For an external table, you need to pre-create the data source and file format in SQL pool using SQL queries:
CREATE EXTERNAL DATA SOURCE <DataSourceName>
WITH
  ( LOCATION = 'abfss://...' ,
    TYPE = HADOOP
  ) ;

CREATE EXTERNAL FILE FORMAT <FileFormatName>
WITH (  
    FORMAT_TYPE = PARQUET,  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'  
);
```

Obiekt poświadczeń zewnętrznych nie jest wymagany w przypadku używania Azure Active Directory uwierzytelniania przekazywanego do konta magazynu.  Upewnij się, że jesteś członkiem roli "Współautor danych obiektów blob magazynu" na koncie magazynu.

```scala

df.write.
    option(Constants.DATA_SOURCE, <DataSourceName>).
    option(Constants.FILE_FORMAT, <FileFormatName>).
    sqlanalytics("<DBName>.<Schema>.<TableName>", Constants.EXTERNAL)

```

### <a name="if-you-transfer-data-to-or-from-a-sql-pool-or-database-outside-the-workspace"></a>W przypadku transferu danych do lub z puli lub bazy danych SQL lub spoza obszaru roboczego

> [!NOTE]
> Importy niewymagane w środowisku notesu

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Odczytaj interfejs API

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Interfejs API zapisu

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-sql-auth-instead-of-aad"></a>Użyj uwierzytelniania SQL zamiast usługi AAD

#### <a name="read-api"></a>Odczytaj interfejs API

Obecnie łącznik nie obsługuje uwierzytelniania opartego na tokenach w puli SQL, która znajduje się poza obszarem roboczym. Musisz użyć uwierzytelniania SQL.

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Interfejs API zapisu

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
sqlanalytics("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-the-pyspark-connector"></a>Korzystanie z łącznika PySpark

> [!NOTE]
> Ten przykład ma na uwadze tylko środowisko notesu.

Załóżmy, że masz element Dataframe "pyspark_df", który chcesz zapisać w magazynie danych.

Tworzenie tabeli tymczasowej przy użyciu ramki Dataframe w PySpark:

```py
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Uruchom komórkę Scala w notesie PySpark przy użyciu MAGICS:

```scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

scala_df.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```

Podobnie w scenariuszu odczytu Odczytaj dane przy użyciu Scala i Zapisz je w tabeli tymczasowej, a następnie użyj platformy Spark SQL w PySpark, aby zbadać tabelę tymczasową w ramce Dataframe.

## <a name="allow-other-users-to-use-the-azure-synapse-apache-spark-to-synapse-sql-connector-in-your-workspace"></a>Zezwalaj innym użytkownikom na korzystanie z usługi Azure Synapse Apache Spark do Synapse łącznika SQL w obszarze roboczym

Musisz być właścicielem danych obiektów blob magazynu na koncie magazynu ADLS Gen2 połączonym z obszarem roboczym, aby zmienić brakujące uprawnienia dla innych. Upewnij się, że użytkownik ma dostęp do obszaru roboczego i uprawnień do uruchamiania notesów.

### <a name="option-1"></a>Opcja 1

- Ustaw użytkownika jako współautora lub właściciela danych obiektu blob magazynu

### <a name="option-2"></a>Opcja 2

- Określ następujące listy ACL w strukturze folderów:

| Folder | / | synapse | obszary robocze  | \<workspacename> | sparkpools | \<sparkpoolname>  | sparkpoolinstances  |
|--|--|--|--|--|--|--|--|
| Uprawnienia dostępu | --X | --X | --X | --X | --X | --X | -WX |
| Uprawnienia domyślne | ---| ---| ---| ---| ---| ---| ---|

- Powinien być możliwy dostęp do listy ACL wszystkich folderów z "Synapse" i w dół z Azure Portal. Aby uzyskać dostęp do listy ACL w folderze głównym "/", postępuj zgodnie z poniższymi instrukcjami.

- Łączenie się z kontem magazynu połączonym z obszarem roboczym z Eksplorator usługi Storage przy użyciu usługi AAD
- Wybierz konto i podaj adres URL ADLS Gen2 i domyślny system plików dla obszaru roboczego
- Po wyświetleniu konta magazynu na liście kliknij prawym przyciskiem myszy obszar roboczy listy i wybierz pozycję "Zarządzaj dostępem".
- Dodaj użytkownika do folderu/z uprawnieniami dostępu "wykonaj". Wybierz pozycję "OK"

> [!IMPORTANT]
> Upewnij się, że nie wybrano opcji "domyślne", jeśli nie chcesz.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie puli SQL przy użyciu Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Utwórz nową pulę Apache Spark przy użyciu Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 
