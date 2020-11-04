---
title: Porady dotyczące wydajności Azure Cosmos DB Async Java SDK V2
description: Informacje o opcjach konfiguracji klienta zwiększające wydajność usługi Azure Cosmos Database w przypadku asynchronicznego zestawu Java SDK V2
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java, contperfq2
ms.openlocfilehash: c1dec2c8451ddd1feb4b5b0dac9c82e1716079b7
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325844"
---
# <a name="performance-tips-for-azure-cosmos-db-async-java-sdk-v2"></a>Porady dotyczące wydajności Azure Cosmos DB Async Java SDK V2
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK 4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK 2](performance-tips-async-java.md)
> * [Sync Java SDK 2](performance-tips-java.md)
> * [Zestaw SDK .NET w wersji 3](performance-tips-dotnet-sdk-v3-sql.md)
> * [Zestaw .NET SDK w wersji 2](performance-tips.md)


> [!IMPORTANT]  
> To *nie* jest najnowszy zestaw SDK języka Java dla Azure Cosmos DB! Należy uaktualnić projekt do [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) , a następnie zapoznać się z [przewodnikiem wskazówek dotyczących wydajności](performance-tips-java-sdk-v4-sql.md)Azure Cosmos DB zestawu Java SDK 4. Aby przeprowadzić uaktualnienie, postępuj zgodnie z instrukcjami w przewodniku [Migrowanie do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) i [reaktorem programu vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) . 
> 
> Porady dotyczące wydajności w tym artykule dotyczą tylko Azure Cosmos DB asynchronicznego zestawu Java SDK V2. Aby uzyskać więcej informacji, zobacz informacje o [wersji](sql-api-sdk-async-java.md)Azure Cosmos DB Async SDK V2, [repozytorium Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)i Azure Cosmos DB asynchroniczny [Przewodnik rozwiązywania problemów z](troubleshoot-java-async-sdk.md) zestawem SDK dla języka Java w wersji 2.
>

Azure Cosmos DB to szybka i elastyczna dystrybuowana baza danych, która bezproblemowo skaluje się do gwarantowanych opóźnień i przepływności. Nie trzeba wprowadzać głównych zmian architektury ani pisać złożonego kodu w celu skalowania bazy danych za pomocą Azure Cosmos DB. Skalowanie w górę i w dół jest tak proste, jak wykonywanie pojedynczego wywołania interfejsu API lub wywołania metody zestawu SDK. Ponieważ jednak dostęp do Azure Cosmos DB odbywa się za pośrednictwem wywołań sieciowych, istnieją optymalizacje po stronie klienta, które umożliwiają osiągnięcie szczytowej wydajności podczas korzystania z [Azure Cosmos DB asynchronicznego zestawu Java SDK V2](sql-api-sdk-async-java.md).

Tak więc w przypadku pytania "jak można poprawić wydajność bazy danych?" należy wziąć pod uwagę następujące opcje:

## <a name="networking"></a>Sieć

