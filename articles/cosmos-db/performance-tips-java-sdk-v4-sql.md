---
title: Porady dotyczące wydajności zestawu Java SDK usługi Azure Cosmos DB w wersji 4
description: Dowiedz się więcej na temat opcji konfiguracji klienta, aby zwiększyć wydajność usługi Azure Cosmos Database dla zestawu Java SDK v4
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: how-to
ms.date: 10/13/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 43206fbc956602ddaf189f45648cf8a44a3dd143
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277326"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Porady dotyczące wydajności zestawu Java SDK usługi Azure Cosmos DB w wersji 4

> [!div class="op_single_selector"]
> * [Java SDK 4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK 2](performance-tips-async-java.md)
> * [Sync Java SDK 2](performance-tips-java.md)
> * [Zestaw SDK .NET w wersji 3](performance-tips-dotnet-sdk-v3-sql.md)
> * [Zestaw .NET SDK w wersji 2](performance-tips.md)
> 

> [!IMPORTANT]  
> Porady dotyczące wydajności w tym artykule dotyczą tylko Azure Cosmos DB Java SDK v4. Aby uzyskać więcej informacji, zapoznaj się z informacjami o [wersji](sql-api-sdk-java-v4.md)Azure Cosmos DB Java SDK v4, [repozytorium Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos)i Azure Cosmos DB [przewodniku rozwiązywania problemów](troubleshoot-java-sdk-v4-sql.md) dotyczących zestawu Java SDK v4. Jeśli obecnie używasz starszej wersji niż v4, zapoznaj się z przewodnikiem [Migrowanie do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) , aby uzyskać pomoc w uaktualnianiu do wersji v4.
>

Azure Cosmos DB to szybka i elastyczna dystrybuowana baza danych, która bezproblemowo skaluje się do gwarantowanych opóźnień i przepływności. Nie trzeba wprowadzać głównych zmian architektury ani pisać złożonego kodu w celu skalowania bazy danych za pomocą Azure Cosmos DB. Skalowanie w górę i w dół jest tak proste, jak wykonywanie pojedynczego wywołania interfejsu API lub wywołania metody zestawu SDK. Ponieważ jednak dostęp do Azure Cosmos DB odbywa się za pośrednictwem wywołań sieciowych, istnieją optymalizacje po stronie klienta, które umożliwiają osiągnięcie szczytowej wydajności podczas korzystania z Azure Cosmos DB Java SDK v4.

Tak więc w przypadku pytania "jak można poprawić wydajność bazy danych?" należy wziąć pod uwagę następujące opcje:

## <a name="networking"></a>Sieć

* **Tryb połączenia: Użyj trybu bezpośredniego**
<a id="direct-connection"></a>
    
    Domyślny tryb połączenia zestawu Java SDK jest bezpośredni. Tryb połączenia można skonfigurować w konstruktorze klienta przy użyciu metod *directmode ()* lub *GATEWAYMODE ()* , jak pokazano poniżej. Aby skonfigurować tryb z ustawieniami domyślnymi, wywołaj każdą metodę bez argumentów. W przeciwnym razie Przekaż wystąpienie klasy ustawień konfiguracji jako argument (*DirectConnectionConfig* for *directmode ()*,  *GatewayConnectionConfig* dla *bramymode ()*.). Aby dowiedzieć się więcej o różnych opcjach łączności, zobacz artykuł dotyczący [trybów łączności](sql-sdk-connection-modes.md) .
    
    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Zestaw SDK Java v4

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientConnectionModeAsync)]

    # <a name="sync"></a>[Synchronizacja](#tab/api-sync)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) — interfejs API synchronizacji

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientConnectionModeSync)]

    --- 

    Metoda *directmode ()* ma dodatkowe przesłonięcie z następującego powodu. Operacje płaszczyzny kontroli, takie jak baza danych i kontener CRUD, *zawsze* wykorzystują tryb bramy; gdy użytkownik skonfigurował tryb bezpośredni dla operacji płaszczyzny danych, operacje płaszczyzny kontroli używają ustawień domyślnego trybu bramy. Jest to takie samo, jak większość użytkowników. Jednak użytkownicy, którzy chcą korzystać z trybu bezpośredniego dla operacji płaszczyzny danych, a także tunability parametry trybu bramy kontroli płaszczyzny sterowania mogą używać następującego przesłonięcia *bezpośredniegomode ()* :

    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Zestaw SDK Java v4

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientDirectOverrideAsync)]

    # <a name="sync"></a>[Synchronizacja](#tab/api-sync)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) — interfejs API synchronizacji

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientDirectOverrideSync)]

    --- 

