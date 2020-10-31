---
title: Odczytywanie danych tabeli interfejs API Cassandra przy użyciu platformy Spark
titleSufix: Azure Cosmos DB
description: W tym artykule opisano sposób odczytywania danych z tabel interfejs API Cassandra w Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 06/02/2020
ms.custom: seodec18
ms.openlocfilehash: ceede96cbf3be12a6129e27d34e318e4c4163458
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073500"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Odczytywanie danych z Azure Cosmos DB interfejs API Cassandra tabelach przy użyciu platformy Spark
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

 W tym artykule opisano sposób odczytywania danych przechowywanych w Azure Cosmos DB interfejs API Cassandra z platformy Spark.

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

### <a name="read-table-using-sessionreadformat-command"></a>Odczytaj tabelę przy użyciu polecenia Session. Read. format

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>Odczytaj tabelę przy użyciu platformy Spark. Read. cassandraFormat 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>Odczytaj określone kolumny w tabeli

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>Stosowanie filtrów

Można wypchnąć predykaty do bazy danych, aby umożliwić lepsze zoptymalizowane zapytania Spark. Predykat to warunek zapytania, który zwraca wartość true lub false, zazwyczaj znajduje się w klauzuli WHERE. Predykat wypchnij w dół filtruje dane w kwerendzie bazy danych, zmniejszając liczbę wpisów pobieranych z bazy danych i zwiększając wydajność zapytań. Domyślnie interfejs API usługi Spark DataSet automatycznie wypycha prawidłowe klauzule WHERE do bazy danych. 

```scala
val df = spark.read.cassandraFormat("books", "books_ks").load
df.explain
val dfWithPushdown = df.filter(df("book_pub_year") > 1891)
dfWithPushdown.explain

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

Sekcja PushedFilters planu fizycznego zawiera filtr wypychania GreaterThan. 

:::image type="content" source="./media/cassandra-spark-read-ops/pushdown-predicates.png" alt-text="Partition":::

## <a name="rdd-api"></a>INTERFEJS API RDD

### <a name="read-table"></a>Odczytaj tabelę
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>Odczytaj określone kolumny w tabeli

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>Widoki SQL 

### <a name="create-a-temporary-view-from-a-dataframe"></a>Tworzenie widoku tymczasowego na podstawie ramki danych

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>Uruchom zapytania względem widoku

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>Następne kroki

Poniżej znajdują się dodatkowe artykuły dotyczące pracy z Azure Cosmos DB interfejs API Cassandra z platformy Spark:
 
 * [Operacje upsert](cassandra-spark-upsert-ops.md)
 * [Operacje usuwania](cassandra-spark-delete-ops.md)
 * [Operacje agregacji](cassandra-spark-aggregation-ops.md)
 * [Operacje kopiowania tabel](cassandra-spark-table-copy-ops.md)

