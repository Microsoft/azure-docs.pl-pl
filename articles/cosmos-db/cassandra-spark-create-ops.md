---
title: Tworzenie lub wstawianie danych do Azure Cosmos DB interfejs API Cassandra z platformy Spark
description: W tym artykule szczegółowo opisano sposób wstawiania przykładowych danych do tabel interfejs API Cassandra Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 3ec44d20b763a98683d9b947c94ad6be75180113
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93092257"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Tworzenie/Wstawianie danych do Azure Cosmos DB interfejs API Cassandra z platformy Spark
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]
 
W tym artykule opisano sposób wstawiania przykładowych danych do tabeli w Azure Cosmos DB interfejs API Cassandra z platformy Spark.

## <a name="cassandra-api-configuration"></a>Konfiguracja interfejs API Cassandra

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="dataframe-api"></a>Interfejs API Dataframe

### <a name="create-a-dataframe-with-sample-data"></a>Tworzenie ramki danych z przykładowymi danymi

```scala
// Generate a dataframe containing five records
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
).toDF("book_id", "book_author", "book_name", "book_pub_year")

//Review schema
booksDF.printSchema

//Print
booksDF.show
```

> [!NOTE]
> Funkcja "Utwórz, jeśli nie istnieje" na poziomie wiersza nie jest jeszcze obsługiwana.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Nie Azure Cosmos DB interfejs API Cassandra

Podczas zapisywania danych można również ustawić ustawienia czasu wygaśnięcia i zasad spójności, jak pokazano w następującym przykładzie:

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> Czas TTL na poziomie kolumny nie jest jeszcze obsługiwany.

#### <a name="validate-in-cqlsh"></a>Weryfikuj w cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>Interfejs API rozproszonej bazy danych (RDD)

### <a name="create-a-rdd-with-sample-data"></a>Tworzenie RDD z przykładowymi danymi
```scala
//Delete records created in the previous section 
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("delete from books_ks.books where book_id in ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999');"))

//Create RDD
val booksRDD = sc.parallelize(Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
))

//Review
booksRDD.take(2).foreach(println)
```

> [!NOTE]
> Tworzenie funkcji, jeśli nie istnieje, nie jest jeszcze obsługiwana.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Nie Azure Cosmos DB interfejs API Cassandra

Podczas zapisywania danych w interfejs API Cassandra można również ustawić ustawienia czasu wygaśnięcia i zasad spójności, jak pokazano w następującym przykładzie:

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>Weryfikuj w cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>Następne kroki

Po wstawieniu danych do tabeli Azure Cosmos DB interfejs API Cassandra, aby wykonać inne operacje na danych przechowywanych w Cosmos DB interfejs API Cassandra, należy przejoć do następujących artykułów:
 
* [Operacje odczytu](cassandra-spark-read-ops.md)
* [Operacje upsert](cassandra-spark-upsert-ops.md)
* [Operacje usuwania](cassandra-spark-delete-ops.md)
* [Operacje agregacji](cassandra-spark-aggregation-ops.md)
* [Operacje kopiowania tabel](cassandra-spark-table-copy-ops.md)