<a name="collocate-clients"></a>
* **Kolokacja klientów w tym samym regionie świadczenia usługi Azure na potrzeby wydajności**<a id="same-region"></a>

    Jeśli to możliwe, należy umieścić dowolne aplikacje wywołujące Azure Cosmos DB w tym samym regionie, w którym znajduje się baza danych usługi Azure Cosmos. Dla przybliżonego porównania, wywołania do Azure Cosmos DB w tym samym regionie, kompletne w ciągu 1-2 MS, ale opóźnienie między zachodnim i wschodnim wybrzeżem Stanów Zjednoczonych to >50 ms. To opóźnienie może się różnić od żądania żądania w zależności od trasy wykonywanej przez żądanie, gdy przechodzi od klienta do granicy centrum danych platformy Azure. Najniższe możliwe opóźnienie jest realizowane przez zagwarantowanie, że aplikacja wywołująca znajduje się w tym samym regionie platformy Azure, co punkt końcowy Azure Cosmos DB aprowizacji. Aby uzyskać listę dostępnych regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/#services).

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Ilustracja zasad połączenia Azure Cosmos DB" border="false":::

    Aplikacja, która współdziała z wieloregionowym kontem Azure Cosmos DB musi skonfigurować [preferowane lokalizacje](tutorial-global-distribution-sql-api.md#preferred-locations) , aby upewnić się, że żądania przechodzą do obszaru rozmieszczonego.

* **Włącz przyspieszone sieci na maszynie wirtualnej platformy Azure w celu uzyskania małych opóźnień.**

Zaleca się wykonanie instrukcji w celu włączenia przyspieszonej sieci w [systemie Windows (kliknij w celu uzyskania instrukcji)](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) lub [Linux (kliknij, aby uzyskać instrukcje)](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) maszyna wirtualna platformy Azure w celu zmaksymalizowania wydajności.

Bez przyspieszonej sieci operacje we/wy, które przesyłają między maszyną wirtualną platformy Azure i innymi zasobami platformy Azure, mogą być niepotrzebnie kierowane za pomocą hosta i przełącznika wirtualnego znajdującego się między maszyną wirtualną a jej kartą sieciową. Posiadanie hosta i przełącznika wirtualnego w ścieżce datapath nie tylko zwiększa opóźnienia i wahania w kanale komunikacji, a także zmniejsza cykle procesora z maszyny wirtualnej. W przypadku przyspieszonej sieci interfejsy maszyn wirtualnych są bezpośrednio z karty sieciowej bez pośredników; wszystkie szczegóły zasad sieciowych, które były obsługiwane przez hosta i przełącznik wirtualny, są teraz obsługiwane sprzętowo na karcie sieciowej. Host i przełącznik wirtualny są pomijane. Ogólnie można oczekiwać mniejszego opóźnienia i większej przepływności, a także bardziej *spójnego* opóźnienia i obniżenia użycia procesora po włączeniu przyspieszonej sieci.

Ograniczenia: przyspieszone sieci muszą być obsługiwane w systemie operacyjnym maszyny wirtualnej i można je włączyć tylko wtedy, gdy maszyna wirtualna została zatrzymana i cofnięta alokacja. Nie można wdrożyć maszyny wirtualnej przy użyciu Azure Resource Manager.

Aby uzyskać więcej informacji, zobacz instrukcje dotyczące [systemów Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) i [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) .

## <a name="sdk-usage"></a>SDK usage (Użycie zestawu SDK)
* **Zainstaluj najnowszy zestaw SDK**

    Zestawy SDK Azure Cosmos DB są stale ulepszane w celu zapewnienia najlepszej wydajności. Zobacz strony [zestawu sdk Azure Cosmos DB](sql-api-sdk-async-java.md) , aby określić najnowszy zestaw SDK i zapoznać się z ulepszeniami.

* **Używanie pojedynczego klienta Azure Cosmos DB w okresie istnienia aplikacji**

    Każde wystąpienie klienta Azure Cosmos DB jest bezpieczne wątkowo i realizuje wydajne zarządzanie połączeniami oraz buforowanie adresów. Aby umożliwić efektywne zarządzanie połączeniami i lepszą wydajność przez klienta Azure Cosmos DB, zaleca się użycie jednego wystąpienia klienta Azure Cosmos DB na domenę aplikacji przez okres istnienia aplikacji.

   <a id="max-connection"></a>

* **Użyj najniższego poziomu spójności wymaganego przez aplikację**

    Podczas tworzenia *CosmosClient*domyślna spójność użyta, jeśli nie jest jawnie ustawiona, to *Session*. Jeśli w logice aplikacji nie jest wymagana spójność *sesji* , ustaw dla *Eventual*niej *spójność* . Uwaga: zaleca się użycie co najmniej spójności *sesji* w aplikacjach korzystających z procesora Azure Cosmos DBego źródła zmian.

* **Korzystanie z asynchronicznego interfejsu API w celu maksymalnego zainicjowanej przepływności**

    Azure Cosmos DB zestawu Java SDK v4 pakietuje dwa interfejsy API, synchronizację i asynchroniczne. Ogólnie mówiąc, asynchroniczny interfejs API implementuje funkcjonalność zestawu SDK, natomiast interfejs API synchronizacji jest elastyczną otoką, która umożliwia blokowanie wywołań do asynchronicznego interfejsu API. Jest to przeciwieństwem do starszej wersji Azure Cosmos DB Async Java SDK V2, która była tylko asynchroniczna, oraz do starszej wersji Azure Cosmos DB synchronizacji Java SDK V2, która była tylko synchronizowana i miała całkowicie oddzielną implementację. 
    
    Wybór interfejsu API jest określany podczas inicjowania klienta. *CosmosAsyncClient* obsługuje ASYNCHRONICZNE interfejsy API, a *CosmosClient* obsługuje interfejs API synchronizacji. 
    
    Asynchroniczny interfejs API implementuje nieblokującą operację we/wy i jest najlepszym wyborem, jeśli celem jest maksymalne użycie przepływności podczas wystawiania żądań do Azure Cosmos DB. 
    
    Korzystanie z interfejsu API synchronizacji może być właściwym wyborem, jeśli potrzebny jest lub wymagany interfejs API, który blokuje odpowiedź na każde żądanie, lub jeśli operacja synchroniczna jest modelem dominującym w aplikacji. Można na przykład użyć interfejsu API synchronizacji w przypadku utrwalania danych w celu Azure Cosmos DB w aplikacji mikrousług, pod warunkiem że przepływność nie jest krytyczna.  
    
    Należy pamiętać, że przepustowość interfejsu API synchronizacji jest obniżana wraz ze wzrostem czasu odpowiedzi na żądania, natomiast asynchroniczny interfejs API umożliwia nasycenie pełnych możliwości przepustowości sprzętu. 
    
    Kolokacja geograficzna może zapewnić większą i bardziej spójną przepływność podczas korzystania z interfejsu API synchronizacji (zobacz [kolokacja klientów w tym samym regionie świadczenia usługi Azure](#collocate-clients)), ale nadal nie jest oczekiwane przekroczenie asynchronicznej PRZEPŁYWNOŚCI interfejsu API.

    Niektórzy użytkownicy mogą również być nieobeznani z [aktorem projektu](https://projectreactor.io/), platformą Reactive Streams używaną do implementowania ASYNCHRONICZNEGO interfejsu API zestawu SDK Java w wersji 4 Azure Cosmos DB. Jeśli jest to problem, zalecamy zapoznanie się z [przewodnikiem po wstępnym wzorcu reaktora](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) , a następnie zapoznaj się z tym [wprowadzeniem, aby](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro) ponownie wykorzystać programowanie w celu zapoznania się z Tobą. Jeśli użyto już Azure Cosmos DB z interfejsem asynchronicznym, a użyty zestaw SDK został Azure Cosmos DB Async SDK w wersji 2, można zapoznać się z [ReactiveX](http://reactivex.io/) / tematem[RxJava](https://github.com/ReactiveX/RxJava) ActiveX, ale bez względu na to, co zmieniło się w reaktorze projektu. W takim przypadku zapoznaj się z [przewodnikiem reaktora a RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) .

    Poniższe fragmenty kodu pokazują, jak zainicjować klienta Azure Cosmos DB na potrzeby asynchronicznego interfejsu API lub operacji synchronizacji interfejsu API odpowiednio:

    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Zestaw SDK Java v4

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientAsync)]

    # <a name="sync"></a>[Synchronizacja](#tab/api-sync)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) — interfejs API synchronizacji

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientSync)]

    --- 

