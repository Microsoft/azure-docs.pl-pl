---
title: Importowanie i eksportowanie danych między pulami bezserwerowymi Apache Spark i pulami SQL
description: Ten artykuł zawiera informacje na temat używania łącznika niestandardowego do przemieszczania danych między dedykowanymi pulami SQL i bezserwerowymi pulami Apache Spark.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: 66a804f645ee56cce62053a51e6aa4fc25b3c1a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671329"
---
# <a name="introduction"></a>Wprowadzenie

Usługa Azure Synapse Apache Spark do Synapse SQL Connector została zaprojektowana w celu wydajnego transferu danych między pulami Apache Spark bezserwerowymi i dedykowanymi pulami SQL na platformie Azure Synapse. Usługa Azure Synapse Apache Spark Synapse łącznik SQL działa tylko w przypadku dedykowanych pul SQL, ale nie działa z pulą SQL bez serwera.

> [!WARNING]
> Nazwa funkcji **sqlanalytics ()** została zmieniona na **synapsesql ()**. Funkcja sqlanalytics będzie nadal działać, ale zostanie wycofana.  Zmień wszelkie odwołania z **sqlanalytics ()** na **synapsesql ()** , aby zapobiec ewentualnemu zakłóceniu w przyszłości.

## <a name="design"></a>Projekt

Przenoszenie danych między pulami platformy Spark i pulami SQL można wykonać przy użyciu JDBC. Jednak w przypadku dwóch systemów rozproszonych, takich jak platforma Spark i pule SQL, JDBC zachodzi wąskie gardło z transferem danych szeregowych.

Pula Apache Spark platformy Azure Synapse do Synapse SQL Connector jest implementacją źródła danych dla Apache Spark. Używa Azure Data Lake Storage Gen2 i Base w dedykowanych pulach SQL do wydajnego transferu danych między klastrem Spark i Synapse dedykowanym wystąpieniem programu SQL Server.

![Architektura łącznika](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Uwierzytelnianie w usłudze Azure Synapse Analytics

Uwierzytelnianie między systemami odbywa się bezproblemowo w usłudze Azure Synapse Analytics. Usługa tokenów nawiązuje połączenie z Azure Active Directory, aby uzyskać tokeny zabezpieczające, które mają być używane podczas uzyskiwania dostępu do konta magazynu lub serwera magazynu danych.

Z tego powodu nie ma potrzeby tworzenia poświadczeń ani określania ich w interfejsie API łącznika, o ile usługa Azure AD-Auth jest skonfigurowana na koncie magazynu i serwerze magazynu danych. W przeciwnym razie można określić uwierzytelnianie SQL. Więcej szczegółów znajduje się w sekcji [użycie](#usage) .

## <a name="constraints"></a>Ograniczenia

- Ten łącznik działa tylko w Scala.
- Aby uzyskać pySpark, zobacz szczegóły w sekcji [use języka Python](#use-pyspark-with-the-connector) .
- Ten łącznik nie obsługuje zapytań do widoków SQL.

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

### <a name="transfer-data-to-or-from-a-dedicated-sql-pool-attached-within-the-workspace"></a>Transferowanie danych do lub z dedykowanej puli SQL dołączonej do obszaru roboczego

> [!NOTE]
> **Importy niewymagane w środowisku notesu**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Odczytaj interfejs API

```scala
val df = spark.read.synapsesql("<DBName>.<Schema>.<TableName>")
```

Powyższy interfejs API będzie działał zarówno wewnętrznie (zarządzany), jak i zewnętrzne tabele w puli SQL.

#### <a name="write-api"></a>Interfejs API zapisu

```scala
df.write.synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

Interfejs API zapisu tworzy tabelę w dedykowanej puli SQL, a następnie wywołuje bazę danych, aby załadować dane.  Tabela nie może istnieć w dedykowanej puli SQL lub zostanie zwrócony błąd wskazujący, że "istnieje już obiekt o nazwie..."

Wartości tabletype

- Stałe. tabela zarządzana wewnętrznie w dedykowanej puli SQL
- Stałe. zewnętrzna — tabela zewnętrzna w dedykowanej puli SQL

Tabela zarządzana przez pulę SQL

```scala
df.write.synapsesql("<DBName>.<Schema>.<TableName>", Constants.INTERNAL)
```

Tabela zewnętrzna puli SQL

Aby zapisać w dedykowanej tabeli zewnętrznej puli SQL, zewnętrzne źródło danych i zewnętrzny FORMAT pliku muszą istnieć w dedykowanej puli SQL.  Aby uzyskać więcej informacji, przeczytaj artykuł [Tworzenie zewnętrznych źródeł danych](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) i [zewnętrznych formatów plików](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) w dedykowanej puli SQL.  Poniżej znajdują się przykłady tworzenia zewnętrznego źródła danych i zewnętrznych formatów plików w dedykowanej puli SQL.

```sql
--For an external table, you need to pre-create the data source and file format in dedicated SQL pool using SQL queries:
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
    synapsesql("<DBName>.<Schema>.<TableName>", Constants.EXTERNAL)

```

### <a name="transfer-data-to-or-from-a-dedicated-sql-pool-or-database-outside-the-workspace"></a>Transferowanie danych do lub z dedykowanej puli SQL lub bazy danych poza obszarem roboczym

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
synapsesql("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Interfejs API zapisu

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-sql-auth-instead-of-azure-ad"></a>Użyj uwierzytelniania SQL zamiast usługi Azure AD

#### <a name="read-api"></a>Odczytaj interfejs API

Obecnie łącznik nie obsługuje uwierzytelniania opartego na tokenach w dedykowanej puli SQL, która znajduje się poza obszarem roboczym. Musisz użyć uwierzytelniania SQL.

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
synapsesql("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Interfejs API zapisu

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-pyspark-with-the-connector"></a>Używanie PySpark z łącznikiem

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

scala_df.write.synapsesql("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
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

- Nawiązywanie połączenia z kontem magazynu połączonym z obszarem roboczym z Eksplorator usługi Storage przy użyciu usługi Azure AD
- Wybierz konto i podaj adres URL ADLS Gen2 i domyślny system plików dla obszaru roboczego
- Po wyświetleniu konta magazynu na liście kliknij prawym przyciskiem myszy obszar roboczy listy i wybierz pozycję "Zarządzaj dostępem".
- Dodaj użytkownika do folderu/z uprawnieniami dostępu "wykonaj". Wybierz pozycję "OK"

> [!IMPORTANT]
> Upewnij się, że nie wybrano opcji "domyślne", jeśli nie chcesz.


## <a name="next-steps"></a>Następne kroki

- [Tworzenie dedykowanej puli SQL przy użyciu Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Utwórz nową pulę Apache Spark przy użyciu Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 
