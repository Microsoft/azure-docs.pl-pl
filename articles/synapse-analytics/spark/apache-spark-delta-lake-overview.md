---
title: Omówienie sposobu korzystania z programu Linux Foundation w Apache Spark usługi Azure Synapse Analytics
description: Dowiedz się, jak używać usługi Delta Lake w Apache Spark for Azure Synapse Analytics, aby tworzyć tabele z właściwościami KWAŚNymi i korzystać z nich.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: euang
ms.topic: overview
ms.subservice: spark
ms.date: 07/28/2020
ms.author: euang
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 655daeb0149228d78d5288b0e5d0d705a5743d28
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89008645"
---
# <a name="linux-foundation-delta-lake-overview"></a>Przegląd zmian w systemie Linux Foundation

Ten artykuł został dostosowany do większej przejrzystości w [tym miejscu](https://docs.delta.io/latest/quick-start.html). Ten artykuł pomaga szybko zapoznać się z głównymi funkcjami [różnicowych](https://delta.io)danych. Artykuł zawiera fragmenty kodu, które pokazują, jak czytać i zapisywać w tabelach różnicowych Lake z zapytań interaktywnych, wsadowych i przesyłania strumieniowego. Fragmenty kodu są również dostępne w zestawie notesów [PySpark tutaj](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Python.ipynb), [Scala tutaj](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Scala/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Scala.ipynb)i [C#](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Spark.NET%20C%23/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20CSharp.ipynb)

Oto co należy zrobić:

* Tworzenie tabeli
* Odczyt danych
* Aktualizowanie danych tabeli
* Zastąp dane tabeli
* Aktualizacja warunkowa bez zastępowania
* Odczytuj starsze wersje danych przy użyciu czasu podróży
* Napisz strumień danych do tabeli
* Odczytywanie strumienia zmian z tabeli
* Obsługa SQL

## <a name="configuration"></a>Konfigurowanie

Upewnij się, że zmodyfikujesz poniższe czynności jako odpowiednie dla danego środowiska.

:::zone pivot = "programming-language-python"

```python
import random

session_id = random.randint(0,1000000)
delta_table_path = "/delta/delta-table-{0}".format(session_id)

delta_table_path
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var sessionId = (new Random()).Next(10000000);
var deltaTablePath = $"/delta/delta-table-{sessionId}";

deltaTablePath
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val sessionId = scala.util.Random.nextInt(1000000)
val deltaTablePath = s"/delta/delta-table-$sessionId";
```

::: zone-end

Wyniki w:

'/delta/delta-table-335323'

## <a name="create-a-table"></a>Tworzenie tabeli

Aby utworzyć tabelę różnic Lake, Zapisz w formacie różnicowym ramkę Dataframe out. Można zmienić format z Parquet, CSV, JSON itd.

Poniższy kod pokazuje, jak utworzyć nową tabelę różnicowych danych, używając schematu wywnioskowanego ze ramki danych.

:::zone pivot = "programming-language-python"

```python
data = spark.range(0,5)
data.show()
data.write.format("delta").save(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(0,5);
data.Show();
data.Write().Format("delta").Save(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(0, 5)
data.show
data.write.format("delta").save(deltaTablePath)
```

::: zone-end

Wyniki w:

| ID (Identyfikator)|
|---|
|  0|
|  1|
|  2|
|  3|
|  4|

## <a name="read-data"></a>Odczyt danych

Dane są odczytywane w tabeli różnicowych, określając ścieżkę do plików i format delty.

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Load(deltaTablePath);
df.Show()
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").load(deltaTablePath)
df.show()
```

::: zone-end

Wyniki w:

| ID (Identyfikator)|
|---|
|  1|
|  3|
|  4|
|  0|
|  2|

Kolejność wyników różni się od powyżej, ponieważ nie określono zamówienia jawnie przed jego umieszczeniem.

## <a name="update-table-data"></a>Aktualizowanie danych tabeli

Różnicowa Lake obsługuje kilka operacji modyfikacji tabel przy użyciu standardowych interfejsów API Dataframe, jest to jedno z ulepszeń, które dodaje format Delta. W poniższym przykładzie uruchomiono zadanie wsadowe w celu zastąpienia danych w tabeli.

:::zone pivot = "programming-language-python"

```python
data = spark.range(5,10)
data.write.format("delta").mode("overwrite").save(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(5,10);
data.Write().Format("delta").Mode("overwrite").Save(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(5, 10)
data.write.format("delta").mode("overwrite").save(deltaTablePath)
df.show()
```

::: zone-end

Wyniki w:

| ID (Identyfikator)|
|---|
|  7|
|  8|
|  5|
|  9|
|  6|

W tym miejscu można zobaczyć, że wszystkie pięć rekordów zostały zaktualizowane w celu przechowywania nowych wartości.

## <a name="save-as-catalog-tables"></a>Zapisz jako tabele wykazu

Różnicowa Lake może zapisywać w tabelach wykazu zarządzanego lub zewnętrznego.

:::zone pivot = "programming-language-python"

```python
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql("CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{0}'".format(delta_table_path))
spark.sql("SHOW TABLES").show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
data.Write().Format("delta").SaveAsTable("ManagedDeltaTable");
spark.Sql($"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{deltaTablePath}'");
spark.Sql("SHOW TABLES").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql(s"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '$deltaTablePath'")
spark.sql("SHOW TABLES").show
```

::: zone-end

Wyniki w:

|database|         tableName|IsTemporary|
|--------|------------------|-----------|
| default|externaldeltatable|      fałsz|
| default| manageddeltatable|      fałsz|

Przy użyciu tego kodu utworzono nową tabelę w wykazie z istniejącej ramki danych, która jest nazywana tabelą zarządzaną. Następnie zdefiniowano nową tabelę zewnętrzną w wykazie, która używa istniejącej lokalizacji, zwanej tabelą zewnętrzną. W danych wyjściowych można zobaczyć obie tabele, niezależnie od tego, jak zostały utworzone, są wymienione w wykazie.

Teraz można przyjrzeć się rozszerzonym właściwościom obu tych tabel

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ManagedDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=false)
```

::: zone-end

Wyniki w:

|col_name                    |data_type                                                                                                    |komentarz|
|----------------------------|-------------------------------------------------------------------------------------------------------------|-------|
|identyfikator                          |bigint                                                                                                       |wartość null   |
|                            |                                                                                                             |       |
|Szczegółowe informacje o tabeli  |                                                                                                             |       |
|baza danych                    |default                                                                                                      |       |
|tabela                       |manageddeltatable                                                                                            |       |
|Właściciel                       |Użytkownik zaufany — usługa                                                                                         |       |
|Czas utworzenia                |SAT kwi 25 00:35:34 UTC 2020                                                                                 |       |
|Ostatni dostęp                 |Czwartek sty 01 00:00:00 UTC 1970                                                                                 |       |
|Created By                  |Spark 2.4.4.2.6.99.201 — 11401300                                                                              |       |
|Typ                        |ZARZĄDZANYCH                                                                                                      |       |
|Dostawca                    |delta                                                                                                        |       |
|Właściwości tabeli            |[transient_lastDdlTime = 1587774934]                                                                           |       |
|Statystyki                  |2407 bajtów                                                                                                   |       |
|Lokalizacja                    |abfss://data @ <data lake> . DFS.Core.Windows.NET/Synapse/Workspaces/ <workspace name> /Warehouse/manageddeltatable|       |
|Biblioteka elementu SERDE               |org. Apache. Hadoop. Hive. serde2. opóźnion. LazySimpleSerDe                                                           |       |
|InputFormat                 |org. Apache. Hadoop. mapred. SequenceFileInputFormat                                                             |       |
|OutputFormat                |org. Apache. Hadoop. Hive. QL. IO. HiveSequenceFileOutputFormat                                                    |       |
|Właściwości magazynu          |[Serializacja. format = 1]                                                                                     |       |

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ExternalDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=false)
```

::: zone-end

Wyniki w:

|col_name                    |data_type                                                             |komentarz|
|----------------------------|----------------------------------------------------------------------|-------|
|identyfikator                          |bigint                                                                |wartość null   |
|                            |                                                                      |       |
|Szczegółowe informacje o tabeli  |                                                                      |       |
|baza danych                    |default                                                               |       |
|tabela                       |externaldeltatable                                                    |       |
|Właściciel                       |Użytkownik zaufany — usługa                                                  |       |
|Czas utworzenia                |SAT kwi 25 00:35:38 UTC 2020                                          |       |
|Ostatni dostęp                 |Czwartek sty 01 00:00:00 UTC 1970                                          |       |
|Created By                  |Spark 2.4.4.2.6.99.201 — 11401300                                       |       |
|Typ                        |ZEWNĘTRZNYCH                                                              |       |
|Dostawca                    |POWSTANIE                                                                 |       |
|Właściwości tabeli            |[transient_lastDdlTime = 1587774938]                                    |       |
|Lokalizacja                    |abfss://data @ <data lake> . DFS.Core.Windows.NET/Delta/Delta-Table-587152|       |
|Biblioteka elementu SERDE               |org. Apache. Hadoop. Hive. serde2. opóźnion. LazySimpleSerDe                    |       |
|InputFormat                 |org. Apache. Hadoop. mapred. SequenceFileInputFormat                      |       |
|OutputFormat                |org. Apache. Hadoop. Hive. QL. IO. HiveSequenceFileOutputFormat             |       |
|Właściwości magazynu          |[Serializacja. format = 1]                                              |       |

## <a name="conditional-update-without-overwrite"></a>Aktualizacja warunkowa bez zastępowania

Funkcja Delta Lake udostępnia programistyczne interfejsy API do warunkowego aktualizowania, usuwania i scalania (jest to często określane jako dane upsert) w tabelach.

:::zone pivot = "programming-language-python"

```python
from delta.tables import *
from pyspark.sql.functions import *

delta_table = DeltaTable.forPath(spark, delta_table_path)

delta_table.update(
  condition = expr("id % 2 == 0"),
  set = { "id": expr("id + 100") })
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Delta;
using Microsoft.Spark.Extensions.Delta.Tables;
using Microsoft.Spark.Sql;
using static Microsoft.Spark.Sql.Functions;

var deltaTable = DeltaTable.ForPath(deltaTablePath);

deltaTable.Update(
  condition: Expr("id % 2 == 0"),
  set: new Dictionary<string, Column>(){{ "id", Expr("id + 100") }});
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
import io.delta.tables._
import org.apache.spark.sql.functions._

val deltaTable = DeltaTable.forPath(deltaTablePath)

// Update every even value by adding 100 to it
deltaTable.update(
  condition = expr("id % 2 == 0"),
  set = Map("id" -> expr("id + 100")))
deltaTable.toDF.show
```

::: zone-end

Wyniki w:

| ID (Identyfikator)|
|---|
|106|
|108|
|  5|
|  7|
|  9|

Tutaj dodaliśmy 100 do każdego parzystego identyfikatora.

:::zone pivot = "programming-language-python"

```python
delta_table.delete("id % 2 == 0")
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.Delete(condition: Expr("id % 2 == 0"));
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.delete(condition = expr("id % 2 == 0"))
deltaTable.toDF.show
```

::: zone-end

Wyniki w:

| ID (Identyfikator)|
|---|
|  5|
|  7|
|  9|

Zauważ, że każdy wiersz parzysty został usunięty.

:::zone pivot = "programming-language-python"

```python
new_data = spark.range(0,20).alias("newData")

delta_table.alias("oldData")\
    .merge(new_data.alias("newData"), "oldData.id = newData.id")\
    .whenMatchedUpdate(set = { "id": lit("-1")})\
    .whenNotMatchedInsert(values = { "id": col("newData.id") })\
    .execute()

delta_table.toDF().show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var newData = spark.Range(20).As("newData");

deltaTable
    .As("oldData")
    .Merge(newData, "oldData.id = newData.id")
    .WhenMatched()
        .Update(new Dictionary<string, Column>() {{"id", Lit("-1")}})
    .WhenNotMatched()
        .Insert(new Dictionary<string, Column>() {{"id", Col("newData.id")}})
    .Execute();

deltaTable.ToDF().Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val newData = spark.range(0, 20).toDF

deltaTable.as("oldData").
  merge(
    newData.as("newData"),
    "oldData.id = newData.id").
  whenMatched.
  update(Map("id" -> lit(-1))).
  whenNotMatched.
  insert(Map("id" -> col("newData.id"))).
  execute()

deltaTable.toDF.show()
```

::: zone-end

Wyniki w:

| ID (Identyfikator)|
|---|
| 18|
| 15|
| 19|
|  2|
|  1|
|  6|
|  8|
|  3|
| -1|
| 10|
| 13|
|  0|
| 16|
|  4|
| -1|
| 12|
| 11|
| 14|
| -1|
| 17|

W tym miejscu masz kombinację istniejących danych. Do istniejących danych została przypisana wartość-1 w ścieżce kodu aktualizacji (WhenMatched). Dodano również nowe dane, które zostały utworzone w górnej części fragmentu kodu i zostały dodane za pomocą instrukcji INSERT Code Path (WhenNotMatched).

### <a name="history"></a>Historia

W przypadku różnic w usłudze Data Lake jest możliwe umożliwienie przeszukiwania historii tabeli. Oznacza to, że zmiany wprowadzone do bazowej tabeli różnicowej. W poniższej komórce pokazano, jak łatwo jest sprawdzić historię.

:::zone pivot = "programming-language-python"

```python
delta_table.history().show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show(false)
```

::: zone-end

Wyniki w:

|Wersja|          sygnatura czasowa|userId|userName|operation|                                                operationParameters| zadanie|notebook|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|---------|-------------------------------------------------------------------|----|--------|---------|-----------|--------------|-------------|
|      4|2020-04-25 00:36:27|  wartość null|    wartość null|    POŁĄCZENIE|                       [predykat-> (oldData.`ID` = newData. `ID` )]|wartość null|    wartość null|     wartość null|          3|          wartość null|        fałsz|
|      3|2020-04-25 00:36:08|  wartość null|    wartość null|   DELETE|[predykat-> ["(( `ID` % CAST (2 jako bigint)) = CAST (0 jako bigint))"]]|wartość null|    wartość null|     wartość null|          2|          wartość null|        fałsz|
|      2|2020-04-25 00:35:51|  wartość null|    wartość null|   UPDATE| [predykat-> ((ID # 744L% CAST (2 jako bigint)) = CAST (0 jako bigint))]|wartość null|    wartość null|     wartość null|          1|          wartość null|        fałsz|
|      1|2020-04-25 00:35:05|  wartość null|    wartość null|    PRAWEM|                             [Mode-> overwrite, partitionBy-> []]|wartość null|    wartość null|     wartość null|          0|          wartość null|        fałsz|
|      0|2020-04-25 00:34:34|  wartość null|    wartość null|    PRAWEM|                         [Mode-> ErrorIfExists, partitionBy-> []]|wartość null|    wartość null|     wartość null|       wartość null|          wartość null|         true|

W tym miejscu można zobaczyć wszystkie modyfikacje wprowadzone w powyższych fragmentach kodu.

## <a name="read-older-versions-of-data-using-time-travel"></a>Odczytuj starsze wersje danych przy użyciu czasu podróży

Istnieje możliwość wykonywania zapytań dotyczących poprzednich migawek tabeli różnicowych, przy użyciu funkcji o nazwie czas podróży. Jeśli chcesz uzyskać dostęp do danych, które zostały nadpisane, możesz wykonać zapytanie dotyczące migawki tabeli przed zapisaniem pierwszego zestawu danych przy użyciu opcji versionAsOf.

Po uruchomieniu poniższej komórki powinien zostać wyświetlony pierwszy zestaw danych przed jego zapisaniem. Czas podróży to niezwykle wydajna funkcja, która umożliwia dostęp do danych, które nie są już w tabeli. Usunięcie opcji wersja 0 (lub określenie wersji 1) umożliwi ponowne wyświetlenie nowszych danych. Aby uzyskać więcej informacji, zobacz [zapytanie o starszą migawkę tabeli](https://docs.delta.io/latest/delta-batch.html#deltatimetravel).

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Option("versionAsOf", 0).Load(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").option("versionAsOf", 0).load(deltaTablePath)
df.show()
```

::: zone-end

Wyniki w:

| ID (Identyfikator)|
|---|
|  0|
|  1|
|  4|
|  3|
|  2|

W tym miejscu możesz zobaczyć, że przywrócono do najwcześniejszej wersji danych.

## <a name="write-a-stream-of-data-to-a-table"></a>Napisz strumień danych do tabeli

Można także zapisywać w tabeli różnic Lake przy użyciu funkcji przesyłania strumieniowego platformy Spark. Dziennik różnicowych transakcji jest gwarantowany dokładnie po przetworzeniu, nawet jeśli istnieją inne strumienie lub zapytania wsadowe uruchomione współbieżnie względem tabeli. Domyślnie strumienie są uruchamiane w trybie dołączania, który dodaje nowe rekordy do tabeli.

Aby uzyskać więcej informacji na temat integracji różnicowej usługi Lake z funkcją przesyłania strumieniowego ze strukturą, zobacz artykuł [przesyłanie strumieniowe w tabeli](https://docs.delta.io/latest/delta-streaming.html).

Oto, co robimy, w poniższych komórkach:

* Komórka 30 pokazuje nowo dołączone dane
* Komórka 31. Inspekcja historii
* Komórka 32 zatrzymać zadanie przesyłania strumieniowego strukturalnego
* Komórka 33 Inspekcja historii < — Zauważ, że dołączania zostały zatrzymane

Najpierw należy skonfigurować proste zadanie przesyłania strumieniowego Spark w celu wygenerowania sekwencji i przetworzyć zadanie zapisu w tabeli różnicowej.

:::zone pivot = "programming-language-python"

```python
streaming_df = spark.readStream.format("rate").load()
stream = streaming_df\
    .selectExpr("value as id")\
    .writeStream\
    .format("delta")\
    .option("checkpointLocation", "/tmp/checkpoint-{0}".format(session_id))\
    .start(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var streamingDf = spark.ReadStream().Format("rate").Load();
var stream = streamingDf.SelectExpr("value as id").WriteStream().Format("delta").Option("checkpointLocation", $"/tmp/checkpoint-{sessionId}").Start(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val streamingDf = spark.readStream.format("rate").load()
val stream = streamingDf.select($"value" as "id").writeStream.format("delta").option("checkpointLocation", s"/tmp/checkpoint-$sessionId").start(deltaTablePath)
```

::: zone-end

## <a name="read-a-stream-of-changes-from-a-table"></a>Odczytywanie strumienia zmian z tabeli

Strumień jest zapisywany w tabeli delty Lake, ale można go również odczytać z tej tabeli jako źródła strumieniowego. Można na przykład uruchomić inne zapytanie przesyłania strumieniowego, które drukuje wszystkie zmiany wprowadzone w tabeli różnic Lake.

:::zone pivot = "programming-language-python"

```python
delta_table.toDF().sort(col("id").desc()).show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.ToDF().Sort(Col("id").Desc()).Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.toDF.sort($"id".desc).show
```

::: zone-end

Wyniki w:

| ID (Identyfikator)|
|---|
| 19|
| 18|
| 17|
| 16|
| 15|
| 14|
| 13|
| 12|
| 11|
| 10|
|  8|
|  6|
|  4|
|  3|
|  2|
|  1|
|  0|
| -1|
| -1|
| -1|

:::zone pivot = "programming-language-python"

```python
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show
```

::: zone-end

Wyniki w:

|Wersja|          sygnatura czasowa|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|AKTUALIZACJA PRZESYŁANIA STRUMIENIOWEGO|[outputmode-> append, queryId-> d26b4f8a-7e5a-44f2-A5FB-23a7bd02aef7, epochId-> 0]|          4|
|      4|2020-04-25 00:36:27|           POŁĄCZENIE|                                         [predykat-> (oldData.`id` = newData. `id` )]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [predykat-> ["(( `id` % CAST (2 jako bigint)) = CAST (0 jako bigint))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predykat-> ((ID # 744L% CAST (2 jako bigint)) = CAST (0 jako bigint))]|          1|
|      1|2020-04-25 00:35:05|           PRAWEM|                                               [Mode-> overwrite, partitionBy-> []]|          0|
|      0|2020-04-25 00:34:34|           PRAWEM|                                           [Mode-> ErrorIfExists, partitionBy-> []]|       wartość null|

W tym miejscu porzucasz niektóre mniej interesujące kolumny, aby uprościć wyświetlanie widoku historii.

:::zone pivot = "programming-language-python"

```python
stream.stop()
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(100, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
stream.Stop();
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(100, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
stream.stop
deltaTable.history.show
```

Wyniki w:

|Wersja|          sygnatura czasowa|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|AKTUALIZACJA PRZESYŁANIA STRUMIENIOWEGO|[outputmode-> append, queryId-> d26b4f8a-7e5a-44f2-A5FB-23a7bd02aef7, epochId-> 0]|          4|
|      4|2020-04-25 00:36:27|           POŁĄCZENIE|                                         [predykat-> (oldData.`id` = newData. `id` )]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [predykat-> ["(( `id` % CAST (2 jako bigint)) = CAST (0 jako bigint))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predykat-> ((ID # 744L% CAST (2 jako bigint)) = CAST (0 jako bigint))]|          1|
|      1|2020-04-25 00:35:05|           PRAWEM|                                               [Mode-> overwrite, partitionBy-> []]|          0|
|      0|2020-04-25 00:34:34|           PRAWEM|                                           [Mode-> ErrorIfExists, partitionBy-> []]|       wartość null|

::: zone-end

## <a name="convert-parquet-to-delta"></a>Konwertuj Parquet na Delta

Konwersję w miejscu można wykonać w formacie Parquet na Delta.

W tym miejscu zamierzasz testować, czy istniejąca tabela jest w formacie różnicowym.
:::zone pivot = "programming-language-python"

```python
parquet_path = "/parquet/parquet-table-{0}".format(session_id)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetPath = $"/parquet/parquet-table-{sessionId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetPath = s"/parquet/parquet-table-$sessionId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Wyniki w:

Fałsz

Teraz przekonwertujesz dane na format Delta i sprawdź, czy zadziałało.

:::zone pivot = "programming-language-python"

```python
DeltaTable.convertToDelta(spark, "parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
DeltaTable.ConvertToDelta(spark, $"parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
DeltaTable.convertToDelta(spark, s"parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Wyniki w:

Prawda

## <a name="sql-support"></a>Obsługa SQL

Delta obsługuje polecenia narzędzia tabeli za poorednictwem języka SQL. Możesz użyć SQL, aby:

* Pobierz historię delty
* Próżniowy a różnicowa
* Konwertuj plik Parquet na Delta

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE HISTORY delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"DESCRIBE HISTORY delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"DESCRIBE HISTORY delta.`$deltaTablePath`").show()
```

::: zone-end

Wyniki w:

|Wersja|          sygnatura czasowa|userId|userName|       operation| operationParameters| zadanie|notebook|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|----------------|--------------------|----|--------|---------|-----------|--------------|-------------|
|      5|2020-04-25 00:37:09|  wartość null|    wartość null|AKTUALIZACJA PRZESYŁANIA STRUMIENIOWEGO|[outputmode-> AP...|wartość null|    wartość null|     wartość null|          4|          wartość null|         true|
|      4|2020-04-25 00:36:27|  wartość null|    wartość null|           POŁĄCZENIE|[predykat-> (OL...|wartość null|    wartość null|     wartość null|          3|          wartość null|        fałsz|
|      3|2020-04-25 00:36:08|  wartość null|    wartość null|          DELETE|[predykat-> ["(...|wartość null|    wartość null|     wartość null|          2|          wartość null|        fałsz|
|      2|2020-04-25 00:35:51|  wartość null|    wartość null|          UPDATE|[predykat-> ((i...|wartość null|    wartość null|     wartość null|          1|          wartość null|        fałsz|
|      1|2020-04-25 00:35:05|  wartość null|    wartość null|           PRAWEM|[Mode-> Overwrit...|wartość null|    wartość null|     wartość null|          0|          wartość null|        fałsz|
|      0|2020-04-25 00:34:34|  wartość null|    wartość null|           PRAWEM|[Mode-> ErrorIfE...|wartość null|    wartość null|     wartość null|       wartość null|          wartość null|         true|

:::zone pivot = "programming-language-python"

```python
spark.sql("VACUUM delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"VACUUM delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"VACUUM delta.`$deltaTablePath`").show()
```

::: zone-end

Wyniki w:

|                path|
|--------------------|
|abfss://data@arca...|

Teraz można sprawdzić, czy tabela nie jest tabelą formatu różnicowego, a następnie przekonwertować ją na format Delta przy użyciu programu Spark SQL i potwierdzić, że została prawidłowo przekonwertowana.

:::zone pivot = "programming-language-python"

```python
parquet_id = random.randint(0,1000)
parquet_path = "/parquet/parquet-table-{0}-{1}".format(session_id, parquet_path)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
spark.sql("CONVERT TO DELTA parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetId =  (new Random()).Next(10000000);
var parquetPath = $"/parquet/parquet-table-{sessionId}-{parquetId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath);
spark.Sql($"CONVERT TO DELTA parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetId = scala.util.Random.nextInt(1000)
val parquetPath = s"/parquet/parquet-table-$sessionId-$parquetId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
spark.sql(s"CONVERT TO DELTA parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

Wyniki w:

Prawda

Pełną dokumentację można znaleźć na [stronie z dokumentacją usługi Delta Lake](https://docs.delta.io/latest/delta-intro.html) .

Aby uzyskać więcej informacji, zobacz temat [różnicowa Lake Project](https://github.com/delta-io/delta).

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja platformy .NET dla Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