* **Dostrajanie ConnectionPolicy**

    Domyślnie żądania Cosmos DB trybu bezpośredniego są wykonywane za pośrednictwem protokołu TCP podczas korzystania z Azure Cosmos DB Java SDK v4. Tryb wewnętrznie w trybie bezpośrednim używa specjalnej architektury do dynamicznego zarządzania zasobami sieci i uzyskiwania najlepszej wydajności.

    W Azure Cosmos DB Java SDK v4 tryb bezpośredni jest najlepszym wyborem, aby zwiększyć wydajność bazy danych przy użyciu większości obciążeń. 

    * ***Przegląd trybu bezpośredniego***

        :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="Ilustracja zasad połączenia Azure Cosmos DB" border="false":::

        Architektura po stronie klienta stosowana w trybie bezpośrednim umożliwia przewidywalne wykorzystanie sieci i dostęp do multipleksera Azure Cosmos DB replik. Na powyższym diagramie przedstawiono sposób, w jaki tryb Direct kieruje żądania klientów do replik w Cosmos DB zaplecza. Architektura trybu bezpośredniego przydziela do 10 **kanałów** po stronie klienta na replikę bazy danych. Kanał jest połączeniem TCP poprzedzonym buforem żądania, który ma 30 żądań głębokiego. Kanały należące do repliki są przydzielane dynamicznie zgodnie z wymaganiami **punktu końcowego usługi**repliki. Gdy użytkownik wystawia żądanie w trybie bezpośrednim, **TransportClient** kieruje żądanie do odpowiedniego punktu końcowego usługi na podstawie klucza partycji. **Kolejka żądań** buforuje żądania przed punktem końcowym usługi.

    * ***Opcje konfiguracji trybu bezpośredniego***

        Jeśli pożądane jest zachowanie trybu bezpośredniego inne niż domyślne, Utwórz wystąpienie *DirectConnectionConfig* i Dostosuj jego właściwości, a następnie Przekaż niestandardowe wystąpienie właściwości do metody *directmode ()* w Azure Cosmos DB konstruktorze klienta.

        Te ustawienia konfiguracji sterują zachowaniem podstawowej architektury trybu bezpośredniego omówionej powyżej.

        Pierwszym krokiem jest użycie poniższych zalecanych ustawień konfiguracji. Te opcje *DirectConnectionConfig* są zaawansowane ustawienia konfiguracji, które mogą wpływać na wydajność zestawu SDK w nieoczekiwany sposób. Zalecamy, aby użytkownicy nie mogli ich modyfikować, chyba że obawiają się one w zrozumieniu kompromisów i są absolutnie niezbędne. Skontaktuj się z [zespołem Azure Cosmos DB](mailto:CosmosDBPerformanceSupport@service.microsoft.com) , jeśli wystąpią problemy z tym konkretnym tematem.

        | Opcja konfiguracji       | Domyślny    |
        | :------------------:       | :-----:    |
        | idleConnectionTimeout      | "PT1M"     |
        | maxConnectionsPerEndpoint  | "PT0S"     |
        | connectTimeout             | "PT1M10S"  |
        | idleEndpointTimeout        | 8388608    |
        | maxRequestsPerConnection   | 10         |

