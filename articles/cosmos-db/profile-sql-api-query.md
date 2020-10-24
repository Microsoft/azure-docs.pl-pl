---
title: Pobierz metryki wykonywania & wydajności zapytań SQL
description: Dowiedz się, jak pobrać metryki wykonywania zapytań SQL i profilować wydajność zapytań SQL dotyczących żądań Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/17/2019
ms.author: girobins
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d47bd90f7704cd3c55f9e5d64fe6b58946d4568
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92475094"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>Pobieranie metryk wykonywania zapytań SQL i analizowanie wydajności zapytań przy użyciu zestawu .NET SDK

W tym artykule przedstawiono, jak profilować wydajność zapytań SQL na Azure Cosmos DB. To profilowanie można wykonać przy użyciu `QueryMetrics` pobranego z zestawu .NET SDK i szczegółowo tutaj. [QueryMetrics](/dotnet/api/microsoft.azure.documents.querymetrics) jest obiektem o jednoznacznie określonym typie z informacjami o wykonaniu zapytania wewnętrznej bazy danych. Te metryki są szczegółowo opisane w artykule [dostrajanie wydajności zapytań](./sql-api-query-metrics.md) .

## <a name="set-the-feedoptions-parameter"></a>Ustaw parametr FeedOptions

Wszystkie przeciążenia dla [DocumentClient. CreateDocumentQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentquery) przyjmują opcjonalny parametr [FeedOptions](/dotnet/api/microsoft.azure.documents.client.feedoptions) . Ta opcja umożliwia precyzyjne i sparametryzowane wykonywanie zapytań. 

Aby zebrać metryki wykonywania zapytania SQL, należy ustawić parametr [PopulateQueryMetrics](/dotnet/api/microsoft.azure.documents.client.feedoptions.populatequerymetrics#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) w [FeedOptions](/dotnet/api/microsoft.azure.documents.client.feedoptions) na `true` . Ustawienie `PopulateQueryMetrics` wartości true spowoduje, że `FeedResponse` będzie zawierać odpowiednie `QueryMetrics` . 

## <a name="get-query-metrics-with-asdocumentquery"></a>Pobierz metryki zapytania za pomocą AsDocumentQuery ()
Poniższy przykład kodu pokazuje, jak pobrać metryki przy użyciu metody [AsDocumentQuery ()](/dotnet/api/microsoft.azure.documents.linq.documentqueryable.asdocumentquery) :

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>Agregowanie QueryMetrics

W poprzedniej sekcji należy zauważyć, że wystąpiły wiele wywołań metody [ExecuteNextAsync](/dotnet/api/microsoft.azure.documents.linq.idocumentquery-1.executenextasync) . Każde wywołanie zwróciło `FeedResponse` obiekt, który ma słownik `QueryMetrics` ; jeden dla każdej kontynuacji zapytania. Poniższy przykład pokazuje, jak agregować te elementy `QueryMetrics` przy użyciu LINQ:

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>Grupowanie metryk zapytania według identyfikatora partycji

Można grupować `QueryMetrics` według identyfikatora partycji. Grupowanie według identyfikatora partycji pozwala sprawdzić, czy określona partycja powoduje problemy z wydajnością w porównaniu z innymi. Poniższy przykład pokazuje, jak grupować `QueryMetrics` przy użyciu LINQ:

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>LINQ w DocumentQuery

Możesz również pobrać `FeedResponse` z zapytania LINQ przy użyciu `AsDocumentQuery()` metody:

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>Drogie zapytania

Jednostki żądań używane przez poszczególne zapytania można przechwytywać, aby badać kosztowne zapytania lub zapytania, które zużywają wysoką przepływność. Opłaty za żądania można uzyskać przy użyciu właściwości [RequestCharge](/dotnet/api/microsoft.azure.documents.client.feedresponse-1.requestcharge) w `FeedResponse` . Aby dowiedzieć się więcej o tym, jak uzyskać opłaty za żądanie przy użyciu Azure Portal i różnych zestawów SDK, zobacz artykuł [Znajdowanie opłaty za jednostkę żądania](find-request-unit-charge.md) .

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>Pobierz czas wykonywania zapytania

Podczas obliczania czasu wymaganego do wykonania zapytania po stronie klienta upewnij się, że jest tylko czas wywoływania `ExecuteNextAsync` metody, a nie inne części bazy kodu. Tylko te wywołania pomagają w obliczaniu czasu wykonywania zapytania, jak pokazano w następującym przykładzie:

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>Zapytania skanowania (często powolne i kosztowne)

Zapytanie skanowania odwołuje się do zapytania, które nie zostało obsłużone przez indeks, z powodu załadowania wielu dokumentów przed zwróceniem zestawu wyników.

Poniżej znajduje się przykład zapytania skanowania:

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

Filtr tego zapytania używa górnej funkcji systemu, która nie jest obsługiwana przez indeks. Wykonanie tego zapytania względem dużej kolekcji wygenerowało następujące metryki zapytań podczas pierwszej kontynuacji:

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

Zwróć uwagę na następujące wartości z danych wyjściowych metryk kwerendy:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

To zapytanie załadowało 60 951 dokumentów, które łączą 399 998 938 bajtów. Załadowanie tej wielu bajtów skutkuje wysokim kosztem lub opłatą jednostkową żądania. Wykonanie zapytania jest również czasochłonne, co oznacza, że jest jasne i łączny czas spędzony:

```
Total Query Execution Time               :        4,500.34 milliseconds
```

Oznacza to, że wykonanie zapytania zajęło 4,5 sekund (i była tylko jedną kontynuacją).

Aby zoptymalizować to przykładowe zapytanie, należy unikać używania górnej części filtra. Zamiast tego podczas tworzenia lub aktualizowania dokumentów `c.description` należy wstawiać wszystkie wielkie litery. Następnie zapytanie będzie: 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

To zapytanie jest teraz możliwe do doręczania z indeksu.

Aby dowiedzieć się więcej na temat dostrajania wydajności zapytań, zobacz artykuł [dostrajanie wydajności zapytań](./sql-api-query-metrics.md) .

## <a name="references"></a><a id="References"></a>Dokumentacja

- [Azure Cosmos DB SQL specification (Specyfikacja języka SQL w usłudze Azure Cosmos DB)](./sql-query-getting-started.md)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>Następne kroki

- [Dostosowywanie wydajności zapytań](sql-api-query-metrics.md)
- [Omówienie indeksowania](index-overview.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)