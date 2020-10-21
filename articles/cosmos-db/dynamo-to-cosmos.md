---
title: Migruj swoją aplikację z usługi Amazon DynamoDB do Azure Cosmos DB
description: Dowiedz się, jak migrować aplikację .NET z DynamoDB firmy Amazon do Azure Cosmos DB
author: manishmsfte
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/29/2020
ms.author: mansha
ms.openlocfilehash: 167d1f21a2eb7ea4c685b5bbbb5d8d64fcc1367e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92278708"
---
# <a name="migrate-your-application-from-amazon-dynamodb-to-azure-cosmos-db"></a>Migruj swoją aplikację z usługi Amazon DynamoDB do Azure Cosmos DB

Azure Cosmos DB to skalowalna, globalnie dystrybuowana, w pełni zarządzana baza danych. Zapewnia ona gwarantowany dostęp do danych o małym opóźnieniu. Aby dowiedzieć się więcej na temat Azure Cosmos DB, zobacz artykuł z [omówieniem](introduction.md) . W tym artykule opisano sposób migrowania aplikacji .NET z programu DynamoDB do Azure Cosmos DB przy minimalnych zmianach kodu.

## <a name="conceptual-differences"></a>Różnice pojęciowe

Poniżej przedstawiono kluczowe różnice pojęciowe między Azure Cosmos DB i DynamoDB:

|  DynamoDB | Azure Cosmos DB  |
|---|---|
|Nie dotyczy|  baza danych |
|Tabela      |  Kolekcja |
|  Element |  Dokument |
|Atrybut|Pole|
|Indeks pomocniczy|Indeks pomocniczy|
|Klucz podstawowy — klucz partycji|Klucz partycji|
|Klucz podstawowy — klucz sortowania| Niewymagane |
|Strumień|ChangeFeed|
|Zapisz jednostkę obliczeniową|Jednostka żądania (elastyczna, może być używana do odczytu lub zapisu)|
|Odczytaj jednostkę obliczeniową    |Jednostka żądania (elastyczna, może być używana do odczytu lub zapisu)|
|Tabele globalne| Niewymagane. Możesz bezpośrednio wybrać region podczas aprowizacji konta usługi Azure Cosmos (możesz zmienić region później)|

## <a name="structural-differences"></a>Różnice strukturalne

Azure Cosmos DB ma prostszy strukturę JSON w porównaniu z DynamoDB. Poniższy przykład pokazuje różnice

**DynamoDB**:

Następujący obiekt JSON reprezentuje format danych w DynamoDB

```json
{
TableName: "Music",
KeySchema: [
{ 
  AttributeName: "Artist",
  KeyType: "HASH", //Partition key
},
{ 
  AttributeName: "SongTitle",
  KeyType: "RANGE" //Sort key
}
],
AttributeDefinitions: [
{ 
  AttributeName: "Artist",
  AttributeType: "S"
},
{ 
  AttributeName: "SongTitle",
  AttributeType: "S"
}
],
ProvisionedThroughput: {
  ReadCapacityUnits: 1,
  WriteCapacityUnits: 1
 }
}
 ```

**Azure Cosmos DB**:

Następujący obiekt JSON reprezentuje format danych w Azure Cosmos DB

```json
{
"Artist": "",
"SongTitle": "",
"AlbumTitle": "",
"Year": 9999,
"Price": 0.0,
"Genre": "",
"Tags": ""
}
```

## <a name="migrate-your-data"></a>Migrowanie danych

Dostępne są różne opcje migracji danych do Azure Cosmos DB. Aby dowiedzieć się więcej, zobacz [Opcje migracji danych lokalnych lub w chmurze do Azure Cosmos DB](cosmosdb-migrationchoices.md) artykułu.

## <a name="migrate-your-code"></a>Migrowanie kodu