* **Dostrajanie równoległych zapytań dla kolekcji partycjonowanych**

    Azure Cosmos DB Java SDK v4 obsługuje zapytania równoległe, które umożliwiają równoległe wykonywanie zapytań do kolekcji partycjonowanej. Aby uzyskać więcej informacji, zobacz [przykłady kodu](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) związane z pracą z programem Azure Cosmos DB Java SDK v4. Zapytania równoległe są przeznaczone do poprawiania opóźnienia zapytań i przepływności w porównaniu z ich odpowiednikami seryjnymi.

    * ***Dostrajanie setMaxDegreeOfParallelism\:***
    
        Zapytania równoległe działają przez wykonywanie zapytań na wielu partycjach równolegle. Jednak dane z pojedynczej kolekcji partycjonowanej są pobierane sekwencyjnie w odniesieniu do zapytania. W tym celu należy użyć setMaxDegreeOfParallelism, aby ustawić liczbę partycji, które mają maksymalną szansę osiągnięcia najbardziej wydajnego zapytania, pod warunkiem, że wszystkie inne warunki systemu pozostają takie same. Jeśli nie znasz liczby partycji, możesz użyć setMaxDegreeOfParallelism, aby ustawić dużą liczbę, a system wybierze minimalną (liczbę partycji, dane wejściowe podane przez użytkownika) jako maksymalny stopień równoległości.

        Należy pamiętać, że zapytania równoległe generują najlepsze korzyści, jeśli dane są równomiernie dystrybuowane we wszystkich partycjach w odniesieniu do zapytania. Jeśli partycjonowana kolekcja jest partycjonowana w taki sposób, że wszystkie lub większość danych zwróconych przez zapytanie jest skoncentrowana na kilku partycjach (jedna partycja w najgorszym przypadku), wydajność zapytania zostałaby przekazana przez te partycje.

    * ***Dostrajanie setMaxBufferedItemCount\:***
    
        Zapytanie równoległe zostało zaprojektowane w celu wstępnego pobrania wyników, podczas gdy bieżąca partia wyników jest przetwarzana przez klienta. Wstępne pobieranie pomaga w ogólnym ulepszaniu opóźnienia zapytania. setMaxBufferedItemCount ogranicza liczbę wstępnie pobranych wyników. Ustawienie setMaxBufferedItemCount na oczekiwaną liczbę zwracanych wyników (lub wyższą liczbę) powoduje, że zapytanie otrzymuje maksymalną korzyść przed pobraniem.

        Przed pobraniem działa w taki sam sposób, niezależnie od MaxDegreeOfParallelism, i istnieje jeden bufor dla danych ze wszystkich partycji.

