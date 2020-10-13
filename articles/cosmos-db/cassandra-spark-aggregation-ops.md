---
title: Operacje agregacji w tabelach interfejsu API Cassandra usługi Azure Cosmos DB z platformy Spark
description: W tym artykule opisano podstawowe operacje agregacji w odniesieniu do tabel Azure Cosmos DB interfejs API Cassandra z platformy Spark
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 5db5cc515e2fbecfaeb58b9e1c02dd0bc6eda375
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86503357"
---
# <a name="aggregate-operations-on-azure-cosmos-db-cassandra-api-tables-from-spark"></a>Operacje agregacji w tabelach interfejsu API Cassandra usługi Azure Cosmos DB z platformy Spark 

W tym artykule opisano podstawowe operacje agregacji w odniesieniu do tabel interfejsu API Cassandra usługi Azure Cosmos DB z platformy Spark. 

> [!NOTE]
> Filtrowanie po stronie serwera i agregacja po stronie serwera nie są obecnie obsługiwane w interfejs API Cassandra Azure Cosmos DB.

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
## <a name="sample-data-generator"></a>Generator przykładowych danych

```scala
// Generate a simple dataset containing five values
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

## <a name="count-operation"></a>Liczba operacji


### <a name="rdd-api"></a>INTERFEJS API RDD

```scala
sc.cassandraTable("books_ks", "books").count
```

**Rozdzielczości**
```bash
res48: Long = 5
```

### <a name="dataframe-api"></a>Interfejs API Dataframe

Liczba elementów dataframes nie jest obecnie obsługiwana.  W poniższym przykładzie pokazano, jak wykonać liczbę ramek Dataframe po utrwaleniu ramki danych do pamięci jako obejście.

Wybierz [opcję magazynu]( https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#which-storage-level-to-choose) z następujących dostępnych opcji, aby uniknąć problemów z brakiem pamięci:

* MEMORY_ONLY: jest to opcja magazynu domyślnego. Przechowuje RDD jako deserializowane obiekty języka Java w JVM. Jeśli RDD nie mieszczą się w pamięci, niektóre partycje nie będą przechowywane w pamięci podręcznej i są ponownie obliczane na bieżąco za każdym razem, gdy są one zbędne.

* MEMORY_AND_DISK: przechowuje RDD jako deserializowane obiekty języka Java w JVM. Jeśli RDD nie mieszczą się w pamięci, należy przechowywać partycje, które nie mieszczą się na dysku, i w razie potrzeby należy je odczytać z lokalizacji, w której są przechowywane.

* MEMORY_ONLY_SER (Java/Scala): przechowuje RDD jako serializowane obiekty języka Java — jednobajtowa tablica na partycję. Ta opcja jest wydajnym miejscem w porównaniu z odszeregowanymi obiektami, szczególnie w przypadku korzystania z szybkiego serializatora, ale większa intensywność procesora do odczytu.

* MEMORY_AND_DISK_SER (Java/Scala): Ta opcja magazynu jest taka sama jak MEMORY_ONLY_SER, jedyną różnicą jest to, że rozłożenie partycji, które nie mieszczą się w pamięci dyskowej, zamiast ich przetwarzania w razie konieczności.

* DISK_ONLY: przechowuje partycje RDD tylko na dysku.

* MEMORY_ONLY_2, MEMORY_AND_DISK_2...: takie same jak powyżej poziomów, ale replikuje każdą partycję w dwóch węzłach klastra.

* OFF_HEAP (wersja eksperymentalna): podobna do MEMORY_ONLY_SER, ale przechowuje dane w pamięci poza stertą i wymaga wcześniejszego włączenia pamięci poza stertą. 

```scala
//Workaround
import org.apache.spark.storage.StorageLevel

//Read from source
val readBooksDF = spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()

//Explain plan
readBooksDF.explain

//Materialize the dataframe
readBooksDF.persist(StorageLevel.MEMORY_ONLY)

//Subsequent execution against this DF hits the cache 
readBooksDF.count

//Persist as temporary view
readBooksDF.createOrReplaceTempView("books_vw")
```

### <a name="sql"></a>SQL

```sql
select * from books_vw;
select count(*) from books_vw where book_pub_year > 1900;
select count(book_id) from books_vw;
select book_author, count(*) as count from books_vw group by book_author;
select count(*) from books_vw;
```

## <a name="average-operation"></a>Operacja średnia

### <a name="rdd-api"></a>INTERFEJS API RDD

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Double) => c).mean
```

