---
title: Migrowanie danych relacyjnych "jeden do kilku" do Azure Cosmos DB interfejsu API SQL
description: Informacje na temat obsługi złożonej migracji danych dla relacji jeden-do-wielu z interfejsem API SQL
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 53a3317f38cc22ffa3745f5f0e58cc01a54b825c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096754"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Migrowanie danych relacyjnych "jeden do kilku" do Azure Cosmos DB konta interfejsu API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Aby przeprowadzić migrację z relacyjnej bazy danych do Azure Cosmos DB SQL API, może być konieczne wprowadzenie zmian w modelu danych na potrzeby optymalizacji.

Jedna wspólna transformacja polega na denormalizacji danych przez osadzenie powiązanych podelementów w ramach jednego dokumentu JSON. Oto kilka opcji dla tego programu przy użyciu Azure Data Factory lub Azure Databricks. Ogólne wskazówki dotyczące modelowania danych dla Cosmos DB można znaleźć w temacie [Modelowanie danych w Azure Cosmos DB](modeling-data.md).  

## <a name="example-scenario"></a>Przykładowy scenariusz

Załóżmy, że mamy dwie tabele w bazie danych SQL, Orders i OrderDetails.


:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/orders.png" alt-text="Zrzut ekranu przedstawiający tabele Orders i OrderDetails w bazie danych SQL." border="false" :::

Chcemy połączyć tę relację "jeden do kilku" z jednym dokumentem JSON podczas migracji. W tym celu możemy utworzyć zapytanie T-SQL przy użyciu polecenia "FOR JSON" w następujący sposób:

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

Wyniki tego zapytania będą wyglądać następująco: 

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png" alt-text="Zrzut ekranu przedstawiający tabele Orders i OrderDetails w bazie danych SQL." lightbox="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png":::

Najlepiej użyć działania kopiowania pojedynczego Azure Data Factory (ADF) do wysyłania zapytań dotyczących danych SQL jako źródła i zapisywania danych wyjściowych bezpośrednio do Azure Cosmos DB ujścia jako odpowiednich obiektów JSON. Obecnie nie jest możliwe wykonywanie transformacji JSON w jednym działaniu kopiowania. Jeśli spróbujemy skopiować wyniki powyższego zapytania do Azure Cosmos DB kontenera interfejsu API SQL, zobaczymy pole OrderDetails jako właściwość ciągu naszego dokumentu zamiast oczekiwanej tablicy JSON.

To bieżące ograniczenie można obejść w jeden z następujących sposobów:

* **Użyj Azure Data Factory z dwoma działaniami kopiowania** : 
  1. Pobierz dane w formacie JSON z bazy danych SQL do pliku tekstowego w lokalizacji magazynu obiektów blob, a następnie 
  2. Załaduj dane z pliku tekstowego JSON do kontenera w Azure Cosmos DB.

* **Użyj Azure Databricks do odczytywania danych z bazy danych SQL i zapisywania do Azure Cosmos DB — w** tym miejscu przedstawimy dwie opcje.


Przyjrzyjmy się tym podejściem bardziej szczegółowo:

## <a name="azure-data-factory"></a>Azure Data Factory

Chociaż nie można osadzić OrderDetails jako tablicy JSON w docelowym dokumencie Cosmos DB, możemy obejść problem, używając dwóch oddzielnych działań kopiowania.

### <a name="copy-activity-1-sqljsontoblobtext"></a>#1 działania kopiowania: SqlJsonToBlobText

W przypadku danych źródłowych używamy zapytania SQL w celu pobrania zestawu wyników jako pojedynczej kolumny z jednym obiektem JSON (reprezentującym kolejność) za pomocą SQL Server OPENJSON i funkcji ścieżki JSON:

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

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf1.png" alt-text="Zrzut ekranu przedstawiający tabele Orders i OrderDetails w bazie danych SQL." bez znaku cudzysłowu ".

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf2.png" alt-text="Zrzut ekranu przedstawiający tabele Orders i OrderDetails w bazie danych SQL.":::

### <a name="copy-activity-2-blobjsontocosmos"></a>#2 działania kopiowania: BlobJsonToCosmos

Następnie zmodyfikujemy nasz potok ADF, dodając drugie działanie kopiowania, które przeszukuje Blob Storage platformy Azure dla pliku tekstowego, który został utworzony przez pierwsze działanie. Przetwarza ją jako Źródło "JSON", aby wstawić do Cosmos DB ujścia, ponieważ jeden dokument na wiersz JSON został znaleziony w pliku tekstowym.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf3.png" alt-text="Zrzut ekranu przedstawiający tabele Orders i OrderDetails w bazie danych SQL." do potoku, aby usunąć wszystkie poprzednie pliki pozostałe w folderze/Orders/przed każdym uruchomieniem. Nasz potok ADF wygląda teraz następująco:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf4.png" alt-text="Zrzut ekranu przedstawiający tabele Orders i OrderDetails w bazie danych SQL.":::