* **Skalowanie obciążenia klienta**

    Jeśli testujesz się na poziomach o wysokiej przepływności, aplikacja kliencka może stać się wąskim gardłem, ponieważ maszyna jest ograniczona do użycia procesora CPU lub sieci. Jeśli docierasz do tego punktu, możesz kontynuować wypychanie konta Azure Cosmos DB przez skalowanie aplikacji klienckich na wiele serwerów.

    Dobrą zasadą dla kciuka nie jest przekroczenie >50% użycia procesora CPU na dowolnym serwerze, aby zachować niskie opóźnienia.

   <a id="tune-page-size"></a>

* **Dostosuj rozmiar strony dla zapytań/Odczytaj źródła w celu uzyskania lepszej wydajności**

    Podczas wykonywania zbiorczego odczytu dokumentów przy użyciu funkcji odczytywania kanału informacyjnego (na przykład *readItems*) lub podczas wystawiania zapytania SQL (*queryItems*) wyniki są zwracane w postaci segmentacji, jeśli zestaw wyników jest zbyt duży. Domyślnie wyniki są zwracane w fragmentach 100 elementów lub 1 MB, w zależności od tego, który limit zostanie osiągnięty jako pierwszy.

    Załóżmy, że aplikacja wysyła zapytanie do Azure Cosmos DB i Załóżmy, że aplikacja wymaga pełnego zestawu wyników zapytania, aby zakończyć jego zadanie. Aby zmniejszyć liczbę podróży sieci wymaganych do pobrania wszystkich odpowiednich wyników, można zwiększyć rozmiar strony przez dostosowanie pola nagłówka żądania [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) . 

    * W przypadku zapytań z jedną partycją dopasowanie wartości pola " [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) " do-1 (bez limitu rozmiaru strony) maksymalizuje opóźnienie przez zminimalizowanie liczby stron odpowiedzi na zapytania: pełny zestaw wyników zwróci wartość na jednej stronie lub jeśli zapytanie trwa dłużej niż interwał limitu czasu, wówczas pełny zestaw wyników zostanie zwrócony w minimalnej liczbie możliwych stron. Powoduje to zapisanie wielokrotności czasu rundy żądania.
    
    * W przypadku zapytań między partycjami ustawienie pola [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) na wartość-1 i usunięcie ograniczenia rozmiaru strony, które zapychają zestaw SDK, za pomocą niezarządzanych rozmiarów stron. W przypadku rozdzielenia między partycjami zaleca się podwyższenie rozmiaru strony do pewnej, ale skończonej wartości, prawdopodobnie 1000, aby zmniejszyć opóźnienie. 
    
    W niektórych aplikacjach nie można wymagać pełnego zestawu wyników zapytania. W przypadkach, gdy konieczne jest wyświetlenie tylko kilku wyników, na przykład jeśli interfejs użytkownika lub interfejsu API aplikacji zwraca tylko 10 wyników naraz, można również zmniejszyć rozmiar strony do 10, aby zmniejszyć przepływność wykorzystywaną do odczytu i zapytań.

    Możesz również ustawić preferowany argument rozmiaru strony metody *byPage* zamiast bezpośrednio modyfikować pole nagłówka Rest. Należy pamiętać, że argument [x-MS-Max-Item-Count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) lub preferowany rozmiar strony *byPage* dotyczy tylko górnego limitu rozmiaru strony, a nie bezwzględnego wymagania; w związku z tym z różnych przyczyn mogą pojawić się Azure Cosmos DB zwracają strony, które są mniejsze niż preferowany rozmiar strony. 

