---
title: Porady dotyczące wydajności Azure Cosmos DB synchronizacji Java SDK V2
description: Informacje o opcjach konfiguracji klienta zwiększające wydajność usługi Azure Cosmos Database na potrzeby synchronizacji zestawu Java SDK V2
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 0ef5c0cb0ffca5cc156d64074ebe241cebbcfb8e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277275"
---
# <a name="performance-tips-for-azure-cosmos-db-sync-java-sdk-v2"></a>Porady dotyczące wydajności Azure Cosmos DB synchronizacji Java SDK V2

> [!div class="op_single_selector"]
> * [Java SDK 4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK 2](performance-tips-async-java.md)
> * [Sync Java SDK 2](performance-tips-java.md)
> * [Zestaw SDK .NET w wersji 3](performance-tips-dotnet-sdk-v3-sql.md)
> * [Zestaw .NET SDK w wersji 2](performance-tips.md)
> 

> [!IMPORTANT]  
> To *nie* jest najnowszy zestaw SDK języka Java dla Azure Cosmos DB! Należy uaktualnić projekt do [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) , a następnie zapoznać się z [przewodnikiem wskazówek dotyczących wydajności](performance-tips-java-sdk-v4-sql.md)Azure Cosmos DB zestawu Java SDK 4. Aby przeprowadzić uaktualnienie, postępuj zgodnie z instrukcjami w przewodniku [Migrowanie do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) i [reaktorem programu vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) . 
> 
> Te porady dotyczące wydajności dotyczą tylko Azure Cosmos DB synchronizacji z zestawem Java SDK V2. Aby uzyskać więcej informacji, zapoznaj się z tematem Azure Cosmos DB zsynchronizuj informacje o [wersji](sql-api-sdk-java.md) zestawu Java SDK V2 i [repozytorium Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) .
>

Azure Cosmos DB to szybka i elastyczna dystrybuowana baza danych, która bezproblemowo skaluje się do gwarantowanych opóźnień i przepływności. Nie trzeba wprowadzać głównych zmian architektury ani pisać złożonego kodu w celu skalowania bazy danych za pomocą Azure Cosmos DB. Skalowanie w górę i w dół jest tak proste jak w przypadku jednego wywołania interfejsu API. Aby dowiedzieć się więcej, zobacz [jak zainicjować przepływność kontenera](how-to-provision-container-throughput.md) lub [jak zapewnić przepływność bazy danych](how-to-provision-database-throughput.md). Ponieważ jednak dostęp do Azure Cosmos DB odbywa się za pośrednictwem wywołań sieciowych, istnieją optymalizacje po stronie klienta, które umożliwiają osiągnięcie szczytowej wydajności podczas korzystania z [Azure Cosmos DB synchronizacji zestawu Java SDK V2](documentdb-sdk-java.md).

Tak więc w przypadku pytania "jak można poprawić wydajność bazy danych?" należy wziąć pod uwagę następujące opcje:

## <a name="networking"></a>Sieć
<a id="direct-connection"></a>

