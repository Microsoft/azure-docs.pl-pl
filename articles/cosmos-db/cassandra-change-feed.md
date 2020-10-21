---
title: Źródło zmian w interfejsie API Azure Cosmos DB dla Cassandra
description: Dowiedz się, jak używać kanału informacyjnego zmian w interfejsie API Azure Cosmos DB Cassandra, aby uzyskać zmiany wprowadzone w danych.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: 1b2e94bfe1bef9ecdeaa4b2b84224967bb1c7741
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92281584"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Źródło zmian w interfejsie API Azure Cosmos DB dla Cassandra

Obsługa [kanału informacyjnego zmian](change-feed.md) w interfejsie API Azure Cosmos DB dla Cassandra jest dostępna za pomocą predykatów zapytań w języku zapytań CASSANDRA (CQL). Za pomocą tych warunków predykatu można wysyłać zapytania do interfejsu API źródła zmian. Aplikacje mogą pobrać zmiany wprowadzone do tabeli przy użyciu klucza podstawowego (zwanego również kluczem partycji), jak jest to wymagane w CQL. Następnie można wykonać dalsze czynności na podstawie wyników. Zmiany w wierszach w tabeli są przechwytywane w kolejności ich modyfikacji i kolejności sortowania na klucz partycji.

Poniższy przykład pokazuje, jak uzyskać Źródło zmian dla wszystkich wierszy w interfejs API Cassandra tabeli przestrzeni kluczy przy użyciu platformy .NET. Predykat COSMOS_CHANGEFEED_START_TIME () jest używany bezpośrednio w CQL do wykonywania zapytań o elementy ze źródła zmian od określonego czasu rozpoczęcia (w tym przypadku Current DateTime). Możesz pobrać pełny przykład dla języka C# [tutaj](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/) i dla środowiska Java [tutaj](https://github.com/Azure-Samples/cosmos-changefeed-cassandra-java).

W każdej iteracji zapytanie zostaje wznowione w ostatnim momencie odczytu, przy użyciu stanu stronicowania. W obszarze kluczy można zobaczyć ciągły strumień nowych zmian w tabeli. Zobaczymy zmiany w wierszach, które są wstawiane lub aktualizowane. Obserwowanie operacji usuwania przy użyciu źródła zmian w interfejs API Cassandra nie jest obecnie obsługiwane.

# <a name="java"></a>[Java](#tab/java)

```java
        Session cassandraSession = utils.getSession();

        try {
              DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");  
               LocalDateTime now = LocalDateTime.now().minusHours(6).minusMinutes(30);  
               String query="SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME()='" 
                    + dtf.format(now)+ "'";
               
             byte[] token=null; 
             System.out.println(query); 
             while(true)
             {
                 SimpleStatement st=new  SimpleStatement(query);
                 st.setFetchSize(100);
                 if(token!=null)
                     st.setPagingStateUnsafe(token);
                 
                 ResultSet result=cassandraSession.execute(st) ;
                 token=result.getExecutionInfo().getPagingState().toBytes();
                 
                 for(Row row:result)
                 {
                     System.out.println(row.getString("user_name"));
                 }
             }
                    

        } finally {
            utils.close();
            LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL");
        }

```

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```
---

Aby uzyskać zmiany w pojedynczym wierszu według klucza podstawowego, można dodać klucz podstawowy w zapytaniu. Poniższy przykład pokazuje, jak śledzić zmiany w wierszu, w którym "user_id = 1"

# <a name="c"></a>[C#](#tab/csharp)

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

# <a name="java"></a>[Java](#tab/java)

```java
    String query="SELECT * FROM uprofile.user where user_id=1 and COSMOS_CHANGEFEED_START_TIME()='" 
                    + dtf.format(now)+ "'";
    SimpleStatement st=new  SimpleStatement(query);
```
---
## <a name="current-limitations"></a>Bieżące ograniczenia

W przypadku korzystania z kanału informacyjnego zmian z interfejs API Cassandra są stosowane następujące ograniczenia:

* Operacje INSERT i Update są obecnie obsługiwane. Operacja usuwania nie jest jeszcze obsługiwana. Aby obejść ten element, można dodać znacznik elastyczny do wierszy, które są usuwane. Na przykład Dodaj pole w wierszu o nazwie "usunięte" i ustaw je na wartość "true".
* Ostatnia aktualizacja jest utrwalona, ponieważ w podstawowym interfejsie API SQL Server nie są dostępne aktualizacje pośrednie.


## <a name="error-handling"></a>Obsługa błędów

Następujące kody błędów i komunikaty są obsługiwane w przypadku używania źródła zmian w interfejs API Cassandra:

* **Kod błędu HTTP 429** — gdy kanał informacyjny zmiany ma ograniczoną szybkość, zwraca pustą stronę.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie zasobami interfejs API Cassandra Azure Cosmos DB przy użyciu szablonów Azure Resource Manager](manage-cassandra-with-resource-manager.md)
