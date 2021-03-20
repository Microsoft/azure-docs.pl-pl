---
title: Federacja wielolokacjowa i wieloregionowa — platforma Azure Event Hubs | Microsoft Docs
description: Ten artykuł zawiera omówienie Federacji wielolokacjowej i wieloregionowej przy użyciu usługi Azure Event Hubs.
ms.topic: article
ms.date: 12/12/2020
ms.author: spelluru
ms.openlocfilehash: 12ef895c8b16fe18ed02ebf01d17624ac71c2f3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861463"
---
# <a name="multi-site-and-multi-region-federation"></a>Federacja z wieloma lokacjami i wieloma regionami

Wiele zaawansowanych rozwiązań wymaga, aby te same strumienie zdarzeń były dostępne do użycia w wielu lokalizacjach, lub wymagają, aby strumienie zdarzeń były zbierane w wielu lokalizacjach, a następnie skonsolidowane w określonej lokalizacji w celu użycia. Często istnieje również potrzeba wzbogacania lub zmniejszania strumieni zdarzeń lub wykonywania konwersji w formacie zdarzeń, również w ramach jednego regionu i rozwiązania.

Praktycznie, oznacza to, że rozwiązanie będzie obsługiwać wiele Event Hubs, często w różnych regionach i Event Hubs przestrzenie nazw, a następnie replikować zdarzenia między nimi. Możesz również wymienić zdarzenia ze źródłami i obiektami docelowymi, takimi jak [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md), [Azure IoT Hub](../iot-fundamentals/iot-introduction.md)lub [Apache Kafka](https://kafka.apache.org). 

Obsługa wielu aktywnych Event Hubs w różnych regionach umożliwia również klientom wybór i przełączenie się między nimi w przypadku scalania ich zawartości, co sprawia, że ogólny system jest bardziej odporny na problemy z dostępnością regionalnymi.

W tym rozdziale "Federacja" objaśniono wzorce federacyjne i sposób wykonywania tych wzorców przy użyciu [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) bezserwerowych lub [Azure Functions][1] środowiska uruchomieniowego, z opcją posiadania własnego przekształcenia lub kodu wzbogacania bezpośrednio w ścieżce przepływu zdarzeń. 

## <a name="federation-patterns"></a>Wzorce Federacji

Istnieje wiele potencjalnych powodów, dla których warto przenieść zdarzenia między różnymi Event Hubsami lub innymi źródłami i obiektami docelowymi. wyliczamy najważniejsze wzorce w tej sekcji, a także łączą się z bardziej szczegółowymi wskazówkami dotyczącymi odpowiedniego wzorca. 

- [Odporność na zdarzenia dostępności regionalnej](#resiliency-against-regional-availability-events)
- [Optymalizacja opóźnienia](#latency-optimization)
- [Sprawdzanie poprawności, zmniejszenie i wzbogacanie](#validation-reduction-and-enrichment)
- [Integracja z usługami Analytics Services](#integration-with-analytics-services)
- [Konsolidacja i normalizacja strumieni zdarzeń](#consolidation-and-normalization-of-event-streams)
- [Dzielenie i Routing strumieni zdarzeń](#splitting-and-routing-of-event-streams)
- [Projekcje dziennika](#log-projections)
  
### <a name="resiliency-against-regional-availability-events"></a>Odporność na zdarzenia dostępności regionalnej 

![Dostępność regionalna](media/event-hubs-federation-overview/regional-availability.jpg)

Chociaż maksymalna dostępność i niezawodność są najwyższymi priorytetami operacyjnymi Event Hubs, istnieje wiele sposobów, w których producent lub odbiorca może być niedostępna do swojego przypisanego "podstawowego" centrum zdarzeń z powodu problemów z siecią lub rozpoznawaniem nazw lub w przypadku, gdy centrum zdarzeń może faktycznie przestać odpowiadać lub zwracać błędy. 

Takie warunki nie są "katastrofalne" w taki sposób, że chcesz zrezygnować z wdrożenia regionalnego, jak to możliwe w sytuacji [odzyskiwania po awarii](event-hubs-geo-dr.md) , ale scenariusz biznesowy niektórych aplikacji może mieć już wpływ na zdarzenia dostępności, które nie przekraczają kilku minut, a nawet sekund. 

Istnieją dwa podstawowe wzorce umożliwiające rozwiązanie takich scenariuszy:

- Wzorzec [replikacji][4] polega na replikacji zawartości podstawowego centrum zdarzeń do pomocniczego centrum zdarzeń, w którym podstawowe centrum zdarzeń jest zwykle używane przez aplikację do tworzenia i zużywania zdarzeń, a pomocnicza służy jako opcja rezerwowa w przypadku, gdy główne centrum zdarzeń staje się niedostępne. Ponieważ replikacja jest jednokierunkowe, od podstawowego do pomocniczego, przełączenie zarówno producentów, jak i konsumentów z niedostępnego elementu podstawowego do pomocniczego spowoduje, że stary element podstawowy nie będzie już otrzymywał nowych zdarzeń i w związku z tym nie jest już aktualny.
  Czysta replikacja jest w związku z tym odpowiednia tylko dla jednokierunkowych scenariuszy trybu failover. Po przełączeniu w tryb failover stary serwer podstawowy zostanie porzucony, a nowe pomocnicze centrum zdarzeń należy utworzyć w innym regionie docelowym.
- Wzorzec [scalania][5] rozszerza wzorzec replikacji przez wykonywanie ciągłego scalania zawartości co najmniej dwóch Event Hubs. Każde zdarzenie, które zostało pierwotnie utworzone w jednym z Event Hubs zawartych w schemacie, jest replikowane do innego Event Hubs. Gdy zdarzenia są replikowane, są one adnotacją w taki sposób, że są następnie ignorowane przez proces replikacji obiektu docelowego replikacji. Wyniki użycia wzorca scalania są dwa lub więcej Event Hubs, które będą zawierać ten sam zestaw zdarzeń w ostatecznie spójny sposób. 
  
W obu przypadkach zawartość Event Hubs nie będzie taka sama. Zdarzenia od dowolnego producenta i pogrupowane według tego samego klucza partycji będą wyświetlane w tej samej kolejności względnej co oryginalnie przesłane, ale bezwzględna kolejność zdarzeń może się różnić. Jest to szczególnie prawdziwe w scenariuszach, w których liczba partycji dla Event Hubs źródłowych i docelowych różni się, co jest pożądane dla kilku wzorców rozszerzonych opisanych tutaj. [Rozdzielacz lub router](#splitting-and-routing-of-event-streams) mogą uzyskać wycink o znacznie większym centrum zdarzeń z setkami partycji i lejków do mniejszego centrum zdarzeń z kilkuą partycjami, co jest bardziej odpowiednie do obsługi podzbioru z ograniczoną ilością zasobów. Odwrotnie, [Konsolidacja](#consolidation-and-normalization-of-event-streams) może zwiększyć rozmiar danych z kilku mniejszych Event Hubs w jednym, większym centrum zdarzeń z więcej partycji, aby sprostać skonsolidowanej przepływności i przetwarzania.

Kryterium utrzymywania zdarzeń w połączeniu jest kluczem partycji, a nie oryginalnym IDENTYFIKATORem partycji. Dodatkowe zagadnienia dotyczące powiązanej kolejności i wykonywania przejścia w tryb failover z jednego centrum zdarzeń do następnego bez polegania na tym samym zakresie przesunięć strumienia jest omawiane w opisie wzorca [replikacji][4] .


Informator 
- [Wzorzec replikacji][4]
- [Scalanie wzorca][5]

### <a name="latency-optimization"></a>Optymalizacja opóźnienia 

![Optymalizacja opóźnienia](media/event-hubs-federation-overview/latency-optimization.jpg)  

Strumienie zdarzeń są zapisywane jeden raz przez producentów, ale mogą być odczytywane przez odbiorców zdarzeń przez wiele razy. W przypadku scenariuszy, w których strumień zdarzeń w regionie jest współużytkowany przez wielu użytkowników i musi być wielokrotnie dostępny podczas przetwarzania analizy znajdującego się w innym regionie lub w ramach wszystkich wymagań, które mogłyby zablokować dostępć klientów współbieżnych, warto umieścić kopię strumienia zdarzeń w sąsiedztwie procesora analitycznego, aby zmniejszyć opóźnienie komunikacji dwukierunkowej. 

Dobrym przykładem, kiedy replikacja powinna być preferowana w odniesieniu do zużywanych zdarzeń zdalnie z różnych regionów, jest szczególnie w przypadku, gdy regiony są skrajnie oddalone od siebie, na przykład Europa i Australia są niemal antipodese. opóźnienie geograficznie i sieć może łatwo przekroczyć 250 MS dla każdej rundy.
Nie jest możliwe przyspieszenie światła, ale można zmniejszyć liczbę rejsów o dużej opóźnieniu na współpracujące z danymi.

Informator 
- [Wzorzec replikacji][4]

### <a name="validation-reduction-and-enrichment"></a>Sprawdzanie poprawności, zmniejszenie i wzbogacanie

![Sprawdzanie poprawności, zmniejszenie, wzbogacanie](media/event-hubs-federation-overview/validation-enrichment.jpg)  

Strumienie zdarzeń mogą być przesyłane do centrum zdarzeń przez klientów zewnętrznych do własnych rozwiązań. Takie strumienie zdarzeń mogą wymagać zaewidencjonowania zdarzeń przesłanych zewnętrznie pod kątem zgodności z danym schematem, a w przypadku porzucenia niezgodnych zdarzeń. 

W scenariuszach, w których klienci mają ograniczoną przepustowość, tak jak w przypadku wielu scenariuszy "Internet rzeczy" z naliczaną przepustowością lub w przypadku, gdy zdarzenia są pierwotnie wysyłane za pośrednictwem sieci innych niż IP z ograniczonymi rozmiarami pakietów, może być konieczne wzbogacanie zdarzeń z danymi referencyjnymi w celu dodania kontekstu do użycia przez procesory zdarzeń podrzędnych.

W innych przypadkach, szczególnie gdy strumienie są konsolidowane, dane zdarzenia mogą być ograniczone do złożoności lub zawiera rozmiaru, pomijając pewne szczegóły.

Każda z tych operacji może wystąpić w ramach replikacji, konsolidacji lub scalonych przepływów. 

Informator 
- [Wzorzec edytora][6]

### <a name="integration-with-analytics-services"></a>Integracja z usługami Analytics Services

![Integracja z usługami Analytics Services](media/event-hubs-federation-overview/integration.jpg)

Niektóre z usług analitycznych w chmurze na platformie Azure, takie jak Azure Stream Analytics lub Azure Synapse, działają najlepiej z danymi przesyłanymi strumieniowo lub wstępnie partiami, które są obsługiwane przez usługę Azure Event Hubs, a usługa Azure Event Hubs umożliwia także integrację z kilkoma pakietami analiz typu open source, takimi jak Apache Samza, Apache Flink, Apache Spark i Apache Storm. 

Jeśli rozwiązanie używa głównie Service Bus lub Event Grid, te zdarzenia można łatwo uzyskać dostęp do takich systemów analitycznych, a także do archiwizowania za pomocą funkcji Event Hubs Capture, jeśli są one umieszczane w centrum zdarzeń. Event Grid może to zrobić natywnie z [integracją centrum zdarzeń](../event-grid/handler-event-hubs.md), w przypadku Service Bus postępuj zgodnie ze [wskazówkami dotyczącymi replikacji Service Bus](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub).

Azure Stream Analytics [integruje się bezpośrednio z Event Hubs](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs).

Informator 
- [Wzorzec replikacji][4]

### <a name="consolidation-and-normalization-of-event-streams"></a>Konsolidacja i normalizacja strumieni zdarzeń

![Konsolidacja i normalizacja strumieni zdarzeń](media/event-hubs-federation-overview/consolidation.jpg)

Rozwiązania globalne często składają się z regionalnych miejsc, które są w dużej mierze niezależne, z uwzględnieniem ich własnych możliwości analitycznych, ale perspektywy analityczne Supra-regionalne i globalne będą wymagały zintegrowanej perspektywy i dlatego, że Centralna konsolidacja tych samych strumieni zdarzeń, które są oceniane w odniesieniu do regionów lokalnych. 

Normalizacja to wersja scenariusza konsolidacji, w którym co najmniej dwa przychodzące strumienie zdarzeń składają się na ten sam rodzaj zdarzeń, ale z różnymi strukturami lub różnymi metodami kodowania, a zdarzenia najczęściej są transkodowane lub przekształcone przed ich użyciem. 

Normalizacja może również obejmować zadania kryptograficzne, takie jak odszyfrowywanie kompleksowych ładunków szyfrowanych i ponowne szyfrowanie z użyciem różnych kluczy i algorytmów dla odbiorców dla klientów podrzędnych. 

Informator 
- [Scalanie wzorca][5]
- [Wzorzec edytora][6]

### <a name="splitting-and-routing-of-event-streams"></a>Dzielenie i Routing strumieni zdarzeń

![Dzielenie i Routing strumieni zdarzeń](media/event-hubs-federation-overview/splitting.jpg)

Usługa Azure Event Hubs jest sporadycznie używana w scenariuszach w stylu "publikowanie-subskrybowanie", w przypadku których przychodzące torrent zdarzeń pozyskanych przekraczają pojemność Azure Service Bus lub Azure Event Grid, z których oba mają natywne możliwości filtrowania i dystrybucji w ramach subskrypcji publikowania oraz są preferowane dla tego wzorca. 

Mimo że możliwość "publikowania-subskrybowania" pozostawia do subskrybentów możliwość wybierania żądanych zdarzeń, wzorzec podziału ma zamapować zdarzenia do partycji według wstępnie określonego modelu dystrybucji i wyznaczonych odbiorców, a następnie wypróbuje się wyłącznie z partycji "ich". Gdy centrum zdarzeń buforuje ogólny ruch, zawartość określonej partycji reprezentująca ułamek oryginalnego woluminu przepływności może być następnie replikowana do kolejki w celu zapewnienia niezawodnego, transakcyjnego, konkurencyjnego użycia klientów.

Wiele scenariuszy, w których Event Hubs jest używany głównie do przeniesienia zdarzeń w ramach aplikacji w obrębie regionu, zawiera kilka przypadków, w których wybrane zdarzenia mogą być tylko z jednej partycji, również muszą zostać udostępnione w innym miejscu. Ten scenariusz jest podobny do scenariusza dzielenia, ale może korzystać z skalowalnego routera, który traktuje wszystkie komunikaty docierające do centrum zdarzeń i selekcjonowanie, a także może odróżnić elementy docelowe routingu przez metadane lub zawartość zdarzeń. 

Informator
- [Wzorzec routingu][7]

### <a name="log-projections"></a>Projekcje dziennika 

![Projekcja dziennika](media/event-hubs-federation-overview/log-projection.jpg)

W niektórych scenariuszach użytkownik chce mieć dostęp do najnowszej wartości wysyłanej do dowolnego podstrumienia zdarzenia i zazwyczaj odróżnia klucz partycji. W Apache Kafka jest to często osiągane przez włączenie "kompaktowanie dzienników" w temacie, który odrzuca wszystkie oprócz najnowszego zdarzenia oznaczonego dowolnym unikatowym kluczem. Podejście kompaktowania dziennika ma trzy wady złożone: 

- Kompaktowanie wymaga ciągłej reorganizacji dziennika, który jest zbyt kosztowną operacją dla brokera, który jest zoptymalizowany pod kątem obciążeń przeznaczonych tylko do dołączania. 
- Kompaktowanie jest szkodliwe i nie pozwala na skompaktowaną i niezwartą perspektywę tego samego strumienia.
- Kompaktowy strumień nadal ma model dostępu sekwencyjnego, co oznacza, że znalezienie żądanej wartości w dzienniku wymaga odczytywania całego dziennika w najgorszym przypadku, który zwykle prowadzi do optymalizacji, która implementuje dokładny wzorzec przedstawiony tutaj: projekcja zawartości dziennika w bazie danych lub pamięci podręcznej. 

Ostatecznie dysk kompaktowy jest magazynem klucz-wartość i w związku z tym jest najgorszą możliwą opcją implementacji dla takiego magazynu. Jest to znacznie bardziej wydajne w przypadku wyszukiwań i zapytań do tworzenia i używania trwałego rzutu dziennika do odpowiedniego magazynu klucz-wartość lub innej bazy danych. 

Ze względu na to, że zdarzenia są niezmienne i zamówienie jest zawsze zachowywane w dzienniku, wszystkie projekcje dziennika w magazynie wartości klucz-wartość zawsze będą identyczne dla tego samego zakresu zdarzeń, co oznacza, że projekcja, która jest aktualizowana zawsze zapewnia autorytatywny widok i nigdy nie istnieje dobry powód, aby ponownie skompilować zawartość dziennika po skompilowaniu. 

Informator
- [Projekcja dziennika][8]

## <a name="replication-application-technologies"></a>Technologie aplikacji replikacji

Wdrożenie powyższych wzorców wymaga skalowalnego i niezawodnego środowiska wykonawczego dla zadań replikacji, które mają zostać skonfigurowane i uruchomione. Na platformie Azure środowiska uruchomieniowe, które najlepiej pasują do takich zadań, są zadaniami bezstanowymi, [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) dla zadań replikacji stanowej i [Azure Functions](../azure-functions/functions-overview.md) do zadań replikacji bezstanowej.

### <a name="stateful-replication-applications-in-azure-stream-analytics"></a>Aplikacje do replikacji stanowej w Azure Stream Analytics

W przypadku aplikacji do replikacji stanowych, które wymagają uwzględnienia relacji między zdarzeniami, tworzenia zdarzeń złożonych, wzbogacania zdarzeń lub zmniejszania zdarzeń, tworzenia agregacji danych i Transform ładunków zdarzeń, [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) jest najlepszą opcją implementacji.

W Azure Stream Analytics [tworzysz zadania](../stream-analytics/stream-analytics-quick-create-portal.md) , które integrują dane [wejściowe](../stream-analytics/stream-analytics-add-inputs.md) i [wyjściowe](../stream-analytics/stream-analytics-define-outputs.md) oraz integrują je z danych wejściowych za pośrednictwem [zapytań](/stream-analytics-query/stream-analytics-query-language-reference) , które dają wynik, który jest następnie dostępny w danych wyjściowych.

Zapytania są oparte na [języku zapytań SQL](/stream-analytics-query/stream-analytics-query-language-reference) i mogą być używane do łatwego filtrowania, sortowania, agregowania i przyłączania danych przesyłanych strumieniowo w danym okresie czasu. Możesz również zwiększyć ten język SQL za pomocą [języka JavaScript](../stream-analytics/stream-analytics-javascript-user-defined-functions.md) i [funkcji zdefiniowanych przez użytkownika w języku C# (UDF)](../stream-analytics/stream-analytics-edge-csharp-udf-methods.md). Można łatwo dostosować opcje porządkowania zdarzeń oraz czas trwania systemu Windows podczas wykonywania operacji agregacji za pomocą prostych konstrukcji i/lub konfiguracji języka.

Każde zadanie ma jedno lub kilka danych wyjściowych dla przekształconych danych i pozwala kontrolować, co dzieje się w odpowiedzi na informacje, które zostały przeanalizowane. Możesz na przykład:

- Wyślij dane do usług, takich jak Azure Functions, Service Bus tematy lub kolejki, aby wyzwolić komunikację lub niestandardowe przepływy pracy.
- Wyślij dane do pulpitu nawigacyjnego Power BI, aby uzyskać pulpit nawigacyjny w czasie rzeczywistym.
- Przechowuj dane w innych usługach Azure Storage (na przykład Azure Data Lake, Azure Synapse Analytics itp.) w celu przeprowadzania analiz wsadowych lub uczenia modeli uczenia maszynowego na podstawie bardzo dużych, indeksowanych pul danych historycznych.
- Projekcje magazynu (nazywane również "widokami z materiałami") w bazach danych ([SQL Database](../stream-analytics/sql-database-output.md), [Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) ).

### <a name="stateless-replication-applications-in-azure-functions"></a>Aplikacje do replikacji bezstanowej w Azure Functions

W przypadku zadań związanych z replikacją bezstanową, w których chcesz przekazywać zdarzenia bez rozpatrywania ich ładunków lub procesów niezależnie bez konieczności rozpatrywania relacji zdarzeń (z wyjątkiem ich kolejności względnej), można użyć Azure Functions, która zapewnia olbrzymią elastyczność.

Azure Functions ma wstępnie skompilowane, skalowalne wyzwalacze i powiązania wyjściowe dla [Event Hubs platformy Azure](../azure-functions/functions-bindings-event-hubs.md), [platformy azure IoT Hub](../azure-functions/functions-bindings-event-iot.md), [Azure Service Bus](../azure-functions/functions-bindings-service-bus.md), [Azure Event Grid](../azure-functions/functions-bindings-event-grid.md)i [platformy azure queue storage](../azure-functions/functions-bindings-storage-queue.md), a także niestandardowych rozszerzeń [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)i [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension). Większość wyzwalaczy dynamicznie dostosowuje się do potrzeb dotyczących przepływności przez skalowanie liczby współbieżnie wykonywanych wystąpień w górę i w dół w oparciu o udokumentowane metryki. 

W przypadku kompilowania projekcji dzienników Azure Functions obsługuje powiązania wyjściowe dla [Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md) i [Table Storage platformy Azure](../azure-functions/functions-bindings-storage-table-output.md).

Azure Functions mogą być uruchamiane w ramach [tożsamości zarządzanej platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) i z tym, że mogą przechowywać wartości konfiguracyjne poświadczeń w dostępie do magazynu z możliwością kontroli dostępu w ramach [Azure Key Vault](../key-vault/general/overview.md).

Ponadto Azure Functions pozwala na bezpośrednie integrację zadań replikacji z sieciami wirtualnymi platformy Azure i [punktami końcowymi usługi](../virtual-network/virtual-network-service-endpoints-overview.md) dla wszystkich usług Azure Messaging. jest ona łatwo zintegrowana z [Azure monitor](../azure-monitor/overview.md).

Zgodnie z planem użycia Azure Functions prekompilowane wyzwalacze mogą nawet skalować w dół do zera, a żadne komunikaty nie są dostępne do replikacji, co oznacza, że nie ponosisz żadnych kosztów, aby zachować konfigurację gotową do skalowania kopii zapasowej. kluczowym minusem korzystania z planu zużycia jest to, że opóźnienie zadań replikacji "wznawianie" z tego stanu jest znacznie wyższe niż w przypadku planów hostingu, w których działa infrastruktura.  

W przeciwieństwie do wszystkich tych, najpopularniejszych aparatów replikacji na potrzeby obsługi komunikatów i zdarzeń, takich jak [narzędzia mirrormaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) Apache Kafka wymaga zapewnienia środowiska hostingu i samodzielnego skalowania aparatu replikacji. Obejmuje to Konfigurowanie i integrację funkcji zabezpieczeń i sieci oraz ułatwianie przepływu danych monitorowania, a następnie nadal nie istnieje możliwość dodawania niestandardowych zadań replikacji do przepływu. 

### <a name="choosing-between-azure-functions-and-azure-stream-analytics"></a>Wybór między Azure Functions i Azure Stream Analytics

Azure Stream Analytics (ASA) to najlepsza opcja, gdy trzeba przetwarzać ładunek zdarzeń podczas replikowania ich. ASA może kopiować zdarzenia jeden według jednego lub może tworzyć zagregowane informacje, które kondensacją strumieni zdarzeń przed ich przekazaniem. Może ona łatwo obsłużyć [dodatkowe dane referencyjne](../stream-analytics/stream-analytics-use-reference-data.md) przechowywane w usłudze Azure Blob Storage lub Azure SQL Database bez konieczności importowania takich danych do strumienia.

Za pomocą ASA można łatwo tworzyć trwałe, materialne widoki strumieni w bazach danych funkcji Hyper-Skala. Jest to dalekie podejście do modelu clunky "kompaktowanie dzienników" Apache Kafka oraz nietrwałych projekcji tabeli po stronie klienta w strumieniach Kafka. 

Funkcja ASA może łatwo przetwarzać zdarzenia mające ładunki zakodowane w [formatach CSV, JSON i Apache Avro](../stream-analytics/stream-analytics-parsing-json.md) i można podłączyć [niestandardowe deserializacji](../stream-analytics/custom-deserializer.md) dla dowolnego innego formatu.

W przypadku wszystkich zadań replikacji, w których mają zostać skopiowane strumienie zdarzeń "AS-IS" i bez dotykania ładunków, lub jeśli zachodzi potrzeba zaimplementowania routera, wykonywania zadań kryptograficznych, zmiany kodowania ładunków lub w przeciwnym razie potrzeby pełnej kontroli nad zawartością strumienia danych, Azure Functions jest najlepszą opcją.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono szereg wzorców Federacji i wyjaśniono rolę Azure Functions jako środowiska uruchomieniowego replikacji zdarzeń i komunikatów na platformie Azure.

Następnie można zapoznać się z artykułem jak skonfigurować aplikację replikatora przy użyciu Azure Stream Analytics lub Azure Functions, a następnie jak replikować przepływy zdarzeń między Event Hubs i różnymi systemami obsługi zdarzeń i komunikatów:

- [Wzorce zadań do replikacji zdarzeń][10]
- [Przetwarzanie danych za pomocą Azure Stream Analytics][9]
- [Aplikacje Replikator zdarzeń w Azure Functions][1]
- [Replikowanie zdarzeń między Event Hubs][2]
- [Replikowanie zdarzeń do Azure Service Bus][3]
- [Użyj Apache Kafka narzędzia MirrorMaker z Event Hubs][11] 

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
[4]: event-hubs-federation-patterns.md#replication
[5]: event-hubs-federation-patterns.md#merge
[6]: event-hubs-federation-patterns.md#editor
[7]: event-hubs-federation-patterns.md#routing
[8]: event-hubs-federation-patterns.md#log-projection
[9]: process-data-azure-stream-analytics.md
[10]: event-hubs-federation-patterns.md#replication
[11]: event-hubs-kafka-mirror-maker-tutorial.md