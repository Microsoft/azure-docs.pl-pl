---
title: Używanie łącznika Spark z Microsoft Azure SQL i SQL Server
description: Dowiedz się, jak używać łącznika Spark z Azure SQL Database, wystąpieniem zarządzanym usługi Azure SQL i SQL Server.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 09/02/2020
ms.openlocfilehash: 2e01e74f5086f7f1eb7e85661fbd35f452d8dae8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790206"
---
# <a name="accelerate-real-time-big-data-analytics-using-the-spark-connector"></a>Przyspieszenie analizy danych Big Data w czasie rzeczywistym za pomocą łącznika Spark
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Od 2020 tego łącznika nie jest aktywnie utrzymywana. Jednak [Apache Spark łącznik dla SQL Server i Azure SQL](/sql/connect/spark/connector) jest teraz dostępny z obsługą powiązań Python i R, łatwiejszym do użycia interfejsem do zbiorczego wstawiania danych i wieloma innymi ulepszeniami. Zdecydowanie zachęcamy do oszacowania i używania nowego łącznika zamiast tego. Informacje o starym łączniku (na stronie) są przechowywane tylko w celach archiwalnych.

Łącznik platformy Spark umożliwia korzystanie z baz danych w Azure SQL Database, wystąpieniu zarządzanym usługi Azure SQL i SQL Server do działania jako dane wejściowe źródła danych lub ujścia danych wyjściowych dla zadań platformy Spark. Umożliwia ona korzystanie z danych transakcyjnych w czasie rzeczywistym w analizie danych Big Data i utrwalanie wyników dla zapytań ad hoc lub raportów. W porównaniu do wbudowanego łącznika JDBC ten łącznik umożliwia zbiorcze Wstawianie danych do bazy danych. Może outperform Wstawianie wierszy po wierszu z 10X, aby 20x wyższą wydajność. Łącznik platformy Spark obsługuje uwierzytelnianie Azure Active Directory (Azure AD) w celu nawiązania połączenia z Azure SQL Database i wystąpieniem zarządzanym usługi Azure SQL, umożliwiając łączenie bazy danych z Azure Databricks przy użyciu konta usługi Azure AD. Zapewnia podobne interfejsy za pomocą wbudowanego łącznika JDBC. Migracja istniejących zadań platformy Spark w celu korzystania z tego nowego łącznika jest łatwa.

## <a name="download-and-build-a-spark-connector"></a>Pobieranie i kompilowanie łącznika platformy Spark