W tym artykule opisano zakres migracji kodu aplikacji do Azure Cosmos DB, który jest krytycznym aspektem migracji bazy danych. Aby pomóc w zmniejszeniu krzywej uczenia, następujące sekcje zawierają porównanie kodu Side-by-Side między usługą Amazon DynamoDB, a Azure Cosmos DB odpowiednikiem fragmentu kodu.

Aby pobrać kod źródłowy, Sklonuj następujące repozytorium:

```bash
git clone https://github.com/Azure-Samples/DynamoDB-to-CosmosDB
```

### <a name="pre-requisites"></a>Wymagania wstępne

-  .NET Framework 4.7.2
- Visual Studio 2019
- Dostęp do Azure Cosmos DB konta interfejsu API SQL
- Lokalna instalacja usługi Amazon DynamoDB
- Java 8
- Uruchom pobieraną wersję usługi Amazon DynamoDB na porcie 8000 (możesz zmienić i skonfigurować kod)

### <a name="set-up-your-code"></a>Konfigurowanie kodu

Dodaj następujący "pakiet NuGet" do projektu:

```bash
Install-Package Microsoft.Azure.Cosmos 
```

### <a name="establish-connection"></a>Ustanów połączenie

**DynamoDB**:

W programie Amazon DynamoDB następujący kod jest używany do nawiązania połączenia:

```csharp
    AmazonDynamoDBConfig addbConfig = new AmazonDynamoDBConfig() ;
        addbConfig.ServiceURL = "endpoint";
        try { aws_dynamodbclient = new AmazonDynamoDBClient( addbConfig ); }
```

**Azure Cosmos DB**:

Aby połączyć Azure Cosmos DB, zaktualizuj kod do:

```csharp
client_documentDB = new CosmosClient("your connectionstring from the Azure portal");
```

**Optymalizowanie połączenia w Azure Cosmos DB**

Za pomocą Azure Cosmos DB można zoptymalizować połączenie przy użyciu następujących opcji:

* **Connectionmode** — Użyj trybu połączenia bezpośredniego, aby nawiązać połączenie z węzłami danych w usłudze Azure Cosmos DB. Tryb bramy służy tylko do inicjowania i buforowania adresów logicznych oraz do odświeżania w ramach aktualizacji. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [trybów łączności](sql-sdk-connection-modes.md) .

* **ApplicationRegion** — ta opcja służy do ustawiania preferowanego regionu geograficznego, który jest używany do współpracy z Azure Cosmos DB. Aby dowiedzieć się więcej, zobacz artykuł dotyczący [dystrybucji globalnej](distribute-data-globally.md) .

* **ConsistencyLevel** — ta opcja służy do przesłonięcia domyślnego poziomu spójności. Aby dowiedzieć się więcej, zobacz artykuł [poziomy spójności](consistency-levels.md) .

* **BulkExecutionMode** — ta opcja służy do wykonywania operacji zbiorczych przez ustawienie właściwości *AllowBulkExecution* na true. Aby dowiedzieć się więcej, zobacz artykuł [import zbiorczy](tutorial-sql-api-dotnet-bulk-import.md) .

   ```csharp
   client_cosmosDB = new CosmosClient(" Your connection string ",new CosmosClientOptions()
   { 
    ConnectionMode=ConnectionMode.Direct,
    ApplicationRegion=Regions.EastUS2,
    ConsistencyLevel=ConsistencyLevel.Session,
    AllowBulkExecution=true  
   });
   ```

### <a name="provision-the-container"></a>Inicjowanie obsługi kontenera

**DynamoDB**:

Aby przechowywać dane w usłudze Amazon DynamoDB, musisz najpierw utworzyć tabelę. W tym procesie definiujesz schemat, typ klucza i atrybuty, jak pokazano w poniższym kodzie:

