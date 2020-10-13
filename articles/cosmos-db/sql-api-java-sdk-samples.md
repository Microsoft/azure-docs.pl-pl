---
title: 'Azure Cosmos DB SQL API: przykłady dla języka Java SDK v4'
description: Znajdź w witrynie GitHub przykłady kodu w języku Java służące do wykonywania typowych zadań przy użyciu interfejsu API SQL w usłudze Azure Cosmos DB, w tym operacji CRUD.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: 8910983ea5946c88d3d560c0cf0a321f8bd2c4ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569967"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>Azure Cosmos DB SQL API: przykłady dla języka Java SDK v4

> [!div class="op_single_selector"]
> * [Przykłady zestawu SDK dla platformy .NET V2](sql-api-dotnet-samples.md)
> * [Przykłady zestawu SDK dla platformy .NET v3](sql-api-dotnet-v3sdk-samples.md)
> * [Przykłady zestawu SDK dla języka Java v4](sql-api-java-sdk-samples.md)
> * [Przykłady zestawu SDK dla danych wiosennych v3](sql-api-spring-data-sdk-samples.md)
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
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account primary key
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

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie bazy danych](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L77-L85) | CosmosClient. createDatabaseIfNotExists |
| [Odczytywanie bazy danych na podstawie identyfikatora](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L88-L95) | CosmosClient. getdatabase |
| [Odczytywanie wszystkich baz danych](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L98-L112) | CosmosClient.readAllDatabases |
| [Usuwanie bazy danych](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L115-L123) | CosmosDatabase. Delete |

## <a name="collection-examples"></a>Przykłady dotyczące kolekcji
Plik [przykładów CRUD kolekcji](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat kolekcji usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) .

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie kolekcji](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L97-L112) | CosmosDatabase. createContainerIfNotExists |
| [Zmienianie skonfigurowanej wydajności kolekcji](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L115-L123) | CosmosContainer.replaceProvisionedThroughput |
| [Pobieranie kolekcji na podstawie identyfikatora](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L126-L133) | CosmosDatabase. getcontainerer |
| [Odczytywanie wszystkich kolekcji w bazie danych](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L136-L150) | CosmosDatabase.readAllContainers |
| [Usuwanie kolekcji](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L153-L161) | CosmosContainer. Delete |

## <a name="autoscale-collection-examples"></a>Przykłady kolekcji automatycznego skalowania

Aby dowiedzieć się więcej na temat automatycznego skalowania przed uruchomieniem tych przykładów, zapoznaj się z instrukcjami dotyczącymi włączania automatycznego skalowania na [koncie](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/) i w [bazach danych i kontenerach](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autoscale).

Plik [przykładów CRUD bazy danych skalowania automatycznego](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java) przedstawia sposób wykonywania następujących zadań.

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Utwórz bazę danych o określonej maksymalnej przepływności skalowania automatycznego](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L78-L89) | CosmosClient. isdatabase<br>ThroughputProperties.createAutoscaledThroughput |

Plik [przykładów CRUD kolekcji skalowania automatycznego](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java) pokazuje, jak wykonać poniższe zadania. 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie kolekcji z określoną maksymalną przepływność skalowania automatycznego](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L97-L110) | CosmosDatabase. createContainerIfNotExists |
| [Zmiana skonfigurowanej maksymalnej przepływności kolekcji](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L113-L120) | CosmosContainer.replaceThroughput |
| [Odczytywanie konfiguracji automatycznego przepływności kolekcji](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L122-L133) | CosmosContainer.readThroughput |

## <a name="analytical-storage-collection-examples"></a>Przykłady kolekcji magazynu analitycznego

Plik [przykładów kolekcji magazynu analitycznego CRUD](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat kolekcji usługi Azure Cosmos przed uruchomieniem poniższych przykładów, przeczytaj temat Azure Cosmos DB Synapse i magazyn analityczny.

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie kolekcji](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L93-L108) | CosmosDatabase. createContainerIfNotExists |

## <a name="document-examples"></a>Przykłady dotyczące dokumentów
Plik [przykłady dokumentu CRUD](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej o dokumentach usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Praca z bazami danych, kontenerami i elementami](databases-containers-items.md) .

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie dokumentu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L133-L147) | CosmosContainer. SetItem |
| [Odczytaj dokument według identyfikatora](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L179-L193) | CosmosContainer.readItem |
| [Wykonywanie zapytań dotyczących dokumentów](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176) | CosmosContainer.queryItems |
| [Zastępowanie dokumentu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L195-L210) | CosmosContainer.replaceItem |
| [Wykonywanie operacji upsert dla dokumentu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L212-L2225) | CosmosContainer.upsertItem |
| [Usuwanie dokumentu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L303-L310) | CosmosContainer.deleteItem |
| [Zastąp dokument elementem warunkowego sprawdzania elementu ETag](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L227-L264) | AccessCondition. SetType<br>AccessCondition. setcondition |
| [Odczytywanie dokumentu tylko wtedy, gdy został zmieniony](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L266-L300) | AccessCondition. SetType<br>AccessCondition. setcondition |