* **Użyj odpowiedniego harmonogramu (Unikaj kradzieży wątków wielosieciowych we/wy pętli zdarzeń)**

    Funkcja asynchroniczna Azure Cosmos DB Java SDK jest [oparta na](https://netty.io/) dyskach nieblokujących we/wy. Zestaw SDK używa ustalonej liczby wątków pętli zdarzeń we/wy (w przypadku wielu rdzeni procesora) na potrzeby wykonywania operacji we/wy. Strumień zwrócony przez interfejs API emituje wynik na jednym ze współużytkowanych wątków pętli zdarzeń we/wy. W związku z tym ważne jest, aby nie blokować wspólnych wątków pętli na potrzeby operacji we/wy. Wykonywanie zadań intensywnie korzystających z procesora CPU lub operacji blokowania w wątku sieci w pętli zdarzeń we/wy może spowodować zakleszczenie lub znacząco zredukować przepływność zestawu SDK.

    Na przykład poniższy kod wykonuje prace intensywnie korzystające z procesora CPU w wątku wielosieciowych operacji we/wy pętli:
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNeedsSchedulerAsync)]

    Po otrzymaniu wyniku, jeśli chcesz wykonać intensywną moc procesora CPU w wyniku, należy unikać wykonywania tej operacji w wątku z wielosieciowymi pętlami we/wy. Zamiast tego możesz wprowadzić własny harmonogram, aby zapewnić własny wątek do uruchamiania pracy, jak pokazano poniżej (wymagane `import reactor.core.scheduler.Schedulers` ).

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddSchedulerAsync)]

    Na podstawie typu pracy należy użyć odpowiedniego istniejącego harmonogramu reaktora dla pracy. Przeczytaj tutaj [``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html) .

    Aby uzyskać więcej informacji na temat Azure Cosmos DB Java SDK v4, zapoznaj się z [katalogiem Cosmos DB zestawu Azure SDK for Java w witrynie GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos).

* **Optymalizowanie ustawień rejestrowania w aplikacji**

    Z różnych powodów może być konieczne lub konieczne dodanie rejestrowania w wątku, który generuje przepływność żądań o wysokim poziomie. Jeśli celem jest pełne nasycenie przepływności aprowizacji kontenera z żądaniami wygenerowanymi przez ten wątek, optymalizacje rejestrowania mogą znacznie poprawić wydajność.

    * ***Konfigurowanie rejestratora asynchronicznego***

        Opóźnienie rejestratora synchronicznego musi być powiązane z ogólnym obliczaniem opóźnienia wątku generującego żądanie. Do rozdzielania obciążeń z wątków aplikacji o wysokiej wydajności zaleca się rejestrowanie asynchroniczne, takie jak [log4j2](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0) .

    * ***Wyłącz rejestrowanie sieci na sieć***

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

