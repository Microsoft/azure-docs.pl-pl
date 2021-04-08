---
title: Dostęp do Azure Cosmos DB interfejs API Cassandra z platformy Spark w ramach PRZĘDZy z usługą HDInsight
description: W tym artykule opisano sposób pracy z Azure Cosmos DB interfejs API Cassandra z platformy Spark w ramach PRZĘDZy z usługą HDInsight
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: e09ac39037024b5c360af4bb6ada26590b142924
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359598"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Dostęp do Azure Cosmos DB interfejs API Cassandra z platformy Spark w ramach PRZĘDZy z usługą HDInsight
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

W tym artykule opisano, jak uzyskać dostęp do Azure Cosmos DB interfejs API Cassandra z platformy Spark na PRZĘDZę z użyciem HDInsight-Spark z powłoki Spark. Usługa HDInsight to Hortonworks usługi Hadoop firmy Microsoft na platformie Azure, która korzysta z magazynu obiektów dla systemu plików HDFS i zawiera kilka wersji, w tym [platformy Spark](../hdinsight/spark/apache-spark-overview.md).  Chociaż zawartość tego dokumentu odwołuje się do usługi HDInsight-Spark, ma zastosowanie do wszystkich dystrybucji usługi Hadoop.  

## <a name="prerequisites"></a>Wymagania wstępne

* [Inicjowanie obsługi administracyjnej Azure Cosmos DB interfejs API Cassandra](create-cassandra-dotnet.md#create-a-database-account)

* [Zapoznaj się z podstawowymi informacjami na temat łączenia się z Azure Cosmos DB interfejs API Cassandra](cassandra-spark-generic.md)

* [Inicjowanie obsługi klastra HDInsight-Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Zapoznaj się z przykładami kodu dotyczącymi pracy z interfejs API Cassandra](cassandra-spark-generic.md#next-steps)

* [Użyj cqlsh do walidacji, jeśli wolisz](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Konfiguracja interfejs API Cassandra w Spark2** — łącznik platformy Spark dla Cassandra wymaga zainicjowania szczegółów połączenia Cassandra jako części kontekstu Spark. Po uruchomieniu notesu Jupyter, sesja i kontekst platformy Spark są już zainicjowane i nie jest zalecane zatrzymywanie i ponowne inicjowanie kontekstu platformy Spark, chyba że zostanie on ukończony z każdym zestawem konfiguracyjnym w ramach usługi HDInsight default Jupyter notesu. Obejście polega na tym, aby dodać szczegóły wystąpienia Cassandra do Ambari, Spark2 konfiguracji usługi. Jest to jednorazowe działanie na klaster, który wymaga ponownego uruchomienia usługi Spark2.
 
  1. Przejdź do Ambari, usługi Spark2 i wybierz pozycję konfiguracje

  2. Następnie przejdź do obszaru niestandardowe spark2 — domyślne i Dodaj nową właściwość z następującymi wartościami, a następnie uruchom ponownie usługę Spark2:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Dostęp do Azure Cosmos DB interfejs API Cassandra z powłoki Spark

Powłoka Spark jest używana do testowania/eksploracji.

* Uruchom powłokę Spark z wymaganymi zależnościami Maven, które są zgodne z wersją platformy Spark w klastrze.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Wykonywanie niektórych operacji DDL i DML

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* Uruchom operacje CRUD

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Dostęp do Azure Cosmos DB interfejs API Cassandra z notesów Jupyter

HDInsight-Spark udostępnia usługi notesów Zeppelin i Jupyter. Są to środowiska notesu opartego na sieci Web, które obsługują Scala i Python. Notesy doskonale nadaje się do interaktywnej analizy poznawczej i współpracy, ale nie są przeznaczone dla procesów operacyjnych/produkcyjnych.

Następujące notesy Jupyter można przekazać do klastra usługi HDInsight Spark i udostępnić próbki gotowe do pracy z Azure Cosmos DB interfejs API Cassandra. Zapoznaj się z pierwszym notesem, `1.0-ReadMe.ipynb` Aby przejrzeć konfigurację usługi platformy Spark w celu nawiązania połączenia z usługą Azure Cosmos DB interfejs API Cassandra.

Pobierz te notesy w obszarze [Azure-Cosmos-DB-Cassandra-API-Spark-Notess-Jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/main/scala/) do komputera.
  
### <a name="how-to-upload"></a>Jak przekazać:
Po uruchomieniu Jupyter przejdź do Scala. Najpierw Utwórz katalog, a następnie Przekaż Notes do katalogu. Przycisk Przekaż znajduje się na górze, po prawej stronie.  

### <a name="how-to-run"></a>Jak uruchomić:
Uruchamiaj za pomocą notesów i każdej komórki notesu sekwencyjnie.  Kliknij przycisk Run (Uruchom) w górnej części każdego notesu, aby wykonać wszystkie komórki, lub naciśnij klawisze Shift + Enter dla każdej komórki.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Dostęp do Azure Cosmos DB interfejs API Cassandra z programu Spark Scala

W przypadku zautomatyzowanych procesów w środowisku produkcyjnym programy Spark są przesyłane do klastra za pośrednictwem usługi [Spark-Submit](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>Następne kroki

* [Jak utworzyć program Spark Scala w środowisku IDE i przesłać go do klastra usługi HDInsight Spark za pomocą usługi Livy na potrzeby wykonywania](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Jak nawiązać połączenie z Azure Cosmos DB interfejs API Cassandra z programu Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/main/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Pełna Lista przykładów kodu do pracy z interfejs API Cassandra](cassandra-spark-generic.md)