**Rozdzielczości**
```
res24: Double = 16.016000175476073
```

### <a name="dataframe-api"></a>Interfejs API Dataframe

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(avg("book_price"))
  .show
```

**Rozdzielczości**
```
+------------------+
|   avg(book_price)|
+------------------+
|16.016000175476073|
+------------------+
```

### <a name="sql"></a>SQL

```sql
select avg(book_price) from books_vw;
```
**Rozdzielczości**
```
16.016000175476073
```

## <a name="min-operation"></a>Minimalna operacja

### <a name="rdd-api"></a>INTERFEJS API RDD

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).min
```

**Rozdzielczości**
```
res31: Float = 11.33
```

### <a name="dataframe-api"></a>Interfejs API Dataframe

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_id","book_price")
  .agg(min("book_price"))
  .show
```

**Rozdzielczości**
```
+---------------+
|min(book_price)|
+---------------+
|          11.33|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select min(book_price) from books_vw;
```

**Rozdzielczości**
```
11.33
```

## <a name="max-operation"></a>Maksymalna operacja

### <a name="rdd-api"></a>INTERFEJS API RDD

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).max
```

### <a name="dataframe-api"></a>Interfejs API Dataframe

```scala 
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(max("book_price"))
  .show
```

**Rozdzielczości**
```
+---------------+
|max(book_price)|
+---------------+
|          22.45|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select max(book_price) from books_vw;
```
**Rozdzielczości**
```
22.45
```

## <a name="sum-operation"></a>Suma operacji

### <a name="rdd-api"></a>INTERFEJS API RDD

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).sum
```

**Rozdzielczości**
```
res46: Double = 80.08000087738037
```

### <a name="dataframe-api"></a>Interfejs API Dataframe

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(sum("book_price"))
  .show
```
**Rozdzielczości**
```
+-----------------+
|  sum(book_price)|
+-----------------+
|80.08000087738037|
+-----------------+
```

### <a name="sql"></a>SQL

```sql
select sum(book_price) from books_vw;
```

**Rozdzielczości**
```
80.08000087738037
```

## <a name="top-or-comparable-operation"></a>Operacja Top lub porównywalna

### <a name="rdd-api"></a>INTERFEJS API RDD

```scala
val readCalcTopRDD = sc.cassandraTable("books_ks", "books").select("book_name","book_price").sortBy(_.getFloat(1), false)
readCalcTopRDD.zipWithIndex.filter(_._2 < 3).collect.foreach(println)
//delivers the first top n items without collecting the rdd to the driver.
```
**Rozdzielczości**
```
(CassandraRow{book_name: A sign of four, book_price: 22.45},0)
(CassandraRow{book_name: The adventures of Sherlock Holmes, book_price: 19.83},1)
(CassandraRow{book_name: The memoirs of Sherlock Holmes, book_price: 14.22},2)
readCalcTopRDD: org.apache.spark.rdd.RDD[com.datastax.spark.connector.CassandraRow] = MapPartitionsRDD[430] at sortBy at command-2371828989676374:1
```
### <a name="dataframe-api"></a>Interfejs API Dataframe

```scala
import org.apache.spark.sql.functions._

val readBooksDF = spark.read.format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_price")
  .orderBy(desc("book_price"))
  .limit(3)

//Explain plan
readBooksDF.explain

//Top
readBooksDF.show
```

**Rozdzielczości**
```
== Physical Plan ==
TakeOrderedAndProject(limit=3, orderBy=[book_price#1840 DESC NULLS LAST], output=[book_name#1839,book_price#1840])
+- *(1) Scan org.apache.spark.sql.cassandra.CassandraSourceRelation@29cd5f58 [book_name#1839,book_price#1840] PushedFilters: [], ReadSchema: struct<book_name:string,book_price:float>
+--------------------+----------+
|           book_name|book_price|
+--------------------+----------+
|      A sign of four|     22.45|
|The adventures of...|     19.83|
|The memoirs of Sh...|     14.22|
+--------------------+----------+

import org.apache.spark.sql.functions._
readBooksDF: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [book_name: string, book_price: float]
```

### <a name="sql"></a>SQL

```sql
select book_name,book_price from books_vw order by book_price desc limit 3;
```

## <a name="next-steps"></a>Następne kroki

Aby wykonać operacje kopiowania tabeli, zobacz:

* [Operacje kopiowania tabel](cassandra-spark-table-copy-ops.md)