Po wyzwoleniu powyższego potoku zobaczymy plik utworzony w naszej lokalizacji Blob Storage platformy Azure zawierającej jeden obiekt JSON dla każdego wiersza:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf5.png" alt-text="Zrzut ekranu przedstawiający tabele Orders i OrderDetails w bazie danych SQL.":::

Zobaczymy również dokumenty Orders z prawidłowo osadzonymi OrderDetails wstawionymi do naszej kolekcji Cosmos DB:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf6.png" alt-text="Zrzut ekranu przedstawiający tabele Orders i OrderDetails w bazie danych SQL.":::


## <a name="azure-databricks"></a>Azure Databricks

Możemy również użyć platformy Spark w [Azure Databricks](https://azure.microsoft.com/services/databricks/) , aby skopiować dane ze źródła SQL Database do Azure Cosmos DB docelowej bez tworzenia pośrednich plików tekstu/JSON na platformie Azure Blob Storage. 

> [!NOTE]
> Dla jasności i prostoty fragmenty kodu poniżej zawierają fikcyjne hasła bazy danych jawnie wbudowane, ale zawsze należy używać Azure Databricks wpisów tajnych.
>

Najpierw tworzymy i dołączymy wymagane biblioteki łączników [SQL](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) i [Azure Cosmos DB łączników](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) do naszego klastra Azure Databricks. Uruchom ponownie klaster, aby upewnić się, że biblioteki są załadowane.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks1.png" alt-text="Zrzut ekranu przedstawiający tabele Orders i OrderDetails w bazie danych SQL.":::

Następnie przedstawimy dwie przykłady dla Scala i Python. 

### <a name="scala"></a>Scala
W tym miejscu uzyskasz wyniki zapytania SQL z danymi wyjściowymi "FOR JSON" w ramce Dataframe:

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
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

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks2.png" alt-text="Zrzut ekranu przedstawiający tabele Orders i OrderDetails w bazie danych SQL.":::

Następnie nawiązujemy połączenie z naszą Cosmos DB bazą danych i kolekcją:

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

Na koniec zdefiniujemy schemat i użyjemy from_json, aby zastosować ramkę danych przed zapisaniem jej w kolekcji CosmosDB.

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

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks3.png" alt-text="Zrzut ekranu przedstawiający tabele Orders i OrderDetails w bazie danych SQL.":::


### <a name="python"></a>Python

Alternatywnie można wykonać przekształcenia JSON w usłudze Spark (jeśli źródłowa baza danych nie obsługuje "FOR JSON" lub podobnej operacji) lub można użyć operacji równoległych dla bardzo dużego zestawu danych. Tutaj przedstawiono przykład PySpark. Zacznij od skonfigurowania połączenia źródłowej i docelowej bazy danych w pierwszej komórce:

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

Następnie wyślemy zapytanie do źródłowej bazy danych (w tym przypadku SQL Server) dla rekordów Order i Order detail, co powoduje umieszczenie wyników w ramkach dataframes. Utworzymy również listę zawierającą wszystkie identyfikatory zamówień i pulę wątków dla operacji równoległych:

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

Następnie Utwórz funkcję do pisania zamówień w docelowej kolekcji interfejsów API SQL. Ta funkcja będzie filtrować wszystkie szczegóły zamówienia dla danego identyfikatora zamówienia, przekonwertować je do tablicy JSON i wstawić tablicę do dokumentu JSON, który zostanie zapisany w docelowej kolekcji interfejsów API SQL dla tej kolejności:

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

Na koniec wywołamy powyższe polecenie przy użyciu funkcji map w puli wątków, aby wykonać je równolegle, przekazując listę utworzonych wcześniej identyfikatorów kolejności:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
W obu przypadkach na końcu powinna zostać prawidłowo zapisana osadzona OrderDetails w każdym dokumencie kolejności w kolekcji Cosmos DB:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks4.png" alt-text="Zrzut ekranu przedstawiający tabele Orders i OrderDetails w bazie danych SQL.":::

## <a name="next-steps"></a>Następne kroki
* Informacje [na temat modelowania danych w Azure Cosmos DB](./modeling-data.md)
* Dowiedz się [, jak modelować i dzielić dane na Azure Cosmos DB](./how-to-model-partition-example.md)