Repozytorium GitHub dla starego łącznika, który został wcześniej połączony z tą stroną, nie jest aktywnie utrzymywana. Zamiast tego zdecydowanie zachęcamy do oszacowania i używania [nowego łącznika](https://github.com/microsoft/sql-spark-connector).

### <a name="official-supported-versions"></a>Oficjalne obsługiwane wersje

| Składnik                             | Wersja                  |
| :-----------------------------------  | :----------------------- |
| Apache Spark                          | 2.0.2 lub nowszy           |
| Scala                                 | 2,10 lub nowszy            |
| Sterownik JDBC firmy Microsoft dla programu SQL Server  | 6,2 lub nowszy             |
| Microsoft SQL Server                  | SQL Server 2008 lub nowsza wersja |
| Azure SQL Database                    | Obsługiwane                |
| Wystąpienie zarządzane Azure SQL            | Obsługiwane                |

Łącznik platformy Spark używa SQL Server sterownika JDBC firmy Microsoft do przenoszenia danych między węzłami procesów roboczych Spark i bazami danych:

Przepływu danych jest następujący:

1. Węzeł główny platformy Spark łączy się z bazami danych w SQL Database lub SQL Server i ładuje dane z określonej tabeli lub przy użyciu określonego zapytania SQL.
2. Węzeł główny platformy Spark dystrybuuje dane do węzłów procesu roboczego na potrzeby transformacji.
3. Węzeł procesu roboczego nawiązuje połączenie z bazami danych, które łączą się z SQL Database i SQL Server i zapisuje dane w bazie danych. Użytkownik może wybrać użycie wstawiania wiersz po wierszu lub wstawianie zbiorcze.

Na poniższym diagramie przedstawiono przepływ danych.

   ![Diagram przedstawia opisany przepływ, z węzłem głównym łączącym się bezpośrednio z bazą danych i łącząc się z trzema węzłami procesu roboczego, które łączą się z bazą danych.](./media/spark-connector/architecture.png)

### <a name="build-the-spark-connector"></a>Kompilowanie łącznika Spark

Obecnie projekt łącznika używa Maven. Aby skompilować łącznik bez zależności, można uruchomić:

- MVN czysty pakiet
- Pobierz najnowsze wersje JAR z folderu wersja
- Uwzględnij SQL Database JAR

## <a name="connect-and-read-data-using-the-spark-connector"></a>Łączenie i odczytywanie danych przy użyciu łącznika Spark

Można połączyć się z bazami danych w SQL Database i SQL Server z zadania Spark w celu odczytu lub zapisu danych. Można również uruchomić zapytanie DML lub DDL w bazach danych w SQL Database i SQL Server.

### <a name="read-data-from-azure-sql-and-sql-server"></a>Odczytywanie danych z usług Azure SQL i SQL Server

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

### <a name="read-data-from-azure-sql-and-sql-server-with-specified-sql-query"></a>Odczytaj dane z usługi Azure SQL i SQL Server przy użyciu określonego zapytania SQL

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

### <a name="write-data-to-azure-sql-and-sql-server"></a>Zapisywanie danych w usłudze Azure SQL i SQL Server

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

### <a name="run-dml-or-ddl-query-in-azure-sql-and-sql-server"></a>Uruchamianie zapytania DML lub DDL w usłudze Azure SQL i SQL Server

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

## <a name="connect-from-spark-using-azure-ad-authentication"></a>Łączenie z platformy Spark przy użyciu uwierzytelniania usługi Azure AD

Można nawiązać połączenie z usługą Azure SQL Database i wystąpieniem zarządzanym SQL przy użyciu uwierzytelniania usługi Azure AD. Użyj uwierzytelniania usługi Azure AD, aby centralnie zarządzać tożsamościami użytkowników bazy danych i alternatywą dla SQL Server uwierzytelniania.

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

### <a name="connecting-using-an-access-token"></a>Łączenie przy użyciu tokenu dostępu

#### <a name="setup-requirement"></a>Wymagania dotyczące konfiguracji

Jeśli używasz trybu uwierzytelniania opartego na tokenach dostępu, musisz pobrać pozycję [Azure-ActiveDirectory-Library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) i jej zależności oraz uwzględnić je w ścieżce kompilacji Java.

Aby dowiedzieć się, jak uzyskać token dostępu do bazy danych w Azure SQL Database lub wystąpieniu zarządzanym Azure SQL, zobacz temat [używanie uwierzytelniania Azure Active Directory](authentication-aad-overview.md) .

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

## <a name="write-data-using-bulk-insert"></a>Zapisz dane przy użyciu wstawiania zbiorczego

Tradycyjny łącznik JDBC zapisuje dane w bazie danych przy użyciu wstawiania wiersz po wierszu. Łącznika Spark służy do zapisywania danych w usłudze Azure SQL i SQL Server przy użyciu instrukcji BULK INSERT. Znacznie poprawia wydajność zapisu podczas ładowania dużych zestawów danych lub ładowania danych do tabel, w których jest używany indeks magazynu kolumn.

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

Jeśli jeszcze tego nie zrobiono, Pobierz łącznik platformy Spark z [repozytorium Azure-SQLDB-Spark GitHub](https://github.com/Azure/azure-sqldb-spark) i przejrzyj dodatkowe zasoby w repozytorium:

- [Przykładowe notesy Azure Databricks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Przykładowe skrypty (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Warto również zapoznać się z [podręcznikiem Apache Spark SQL, Dataframes i zestawami danych](https://spark.apache.org/docs/latest/sql-programming-guide.html) oraz [dokumentacją Azure Databricks](/azure/azure-databricks/).