```csharp
// movies_key_schema
public static List<KeySchemaElement> movies_key_schema
  = new List<KeySchemaElement>
{
  new KeySchemaElement
  {
    AttributeName = partition_key_name,
    KeyType = "HASH"
  },
  new KeySchemaElement
  {
    AttributeName = sort_key_name,
    KeyType = "RANGE"
  }
};

// key names for the Movies table
public const string partition_key_name = "year";
public const string sort_key_name      = "title";
  public const int readUnits=1, writeUnits=1; 

    // movie_items_attributes
    public static List<AttributeDefinition> movie_items_attributes
  = new List<AttributeDefinition>
{
  new AttributeDefinition
  {
    AttributeName = partition_key_name,
    AttributeType = "N"
  },
  new AttributeDefinition
  {
    AttributeName = sort_key_name,
    AttributeType = "S"
  }

CreateTableRequest  request;
CreateTableResponse response;

// Build the 'CreateTableRequest' structure for the new table
request = new CreateTableRequest
{
  TableName             = table_name,
  AttributeDefinitions  = table_attributes,
  KeySchema             = table_key_schema,
  // Provisioned-throughput settings are always required,
  // although the local test version of DynamoDB ignores them.
  ProvisionedThroughput = new ProvisionedThroughput( readUnits, writeUnits );
};
```

**Azure Cosmos DB**:

W usłudze Amazon DynamoDB należy zainicjować obsługę odczytu jednostek obliczeniowych & pisać jednostki obliczeniowe. W Azure Cosmos DB określić przepływność jako [jednostki żądań (ru/s)](request-units.md), które mogą być dynamicznie używane dla każdej operacji. Dane są zorganizowane jako baza danych > kontener — > elementu. Można określić przepływność na poziomie bazy danych lub na poziomie kolekcji lub obu.

Aby utworzyć bazę danych:

```csharp
await client_cosmosDB.CreateDatabaseIfNotExistsAsync(movies_table_name);
```

Aby utworzyć kontener:

```csharp
await cosmosDatabase.CreateContainerIfNotExistsAsync(new ContainerProperties() { PartitionKeyPath = "/" + partitionKey, Id = new_collection_name }, provisionedThroughput);
```

### <a name="load-the-data"></a>Ładowanie danych

**DynamoDB**:

Poniższy kod przedstawia sposób ładowania danych w usłudze Amazon DynamoDB. MoviesArray składa się z listy dokumentów JSON, a następnie należy wykonać iterację i załadować dokument JSON do Amazon DynamoDB:

```csharp
int n = moviesArray.Count;
for( int i = 0, j = 99; i < n; i++ )
    {
  try
  {
    string itemJson = moviesArray[i].ToString();
    Document doc = Document.FromJson(itemJson);
    Task putItem = moviesTable.PutItemAsync(doc);
    if( i >= j )
    {
      j++;
      Console.Write( "{0,5:#,##0}, ", j );
      if( j % 1000 == 0 )
        Console.Write( "\n " );
      j += 99;
    }
    await putItem;
```

**Azure Cosmos DB**:

W Azure Cosmos DB można wybrać opcję Prześlij strumieniowo i napisać polecenie `moviesContainer.CreateItemStreamAsync()` . Jednak w tym przykładzie kod JSON zostanie rozszeregowany do typu *MovieModel* w celu przedstawienia funkcji rzutowania typu. Kod jest wielowątkowy, co spowoduje użycie rozproszonej architektury Azure Cosmos DB i przyspieszenie ładowania:

```csharp
List<Task> concurrentTasks = new List<Task>();
for (int i = 0, j = 99; i < n; i++)
{
  try
  {
      MovieModel doc= JsonConvert.DeserializeObject<MovieModel>(moviesArray[i].ToString());
      doc.Id = Guid.NewGuid().ToString();
      concurrentTasks.Add(moviesContainer.CreateItemAsync(doc,new PartitionKey(doc.Year)));
      {
          j++;
          Console.Write("{0,5:#,##0}, ", j);
          if (j % 1000 == 0)
              Console.Write("\n               ");
          j += 99;
      }
      
  }
  catch (Exception ex)
  {
      Console.WriteLine("\n     ERROR: Could not write the movie record #{0:#,##0}, because:\n       {1}",
                          i, ex.Message);
      operationFailed = true;
      break;
  }
}
await Task.WhenAll(concurrentTasks);
```

