---
title: 'Azure Cosmos DB SQL API: przykłady dla języka Java SDK v4'
description: Znajdź w witrynie GitHub przykłady kodu w języku Java służące do wykonywania typowych zadań przy użyciu interfejsu API SQL w usłudze Azure Cosmos DB, w tym operacji CRUD.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: db291b735ed6456a007446ad23373ff25e806aab
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727167"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>Azure Cosmos DB SQL API: przykłady dla języka Java SDK v4

> [!div class="op_single_selector"]
> * [Przykłady zestawu SDK dla platformy .NET V2](sql-api-dotnet-samples.md)
> * [Przykłady zestawu SDK dla platformy .NET v3](sql-api-dotnet-v3sdk-samples.md)
> * [Przykłady zestawu SDK dla języka Java v4](sql-api-java-sdk-samples.md)
> * [Przykłady dla platformy Node.js](sql-api-nodejs-samples.md)
> * [Przykłady języka Python](sql-api-python-samples.md)
> * [Galeria przykładów kodu dla platformy Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> Aby dowiedzieć się więcej na temat zestawu Java SDK v4, Azure Cosmos DB Zobacz informacje o [wersji](sql-api-sdk-java-v4.md)zestawu [Java SDK v4](performance-tips-java-sdk-v4-sql.md), [repozytorium Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB zestawu Java SDK 4 Azure Cosmos DB — [Przewodnik rozwiązywania problemów](troubleshoot-java-sdk-v4-sql.md) , aby uzyskać więcej informacji. Jeśli obecnie używasz starszej wersji niż v4, zapoznaj się z przewodnikiem [Migrowanie do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) , aby uzyskać pomoc w uaktualnianiu do wersji v4.
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- Możesz [aktywować korzyści subskrybenta programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): w ramach subskrypcji programu Visual Studio co miesiąc otrzymasz środki, które można przeznaczyć na płatne usługi platformy Azure.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

Najnowsze przykładowe aplikacje, które wykonują operacje CRUD i innych typowych operacji dotyczących zasobów Azure Cosmos DB są zawarte w repozytorium GitHub [Azure-Cosmos-Java-SQL-API-Samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) . Ten artykuł zawiera:

* Linki do zadań w poszczególnych plikach projektów przykładowych w języku Java. 
* Linki do powiązanej dokumentacji interfejsu API.

**Wymagania wstępne**

Do uruchomienia tej aplikacji przykładowej potrzebne są następujące elementy:

* Zestaw Java Development Kit 8
* Azure Cosmos DB Java SDK v4

Opcjonalnie możesz użyć Maven, aby uzyskać Azure Cosmos DB najnowsze pliki binarne zestawu Java SDK v4 do użycia w projekcie. Rozwiązanie Maven automatycznie dodaje wszystkie wymagane zależności. Innym sposobem jest bezpośrednie pobranie zależności wymienionych w pliku pom.xml i dodanie ich do ścieżki kompilacji.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Uruchamianie aplikacji przykładowych**

Sklonuj repozytorium przykładowe:
```bash
$ git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples.git

$ cd azure-cosmos-java-sql-api-samples
```

Możesz uruchomić próbki przy użyciu środowiska IDE (zaćmienie, IntelliJ lub programu VSCODE) lub z wiersza polecenia przy użyciu Maven.

Należy ustawić te zmienne środowiskowe

```
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account master key
```

w celu udzielenia przykładom dostępu do odczytu/zapisu do Twojego konta.

Aby uruchomić przykład, określ jego klasę główną 

```
com.azure.cosmos.examples.sample.synchronicity.MainClass
```

gdzie *Sample. Synchronicity. MainClass* może być
* crudquickstart. Sync. SampleCRUDQuickstart
* crudquickstart. Async. SampleCRUDQuickstartAsync
* indexmanagement. Sync. SampleIndexManagement
* indexmanagement. Async. SampleIndexManagementAsync
* StoredProcedure. Sync. SampleStoredProcedure
* StoredProcedure. Async. SampleStoredProcedureAsync
* changefeed. SampleChangeFeedProcessor *(Changefeed ma tylko próbkę asynchroniczną, brak próbki synchronizacji)* ... itp...

> [!NOTE]
> Przykłady są niezależne — każdy z nich jest automatycznie konfigurowany i automatycznie czyszczony po użyciu. Przykłady generują wiele wywołań, aby utworzyć `CosmosContainer` . Za każdym razem w ramach subskrypcji jest naliczana opłata za 1 godzinę użycia warstwy wydajności, w której jest tworzona kolekcja. 
> 
> 

## <a name="database-examples"></a>Przykłady dotyczące baz danych
Plik [przykładów bazy danych CRUD](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat baz danych usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) . 

| Zadanie | Odwołanie API |
| --- | --- |
| [Tworzenie bazy danych](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L76-L84) | CosmosClient. createDatabaseIfNotExists |
| [Odczytywanie bazy danych na podstawie identyfikatora](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L86-L94) | CosmosClient. getdatabase |
| [Odczytywanie wszystkich baz danych](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L96-L111) | CosmosClient.readAllDatabases |
| [Usuwanie bazy danych](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L113-L122) | CosmosDatabase. Delete |

## <a name="collection-examples"></a>Przykłady dotyczące kolekcji
Plik [przykładów CRUD kolekcji](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat kolekcji usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) .

| Zadanie | Odwołanie API |
| --- | --- |
| [Tworzenie kolekcji](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L94-106) | CosmosDatabase. createContainerIfNotExists |
| [Zmienianie skonfigurowanej wydajności kolekcji](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L108-116) | CosmosContainer.replaceProvisionedThroughput |
| [Pobieranie kolekcji na podstawie identyfikatora](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L118-126) | CosmosDatabase. getcontainerer |
| [Odczytywanie wszystkich kolekcji w bazie danych](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L128-143) | CosmosDatabase.readAllContainers |
| [Usuwanie kolekcji](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L145-154) | CosmosContainer. Delete |

## <a name="autoscale-collection-examples"></a>Przykłady kolekcji automatycznego skalowania

Aby dowiedzieć się więcej na temat automatycznego skalowania przed uruchomieniem tych przykładów, zapoznaj się z instrukcjami dotyczącymi włączania automatycznego skalowania na [koncie](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/) i w [bazach danych i kontenerach](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autoscale).

Plik [przykładów CRUD bazy danych skalowania automatycznego](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java) przedstawia sposób wykonywania następujących zadań.

| Zadanie | Odwołanie API |
| --- | --- |
| [Utwórz bazę danych o określonej maksymalnej przepływności skalowania automatycznego](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L76-L87) | CosmosClient. isdatabase<br>ThroughputProperties.createAutoscaledThroughput |

Plik [przykładów CRUD kolekcji skalowania automatycznego](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java) pokazuje, jak wykonać poniższe zadania. 

| Zadanie | Odwołanie API |
| --- | --- |
| [Tworzenie kolekcji z określoną maksymalną przepływność skalowania automatycznego](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L95-L107) | CosmosDatabase. createContainerIfNotExists |
| [Zmiana skonfigurowanej maksymalnej przepływności kolekcji](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L109-L117) | CosmosContainer.replaceThroughput |
| [Odczytywanie konfiguracji automatycznego przepływności kolekcji](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L119-L130) | CosmosContainer.readThroughput |

## <a name="analytical-storage-collection-examples"></a>Przykłady kolekcji magazynu analitycznego

Plik [przykładów kolekcji magazynu analitycznego CRUD](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat kolekcji usługi Azure Cosmos przed uruchomieniem poniższych przykładów, przeczytaj temat Azure Cosmos DB Synapse i magazyn analityczny.

| Zadanie | Odwołanie API |
| --- | --- |
| [Tworzenie kolekcji](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L94-L109) | CosmosDatabase. createContainerIfNotExists |

## <a name="document-examples"></a>Przykłady dotyczące dokumentów
Plik [przykłady dokumentu CRUD](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej o dokumentach usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) .

| Zadanie | Odwołanie API |
| --- | --- |
| [Tworzenie dokumentu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L127-L141) | CosmosContainer. SetItem |
| [Odczytaj dokument według identyfikatora](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L143-L154) | CosmosContainer.readItem |
| [Odczytywanie wszystkich dokumentów w kolekcji](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L156-171) | CosmosContainer.readAllItems |
| [Wykonywanie zapytań dotyczących dokumentów](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L173-L187) | CosmosContainer.queryItems |
| [Zastępowanie dokumentu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L189-L204) | CosmosContainer.replaceItem |
| [Wykonywanie operacji upsert dla dokumentu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L206-L219) | CosmosContainer.upsertItem |
| [Usuwanie dokumentu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L302-L310) | CosmosContainer.deleteItem |
| [Zastąp dokument elementem warunkowego sprawdzania elementu ETag](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L221-L261) | AccessCondition. SetType<br>AccessCondition. setcondition |
| [Odczytywanie dokumentu tylko wtedy, gdy został zmieniony](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L263-L300) | AccessCondition. SetType<br>AccessCondition. setcondition |

## <a name="indexing-examples"></a>Przykłady dotyczące indeksowania
Plik [przykładów CRUD kolekcji](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat indeksowania Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [zasady indeksowania](index-policy.md), [typy indeksowania](index-types.md)i [ścieżki indeksowania](index-paths.md) artykuły koncepcyjne. 

| Zadanie | Odwołanie API |
| --- | --- |
| Wyłączanie dokumentu z indeksu | ExcludedIndex<br>IndexingPolicy |
| Użyj indeksowania z opóźnieniem | IndexingPolicy. Indexing |
| [Uwzględnij określone ścieżki dokumentów w indeksie](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L142-L147) | IndexingPolicy.IncludedPaths |
| [Wyklucz określone ścieżki dokumentów z indeksu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L149-L154) | IndexingPolicy.ExcludedPaths |
| [Tworzenie indeksu złożonego](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L170-L188) | IndexingPolicy.setCompositeIndexes<br>CompositePath |
| Wymuszanie operacji skanowania zakresu na ścieżce z indeksem skrótu | FeedOptions.EnableScanInQuery |
| Używaj indeksów zakresu dla ciągów | IndexingPolicy.IncludedPaths<br>RangeIndex |
| Przekształcanie indeksu | - |
| [Tworzenie indeksu geoprzestrzennego](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L156-L168) | IndexingPolicy.setSpatialIndexes<br>SpatialSpec<br>Przestrzennytype |

Aby uzyskać więcej informacji na temat indeksowania, zobacz [Zasady indeksowania w usłudze Azure Cosmos DB](index-policy.md).

## <a name="query-examples"></a>Przykłady zapytań
Plik [przykładów zapytania](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java) przedstawia sposób wykonywania następujących zadań przy użyciu gramatyki zapytań SQL. Aby dowiedzieć się więcej na temat odwołania do zapytań SQL w Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [przykłady zapytań SQL dotyczących Azure Cosmos DB](how-to-sql-query.md). 

| Zadanie | Odwołanie API |
| --- | --- |
| [Wykonywanie zapytań dotyczących wszystkich dokumentów](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L194-L198) | CosmosContainer.queryItems |
| [Wykonywanie zapytań dotyczących równości przy użyciu operatora ==](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L279-183) | CosmosContainer.queryItems |
| [Wykonywanie zapytań dotyczących nierówności przy użyciu operatorów != i NOT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L285-L293) | CosmosContainer.queryItems |
| [Wykonywanie zapytań przy użyciu operatorów zakresu, takich jak >, <, >=, <=](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L295-L300) | CosmosContainer.queryItems |
| [Wykonywanie zapytań przy użyciu operatorów zakresu względem ciągów](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L302-L307) | CosmosContainer.queryItems |
| [Zapytanie z KOLEJNOŚCIą według](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L309-L314) | CosmosContainer.queryItems |
| [Zapytanie z instrukcją DISTINCT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L316-L321) | CosmosContainer.queryItems |
| [Zapytanie z funkcjami agregującymi](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L323-L331) | CosmosContainer.queryItems |
| [Praca z dokumentami podrzędnymi](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L333-L341) | CosmosContainer.queryItems |
| [Wykonywanie zapytań przy użyciu sprzężeń wewnątrz dokumentu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L343-L365) | CosmosContainer.queryItems |
| [Zapytanie z operatorami String, Math i Array](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L367-L378) | CosmosContainer.queryItems |
| [Wykonywanie zapytań ze sparametryzowanym kodem SQL przy użyciu elementu SqlQuerySpec](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L380-L409) |CosmosContainer.queryItems |
| [Wykonywanie zapytań przy użyciu stronicowania jawnego](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L200-L254) | CosmosContainer.queryItems |
| [Równoległe wykonywanie zapytań o kolekcje partycjonowane](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L256-L277) | CosmosContainer.queryItems |
| Zapytanie z KOLEJNOŚCIą według dla kolekcji partycjonowanych | CosmosContainer.queryItems |

## <a name="change-feed-examples"></a>Przykłady zestawienia zmian 
[Przykładowy plik procesora źródła zmian](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat źródła zmian w Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [odczytywanie Azure Cosmos DB Zmienianie źródła danych](read-change-feed.md) i [Zmienianie procesora](change-feed-processor.md).

| Zadanie | Odwołanie API |
| --- | --- |
| [Podstawowe funkcje kanału informacyjnego zmiany](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L50-L120) |ChangeFeedProcessor.changeFeedProcessorBuilder |
| Odczytaj Źródło zmian z określonego czasu | ChangeFeedProcessor.changeFeedProcessorBuilder |
| [Odczytaj Źródło zmian od początku](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L50-L120) | - |

## <a name="server-side-programming-examples"></a>Przykłady programowania po stronie serwera

[Przykładowy plik procedury składowanej](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat programowania po stronie serwera w Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika](stored-procedures-triggers-udfs.md).

| Zadanie | Odwołanie API |
| --- | --- |
| [Tworzenie procedury składowanej](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L124-L143) | CosmosScripts.createStoredProcedure |
| [Wykonywanie procedury składowanej](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L160-L174) | CosmosStoredProcedure. Execute |
| [Usuwanie procedury składowanej](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L176-L186) | CosmosStoredProcedure. Delete |

## <a name="user-management-examples"></a>Przykłady zarządzania użytkownikami
Przykładowy plik zarządzania użytkownikami przedstawia sposób wykonywania następujących zadań:

| Zadanie | Odwołanie API |
| --- | --- |
| Tworzenie użytkownika | - |
| Ustawianie uprawnień w kolekcji lub dokumencie | - |
| Pobieranie listy uprawnień użytkownika |- |