1. **Tryb połączenia: Użyj DirectHttps**

    Sposób, w jaki klient nawiązuje połączenie z usługą Azure Cosmos DB, ma istotny wpływ na wydajność, szczególnie jeśli chodzi o zaobserwowane opóźnienie po stronie klienta. Istnieje jedno ustawienie konfiguracji klucza dostępne do konfigurowania [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) klienta — wartość [connectionmode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode).  Dostępne są następujące dwie ConnectionModes:

   1. [Brama (domyślnie)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [DirectHttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)

      Tryb bramy jest obsługiwany na wszystkich platformach SDK i jest skonfigurowany domyślnie.  Jeśli aplikacja działa w sieci firmowej z rygorystycznymi ograniczeniami zapory, najlepszym wyborem jest brama, ponieważ używa ona standardowego portu HTTPS i pojedynczego punktu końcowego. Jednak jest to, że tryb bramy obejmuje dodatkowy przeskok sieciowy za każdym razem, gdy dane są odczytywane lub zapisywane w Azure Cosmos DB. Z tego powodu tryb DirectHttps zapewnia lepszą wydajność z powodu mniejszej liczby przeskoków sieci. 

      Azure Cosmos DB Synchronize Java SDK V2 używa protokołu HTTPS jako protokół transportowy. HTTPS używa protokołu TLS do uwierzytelniania początkowego i szyfrowania ruchu. W przypadku korzystania z Azure Cosmos DB synchronizacji Java SDK V2 tylko port HTTPS 443 musi być otwarty. 

      Wartość Connectionmode jest konfigurowana podczas konstruowania wystąpienia DocumentClient z parametrem ConnectionPolicy. 

    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-connectionpolicy"></a>Synchronize Java SDK V2 (Maven com. Microsoft. Azure:: Azure-DocumentDB)

      ```Java
      public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
      }
        
      ConnectionPolicy connectionPolicy = new ConnectionPolicy();
      DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
      ```

      :::image type="content" source="./media/performance-tips-java/connection-policy.png" alt-text="Diagram przedstawia zasady połączenia platformy Azure Cosmos D B." border="false":::

   <a id="same-region"></a>
2. **Kolokacja klientów w tym samym regionie świadczenia usługi Azure na potrzeby wydajności**

    Jeśli to możliwe, należy umieścić dowolne aplikacje wywołujące Azure Cosmos DB w tym samym regionie, w którym znajduje się baza danych usługi Azure Cosmos. Dla przybliżonego porównania, wywołania do Azure Cosmos DB w tym samym regionie, kompletne w ciągu 1-2 MS, ale opóźnienie między zachodnim i wschodnim wybrzeżem Stanów Zjednoczonych to >50 ms. To opóźnienie może się różnić od żądania żądania w zależności od trasy wykonywanej przez żądanie, gdy przechodzi od klienta do granicy centrum danych platformy Azure. Najniższe możliwe opóźnienie jest realizowane przez zagwarantowanie, że aplikacja wywołująca znajduje się w tym samym regionie platformy Azure, co punkt końcowy Azure Cosmos DB aprowizacji. Aby uzyskać listę dostępnych regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/#services).

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Diagram przedstawia zasady połączenia platformy Azure Cosmos D B." border="false":::
   
## <a name="sdk-usage"></a>Użycie zestawu SDK
1. **Zainstaluj najnowszy zestaw SDK**

    Zestawy SDK Azure Cosmos DB są stale ulepszane w celu zapewnienia najlepszej wydajności. Zobacz strony [zestawu sdk Azure Cosmos DB](documentdb-sdk-java.md) , aby określić najnowszy zestaw SDK i zapoznać się z ulepszeniami.
2. **Używanie pojedynczego klienta Azure Cosmos DB w okresie istnienia aplikacji**

    Każde wystąpienie [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient) jest bezpieczne wątkowo i wykonuje wydajne zarządzanie połączeniami oraz buforowanie adresów podczas pracy w trybie bezpośrednim. Aby umożliwić efektywne zarządzanie połączeniami i lepszą wydajność dzięki DocumentClient, zaleca się użycie jednego wystąpienia elementu DocumentClient na domenę aplikacji przez okres istnienia programu.

   <a id="max-connection"></a>
3. **Zwiększenie MaxPoolSize na hosta w przypadku korzystania z trybu bramy**

    Żądania Azure Cosmos DB są wykonywane za pośrednictwem protokołu HTTPS/REST podczas korzystania z trybu bramy i podlegają domyślnemu limitowi połączeń na nazwę hosta lub adres IP. Może być konieczne ustawienie MaxPoolSize na wyższą wartość (200-1000), aby Biblioteka klienta mogła używać wielu jednoczesnych połączeń do Azure Cosmos DB. W Azure Cosmos DB synchronizacji Java SDK V2 wartość domyślna dla [ConnectionPolicy. getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) to 100. Użyj [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) , aby zmienić wartość.

4. **Dostrajanie równoległych zapytań dla kolekcji partycjonowanych**

    Azure Cosmos DB zsynchronizuj zestaw SDK Java w wersji 1.9.0 lub nowszej obsługuje zapytania równoległe, które umożliwiają równoległe wykonywanie zapytań do kolekcji partycjonowanej. Aby uzyskać więcej informacji, zobacz [przykłady kodu](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) związane z pracą z zestawami SDK. Zapytania równoległe są przeznaczone do poprawiania opóźnienia zapytań i przepływności w porównaniu z ich odpowiednikami seryjnymi.

    (a) ***dostrajanie \: *** równoległych zapytań setMaxDegreeOfParallelism działają przez wykonywanie zapytań na wielu partycjach równolegle. Jednak dane z pojedynczej kolekcji partycjonowanej są pobierane sekwencyjnie w odniesieniu do zapytania. W tym celu należy użyć [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) , aby ustawić liczbę partycji, które mają maksymalną szansę osiągnięcia najbardziej wydajnego zapytania, pod warunkiem, że wszystkie inne warunki systemu pozostają takie same. Jeśli nie znasz liczby partycji, możesz użyć setMaxDegreeOfParallelism, aby ustawić dużą liczbę, a system wybierze minimalną (liczbę partycji, dane wejściowe podane przez użytkownika) jako maksymalny stopień równoległości. 

    Należy pamiętać, że zapytania równoległe generują najlepsze korzyści, jeśli dane są równomiernie dystrybuowane we wszystkich partycjach w odniesieniu do zapytania. Jeśli partycjonowana kolekcja jest partycjonowana w taki sposób, że wszystkie lub większość danych zwróconych przez zapytanie jest skoncentrowana na kilku partycjach (jedna partycja w najgorszym przypadku), wydajność zapytania zostałaby przekazana przez te partycje.

    (b) ***dostrajanie \: *** równoległe zapytania setMaxBufferedItemCount zaprojektowano w celu wstępnego pobrania wyników podczas przetwarzania bieżącej partii wyników przez klienta. Wstępne pobieranie pomaga w ogólnym ulepszaniu opóźnienia zapytania. setMaxBufferedItemCount ogranicza liczbę wstępnie pobranych wyników. Ustawiając [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) na oczekiwaną liczbę zwracanych wyników (lub wyższą liczbę), umożliwia to zapytanie otrzymujące maksymalną korzyść z pobierania wstępnego.

    Przed pobraniem działa w taki sam sposób, niezależnie od MaxDegreeOfParallelism, i istnieje jeden bufor dla danych ze wszystkich partycji.  

5. **Implementowanie wycofywania w interwałach getRetryAfterInMilliseconds**

    Podczas testowania wydajności należy zwiększyć obciążenie, dopóki nie zostanie ograniczona niewielka liczba żądań. W przypadku ograniczenia przepustowości aplikacja kliencka powinna wycofywania przy użyciu ograniczenia przepustowości dla interwału ponawiania określonych przez serwer. Poszanowanie wycofywania gwarantuje, że spędzasz minimalny czas oczekiwania między ponownymi próbami. Obsługa zasad ponownych prób jest zawarta w wersji 1.8.0 i nowszej z poziomu [zestawu Java SDK Azure Cosmos DB Sync](documentdb-sdk-java.md). Aby uzyskać więcej informacji, zobacz [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds).

6. **Skalowanie obciążenia klienta**

    Jeśli testujesz na poziomach o wysokiej przepływności (>50 000 RU/s), aplikacja kliencka może stać się wąskim gardłem ze względu na obciążenie procesora CPU lub sieci. Jeśli docierasz do tego punktu, możesz kontynuować wypychanie konta Azure Cosmos DB przez skalowanie aplikacji klienckich na wiele serwerów.

7. **Użyj adresowania na podstawie nazwy**

    Użyj adresowania opartego na nazwach, gdzie linki mają format `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` zamiast SelfLinks ( \_ własny), który ma format, `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` Aby uniknąć pobierania ResourceID wszystkich zasobów użytych do skonstruowania łącza. Ponadto, ponieważ te zasoby zostaną utworzone ponownie (prawdopodobnie z tą samą nazwą), buforowanie może nie pomóc.

   <a id="tune-page-size"></a>
8. **Dostosuj rozmiar strony dla zapytań/Odczytaj źródła w celu uzyskania lepszej wydajności**

    Podczas wykonywania zbiorczej odczytu dokumentów przy użyciu funkcji odczytywania kanału informacyjnego (na przykład [readDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)) lub podczas wystawiania zapytania SQL wyniki są zwracane w postaci segmentacji, jeśli zestaw wyników jest zbyt duży. Domyślnie wyniki są zwracane w fragmentach 100 elementów lub 1 MB, w zależności od tego, który limit zostanie osiągnięty jako pierwszy.

    Aby zmniejszyć liczbę podróży sieci wymaganych do pobrania wszystkich stosownych wyników, można zwiększyć rozmiar strony przy użyciu nagłówka żądania [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) do 1000. W przypadkach, gdy konieczne jest wyświetlenie tylko kilku wyników, na przykład jeśli interfejs użytkownika lub interfejsu API aplikacji zwraca tylko 10 wyników, można również zmniejszyć rozmiar strony do 10, aby zmniejszyć przepływność wykorzystywaną do odczytu i zapytań.

    Rozmiar strony można również ustawić przy użyciu [metody SetPageSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize).

## <a name="indexing-policy"></a>Zasady indeksowania
 
1. **Wykluczanie nieużywanych ścieżek z indeksowania w celu przyspieszenia operacji zapisu**

    Zasady indeksowania Azure Cosmos DB umożliwiają określenie, które ścieżki dokumentów mają być dołączone lub wykluczone z indeksowania przy użyciu ścieżek indeksowania ([setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) i [setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths)). Użycie ścieżek indeksowania może oferować ulepszoną wydajność zapisu i niższy indeks magazynu dla scenariuszy, w których wzorce zapytania są znane wcześniej, ponieważ koszty indeksowania są bezpośrednio skorelowane z liczbą unikatowych ścieżek indeksowanych.  Na przykład poniższy kod pokazuje, jak wykluczyć całą sekcję (poddrzewo) dokumentów z indeksowania przy użyciu symbolu wieloznacznego "*".


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-indexing"></a>Synchronize Java SDK V2 (Maven com. Microsoft. Azure:: Azure-DocumentDB)

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Aby uzyskać więcej informacji, zobacz [Azure Cosmos DB zasad indeksowania](indexing-policies.md).

## <a name="throughput"></a>Przepływność
<a id="measure-rus"></a>

1. **Mierzenie i dostrajanie dla niższych jednostek żądań/drugiego użycia**

    Azure Cosmos DB oferuje bogaty zestaw operacji bazy danych, w tym relacyjne i hierarchiczne zapytania z UDF, procedurami składowanymi i wyzwalaczami — wszystko to działa na dokumentach w ramach kolekcji baz danych. Koszt związany z każdą z tych operacji zależy od procesora, danych We/Wy i pamięci wymaganej do wykonania danej operacji. Zamiast rozważać zasoby sprzętowe i zarządzać nimi, można traktować jednostkę żądania (RU) jako pojedynczą miarę dla zasobów wymaganych do wykonywania różnych operacji bazy danych i obsługi żądania aplikacji.

    Obsługa przepływności zależy od liczby [jednostek żądania](request-units.md) ustawionych dla każdego kontenera. Użycie jednostki żądania jest oceniane jako stawka na sekundę. Aplikacje, które przekraczają zainicjowaną stawkę jednostkową żądania dla ich kontenera, są ograniczone do momentu spadku stawki poniżej poziomu aprowizacji kontenera. Jeśli aplikacja wymaga wyższego poziomu przepływności, można zwiększyć przepływność przez zainicjowanie obsługi dodatkowych jednostek żądania. 

    Złożoność zapytania wpływa na liczbę jednostek żądań używanych dla operacji. Liczba predykatów, charakter predykatów, liczba UDF i rozmiar zestawu danych źródłowych wpływają na koszt operacji zapytania.

    Aby zmierzyć obciążenie związane z jakąkolwiek operacją (tworzenie, aktualizowanie lub usuwanie), Sprawdź nagłówek [x-MS-Request-obciążeni](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (lub równoważną właściwość RequestCharge w [ResourceResponse \<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resourceresponse) lub [FeedResponse \<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse) , aby zmierzyć liczbę jednostek żądania używanych przez te operacje.


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-requestcharge"></a>Synchronize Java SDK V2 (Maven com. Microsoft. Azure:: Azure-DocumentDB)

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    Opłata za żądanie zwrócona w tym nagłówku jest częścią alokowanej przepływności. Na przykład jeśli masz 2000 RU/s, a poprzednia kwerenda zwróci 1000 rozmiarze 1 KB-Documents, koszt operacji to 1000. W związku z tym w ciągu jednej sekundy serwer honoruje tylko dwa takie żądania przed szybkością ograniczania kolejnych żądań. Aby uzyskać więcej informacji, zobacz [jednostki żądań](request-units.md) i [Kalkulator jednostek żądania](https://www.documentdb.com/capacityplanner).
   <a id="429"></a>
1. **Zbyt duży współczynnik obsługi — limit liczby żądań**

    Gdy klient próbuje przekroczyć zarezerwowaną przepływność dla konta, nie ma obniżenia wydajności na serwerze i nie będzie korzystać z wydajności przepływności poza poziomem zarezerwowanym. Serwer zapobiegawczo zakończyć żądanie z RequestRateTooLarge (kod stanu HTTP 429) i zwróci nagłówek [x-MS-retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) wskazujący ilość czasu (w milisekundach), przez który użytkownik musi czekać przed ponowną próbą wykonania żądania.
    
    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```
    Zestaw SDK niejawnie przechwytuje tę odpowiedź, przestrzegając określonego przez serwer nagłówka retry-After i ponów próbę wykonania żądania. O ile Twoje konto nie jest dostępne współbieżnie przez wielu klientów, kolejna próba powiodła się.

    Jeśli masz więcej niż jeden klient, który działa w sposób ciągły nad częstotliwością żądań, domyślna liczba ponownych prób aktualnie ustawiona na 9 wewnętrznie przez klienta może nie być wystarczająca; w takim przypadku klient zgłasza [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception) z kodem stanu 429 do aplikacji. Domyślną liczbę ponownych prób można zmienić za pomocą [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) w wystąpieniu [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) . Domyślnie DocumentClientException z kodem stanu 429 jest zwracany po skumulowanym czasie oczekiwania 30 sekund, jeśli żądanie będzie nadal działać powyżej stawki żądania. Dzieje się tak nawet wtedy, gdy bieżąca liczba ponownych prób jest mniejsza niż maksymalna liczba ponownych prób, być wartością domyślną 9 lub wartości zdefiniowanej przez użytkownika.

    Mimo że automatyczne zachowanie ponowienia próby pozwala zwiększyć odporność i użyteczność dla większości aplikacji, może się to zdarzyć szanse podczas wykonywania testów wydajnościowych, szczególnie podczas mierzenia opóźnień.  Opóźnienie obserwowane przez klienta zostanie wykonane, jeśli eksperyment trafi na ograniczenia serwera i spowoduje, że zestaw SDK klienta zostanie ponownie powtórzony. Aby uniknąć opóźnień opóźnienia podczas eksperymentów w wydajności, należy zmierzyć opłaty zwrócone przez poszczególne operacje i upewnić się, że żądania działają poniżej zarezerwowanej stawki żądania. Aby uzyskać więcej informacji, zobacz [jednostki żądania](request-units.md).
3. **Projektowanie dla mniejszych dokumentów w celu zwiększenia przepływności**

    Opłata za żądanie (koszt przetwarzania żądania) danej operacji jest bezpośrednio skorelowana z rozmiarem dokumentu. Operacje na dużych dokumentach są droższe niż operacje w przypadku małych dokumentów.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat projektowania aplikacji pod kątem skalowania i wysokiej wydajności, zobacz [partycjonowanie i skalowanie w Azure Cosmos DB](partitioning-overview.md).