* **Określ klucz partycji w obszarze zapisy**

    Aby zwiększyć wydajność operacji zapisu, należy określić klucz partycji elementu w wywołaniu interfejsu API zapisu, jak pokazano poniżej:

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNoPKAsync)]

    # <a name="sync"></a>[Synchronizacja](#tab/api-sync)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) — interfejs API synchronizacji

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceNoPKSync)]

    --- 

    zamiast udostępniać tylko wystąpienie elementu, jak pokazano poniżej:

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddPKAsync)]

    # <a name="sync"></a>[Synchronizacja](#tab/api-sync)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) — interfejs API synchronizacji

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceAddPKSync)]

    --- 

    Ta ostatnia jest obsługiwana, ale spowoduje dodanie opóźnienia do aplikacji. zestaw SDK musi przeanalizować element i wyodrębnić klucz partycji.

## <a name="indexing-policy"></a>Zasady indeksowania
 
* **Wykluczanie nieużywanych ścieżek z indeksowania w celu przyspieszenia operacji zapisu**

    Zasady indeksowania Azure Cosmos DB umożliwiają określenie, które ścieżki dokumentów mają być dołączone lub wykluczone z indeksowania przy użyciu ścieżek indeksowania (setIncludedPaths i setExcludedPaths). Użycie ścieżek indeksowania może oferować ulepszoną wydajność zapisu i niższy indeks magazynu dla scenariuszy, w których wzorce zapytania są znane wcześniej, ponieważ koszty indeksowania są bezpośrednio skorelowane z liczbą unikatowych ścieżek indeksowanych. Na przykład poniższy kod pokazuje, jak dołączać i wykluczać całe sekcje dokumentów (nazywanych również poddrzewami) z indeksowania przy użyciu symbolu wieloznacznego "*".

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

    Aby uzyskać więcej informacji, zobacz [Azure Cosmos DB zasad indeksowania](indexing-policies.md).

## <a name="throughput"></a>Przepływność
<a id="measure-rus"></a>

