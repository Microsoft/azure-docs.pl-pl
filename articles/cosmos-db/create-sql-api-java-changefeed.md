---
title: Tworzenie przykładowej aplikacji do Azure Cosmos DB Java SDK v4 przy użyciu kanału zmiany
description: Ten przewodnik przeprowadzi Cię przez prostą aplikację interfejsu API SQL języka Java, która wstawia dokumenty do kontenera Azure Cosmos DB, zachowując materiałowy widok kontenera za pomocą źródła zmian.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: anfeldma
ms.openlocfilehash: 5e8656e891d250547174aa3deb27a94eebaa0ba3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125676"
---
# <a name="how-to-create-a-java-application-that-uses-azure-cosmos-db-sql-api-and-change-feed-processor"></a>Jak utworzyć aplikację Java, która używa Azure Cosmos DB interfejsu API SQL i procesora źródła zmian

> [!IMPORTANT]  
> Aby uzyskać więcej informacji na temat Azure Cosmos DB Java SDK v4, Wyświetl informacje o wersji pakietu Azure Cosmos DB SDK v4, [repozytorium Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos)Azure Cosmos DB, [wskazówki dotyczące wydajności](performance-tips-java-sdk-v4-sql.md)zestawu Java SDK 4 Azure Cosmos DB i [Przewodnik rozwiązywania problemów](troubleshoot-java-sdk-v4-sql.md)z zestawem SDK Java w wersji 4.
>

Ten Przewodnik instruktażowy przeprowadzi Cię przez prostą aplikację Java, która używa Azure Cosmos DB interfejsu API SQL do wstawiania dokumentów do kontenera Azure Cosmos DB, zachowując istotny widok kontenera przy użyciu kanału informacyjnego zmian i procesora źródła zmian. Aplikacja Java komunikuje się z Azure Cosmos DB interfejsem API SQL przy użyciu Azure Cosmos DB Java SDK v4.

## <a name="prerequisites"></a>Wymagania wstępne

* Identyfikator URI i klucz konta Azure Cosmos DB

* Maven

* Java 8

## <a name="background"></a>Tło

Kanał informacyjny zmiany Azure Cosmos DB zapewnia interfejs sterowany zdarzeniami do wyzwalania akcji w odpowiedzi na wstawienie dokumentu. Ma wiele użycia. Na przykład w przypadku aplikacji, które mają duże znaczenie zarówno do odczytu, jak i zapisu, głównym zastosowaniem kanału informacyjnego zmiany jest utworzenie **materiałuowego widoku** kontenera w czasie rzeczywistym w miarę pozyskiwania dokumentów. Kontener widoku z materiałami będzie przechowywać te same dane, ale partycjonowany do wydajnego odczytu, co sprawia, że aplikacja zarówno do odczytu, jak i do zapisu.

Praca nad zarządzaniem zdarzeniami związanych ze źródłem zmian jest w dużym stopniu przejmowana przez bibliotekę procesora źródła zmian wbudowaną w zestawie SDK. Ta biblioteka jest wystarczająca do dystrybucji zdarzeń strumieniowego źródła danych między wieloma pracownikami, jeśli jest to wymagane. Wszystkie czynności, które należy wykonać, to w przypadku wywołania zwrotnego biblioteki źródeł zmian.

W tym prostym przykładzie przedstawiono bibliotekę procesora kanału informacyjnego z pojedynczym procesem roboczym tworzącym i usuwającym dokumenty z widoku z materiałami.

## <a name="setup"></a>Konfigurowanie

Jeśli jeszcze tego nie zrobiono, Sklonuj repozytorium przykładowej aplikacji:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

Otwórz terminal w katalogu repozytorium. Kompilowanie aplikacji przez uruchomienie

```bash
mvn clean package
```

## <a name="walkthrough"></a>Przewodnik

1. Najpierw należy sprawdzić konto Azure Cosmos DB. Otwórz **Azure Portal** w przeglądarce, przejdź do swojego konta Azure Cosmos DB, a następnie w okienku po lewej stronie przejdź do **Eksplorator danych**.

    ![Konto Azure Cosmos DB](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. Uruchom aplikację w terminalu przy użyciu następującego polecenia:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Naciśnij klawisz ENTER, gdy zobaczysz

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    następnie wróć do Eksplorator danych Azure Portal w przeglądarce. Zobaczysz, że baza danych **GroceryStoreDatabase** została dodana z trzema pustymi kontenerami: 

    * **InventoryContainer** — rekord spisu dla naszego przykładowego sklepu w postaci spożywczej, podzielony na element, ```id``` który jest identyfikatorem UUID.
    * **InventoryContainer-pktype** — materiałowy widok rekordu spisu zoptymalizowany pod kątem zapytań względem elementu```type```
    * **InventoryContainer-leases** — kontener dzierżawy jest zawsze wymagany dla źródła zmian; dzierżawy śledzą postęp aplikacji w odczytaniu źródła zmian.


    ![Puste kontenery](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. W terminalu powinien pojawić się monit

    ```bash
    Press enter to start creating the materialized view...
    ```

    Naciśnij klawisz ENTER. Teraz Poniższy blok kodu zostanie wykonany i zainicjuje procesor źródła zmian w innym wątku: 

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for change feed processor start
    ```

    ```"SampleHost_1"```jest nazwą procesu roboczego procesora źródła zmian. ```changeFeedProcessorInstance.start()```Czy w rzeczywistości zaczyna się procesorem źródła zmian.

    Wróć do Eksplorator danych Azure Portal w przeglądarce. W kontenerze **InventoryContainer-leases** kliknij pozycję **Items (elementy** ), aby wyświetlić jego zawartość. Zobaczysz, że procesor danych zmiany wypełnił kontener dzierżawy, tj. procesor przypisał ```SampleHost_1``` proces roboczy do dzierżawy na niektórych partycjach **InventoryContainer**.

    ![Dzierżawy](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. Naciśnij klawisz Enter ponownie w terminalu. Spowoduje to wyzwolenie 10 dokumentów do wstawienia do **InventoryContainer**. Każda wstawka dokumentu pojawia się w kanale zmian jako kod JSON; Poniższy kod wywołania zwrotnego obsługuje te zdarzenia przez dublowanie dokumentów JSON do widoku z materiałami:

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosAsyncContainer feedContainer, CosmosAsyncContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.setFeedPollDelay(Duration.ofMillis(100));
        cfOptions.setStartFromBeginning(true);
        return ChangeFeedProcessor.changeFeedProcessorBuilder()
            .setOptions(cfOptions)
            .setHostName(hostName)
            .setFeedContainer(feedContainer)
            .setLeaseContainer(leaseContainer)
            .setHandleChanges((List<JsonNode> docs) -> {
                for (JsonNode document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(JsonNode document) {
        typeContainer.upsertItem(document).subscribe();
    }
    ```

1. Zezwól na uruchomienie kodu 5-10sec. Następnie wróć do Azure Portal Eksplorator danych i przejdź do **InventoryContainer > elementów**. Należy zobaczyć, że elementy są wstawiane do kontenera spisu; Zanotuj klucz partycji ( ```id``` ).

    ![Kontener kanału informacyjnego](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. Teraz w Eksplorator danych przejdź do **pozycji > InventoryContainer-pktype**. Jest to widok z materiałami — elementy znajdujące się w tym kontenerze dublowane **InventoryContainer** , ponieważ zostały wstawione programowo przez źródło zmian. Zanotuj klucz partycji ( ```type``` ). Dlatego ten widok z materiałami jest zoptymalizowany pod kątem zapytań filtrowanych ```type``` , co byłoby niewydajne w **InventoryContainer** , ponieważ jest on podzielony na partycje ```id``` .

    ![Zmaterializowany widok](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. Zamierzamy usunąć dokument z zarówno **InventoryContainer** , jak i **InventoryContainer-pktype** przy użyciu tylko jednego ```upsertItem()``` wywołania. Najpierw zapoznaj się z Azure Portal Eksplorator danych. Usuniemy dokument, dla którego ```/type == "plums"``` jest on otoczony czerwono poniżej

    ![Zmaterializowany widok](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Ponownie naciśnij klawisz ENTER, aby wywołać funkcję ```deleteDocument()``` w przykładowym kodzie. Ta funkcja, pokazana poniżej, upserts nową wersję dokumentu z ```/ttl == 5``` , który ustawia czas wygaśnięcia dokumentu na Live (TTL) na 5Sec. 
    
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    Kanał informacyjny zmiany ```feedPollDelay``` jest ustawiany na 100 MS; w związku z tym, kanał informacyjny zmiany reaguje na tę aktualizację niemal natychmiast i ```updateInventoryTypeMaterializedView()``` pokazane powyżej wywołania. To ostatnie wywołanie funkcji będzie upsert nowy dokument z wartością TTL 5Sec do **InventoryContainer-pktype**.

    Ten efekt jest taki, że po około 5 sekundach dokument utraci ważność i zostanie usunięty z obu kontenerów.

    Ta procedura jest niezbędna, ponieważ w przypadku usuwania elementu Źródło zmian tylko wystawia zdarzenia dotyczące wstawiania lub aktualizowania elementu.

1. Naciśnij klawisz ENTER jeszcze raz, aby zamknąć program i oczyścić jego zasoby.
