---
title: Migrowanie danych relacyjnych "jeden do kilku" do Azure Cosmos DB API SQL
description: Dowiedz się, jak obsługiwać złożoną migrację danych dla relacji "jeden do kilku" do interfejsu API SQL
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 7dbb162749e0a2f84140b8e9394934198d096eac
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589622"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Migrowanie danych relacyjnych "jeden do kilku" do Azure Cosmos DB interfejsu API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Aby przeprowadzić migrację z relacyjnej bazy danych do Azure Cosmos DB API SQL, może być konieczne przeprowadzenie zmian w modelu danych w celu optymalizacji.

Jednym z typowych przekształceń jest denormalizowanie danych przez osadzenie powiązanych poditów w jednym dokumencie JSON. W tym miejscu przyjrzymy się kilku opcjam tego przy użyciu Azure Data Factory lub Azure Databricks. Aby uzyskać ogólne wskazówki dotyczące modelowania danych dla Cosmos DB, zapoznaj się [z tematem Modelowanie danych](modeling-data.md)w Azure Cosmos DB .  

## <a name="example-scenario"></a>Przykładowy scenariusz

Załóżmy, że w bazie danych SQL mamy dwie poniższe tabele: Orders (Zamówienia) i OrderDetails (Szczegóły zamówienia).


:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/orders.png" alt-text="Zrzut ekranu przedstawiający tabele Orders i OrderDetails w bazie danych SQL." border="false" :::

Chcemy połączyć tę relację jeden do kilku w jednym dokumencie JSON podczas migracji. W tym celu możemy utworzyć zapytanie T-SQL przy użyciu instrukcji "FOR JSON", jak podano poniżej:

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

Wyniki tego zapytania będą wyglądać tak, jak poniżej: 

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png" alt-text="Szczegóły zamówienia" lightbox="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png":::

Idealnym rozwiązaniem jest użycie pojedynczego działania kopiowania Azure Data Factory (ADF) w celu wykonywania zapytań o dane SQL jako źródła i zapisu danych wyjściowych bezpośrednio w ujściu Azure Cosmos DB jako odpowiednie obiekty JSON. Obecnie nie jest możliwe wykonanie wymaganych przekształceń JSON w jednym działaniu kopiowania. Jeśli spróbujemy skopiować wyniki powyższego zapytania do kontenera interfejsu API SQL usługi Azure Cosmos DB, zobaczymy pole OrderDetails jako właściwość ciągu naszego dokumentu, a nie oczekiwaną tablicę JSON.

Możemy ominą to bieżące ograniczenie na jeden z następujących sposobów:

* **Użyj Azure Data Factory z dwoma działaniami kopiowania:** 
  1. Pobierz dane w formacie JSON z bazy danych SQL do pliku tekstowego w pośredniej lokalizacji magazynu obiektów blob i 
  2. Załaduj dane z pliku tekstowego JSON do kontenera w Azure Cosmos DB.

* **Użyj Azure Databricks do odczytu z bazy danych SQL i** zapisu w Azure Cosmos DB — w tym miejscu przedstawimy dwie opcje.


Przyjrzyjmy się tym metodom bardziej szczegółowo:

## <a name="azure-data-factory"></a>Azure Data Factory

Mimo że nie można osadzić funkcji OrderDetails jako tablicy JSON w dokumencie docelowym Cosmos DB, możemy omiąć problem przy użyciu dwóch oddzielnych działań kopiowania.

### <a name="copy-activity-1-sqljsontoblobtext"></a>Działanie kopiowania #1: SqlJsonToBlobText

W przypadku danych źródłowych używamy zapytania SQL, aby uzyskać zestaw wyników jako pojedynczą kolumnę z jednym obiektem JSON (reprezentującym kolejność) na wiersz przy użyciu funkcji SQL Server OPENJSON i FOR JSON PATH:

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf1.png" alt-text="Kopia ADF":::


W przypadku ujścia działania kopiowania SqlJsonToBlobText wybieramy opcję "Tekst rozdzielany" i wskazujemy określony folder w programie Azure Blob Storage z dynamicznie wygenerowaną unikatową nazwą pliku (na przykład ' @concat (pipeline(). RunId,'.json').
Ponieważ nasz plik tekstowy nie jest tak naprawdę "rozdzielany" i nie chcemy, aby był analizowany w oddzielnych kolumnach przy użyciu przecinków i chcemy zachować podwójne cudzysłowy ("), ustawiamy "Ogranicznik kolumny" na tabulator ("\t") — lub inny znak, który nie występuje w danych , i "Cudzysłów" na wartość "Brak znaku cudzysłowu".

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf2.png" alt-text="Zrzut ekranu przedstawiający ustawienia Ogranicznik kolumny i Znak cudzysłowu.":::

### <a name="copy-activity-2-blobjsontocosmos"></a>Działanie kopiowania #2: BlobJsonToCosmos

Następnie zmodyfikujesz nasz potok funkcji ADF, dodając drugie działanie kopiowania, które wyszukuje Azure Blob Storage pliku tekstowego utworzonego przez pierwsze działanie. Przetwarza je jako źródło "JSON", aby wstawić do Cosmos DB jako jeden dokument na wiersz JSON znaleziony w pliku tekstowym.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf3.png" alt-text="Zrzut ekranu przedstawiający wyróżniony plik źródłowy JSON i pola Ścieżka pliku.":::

Opcjonalnie dodamy również działanie "Usuń" do potoku, aby usunąć wszystkie poprzednie pliki pozostałe w folderze /Orders/ przed każdym uruchomieniem. Nasz potok ADF wygląda teraz następująco:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf4.png" alt-text="Zrzut ekranu przedstawiający działanie Usuń.":::

Po wyzwoleniu potoku powyżej w lokalizacji pośredniczącej zostanie utworzony Azure Blob Storage zawierający jeden obiekt JSON na wiersz:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf5.png" alt-text="Zrzut ekranu przedstawiający utworzony plik, który zawiera obiekty JSON.":::

Zobaczymy również dokumenty Orders (Zamówienia) z prawidłowo osadzonymi elementami OrderDetails wstawionych do Cosmos DB kolekcji:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf6.png" alt-text="Zrzut ekranu przedstawiający szczegóły zamówienia w ramach Cosmos DB dokumentu":::


## <a name="azure-databricks"></a>Azure Databricks

Możemy również użyć platformy Spark w [programie Azure Databricks,](https://azure.microsoft.com/services/databricks/) aby skopiować dane ze źródła SQL Database do miejsca docelowego Azure Cosmos DB bez tworzenia pośredniczących plików tekstowych/JSON w Azure Blob Storage. 

> [!NOTE]
> Dla przejrzystości i prostoty poniższe fragmenty kodu zawierają fikcyjne hasła bazy danych jawnie wbudowane, ale zawsze należy używać Azure Databricks tajnych.
>

Najpierw utworzymy i dołączymy wymagany łącznik [SQL](/connectors/sql/) [oraz Azure Cosmos DB łączników](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) do naszego Azure Databricks klastra. Uruchom ponownie klaster, aby upewnić się, że biblioteki zostały załadowane.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks1.png" alt-text="Zrzut ekranu przedstawiający miejsce utworzenia i dołączenia wymaganego łącznika SQL oraz Azure Cosmos DB łączników do naszego Azure Databricks klastra.":::

Następnie przedstawimy dwie próbki dla języka Scala i Python. 

### <a name="scala"></a>Scala
W tym miejscu pobierzemy wyniki zapytania SQL z danymi wyjściowych "FOR JSON" do ramki danych:

```scala
// Connect to Azure SQL /connectors/sql/
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks2.png" alt-text="Zrzut ekranu przedstawiający dane wyjściowe zapytania SQL w ramce danych.":::

Następnie połączymy się z naszą Cosmos DB i kolekcją:

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

Na koniec definiujemy nasz schemat i from_json obiekt DataFrame przed zapisaniem go w kolekcji CosmosDB.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks3.png" alt-text="Zrzut ekranu przedstawiający właściwą tablicę do zapisywania w Cosmos DB kolekcji.":::


### <a name="python"></a>Python

Alternatywnie może być konieczne wykonanie przekształceń JSON na platformie Spark (jeśli źródłowa baza danych nie obsługuje operacji "FOR JSON" lub podobnej operacji) lub użyć operacji równoległych dla bardzo dużego zestawu danych. Poniżej przedstawiamy przykład PySpark. Rozpocznij od skonfigurowania połączeń źródłowej i docelowej bazy danych w pierwszej komórce:

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

Następnie odpytniemy źródłową bazę danych (w tym przypadku SQL Server) o rekordy szczegółów zamówień i zamówień, umieszczając wyniki w ramce danych platformy Spark. Utworzymy również listę zawierającą wszystkie identyfikatory zamówień i pulę wątków dla operacji równoległych:

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

Następnie utwórz funkcję do zapisywania zamówień w docelowej kolekcji interfejsu API SQL. Ta funkcja przefiltruje wszystkie szczegóły zamówienia dla danego identyfikatora zamówienia, przekonwertuje je na tablicę JSON i wstawi tablicę do dokumentu JSON, który w tej kolejności zapiszemy w docelowej kolekcji interfejsów API SQL:

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

Na koniec wywołamy powyższe wywołanie przy użyciu funkcji mapy w puli wątków w celu wykonania równoległego, przekazując listę identyfikatorów kolejności, które zostały utworzone wcześniej:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
W obu metodach, na końcu, powinniśmy uzyskać prawidłowo zapisane osadzone szczegóły Zamówienia w ramach każdego dokumentu Order w Cosmos DB kolekcji:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks4.png" alt-text="Databricks":::

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej [o modelowaniu danych w Azure Cosmos DB](./modeling-data.md)
* Dowiedz [się, jak modelować i partycjonować dane na Azure Cosmos DB](./how-to-model-partition-example.md)
