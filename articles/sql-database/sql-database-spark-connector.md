---
title: Łącznik platformy Spark z Azure SQL Database i SQL Server
description: Dowiedz się, jak używać łącznika Spark dla Azure SQL Database i SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: b22ec475c0281a54d65921bc450b35723aa23219
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77471656"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Przyspieszenie analizy danych Big Data w czasie rzeczywistym za pomocą łącznika Spark dla Azure SQL Database i SQL Server

Łącznik platformy Spark dla Azure SQL Database i SQL Server umożliwia korzystanie z baz danych SQL, w tym Azure SQL Database i SQL Server, do działania jako dane wejściowe źródła danych lub ujścia danych wyjściowych dla zadań platformy Spark. Umożliwia ona korzystanie z danych transakcyjnych w czasie rzeczywistym w analizie danych Big Data i utrwalanie wyników dla zapytań ad hoc lub raportów. W porównaniu do wbudowanego łącznika JDBC ten łącznik umożliwia zbiorcze Wstawianie danych do baz danych SQL. Może outperform wiersz po wstawieniu wierszy z 10X, aby 20x wyższą wydajność. Łącznik platformy Spark dla Azure SQL Database i SQL Server obsługuje również uwierzytelnianie w usłudze AAD. Umożliwia ona bezpieczne nawiązywanie połączenia z usługą Azure SQL Database z Azure Databricks przy użyciu konta usługi AAD. Zapewnia podobne interfejsy za pomocą wbudowanego łącznika JDBC. Migracja istniejących zadań platformy Spark w celu korzystania z tego nowego łącznika jest łatwa.

## <a name="download"></a>Pliki do pobrania
Aby rozpocząć, Pobierz łącznik Spark do bazy danych SQL z [repozytorium Azure-SQLDB-Spark](https://github.com/Azure/azure-sqldb-spark) w witrynie GitHub.

## <a name="official-supported-versions"></a>Oficjalne obsługiwane wersje

| Składnik                            | Wersja                  |
| :----------------------------------- | :----------------------- |
| Apache Spark                         | 2.0.2 lub nowszy           |
| Scala                                | 2,10 lub nowszy            |
| Sterownik JDBC firmy Microsoft dla SQL Server | 6,2 lub nowszy             |
| Microsoft SQL Server                 | SQL Server 2008 lub nowsza wersja |
| Azure SQL Database                   | Obsługiwane                |

Łącznik platformy Spark dla Azure SQL Database i SQL Server wykorzystuje sterownik JDBC firmy Microsoft na potrzeby SQL Server przenoszenia danych między węzłami procesów roboczych Spark i bazami danych SQL:
 
Przepływu danych jest następujący:
1. Węzeł główny platformy Spark łączy się z SQL Server lub Azure SQL Database i ładuje dane z określonej tabeli lub przy użyciu określonego zapytania SQL
2. Węzeł główny platformy Spark dystrybuuje dane do węzłów procesu roboczego na potrzeby transformacji. 
3. Węzeł procesu roboczego łączy się z SQL Server lub Azure SQL Database i zapisuje dane w bazie danych. Użytkownik może wybrać użycie wstawiania wiersz po wierszu lub wstawianie zbiorcze.

Na poniższym diagramie przedstawiono przepływ danych.

   ![architektura](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Tworzenie łącznika platformy Spark do bazy danych SQL
Obecnie projekt łącznika używa Maven. Aby skompilować łącznik bez zależności, można uruchomić:

- MVN czysty pakiet
- Pobierz najnowsze wersje JAR z folderu wersja
- Uwzględnij plik JAR programu SQL DB Spark

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Łączenie platformy Spark z bazą danych SQL przy użyciu łącznika
Można nawiązać połączenie z usługą Azure SQL Database lub SQL Server z zadań platformy Spark, odczytywać lub zapisywać dane. Można również uruchomić zapytanie DML lub DDL w bazie danych SQL Azure lub w bazie danych SQL Server.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Odczytaj dane z Azure SQL Database lub SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Odczytywanie danych z Azure SQL Database lub SQL Server z określonym zapytaniem SQL
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Zapisz dane do Azure SQL Database lub SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Uruchamianie zapytania DML lub DDL w Azure SQL Database lub SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Łączenie platformy Spark z Azure SQL Database przy użyciu uwierzytelniania usługi AAD
Można nawiązać połączenie z usługą Azure SQL Database przy użyciu uwierzytelniania Azure Active Directory (AAD). Uwierzytelnianie przy użyciu usługi AAD umożliwia centralne zarządzanie tożsamościami użytkowników baz danych i alternatywą dla SQL Server uwierzytelniania.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Nawiązywanie połączenia przy użyciu trybu uwierzytelniania ActiveDirectoryPassword
#### <a name="setup-requirement"></a>Wymagania dotyczące konfiguracji
W przypadku korzystania z trybu uwierzytelniania ActiveDirectoryPassword należy pobrać pozycję [Azure-ActiveDirectory-Library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) i jej zależności oraz uwzględnić je w ścieżce kompilacji Java.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Łączenie przy użyciu tokenu dostępu
#### <a name="setup-requirement"></a>Wymagania dotyczące konfiguracji
Jeśli używasz trybu uwierzytelniania opartego na tokenach dostępu, musisz pobrać pozycję [Azure-ActiveDirectory-Library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) i jej zależności oraz uwzględnić je w ścieżce kompilacji Java.

Aby dowiedzieć się, jak uzyskać token dostępu do bazy danych Azure SQL, zobacz temat [używanie uwierzytelniania Azure Active Directory na potrzeby uwierzytelniania w programie SQL Database](sql-database-aad-authentication.md) .

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Zapisywanie danych w usłudze Azure SQL Database lub SQL Server przy użyciu wstawiania zbiorczego
Tradycyjny łącznik JDBC zapisuje dane w usłudze Azure SQL Database lub SQL Server przy użyciu wstawiania wiersz po wierszu. Łącznika Spark do usługi SQL DB służy do zapisywania danych w bazie danych SQL przy użyciu instrukcji BULK INSERT. Znacznie poprawia wydajność zapisu podczas ładowania dużych zestawów danych lub ładowania danych do tabel, w których jest używany indeks magazynu kolumn.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/** 
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Następne kroki
Jeśli jeszcze tego nie zrobiono, Pobierz łącznik Spark dla Azure SQL Database i SQL Server z [repozytorium Azure-SQLDB-Spark GitHub](https://github.com/Azure/azure-sqldb-spark) i przejrzyj dodatkowe zasoby w repozytorium:

- [Przykładowe notesy Azure Databricks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Przykładowe skrypty (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Warto również zapoznać się z [podręcznikiem Apache Spark SQL, Dataframes i zestawami danych](https://spark.apache.org/docs/latest/sql-programming-guide.html) oraz [dokumentacją Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