* **Tryb połączenia: Użyj trybu bezpośredniego**
    
  Sposób, w jaki klient nawiązuje połączenie z Azure Cosmos DB, ma ważne konsekwencje dotyczące wydajności, szczególnie w odniesieniu do opóźnienia po stronie klienta. *Connectionmode* jest ustawieniem konfiguracji klucza dostępnym do konfigurowania *ConnectionPolicy* klienta. W przypadku Azure Cosmos DB asynchronicznego zestawu Java SDK V2 dostępne są dwie ConnectionModes:  
      
  * [Brama (domyślnie)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
  * [Direct](/java/api/com.microsoft.azure.cosmosdb.connectionmode)
  
  Tryb bramy jest obsługiwany na wszystkich platformach SDK i jest domyślnie skonfigurowanym rozwiązaniem. Jeśli aplikacje działają w sieci firmowej z rygorystycznymi ograniczeniami zapory, najlepszym wyborem jest tryb bramy, ponieważ używa on standardowego portu HTTPS i jednego punktu końcowego.   Jednak jest to, że tryb bramy obejmuje dodatkowy przeskok sieciowy za każdym razem, gdy dane są odczytywane lub zapisywane w Azure Cosmos DB. Z tego powodu tryb bezpośredni zapewnia lepszą wydajność ze względu na mniejszą liczbę przeskoków sieci.
  
  Wartość *connectionmode* jest konfigurowana podczas konstruowania wystąpienia *DocumentClient* z parametrem *ConnectionPolicy* .

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-connectionpolicy"></a>Async Java SDK V2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)

```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.Direct);
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
```

* **Kolokacja klientów w tym samym regionie świadczenia usługi Azure na potrzeby wydajności**

  Jeśli to możliwe, należy umieścić dowolne aplikacje wywołujące Azure Cosmos DB w tym samym regionie, w którym znajduje się baza danych usługi Azure Cosmos. Dla przybliżonego porównania, wywołania do Azure Cosmos DB w tym samym regionie, kompletne w ciągu 1-2 MS, ale opóźnienie między zachodnim i wschodnim wybrzeżem Stanów Zjednoczonych to >50 ms. To opóźnienie może się różnić od żądania żądania w zależności od trasy wykonywanej przez żądanie, gdy przechodzi od klienta do granicy centrum danych platformy Azure. Najniższe możliwe opóźnienie jest realizowane przez zagwarantowanie, że aplikacja wywołująca znajduje się w tym samym regionie platformy Azure, co punkt końcowy Azure Cosmos DB aprowizacji. Aby uzyskać listę dostępnych regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/#services).

  :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Ilustracja zasad połączenia Azure Cosmos DB" border="false":::

## <a name="sdk-usage"></a>Użycie zestawu SDK

* **Zainstaluj najnowszy zestaw SDK**

  Zestawy SDK Azure Cosmos DB są stale ulepszane w celu zapewnienia najlepszej wydajności. Zapoznaj się z informacjami o najnowszym zestawie SDK i zapoznaj się ze stronami Azure Cosmos DB asynchroniczne [Informacje o wersji](sql-api-sdk-async-java.md) w programie Java SDK V2.

* **Używanie pojedynczego klienta Azure Cosmos DB w okresie istnienia aplikacji**

  Każde wystąpienie AsyncDocumentClient jest bezpieczne wątkowo i wykonuje wydajne zarządzanie połączeniami oraz buforowanie adresów. Aby umożliwić efektywne zarządzanie połączeniami i lepszą wydajność dzięki AsyncDocumentClient, zaleca się użycie jednego wystąpienia elementu AsyncDocumentClient na domenę aplikacji przez okres istnienia programu.

* **Dostrajanie ConnectionPolicy**

  Domyślnie żądania Cosmos DB trybu bezpośredniego są wykonywane za pośrednictwem protokołu TCP przy użyciu Azure Cosmos DB Async SDK w wersji 2. Wewnętrznie zestaw SDK używa specjalnej architektury trybu bezpośredniego do dynamicznego zarządzania zasobami sieci i uzyskiwania najlepszej wydajności.

  W Azure Cosmos DB asynchronicznego zestawu Java SDK V2 tryb bezpośredni jest najlepszym wyborem, aby zwiększyć wydajność bazy danych przy użyciu większości obciążeń. 

  * ***Przegląd trybu bezpośredniego** _

  :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="Ilustracja architektury trybu bezpośredniego" border="false":::
  
  Architektura po stronie klienta stosowana w trybie bezpośrednim umożliwia przewidywalne wykorzystanie sieci i dostęp do multipleksera Azure Cosmos DB replik. Na powyższym diagramie przedstawiono sposób, w jaki tryb Direct kieruje żądania klientów do replik w Cosmos DB zaplecza. Architektura trybu bezpośredniego przydziela do 10 _ *kanałów* * po stronie klienta na replikę bazy danych. Kanał jest połączeniem TCP poprzedzonym buforem żądania, który ma 30 żądań głębokiego. Kanały należące do repliki są przydzielane dynamicznie zgodnie z wymaganiami **punktu końcowego usługi** repliki. Gdy użytkownik wystawia żądanie w trybie bezpośrednim, **TransportClient** kieruje żądanie do odpowiedniego punktu końcowego usługi na podstawie klucza partycji. **Kolejka żądań** buforuje żądania przed punktem końcowym usługi.

  * ***Opcje konfiguracji ConnectionPolicy dla trybu bezpośredniego** _

    Pierwszym krokiem jest użycie poniższych zalecanych ustawień konfiguracji. Skontaktuj się z [zespołem Azure Cosmos DB](mailto:CosmosDBPerformanceSupport@service.microsoft.com) , jeśli wystąpią problemy z tym konkretnym tematem.

    Jeśli używasz Azure Cosmos DB jako bazy danych referencyjnych (to oznacza, że baza danych jest używana w wielu operacjach odczytu punktu i kilku operacjach zapisu), można zaakceptować ustawienie _idleEndpointTimeout * na 0 (czyli bez limitu czasu).


    | Opcja konfiguracji       | Domyślny    |
    | :------------------:       | :-----:    |
    | bufferPageSize             | 8192       |
    | Parametru          | "PT1M"     |
    | idleChannelTimeout         | "PT0S"     |
    | idleEndpointTimeout        | "PT1M10S"  |
    | maxBufferCapacity          | 8388608    |
    | maxChannelsPerEndpoint     | 10         |
    | maxRequestsPerChannel      | 30         |
    | receiveHangDetectionTime   | "PT1M5S"   |
    | requestExpiryInterval      | "PT5S"     |
    | requestTimeout             | "PT1M"     |
    | requestTimerResolution     | "PT 0,5 S"   |
    | sendHangDetectionTime      | "PT10S"    |
    | shutdownTimeout            | "PT15S"    |

* ***Wskazówki dotyczące programowania w trybie bezpośrednim** _

  Zapoznaj się z artykułem dotyczącym [rozwiązywania](troubleshoot-java-async-sdk.md) problemów z zestawem SDK Azure Cosmos DB Async SDK V2.
  
  Niektóre ważne porady dotyczące programowania w trybie bezpośrednim:
  
  _ **Użyj wielowątkowości w aplikacji w celu wydajnego transferu danych TCP** — po wysłaniu żądania aplikacja powinna subskrybować dane w innym wątku. Nie robi to wymusza niezamierzonej operacji "Half-duplex", a kolejne żądania są blokowane podczas oczekiwania na odpowiedź poprzedniego żądania.
  
  * **Przeprowadzenie obciążeń intensywnie korzystających z obliczeń w dedykowanym wątku** — w przypadku podobnych przyczyn do poprzedniej porady, operacje, takie jak złożone przetwarzanie danych, najlepiej znajdują się w osobnym wątku. Żądanie ściągania danych z innego magazynu danych (na przykład jeśli wątek wykorzystuje jednocześnie Azure Cosmos DB i magazyny danych Spark) może powodować zwiększone opóźnienia i zalecamy duplikowanie dodatkowego wątku, który czeka na odpowiedź z innego magazynu danych.
  
    * Podstawowa sieciowa operacja we/wy w Azure Cosmos DB asynchronicznej wersji zestawu Java SDK V2 jest zarządzana za pomocą podsieci, zobacz te [wskazówki w celu uniknięcia wzorców kodowania, które blokują wątki we/wy](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread).
  
  * **Modelowanie danych** — umowa SLA Azure Cosmos DB zakłada, że rozmiar dokumentu jest mniejszy niż rozmiarze 1 KB. Optymalizacja modelu i programowania danych w celu uzyskania mniejszego rozmiaru dokumentu zwykle prowadzi do zmniejszenia opóźnień. Jeśli zamierzasz potrzebować magazynu i pobrania dokumentów o rozmiarze większym niż rozmiarze 1 KB, zaleca się zastosowanie dokumentów do łączenia się z danymi w usłudze Azure Blob Storage.

* **Dostrajanie równoległych zapytań dla kolekcji partycjonowanych**

  Azure Cosmos DB Async Java SDK V2 obsługuje zapytania równoległe, które umożliwiają równoległe wykonywanie zapytań do kolekcji partycjonowanej. Aby uzyskać więcej informacji, zobacz [przykłady kodu](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) związane z pracą z zestawami SDK. Zapytania równoległe są przeznaczone do poprawiania opóźnienia zapytań i przepływności w porównaniu z ich odpowiednikami seryjnymi.

  * ***Dostrajanie \: setMaxDegreeOfParallelism** _
    
    Zapytania równoległe działają przez wykonywanie zapytań na wielu partycjach równolegle. Jednak dane z pojedynczej kolekcji partycjonowanej są pobierane sekwencyjnie w odniesieniu do zapytania. W tym celu należy użyć setMaxDegreeOfParallelism, aby ustawić liczbę partycji, które mają maksymalną szansę osiągnięcia najbardziej wydajnego zapytania, pod warunkiem, że wszystkie inne warunki systemu pozostają takie same. Jeśli nie znasz liczby partycji, możesz użyć setMaxDegreeOfParallelism, aby ustawić dużą liczbę, a system wybierze minimalną (liczbę partycji, dane wejściowe podane przez użytkownika) jako maksymalny stopień równoległości.

    Należy pamiętać, że zapytania równoległe generują najlepsze korzyści, jeśli dane są równomiernie dystrybuowane we wszystkich partycjach w odniesieniu do zapytania. Jeśli partycjonowana kolekcja jest partycjonowana w taki sposób, że wszystkie lub większość danych zwróconych przez zapytanie jest skoncentrowana na kilku partycjach (jedna partycja w najgorszym przypadku), wydajność zapytania zostałaby przekazana przez te partycje.

  _ * **Strojenie setMaxBufferedItemCount \:** _
    
    Zapytanie równoległe zostało zaprojektowane w celu wstępnego pobrania wyników, podczas gdy bieżąca partia wyników jest przetwarzana przez klienta. Wstępne pobieranie pomaga w ogólnym ulepszaniu opóźnienia zapytania. setMaxBufferedItemCount ogranicza liczbę wstępnie pobranych wyników. Ustawienie setMaxBufferedItemCount na oczekiwaną liczbę zwracanych wyników (lub wyższą liczbę) powoduje, że zapytanie otrzymuje maksymalną korzyść przed pobraniem.

    Przed pobraniem działa w taki sam sposób, niezależnie od MaxDegreeOfParallelism, i istnieje jeden bufor dla danych ze wszystkich partycji.

_ **Implementuj wycofywania w interwałach getRetryAfterInMilliseconds**

  Podczas testowania wydajności należy zwiększyć obciążenie, dopóki nie zostanie ograniczona niewielka liczba żądań. W przypadku ograniczenia przepustowości aplikacja kliencka powinna wycofywania dla interwału ponawiania określonych przez serwer. Poszanowanie wycofywania gwarantuje, że spędzasz minimalny czas oczekiwania między ponownymi próbami.

* **Skalowanie obciążenia klienta**

  Jeśli testujesz na poziomach o wysokiej przepływności (>50 000 RU/s), aplikacja kliencka może stać się wąskim gardłem ze względu na obciążenie procesora CPU lub sieci. Jeśli docierasz do tego punktu, możesz kontynuować wypychanie konta Azure Cosmos DB przez skalowanie aplikacji klienckich na wiele serwerów.

* **Użyj adresowania na podstawie nazwy**

  Użyj adresowania opartego na nazwach, gdzie linki mają format `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` zamiast SelfLinks ( \_ własny), który ma format, `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` Aby uniknąć pobierania ResourceID wszystkich zasobów użytych do skonstruowania łącza. Ponadto, ponieważ te zasoby zostaną utworzone ponownie (prawdopodobnie z tą samą nazwą), buforowanie może nie pomóc.

* **Dostosuj rozmiar strony dla zapytań/Odczytaj źródła w celu uzyskania lepszej wydajności**

  Podczas wykonywania zbiorczej odczytu dokumentów przy użyciu funkcji odczytywania kanału informacyjnego (na przykład readDocuments) lub podczas wystawiania zapytania SQL wyniki są zwracane w postaci segmentacji, jeśli zestaw wyników jest zbyt duży. Domyślnie wyniki są zwracane w fragmentach 100 elementów lub 1 MB, w zależności od tego, który limit zostanie osiągnięty jako pierwszy.

  Aby zmniejszyć liczbę podróży sieci wymaganych do pobrania wszystkich stosownych wyników, można zwiększyć rozmiar strony przy użyciu nagłówka żądania [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) do 1000. W przypadkach, gdy konieczne jest wyświetlenie tylko kilku wyników, na przykład jeśli interfejs użytkownika lub interfejsu API aplikacji zwraca tylko 10 wyników, można również zmniejszyć rozmiar strony do 10, aby zmniejszyć przepływność wykorzystywaną do odczytu i zapytań.

  Rozmiar strony można również ustawić przy użyciu metody setMaxItemCount.

* **Użyj odpowiedniego harmonogramu (Unikaj kradzieży wątków wielosieciowych we/wy pętli zdarzeń)**

  W Azure Cosmos DB Async SDK dla języka Java v2 używa się [podsieci](https://netty.io/) w przypadku nieblokującego we/wy. Zestaw SDK używa ustalonej liczby wątków pętli zdarzeń we/wy (w przypadku wielu rdzeni procesora) na potrzeby wykonywania operacji we/wy. Zauważalny zwracany przez interfejs API emituje wynik na jednym ze współużytkowanych wątków pętli zdarzeń we/wy. W związku z tym ważne jest, aby nie blokować wspólnych wątków pętli na potrzeby operacji we/wy. Wykonywanie zadań intensywnie korzystających z procesora CPU lub operacji blokowania w wątku sieci w pętli zdarzeń we/wy może spowodować zakleszczenie lub znacząco zredukować przepływność zestawu SDK.

  Na przykład poniższy kod wykonuje prace intensywnie korzystające z procesora CPU w wątku wielosieciowych operacji we/wy pętli:

  **Async Java SDK V2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)**

  ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
  ```

  Po otrzymaniu wyniku, jeśli chcesz wykonać intensywną moc procesora CPU w wyniku, należy unikać wykonywania tej operacji w wątku z wielosieciowymi pętlami we/wy. Zamiast tego możesz wprowadzić własny harmonogram, aby zapewnić własny wątek do uruchamiania pracy.

  **Async Java SDK V2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)**

  ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
  ```

  Na podstawie typu pracy należy użyć odpowiedniego istniejącego harmonogramu RxJava do pracy. Przeczytaj tutaj [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html) .

  Aby uzyskać więcej informacji, zapoznaj się ze [stroną usługi GitHub](https://github.com/Azure/azure-cosmosdb-java) , Azure Cosmos DB ASYNC Java SDK V2.

* **Wyłącz rejestrowanie sieci na sieć**

    Rejestrowanie biblioteki sieci z sieciami zawiera czat i musi być wyłączone (Pomijanie logowania może być niewystarczające), aby uniknąć dodatkowych kosztów procesora. Jeśli nie jesteś w trybie debugowania, wyłącz rejestrację sieci na sieć. Dlatego jeśli używasz Log4J do usuwania dodatkowych kosztów procesora CPU ponoszonych przez ``org.apache.log4j.Category.callAppenders()`` z sieci, Dodaj następujący wiersz do bazy kodu:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

 * **Limit zasobów otwartych plików systemu operacyjnego**
 
    Niektóre systemy Linux (np. Red Hat) mają górny limit liczby otwartych plików, a więc łączną liczbę połączeń. Uruchom następujące, aby wyświetlić bieżące limity:

    ```bash
    ulimit -a
    ```

    Liczba otwartych plików (nofile) musi być wystarczająco duża, aby mieć wystarczającą ilość miejsca na skonfigurowany rozmiar puli połączeń i inne otwarte pliki w systemie operacyjnym. Można ją zmodyfikować tak, aby zezwalała na większy rozmiar puli połączeń.

    Otwórz plik Limits. conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Dodaj/zmodyfikuj następujące wiersze:

    ```
    * - nofile 100000
    ```

## <a name="indexing-policy"></a>Zasady indeksowania
 
* **Wykluczanie nieużywanych ścieżek z indeksowania w celu przyspieszenia operacji zapisu**

    Zasady indeksowania Azure Cosmos DB umożliwiają określenie, które ścieżki dokumentów mają być dołączone lub wykluczone z indeksowania przy użyciu ścieżek indeksowania (setIncludedPaths i setExcludedPaths). Użycie ścieżek indeksowania może oferować ulepszoną wydajność zapisu i niższy indeks magazynu dla scenariuszy, w których wzorce zapytania są znane wcześniej, ponieważ koszty indeksowania są bezpośrednio skorelowane z liczbą unikatowych ścieżek indeksowanych. Na przykład poniższy kod pokazuje, jak wykluczyć całą sekcję dokumentów (nazywaną również poddrzewem) z indeksowania przy użyciu symbolu wieloznacznego "*".

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-indexing"></a>Async Java SDK V2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Aby uzyskać więcej informacji, zobacz [Azure Cosmos DB zasad indeksowania](/azure/cosmos-db/index-policy).

## <a name="throughput"></a><a id="measure-rus"></a>Przepływność

* **Mierzenie i dostrajanie dla niższych jednostek żądań/drugiego użycia**

    Azure Cosmos DB oferuje bogaty zestaw operacji bazy danych, w tym relacyjne i hierarchiczne zapytania z UDF, procedurami składowanymi i wyzwalaczami — wszystko to działa na dokumentach w ramach kolekcji baz danych. Koszt związany z każdą z tych operacji zależy od procesora, danych We/Wy i pamięci wymaganej do wykonania danej operacji. Zamiast rozważać zasoby sprzętowe i zarządzać nimi, można traktować jednostkę żądania (RU) jako pojedynczą miarę dla zasobów wymaganych do wykonywania różnych operacji bazy danych i obsługi żądania aplikacji.

    Obsługa przepływności zależy od liczby [jednostek żądania](request-units.md) ustawionych dla każdego kontenera. Użycie jednostki żądania jest oceniane jako stawka na sekundę. Aplikacje, które przekraczają zainicjowaną stawkę jednostkową żądania dla ich kontenera, są ograniczone do momentu spadku stawki poniżej poziomu aprowizacji kontenera. Jeśli aplikacja wymaga wyższego poziomu przepływności, można zwiększyć przepływność przez zainicjowanie obsługi dodatkowych jednostek żądania.

    Złożoność zapytania wpływa na liczbę jednostek żądań używanych dla operacji. Liczba predykatów, charakter predykatów, liczba UDF i rozmiar zestawu danych źródłowych wpływają na koszt operacji zapytania.

    Aby zmierzyć obciążenie związane z jakąkolwiek operacją (tworzenie, aktualizowanie lub usuwanie), Sprawdź nagłówek [x-MS-Request-opłata](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) , aby zmierzyć liczbę jednostek żądań używanych przez te operacje. Możesz również przyjrzeć się równoważnej właściwości RequestCharge w ResourceResponse \<T> lub FeedResponse \<T> .

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-requestcharge"></a>Async Java SDK V2 (Maven com. Microsoft. Azure:: Azure-cosmosdb)

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    Opłata za żądanie zwrócona w tym nagłówku jest częścią alokowanej przepływności. Na przykład jeśli masz 2000 RU/s, a poprzednia kwerenda zwróci 1000 rozmiarze 1 KB-Documents, koszt operacji to 1000. W związku z tym w ciągu jednej sekundy serwer honoruje tylko dwa takie żądania przed szybkością ograniczania kolejnych żądań. Aby uzyskać więcej informacji, zobacz [jednostki żądań](request-units.md) i [Kalkulator jednostek żądania](https://www.documentdb.com/capacityplanner).

* **Zbyt duży współczynnik obsługi — limit liczby żądań**

    Gdy klient próbuje przekroczyć zarezerwowaną przepływność dla konta, nie ma obniżenia wydajności na serwerze i nie będzie korzystać z wydajności przepływności poza poziomem zarezerwowanym. Serwer zapobiegawczo zakończyć żądanie z RequestRateTooLarge (kod stanu HTTP 429) i zwróci nagłówek [x-MS-retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) wskazujący ilość czasu (w milisekundach), przez który użytkownik musi czekać przed ponowną próbą wykonania żądania.

   ```xml
   HTTP Status 429,
   Status Line: RequestRateTooLarge
   x-ms-retry-after-ms :100
   ```

    Zestaw SDK niejawnie przechwytuje tę odpowiedź, przestrzegając określonego przez serwer nagłówka retry-After i ponów próbę wykonania żądania. O ile Twoje konto nie jest dostępne współbieżnie przez wielu klientów, kolejna próba powiodła się.

    Jeśli masz więcej niż jeden klient, który działa w sposób ciągły nad częstotliwością żądań, domyślna liczba ponownych prób aktualnie ustawiona na 9 wewnętrznie przez klienta może nie być wystarczająca; w takim przypadku klient zgłasza DocumentClientException z kodem stanu 429 do aplikacji. Domyślną liczbę ponownych prób można zmienić za pomocą setRetryOptions w wystąpieniu ConnectionPolicy. Domyślnie DocumentClientException z kodem stanu 429 jest zwracany po skumulowanym czasie oczekiwania 30 sekund, jeśli żądanie będzie nadal działać powyżej stawki żądania. Dzieje się tak nawet wtedy, gdy bieżąca liczba ponownych prób jest mniejsza niż maksymalna liczba ponownych prób, być wartością domyślną 9 lub wartości zdefiniowanej przez użytkownika.

    Mimo że automatyczne zachowanie ponowienia próby pozwala zwiększyć odporność i użyteczność dla większości aplikacji, może się to zdarzyć szanse podczas wykonywania testów wydajnościowych, szczególnie podczas mierzenia opóźnień. Opóźnienie obserwowane przez klienta zostanie wykonane, jeśli eksperyment trafi na ograniczenia serwera i spowoduje, że zestaw SDK klienta zostanie ponownie powtórzony. Aby uniknąć opóźnień opóźnienia podczas eksperymentów w wydajności, należy zmierzyć opłaty zwrócone przez poszczególne operacje i upewnić się, że żądania działają poniżej zarezerwowanej stawki żądania. Aby uzyskać więcej informacji, zobacz [jednostki żądania](request-units.md).

* **Projektowanie dla mniejszych dokumentów w celu zwiększenia przepływności**

    Opłata za żądanie (koszt przetwarzania żądania) danej operacji jest bezpośrednio skorelowana z rozmiarem dokumentu. Operacje na dużych dokumentach są droższe niż operacje w przypadku małych dokumentów.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat projektowania aplikacji pod kątem skalowania i wysokiej wydajności, zobacz [partycjonowanie i skalowanie w Azure Cosmos DB](partitioning-overview.md).