### <a name="create-a-document"></a>Tworzenie dokumentu

**DynamoDB**:

Pisanie nowego dokumentu w usłudze Amazon DynamoDB nie jest bezpieczne, w poniższym przykładzie używa się newItem jako typu dokumentu:

```csharp
Task<Document> writeNew = moviesTable.PutItemAsync(newItem, token);
await writeNew;
```

**Azure Cosmos DB**:

Azure Cosmos DB zapewnia bezpieczeństwo typów za pośrednictwem modelu danych. Korzystamy z modelu danych o nazwie "MovieModel":

```csharp
public class MovieModel
{
    [JsonProperty("id")]
    public string Id { get; set; }
    [JsonProperty("title")]
    public string Title{ get; set; }
    [JsonProperty("year")]
    public int Year { get; set; }
    public MovieModel(string title, int year)
    {
        this.Title = title;
        this.Year = year;
    }
    public MovieModel()
    {

    }
    [JsonProperty("info")]
    public   MovieInfo MovieInfo { get; set; }

    internal string PrintInfo()
    {
        if(this.MovieInfo!=null)
        return            string.Format("\nMovie with title:{1}\n Year: {2}, Actors: {3}\n Directors:{4}\n Rating:{5}\n", this.Id, this.Title, this.Year, String.Join(",",this.MovieInfo.Actors), this.MovieInfo, this.MovieInfo.Rating);
        else
            return string.Format("\nMovie with  title:{0}\n Year: {1}\n",  this.Title, this.Year);
    }
}
```

W Azure Cosmos DB newItem będzie MovieModel:

```csharp
 MovieModel movieModel = new MovieModel()
            {
                Id = Guid.NewGuid().ToString(),
                Title = "The Big New Movie",
                Year = 2018,
                MovieInfo = new MovieInfo() { Plot = "Nothing happens at all.", Rating = 0 }
            };
    var writeNew= moviesContainer.CreateItemAsync(movieModel, new Microsoft.Azure.Cosmos.PartitionKey(movieModel.Year));
    await writeNew;
```

### <a name="read-a-document"></a>Odczytaj dokument

**DynamoDB**:

Aby zapoznać się z usługą Amazon DynamoDB, należy zdefiniować elementy podstawowe:

```csharp
// Create Primitives for the HASH and RANGE portions of the primary key
Primitive hash = new Primitive(year.ToString(), true);
Primitive range = new Primitive(title, false);

  Task<Document> readMovie = moviesTable.GetItemAsync(hash, range, token);
  movie_record = await readMovie;
```

**Azure Cosmos DB**:

Jednak w przypadku Azure Cosmos DB kwerenda jest naturalna (LINQ):

```csharp
IQueryable<MovieModel> movieQuery = moviesContainer.GetItemLinqQueryable<MovieModel>(true)
                        .Where(f => f.Year == year && f.Title == title);
// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
    foreach (MovieModel movie in movieQuery)
    {
      movie_record_cosmosdb = movie;
    }
```

Kolekcja dokumentów w powyższym przykładzie będzie:

- typ bezpieczny
- Podaj opcję naturalnego zapytania.

### <a name="update-an-item"></a>Aktualizowanie elementu

**DynamoDB**: Aby zaktualizować element w usłudze Amazon DynamoDB:

```csharp
updateResponse = await client.UpdateItemAsync( updateRequest );
````

**Azure Cosmos DB**:

W Azure Cosmos DB aktualizacja będzie traktowana jako operacja Upsert, co oznacza, że jeśli nie istnieje, należy wstawić dokument:

```csharp
await moviesContainer.UpsertItemAsync<MovieModel>(updatedMovieModel);
```

### <a name="delete-a-document"></a>Usuwanie dokumentu

**DynamoDB**:

Aby usunąć element z usługi Amazon DynamoDB, należy ponownie podzielić na elementy podstawowe:

```csharp
Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);
      DeleteItemOperationConfig deleteConfig = new DeleteItemOperationConfig( );
      deleteConfig.ConditionalExpression = condition;
      deleteConfig.ReturnValues = ReturnValues.AllOldAttributes;
      
  Task<Document> delItem = table.DeleteItemAsync( hash, range, deleteConfig );
        deletedItem = await delItem;
