---
title: Azure Cosmos DB Java SDK v4 dla informacji o wersji i zasobów interfejsu API SQL
description: Dowiedz się więcej na temat Azure Cosmos DB Java SDK v4 dla interfejsu API SQL i zestawu SDK, włącznie z datami wydania, datami wycofania i zmianami wprowadzonymi między każdą wersją Azure Cosmos DB asynchronicznej wersji zestawu Java SDK języka SQL.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: ccc872b24c78fbdf6e55673f9d1f78efc0647895
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537888"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for Core (SQL) API: informacje o wersji i zasoby
> [!div class="op_single_selector"]
> * [Zestaw SDK .NET w wersji 3](sql-api-sdk-dotnet-standard.md)
> * [Zestaw .NET SDK w wersji 2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK 2](sql-api-sdk-dotnet-core.md)
> * [Zestaw SDK zmiany źródła .NET w wersji 2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK 4](sql-api-sdk-java-v4.md)
> * [Async Java SDK 2](sql-api-sdk-async-java.md)
> * [Sync Java SDK 2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Wykonawca zbiorczy — .NET V2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonawczy zbiorczej — Java](sql-api-sdk-bulk-executor-java.md)

Azure Cosmos DB Java SDK v4 for Core (SQL) łączy asynchroniczny interfejs API i interfejs API synchronizacji w jeden artefakt Maven. Zestaw SDK v4 oferuje ulepszoną wydajność, nowe funkcje interfejsu API i obsługę asynchroniczną na podstawie aktora projektu i [biblioteki](https://netty.io/)sieci. Użytkownicy mogą oczekiwać zwiększonej wydajności za pomocą Azure Cosmos DB Java SDK v4 a [Azure Cosmos DB ASYNC SDK w wersji 2](sql-api-sdk-async-java.md) i [Azure Cosmos DB synchronizacji Java SDK V2](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Te informacje o wersji dotyczą tylko Azure Cosmos DB Java SDK v4. Jeśli obecnie używasz starszej wersji niż v4, zapoznaj się z przewodnikiem [Migrowanie do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) , aby uzyskać pomoc w uaktualnianiu do wersji v4.
>
> Oto trzy kroki, aby szybko rozpocząć.
> 1. Zainstaluj [minimalny obsługiwany środowisko uruchomieniowe Java, JDK 8,](/java/azure/jdk/?view=azure-java-stable) aby można było użyć zestawu SDK.
> 2. Zapoznaj się z [przewodnikiem Szybki Start dla Azure Cosmos DB Java SDK v4](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) , który uzyskuje dostęp do artefaktu Maven i przeprowadzi Cię przez podstawowe żądania Azure Cosmos DB.
> 3. Przeczytaj Azure Cosmos DB [wskazówki dotyczące wydajności](performance-tips-java-sdk-v4-sql.md) i przewodniki dotyczące [rozwiązywania problemów](troubleshoot-java-sdk-v4-sql.md) z zestawem Java SDK v4, aby zoptymalizować zestaw SDK aplikacji.
>
> [Azure Cosmos DB warsztaty i laboratoria](https://aka.ms/cosmosworkshop) są kolejną doskonałym zasobem do uczenia się, jak używać Azure Cosmos DB Java SDK v4!
>

| |  |
|---|---|
| **Pobieranie zestawu SDK** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Dokumentacja interfejsu API** | [Dokumentacja interfejsu API języka Java](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable) |
|**Współtworzenie zestawu SDK** | [Zestaw Azure SDK dla centralnego repozytorium Java w serwisie GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**Wprowadzenie** | [Szybki Start: Tworzenie aplikacji Java do zarządzania Azure Cosmos DB danych interfejsu API SQL](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) · [Repozytorium GitHub z kodem szybkiego startu](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Podstawowe przykłady kodu** | [Azure Cosmos DB: przykłady języka Java dla interfejsu API SQL](sql-api-java-sdk-samples.md) · [Repozytorium GitHub z przykładowym kodem](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Aplikacja konsolowa ze źródłem zmian**| [Źródło zmian — przykład zestawu SDK dla języka Java w wersji 4](create-sql-api-java-changefeed.md) [Repozytorium GitHub z przykładowym kodem](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Przykład aplikacji sieci Web**| [Tworzenie aplikacji sieci Web za pomocą zestawu Java SDK v4](sql-api-java-application.md) · [Repozytorium GitHub z przykładowym kodem](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Porady dotyczące wydajności**| [Porady dotyczące wydajności dla zestawu Java SDK w wersji 4](performance-tips-java-sdk-v4-sql.md)| 
| **Rozwiązywanie problemów** | [Rozwiązywanie problemów z zestawem Java SDK w wersji 4](troubleshoot-java-sdk-v4-sql.md) |
| **Migrowanie do wersji v4 ze starszego zestawu SDK** | [Migracja do zestawu SDK języka Java w wersji 4](migrate-java-v4-sdk.md) |
| **Minimalne obsługiwane środowisko uruchomieniowe**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB warsztatów i laboratoriów** |[Strona główna Cosmos DB warsztatów](https://aka.ms/cosmosworkshop)

## <a name="release-history"></a>Historia wersji

### <a name="420-2020-07-14"></a>4.2.0 (2020-07-14)
* Dodano interfejs API z włączoną obsługą rejestrowania skryptów do programu `CosmosStoredProcedureRequestOptions` .
* Zaktualizowano `DirectConnectionConfig` wartość domyślną `idleEndpointTimeout` 1 godz, a wartość domyślna `connectTimeout` to 5 s.
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Rozwiązano problem związany z `GatewayConnectionConfig` `idleConnectionTimeout` zastępowaniem `DirectConnectionConfig` `idleConnectionTimeout` .
* Naprawiono `responseContinuationTokenLimitInKb` pobieranie i Ustawianie interfejsów API w programie `CosmosQueryRequestOptions` .
* Rozwiązano problem związany z kwerendą i źródłem zmian podczas ponownego tworzenia kolekcji o tej samej nazwie.
* Rozwiązano problem z najważniejszym zapytaniem zwracającym ClassCastException.
* Rozwiązano problem z kolejnością order by Query zwracającej NullPointerException.
* Rozwiązano problem podczas obsługi anulowanych żądań w trybie bezpośrednim, powodując wywoływanie reaktora `onErrorDropped` . 

### <a name="410-2020-06-25"></a>4.1.0 (2020-06-25)
#### <a name="new-features"></a>Nowe funkcje
* Dodano obsługę `GROUP BY` zapytania.
* Zwiększona wartość domyślna maxConnectionsPerEndpoint do 130 w DirectConnectionConfig.
* Zwiększona wartość domyślna maxRequestsPerConnection do 30 w DirectConnectionConfig.
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Rozwiązano problemy z kwerendą order by, zwracając zduplikowane wyniki podczas wznawiania przy użyciu tokenu kontynuacji. 
* Rozwiązano problemy z kwerendą wartości zwracającej wartości null dla zagnieżdżonego obiektu.
* Naprawiono wyjątek wskaźnika o wartości null w Menedżerze żądań w RntbdClientChannelPool.

### <a name="401-2020-06-10"></a>4.0.1 (2020-06-10)
#### <a name="new-features"></a>Nowe funkcje
* Zmieniono nazwę `QueryRequestOptions` na `CosmosQueryRequestOptions` .
* Zaktualizowano `ChangeFeedProcessorBuilder` do wzorca konstruktora.
* Zaktualizowano `CosmosPermissionProperties` o nową nazwę kontenera i interfejsy API zasobów podrzędnych.
* Dodano więcej próbek & wzbogacone dokumenty do programu `CosmosClientBuilder` . 
* Zaktualizowano `CosmosDatabase`  &  `CosmosContainer` interfejsy API with throughputProperties na potrzeby obsługi automatycznego skalowania/autopilotażu. 
* Zmieniono nazwę `CosmosClientException` na `CosmosException` . 
* Zastąpione `AccessCondition`  &  `AccessConditionType` przez `ifMatchETag()`  &  `ifNoneMatchETag()` interfejsy API. 
* Wszystkie typy zostały scalone `Cosmos*AsyncResponse`  &  `CosmosResponse` z pojedynczym `CosmosResponse` typem.
* Zmieniono nazwę `CosmosResponseDiagnostics` na `CosmosDiagnostics` .  
* Opakowany `FeedResponseDiagnostics` `CosmosDiagnostics` . 
* Usunięto `jackson` zależność od platformy Azure-cosmos & opartej na platformie Azure. 
* Zamieniono `CosmosKeyCredential` na `AzureKeyCredential` Typ. 
* Dodano `ProxyOptions` interfejsy API do programu `GatewayConnectionConfig` . 
* Zaktualizowany zestaw SDK do użycia `Instant` typu zamiast `OffsetDateTime` . 
* Dodano nowy typ wyliczeniowy `OperationKind` . 
* Zmieniono nazwę `FeedOptions` na `QueryRequestOptions` . 
* Dodano `getETag()`  &  `getTimestamp()` interfejsy API do `Cosmos*Properties` typów. 
* Dodano `userAgent` informacje w `CosmosException`  &  `CosmosDiagnostics` . 
* Zaktualizowano znak nowego wiersza w `Diagnostics` systemie do znaku nowego wiersza. 
* Usunięto `readAll*` interfejsy API, należy zamiast tego użyć zapytania zaznacz wszystkie interfejsy API.
* Dodano `ChangeFeedProcessor` interfejs API opóźnienia szacowania.   
* Dodano obsługę funkcji automatycznego skalowania przepływności do zestawu SDK.  
* Zastąpione `ConnectionPolicy` nowymi konfiguracjami połączeń. Uwidocznione `DirectConnectionConfig`  &  `GatewayConnectionConfig` interfejsy API za pośrednictwem `CosmosClientBuilder` programu dla usług bezpośrednie & Konfiguracja połączenia trybu bramy.
* Przeniesiono `JsonSerializable`  &  `Resource` do pakietu implementacji. 
* Dodano `contentResponseOnWriteEnabled` interfejs API do CosmosClientBuilder, który wyłącza pełną zawartość odpowiedzi podczas operacji zapisu.
* Uwidocznione `getETag()` interfejsy API w typach odpowiedzi.
* Przeniesiono `CosmosAuthorizationTokenResolver` do implementacji. 
* Zmieniono nazwę `preferredLocations`  &  `multipleWriteLocations` interfejsu API na `preferredRegions`  &  `multipleWriteRegions` . 
* Zaktualizowano `reactor-core` do wersji 3.3.5. Release, `reactor-netty` Aby 0.9.7. Release & `netty` do 4.1.49. final. 
* Dodano obsługę `analyticalStoreTimeToLive` zestawu SDK.     
* `CosmosClientException`rozszerza `AzureException` . 
* `maxItemCount`  &  `requestContinuationToken` Zamiast tego usunięte interfejsy API z programu `FeedOptions` używają `byPage()` interfejsów API `CosmosPagedFlux`  &  `CosmosPagedIterable` .
* Wprowadzone `CosmosPermissionProperties` na publicznej powierzchni dla `Permission` interfejsów API.
* Usunięty `SqlParameterList` typ & zastępowany przez`List`
* Naprawiono wiele przecieków pamięci w bezpośrednim kliencie TCP. 
* Dodano obsługę `DISTINCT` zapytań. 
* Usunięto zależności zewnętrzne `fasterxml.uuid, guava, commons-io, commons-collection4, commons-text` .  
* Przeniesiono `CosmosPagedFlux`  &  `CosmosPagedIterable` do `utils` pakietu. 
* Zaktualizowano sieci z 4.1.45. Final & reaktora projektu do wersji 3.3.3.
* Zaktualizowano publiczne kontrakty REST do `Final` klas.
* Dodano obsługę zaawansowanej diagnostyki dla operacji punktowych.
* Zaktualizowano pakiet do programu`com.azure.cosmos`
* Dodano `models` pakiet dla umów model/REST
* Dodano `utils` pakiet dla `CosmosPagedFlux`  &  `CosmosPagedIterable` typów. 
* Zaktualizowano publiczne interfejsy API do użycia `Duration` w zestawie SDK.
* Dodano wszystkie kontrakty REST do `models` pakietu.
* `RetryOptions`Zmieniono nazwę na `ThrottlingRetryOptions` .
* Dodano `CosmosPagedFlux`  &  `CosmosPagedIterable` typy stronicowania dla interfejsów API zapytań. 
* Dodano obsługę udostępniania TransportClient w wielu wystąpieniach CosmosClients przy użyciu nowego interfejsu API w`CosmosClientBuilder#connectionSharingAcrossClientsEnabled(true)`
* Optymalizacje zapytań przez usunięcie podwójnej serializacji/deserializacji. 
* Optymalizacje nagłówków odpowiedzi przez usunięcie niepotrzebnego kopiowania z tyłu i do przodu. 
* Optymalizacja `ByteBuffer` serializacji/deserializacji przez usunięcie pośrednich wystąpień ciągu.
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Wyjątek stałego `toString()` wskaźnika ConnectionPolicy o wartości null.
* Rozwiązano problem związany z analizowaniem wyników zapytania w przypadku kolejności wartości według zapytań. 
* Rozwiązano problemy z przeciekiem gniazda przy użyciu bezpośredniego klienta protokołu TCP.
* Naprawiono `orderByQuery` błąd tokenu kontynuacji.
* `ChangeFeedProcessor`Poprawka usterki dla podzielonych partycji &, gdy nie odnaleziono partycji.
* `ChangeFeedProcessor`Poprawka błędu podczas synchronizowania aktualizacji dzierżawy w różnych wątkach.
* Stały warunek wyścigu powodujący `ArrayIndexOutOfBound` wyjątek w StoreReader

## <a name="faq"></a>Najczęściej zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .