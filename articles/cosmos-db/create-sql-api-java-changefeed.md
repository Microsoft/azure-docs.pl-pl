---
title: Tworzenie przykładowej aplikacji do Azure Cosmos DB Java SDK v4 przy użyciu kanału zmiany
description: Ten przewodnik przeprowadzi Cię przez prostą aplikację interfejsu API SQL języka Java, która wstawia dokumenty do kontenera Azure Cosmos DB, zachowując materiałowy widok kontenera za pomocą źródła zmian.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 06/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 765fd3afc7fe688d3e6b0e3394e7dc8c39af69b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096856"
---
# <a name="how-to-create-a-java-application-that-uses-azure-cosmos-db-sql-api-and-change-feed-processor"></a>Jak utworzyć aplikację Java, która używa Azure Cosmos DB interfejsu API SQL i procesora źródła zmian
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ten Przewodnik instruktażowy przeprowadzi Cię przez prostą aplikację Java, która używa Azure Cosmos DB interfejsu API SQL do wstawiania dokumentów do kontenera Azure Cosmos DB, zachowując istotny widok kontenera przy użyciu kanału informacyjnego zmian i procesora źródła zmian. Aplikacja Java komunikuje się z Azure Cosmos DB interfejsem API SQL przy użyciu Azure Cosmos DB Java SDK v4.

> [!IMPORTANT]  
> Ten samouczek dotyczy tylko Azure Cosmos DB Java SDK v4. Aby uzyskać więcej informacji, zapoznaj się z informacjami o [wersji](sql-api-sdk-java-v4.md)Azure Cosmos DB Java SDK v4, [repozytorium Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB przewodniku Azure Cosmos DB [dotyczącym](performance-tips-java-sdk-v4-sql.md) [rozwiązywania problemów](troubleshoot-java-sdk-v4-sql.md) z zestawem Java SDK 4. Jeśli obecnie używasz starszej wersji niż v4, zapoznaj się z przewodnikiem [Migrowanie do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) , aby uzyskać pomoc w uaktualnianiu do wersji v4.
>

## <a name="prerequisites"></a>Wymagania wstępne

* Identyfikator URI i klucz konta Azure Cosmos DB

* Maven

* Java 8

## <a name="background"></a>Tło

Kanał informacyjny zmiany Azure Cosmos DB zapewnia interfejs sterowany zdarzeniami do wyzwalania akcji w odpowiedzi na wstawienie dokumentu. Ma wiele użycia. Na przykład w przypadku aplikacji, które mają duże znaczenie zarówno do odczytu, jak i zapisu, głównym zastosowaniem kanału informacyjnego zmiany jest utworzenie **materiałuowego widoku** kontenera w czasie rzeczywistym w miarę pozyskiwania dokumentów. Kontener widoku z materiałami będzie przechowywać te same dane, ale partycjonowany do wydajnego odczytu, co sprawia, że aplikacja zarówno do odczytu, jak i do zapisu.

Praca nad zarządzaniem zdarzeniami związanych ze źródłem zmian jest w dużym stopniu przejmowana przez bibliotekę procesora źródła zmian wbudowaną w zestawie SDK. Ta biblioteka jest wystarczająca do dystrybucji zdarzeń strumieniowego źródła danych między wieloma pracownikami, jeśli jest to wymagane. Wszystkie czynności, które należy wykonać, to w przypadku wywołania zwrotnego biblioteki źródeł zmian.

W tym prostym przykładzie przedstawiono bibliotekę procesora kanału informacyjnego z pojedynczym procesem roboczym tworzącym i usuwającym dokumenty z widoku z materiałami.

## <a name="setup"></a>Konfiguracja

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

   :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_empty.JPG" alt-text="Konto usługi Azure Cosmos DB":::

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
    * **InventoryContainer-pktype** — materiałowy widok rekordu spisu zoptymalizowany pod kątem zapytań względem elementu ```type```
    * **InventoryContainer-leases** — kontener dzierżawy jest zawsze wymagany dla źródła zmian; dzierżawy śledzą postęp aplikacji w odczytaniu źródła zmian.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG" alt-text="Puste kontenery":::

1. W terminalu powinien pojawić się monit

    ```bash
    Press enter to start creating the materialized view...
    ```

    Naciśnij klawisz ENTER. Teraz Poniższy blok kodu zostanie wykonany i zainicjuje procesor źródła zmian w innym wątku: 

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=InitializeCFP)]

    ```"SampleHost_1"``` jest nazwą procesu roboczego procesora źródła zmian. ```changeFeedProcessorInstance.start()``` Czy w rzeczywistości zaczyna się procesorem źródła zmian.

    Wróć do Eksplorator danych Azure Portal w przeglądarce. W kontenerze **InventoryContainer-leases** kliknij pozycję **Items (elementy** ), aby wyświetlić jego zawartość. Zobaczysz, że procesor danych zmiany wypełnił kontener dzierżawy, tj. procesor przypisał ```SampleHost_1``` proces roboczy do dzierżawy na niektórych partycjach **InventoryContainer**.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_leases.JPG" alt-text="Dzierżawy":::

1. Naciśnij klawisz Enter ponownie w terminalu. Spowoduje to wyzwolenie 10 dokumentów do wstawienia do **InventoryContainer**. Każda wstawka dokumentu pojawia się w kanale zmian jako kod JSON; Poniższy kod wywołania zwrotnego obsługuje te zdarzenia przez dublowanie dokumentów JSON do widoku z materiałami:

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=CFPCallback)]

1. Zezwól na uruchomienie kodu 5-10sec. Następnie wróć do Azure Portal Eksplorator danych i przejdź do **InventoryContainer > elementów**. Należy zobaczyć, że elementy są wstawiane do kontenera spisu; Zanotuj klucz partycji ( ```id``` ).

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_items.JPG" alt-text="Kontener kanału informacyjnego":::

1. Teraz w Eksplorator danych przejdź do **pozycji > InventoryContainer-pktype**. Jest to widok z materiałami — elementy znajdujące się w tym kontenerze dublowane **InventoryContainer** , ponieważ zostały wstawione programowo przez źródło zmian. Zanotuj klucz partycji ( ```type``` ). Dlatego ten widok z materiałami jest zoptymalizowany pod kątem zapytań filtrowanych ```type``` , co byłoby niewydajne w **InventoryContainer** , ponieważ jest on podzielony na partycje ```id``` .

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG" alt-text="Zrzut ekranu przedstawia stronę Eksplorator danych dla konta usługi Azure Cosmos D B z wybranymi elementami.":::

1. Zamierzamy usunąć dokument z zarówno **InventoryContainer** , jak i **InventoryContainer-pktype** przy użyciu tylko jednego ```upsertItem()``` wywołania. Najpierw zapoznaj się z Azure Portal Eksplorator danych. Usuniemy dokument, dla którego ```/type == "plums"``` jest on otoczony czerwono poniżej

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG" alt-text="Zrzut ekranu przedstawia stronę Eksplorator danych dla konta usługi Azure Cosmos D B z wybranym elementem I D.":::

    Ponownie naciśnij klawisz ENTER, aby wywołać funkcję ```deleteDocument()``` w przykładowym kodzie. Ta funkcja, pokazana poniżej, upserts nową wersję dokumentu z ```/ttl == 5``` , który ustawia czas wygaśnięcia dokumentu na Live (TTL) na 5Sec. 
    
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=DeleteWithTTL)]

    Kanał informacyjny zmiany ```feedPollDelay``` jest ustawiany na 100 MS; w związku z tym, kanał informacyjny zmiany reaguje na tę aktualizację niemal natychmiast i ```updateInventoryTypeMaterializedView()``` pokazane powyżej wywołania. To ostatnie wywołanie funkcji będzie upsert nowy dokument z wartością TTL 5Sec do **InventoryContainer-pktype**.

    Ten efekt jest taki, że po około 5 sekundach dokument utraci ważność i zostanie usunięty z obu kontenerów.

    Ta procedura jest niezbędna, ponieważ w przypadku usuwania elementu Źródło zmian tylko wystawia zdarzenia dotyczące wstawiania lub aktualizowania elementu.

1. Naciśnij klawisz ENTER jeszcze raz, aby zamknąć program i oczyścić jego zasoby.