```

**Azure Cosmos DB**:

W Azure Cosmos DB można pobrać dokument i usunąć go asynchronicznie:

```csharp
var result= ReadingMovieItem_async_List_CosmosDB("select * from c where c.info.rating>7 AND c.year=2018 AND c.title='The Big New Movie'");
while (result.HasMoreResults)
{
  var resultModel = await result.ReadNextAsync();
  foreach (var movie in resultModel.ToList<MovieModel>())
  {
    await moviesContainer.DeleteItemAsync<MovieModel>(movie.Id, new PartitionKey(movie.Year));
  }
  }
```

### <a name="query-documents"></a>Dokumenty z zapytaniami

**DynamoDB**:

W usłudze Amazon DynamoDB wymagane są funkcje API do wykonywania zapytań dotyczących danych:

```csharp
QueryOperationConfig config = new QueryOperationConfig( );
  config.Filter = new QueryFilter( );
  config.Filter.AddCondition( "year", QueryOperator.Equal, new DynamoDBEntry[ ] { 1992 } );
  config.Filter.AddCondition( "title", QueryOperator.Between, new DynamoDBEntry[ ] { "B", "Hzz" } );
  config.AttributesToGet = new List<string> { "year", "title", "info" };
  config.Select = SelectValues.SpecificAttributes;
  search = moviesTable.Query( config ); 
```

**Azure Cosmos DB**:

W Azure Cosmos DB można wykonać projekcję i filtrowanie wewnątrz prostej kwerendy SQL:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.Year, c.Title, c.info from c where Year=1998 AND (CONTAINS(Title,'B') OR CONTAINS(Title,'Hzz'))");
```

Dla operacji zakresu, na przykład "Between", należy wykonać skanowanie w usłudze Amazon DynamoDB:

```csharp
ScanRequest sRequest = new ScanRequest
{
  TableName = "Movies",
  ExpressionAttributeNames = new Dictionary<string, string>
  {
    { "#yr", "year" }
  },
  ExpressionAttributeValues = new Dictionary<string, AttributeValue>
  {
      { ":y_a", new AttributeValue { N = "1960" } },
      { ":y_z", new AttributeValue { N = "1969" } },
  },
  FilterExpression = "#yr between :y_a and :y_z",
  ProjectionExpression = "#yr, title, info.actors[0], info.directors, info.running_time_secs"
};

ClientScanning_async( sRequest ).Wait( );
```

W Azure Cosmos DB można użyć zapytania SQL i jednowierszowej instrukcji:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.title, c.info.actors[0], c.info.directors,c.info.running_time_secs from c where BETWEEN year 1960 AND 1969");
```

### <a name="delete-a-container"></a>Usuwanie kontenera

**DynamoDB**:

Aby usunąć tabelę z usługi Amazon DynamoDB, można określić:

```csharp
client.DeleteTableAsync( tableName );
```

**Azure Cosmos DB**:

Aby usunąć kolekcję w Azure Cosmos DB, można określić:

```csharp
await moviesContainer.DeleteContainerAsync();
```
Następnie usuń niezbędną bazę danych:

```csharp
await cosmosDatabase.DeleteAsync();
```

Jak widać, Azure Cosmos DB obsługuje zapytania naturalne (SQL), operacje są asynchroniczne i znacznie łatwiejsze. Można z łatwością migrować kod złożony do Azure Cosmos DB, który będzie prostszy po migracji.

### <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [optymalizacji wydajności](performance-tips.md).
- Dowiedz się więcej na temat [optymalizowania odczytów i zapisów](key-value-store-cost.md)
- Informacje o [monitorowaniu w Cosmos DB](monitor-cosmos-db.md)