* **Mierzenie i dostrajanie dla niższych jednostek żądań/drugiego użycia**

    Azure Cosmos DB oferuje bogaty zestaw operacji bazy danych, w tym relacyjne i hierarchiczne zapytania z UDF, procedurami składowanymi i wyzwalaczami — wszystko to działa na dokumentach w ramach kolekcji baz danych. Koszt związany z każdą z tych operacji zależy od procesora, danych We/Wy i pamięci wymaganej do wykonania danej operacji. Zamiast rozważać zasoby sprzętowe i zarządzać nimi, można traktować jednostkę żądania (RU) jako pojedynczą miarę dla zasobów wymaganych do wykonywania różnych operacji bazy danych i obsługi żądania aplikacji.

    Obsługa przepływności zależy od liczby [jednostek żądania](request-units.md) ustawionych dla każdego kontenera. Użycie jednostki żądania jest oceniane jako stawka na sekundę. Aplikacje, które przekraczają zainicjowaną stawkę jednostkową żądania dla ich kontenera, są ograniczone do momentu spadku stawki poniżej poziomu aprowizacji kontenera. Jeśli aplikacja wymaga wyższego poziomu przepływności, można zwiększyć przepływność przez zainicjowanie obsługi dodatkowych jednostek żądania.

    Złożoność zapytania wpływa na liczbę jednostek żądań używanych dla operacji. Liczba predykatów, charakter predykatów, liczba UDF i rozmiar zestawu danych źródłowych wpływają na koszt operacji zapytania.

    Aby zmierzyć obciążenie związane z jakąkolwiek operacją (tworzenie, aktualizowanie lub usuwanie), Sprawdź nagłówek [x-MS-Request-opłata](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) , aby zmierzyć liczbę jednostek żądań używanych przez te operacje. Możesz również przyjrzeć się równoważnej właściwości RequestCharge w ResourceResponse \<T> lub FeedResponse \<T> .

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceRequestChargeAsync)]

    # <a name="sync"></a>[Synchronizacja](#tab/api-sync)

    Java SDK v4 (Maven com. Azure:: Azure-Cosmos) — interfejs API synchronizacji

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceRequestChargeSync)]

    --- 

    Opłata za żądanie zwrócona w tym nagłówku jest częścią alokowanej przepływności. Na przykład jeśli masz 2000 RU/s, a poprzednia kwerenda zwróci 1000 rozmiarze 1 KB-Documents, koszt operacji to 1000. W związku z tym w ciągu jednej sekundy serwer honoruje tylko dwa takie żądania przed szybkością ograniczania kolejnych żądań. Aby uzyskać więcej informacji, zobacz [jednostki żądań](request-units.md) i [Kalkulator jednostek żądania](https://www.documentdb.com/capacityplanner).

<a id="429"></a>
* **Zbyt duży współczynnik obsługi — limit liczby żądań**

    Gdy klient próbuje przekroczyć zarezerwowaną przepływność dla konta, nie ma obniżenia wydajności na serwerze i nie będzie korzystać z wydajności przepływności poza poziomem zarezerwowanym. Serwer zapobiegawczo zakończyć żądanie z RequestRateTooLarge (kod stanu HTTP 429) i zwróci nagłówek [x-MS-retry-After-MS](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) wskazujący ilość czasu (w milisekundach), przez który użytkownik musi czekać przed ponowną próbą wykonania żądania.

    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```

    Zestaw SDK niejawnie przechwytuje tę odpowiedź, przestrzegając określonego przez serwer nagłówka retry-After i ponów próbę wykonania żądania. O ile Twoje konto nie jest dostępne współbieżnie przez wielu klientów, kolejna próba powiodła się.

    Jeśli masz więcej niż jeden klient, który działa w sposób ciągły nad częstotliwością żądań, domyślna liczba ponownych prób aktualnie ustawiona na 9 wewnętrznie przez klienta może nie być wystarczająca; w takim przypadku klient zgłasza *CosmosClientException* z kodem stanu 429 do aplikacji. Domyślną liczbę ponownych prób można zmienić za pomocą setRetryOptions w wystąpieniu ConnectionPolicy. Domyślnie *CosmosClientException* z kodem stanu 429 jest zwracany po skumulowanym czasie oczekiwania 30 sekund, jeśli żądanie będzie nadal działać powyżej stawki żądania. Dzieje się tak nawet wtedy, gdy bieżąca liczba ponownych prób jest mniejsza niż maksymalna liczba ponownych prób, być wartością domyślną 9 lub wartości zdefiniowanej przez użytkownika.

    Mimo że automatyczne zachowanie ponowienia próby pozwala zwiększyć odporność i użyteczność dla większości aplikacji, może się to zdarzyć szanse podczas wykonywania testów wydajnościowych, szczególnie podczas mierzenia opóźnień. Opóźnienie obserwowane przez klienta zostanie wykonane, jeśli eksperyment trafi na ograniczenia serwera i spowoduje, że zestaw SDK klienta zostanie ponownie powtórzony. Aby uniknąć opóźnień opóźnienia podczas eksperymentów w wydajności, należy zmierzyć opłaty zwrócone przez poszczególne operacje i upewnić się, że żądania działają poniżej zarezerwowanej stawki żądania. Aby uzyskać więcej informacji, zobacz [jednostki żądania](request-units.md).

* **Projektowanie dla mniejszych dokumentów w celu zwiększenia przepływności**

    Opłata za żądanie (koszt przetwarzania żądania) danej operacji jest bezpośrednio skorelowana z rozmiarem dokumentu. Operacje na dużych dokumentach są droższe niż operacje w przypadku małych dokumentów. Najlepiej, aby zaprojektować aplikację i przepływy pracy w celu uzyskania rozmiaru elementu ~ rozmiarze 1 KB lub podobnej kolejności lub wielkości. W przypadku aplikacji z uwzględnieniem opóźnień należy unikać dużej ilości dokumentów — wiele MB spowoduje spowolnienie działania aplikacji.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat projektowania aplikacji pod kątem skalowania i wysokiej wydajności, zobacz [partycjonowanie i skalowanie w Azure Cosmos DB](partitioning-overview.md).