## <a name="indexing-examples"></a>Przykłady dotyczące indeksowania
Plik [przykładów CRUD kolekcji](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat indeksowania Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [zasady indeksowania](index-policy.md), [typy indeksowania](index-types.md)i [ścieżki indeksowania](index-paths.md) artykuły koncepcyjne. 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| Wyłączanie dokumentu z indeksu | ExcludedIndex<br>IndexingPolicy |
| Użyj indeksowania z opóźnieniem | IndexingPolicy. Indexing |
| [Uwzględnij określone ścieżki dokumentów w indeksie](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L145-L148) | IndexingPolicy.IncludedPaths |
| [Wyklucz określone ścieżki dokumentów z indeksu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L150-L153) | IndexingPolicy.ExcludedPaths |
| [Tworzenie indeksu złożonego](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L171-L186) | IndexingPolicy.setCompositeIndexes<br>CompositePath |
| Wymuszanie operacji skanowania zakresu na ścieżce z indeksem skrótu | FeedOptions.EnableScanInQuery |
| Używaj indeksów zakresu dla ciągów | IndexingPolicy.IncludedPaths<br>RangeIndex |
| Przekształcanie indeksu | - |
| [Tworzenie indeksu geoprzestrzennego](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L157-L166) | IndexingPolicy.setSpatialIndexes<br>SpatialSpec<br>Przestrzennytype |

Aby uzyskać więcej informacji na temat indeksowania, zobacz [Zasady indeksowania w usłudze Azure Cosmos DB](index-policy.md).

## <a name="query-examples"></a>Przykłady zapytań
Plik [przykładów zapytania](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java) przedstawia sposób wykonywania następujących zadań przy użyciu gramatyki zapytań SQL. Aby dowiedzieć się więcej na temat odwołania do zapytań SQL w Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [przykłady zapytań SQL dotyczących Azure Cosmos DB](how-to-sql-query.md). 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Wykonywanie zapytań dotyczących wszystkich dokumentów](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L210-L214) | CosmosContainer.queryItems |
| [Wykonywanie zapytań dotyczących równości przy użyciu operatora ==](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L291-L295) | CosmosContainer.queryItems |
| [Wykonywanie zapytań dotyczących nierówności przy użyciu operatorów != i NOT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L297-L305) | CosmosContainer.queryItems |
| [Wykonywanie zapytań przy użyciu operatorów zakresu, takich jak >, <, >=, <=](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L307-L312) | CosmosContainer.queryItems |
| [Wykonywanie zapytań przy użyciu operatorów zakresu względem ciągów](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L314-L319) | CosmosContainer.queryItems |
| [Zapytanie z KOLEJNOŚCIą według](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L321-L326) | CosmosContainer.queryItems |
| [Zapytanie z instrukcją DISTINCT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L328-L333) | CosmosContainer.queryItems |
| [Zapytanie z funkcjami agregującymi](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L335-L343) | CosmosContainer.queryItems |
| [Praca z dokumentami podrzędnymi](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L345-L353) | CosmosContainer.queryItems |
| [Wykonywanie zapytań przy użyciu sprzężeń wewnątrz dokumentu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L355-L377) | CosmosContainer.queryItems |
| [Zapytanie z operatorami String, Math i Array](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L379-L390) | CosmosContainer.queryItems |
| [Wykonywanie zapytań ze sparametryzowanym kodem SQL przy użyciu elementu SqlQuerySpec](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421) |CosmosContainer.queryItems |
| [Wykonywanie zapytań przy użyciu stronicowania jawnego](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216-L266) | CosmosContainer.queryItems |
| [Równoległe wykonywanie zapytań o kolekcje partycjonowane](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L268-L289) | CosmosContainer.queryItems |
| Zapytanie z KOLEJNOŚCIą według dla kolekcji partycjonowanych | CosmosContainer.queryItems |

## <a name="change-feed-examples"></a>Przykłady zestawienia zmian 
[Przykładowy plik procesora źródła zmian](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat źródła zmian w Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [odczytywanie Azure Cosmos DB Zmienianie źródła danych](read-change-feed.md) i [Zmienianie procesora](change-feed-processor.md).

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Podstawowe funkcje kanału informacyjnego zmiany](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) |ChangeFeedProcessor.changeFeedProcessorBuilder |
| Odczytaj Źródło zmian z określonego czasu | ChangeFeedProcessor.changeFeedProcessorBuilder |
| [Odczytaj Źródło zmian od początku](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) | - |

## <a name="server-side-programming-examples"></a>Przykłady programowania po stronie serwera

[Przykładowy plik procedury składowanej](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java) pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat programowania po stronie serwera w Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika](stored-procedures-triggers-udfs.md).

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie procedury składowanej](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L132-L151) | CosmosScripts.createStoredProcedure |
| [Wykonywanie procedury składowanej](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L167-L181) | CosmosStoredProcedure.exeurocze |
| [Usuwanie procedury składowanej](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L183-L193) | CosmosStoredProcedure. Delete |

## <a name="user-management-examples"></a>Przykłady zarządzania użytkownikami
Przykładowy plik zarządzania użytkownikami przedstawia sposób wykonywania następujących zadań:

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| Tworzenie użytkownika | - |
| Ustawianie uprawnień w kolekcji lub dokumencie | - |
| Pobieranie listy uprawnień użytkownika |- |