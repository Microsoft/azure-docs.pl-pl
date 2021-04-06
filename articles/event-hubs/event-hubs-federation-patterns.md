---
title: Wzorce zadań replikacji zdarzeń — Azure Event Hubs | Microsoft Docs
description: Ten artykuł zawiera szczegółowe wskazówki dotyczące implementowania określonych wzorców zadań replikacji zdarzeń
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 438964c228f060dede93abf582c9504b698db8b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934615"
---
# <a name="event-replication-tasks-patterns"></a>Wzorce zadań replikacji zdarzeń

Omówienie [Federacji](event-hubs-federation-overview.md) i usługi [replikatora](event-hubs-federation-replicator-functions.md) wyjaśniają racjonalne znaczenie dla i podstawowych elementów zadań związanych z replikacją oraz zaleca się zapoznanie się z nimi przed kontynuowaniem pracy z tym artykułem.

W tym artykule szczegółowo przedstawiono wskazówki dotyczące implementacji kilku wzorców wyróżnionych w sekcji Przegląd.

## <a name="replication"></a>Replikacja

Wzorzec replikacji kopiuje zdarzenia z jednego centrum zdarzeń do następnego lub z centrum zdarzeń do innego miejsca docelowego, takiego jak Kolejka Service Bus. Zdarzenia są przekazywane bez wprowadzania jakichkolwiek modyfikacji ładunku zdarzenia.

Implementacja tego wzorca jest objęta [replikacją zdarzeń między Event Hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy) i [replikacją zdarzeń między Event Hubs i Service Bus](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus) przykładami, a następnie [Użyj Apache Kafka narzędzia MirrorMaker z Event Hubs](event-hubs-kafka-mirror-maker-tutorial.md) samouczkiem dotyczącym określonego przypadku replikowania danych z brokera Apache Kafka do Event Hubs.

### <a name="streams-and-order-preservation"></a>Strumienie i zachowywanie kolejności

Replikacja, za pomocą Azure Functions lub Azure Stream Analytics, nie ma na celu zapewnienia tworzenia dokładnie 1:1 klonów źródłowego centrum zdarzeń w docelowym centrum zdarzeń, ale koncentruje się na zachowaniu względnej kolejności zdarzeń, w których aplikacja tego wymaga. Aplikacja komunikuje się w ten sposób, grupując zdarzenia powiązane z tym samym kluczem partycji, a [Event Hubs rozmieszcza komunikaty z tym samym kluczem partycji sekwencyjnie w tej samej partycji](event-hubs-features.md#partitions).

> [!IMPORTANT] 
> Informacje "offset" są unikatowe dla każdego centrum zdarzeń i przesunięcia dla tych samych zdarzeń będą się różnić w zależności od wystąpień centrum zdarzeń. Aby zlokalizować pozycję w kopiowanym strumieniu zdarzeń, użyj przesunięć opartych na czasie i zapoznaj się z [propagowanymi metadanymi przypisanymi do usługi](#service-assigned-metadata).
>
> Przesunięcia oparte na czasie — umożliwia uruchomienie odbiornika w określonym momencie:
> - *EventPosition. FromStart ()* — ponownie Przeczytaj wszystkie zachowane dane.
> - *EventPosition. FromEnd ()* — odczytywanie wszystkich nowych danych z czasu połączenia.
> - *EventPosition. FromEnqueuedTime (DateTime)* — wszystkie dane zaczynające się od podanej daty i godziny.
>
> W EventProcessor ustawia się pozycję za pomocą InitialOffsetProvider na EventProcessorOptions. W przypadku innych interfejsów API odbiornika pozycja jest przenoszona przez Konstruktor. 


Wstępnie skompilowane pomocnicy funkcji replikacji [, które są](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) używane w ramach wskazówek opartych na Azure Functionsach, zapewniają, że strumienie zdarzeń z tym samym kluczem partycji pobrane z partycji źródłowej są przesyłane do docelowego centrum zdarzeń jako partia w oryginalnym strumieniu i z tym samym kluczem partycji.

Jeśli liczba partycji źródłowego i docelowego centrum zdarzeń jest taka sama, wszystkie strumienie w elemencie docelowym będą mapowane na te same partycje, które znajdowały się w źródle.
Jeśli liczba partycji jest różna, co jest istotne w przypadku niektórych innych wzorców opisanych w poniższej tabeli, mapowanie będzie się różnić, ale strumienie są zawsze połączone i w określonej kolejności.

Względna kolejność zdarzeń należących do różnych strumieni lub niezależnych zdarzeń bez klucza partycji w partycji docelowej może być zawsze inna niż partycja źródłowa.

### <a name="service-assigned-metadata"></a>Metadane przypisane do usługi

Metadane przypisane do usługi zdarzenia uzyskane z źródłowego centrum zdarzeń, pierwotny czas, numer sekwencyjny i przesunięcie, są zastępowane przez nowe wartości przypisane do usługi w docelowym centrum zdarzeń, ale z [funkcjami pomocnika](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication), zadaniami replikacji, które są dostępne w naszych przykładach, oryginalne wartości są zachowywane we właściwościach użytkownika: `repl-enqueue-time` (ciąg ISO8601), `repl-sequence` , `repl-offset` .

Te właściwości są typu String i zawierają wartość skonwertowanej odpowiednich oryginalnych właściwości. Jeśli zdarzenie jest przekazywane wiele razy, metadane bezpośrednie źródła dołączone do usługi są dołączane do już istniejących właściwości, z wartościami oddzielonymi średnikami.

### <a name="failover"></a>Tryb failover

W przypadku korzystania z replikacji na potrzeby odzyskiwania po awarii, w celu ochrony przed regionalnymi zdarzeniami dostępności w usłudze Event Hubs lub przed zakłóceniami w sieci, każdy taki scenariusz awarii będzie wymagał przełączenia w tryb failover z jednego centrum zdarzeń do kolejnego, poinformowania producentów i/lub konsumentów o konieczności użycia pomocniczego punktu końcowego.

W przypadku wszystkich scenariuszy trybu failover zakłada się, że wymagane elementy przestrzeni nazw są strukturalnie identyczne, co oznacza, że Event Hubs i grupy konsumentów są identyczne o nazwie i reguły sygnatur dostępu współdzielonego i/lub reguły kontroli dostępu oparte na rolach zostały skonfigurowane w taki sam sposób. Można utworzyć (i zaktualizować) pomocniczą przestrzeń nazw, postępując zgodnie ze [wskazówkami dotyczącymi przesuwania przestrzeni nazw](move-across-regions.md) i pomijania kroku oczyszczania.

Aby wymusić przełączenie producentów i konsumentów, należy wprowadzić informacje o tym, która przestrzeń nazw ma być dostępna do wyszukania w lokalizacji, która jest łatwa do osiągnięcia i zaktualizowania. Jeśli producenci lub konsumenci napotykają częste lub trwałe błędy, powinni skonsultować się z tą lokalizacją i dostosować ich konfigurację. Istnieje wiele sposobów, aby udostępnić tę konfigurację, ale od tego momentu podaliśmy dwa elementy: DNS i udziały plików.

#### <a name="dns-based-failover-configuration"></a>Konfiguracja trybu failover oparta na systemie DNS

Jednym z kandydatów jest przechowywanie informacji w rekordach DNS SRV w kontrolowanej przez Ciebie systemie DNS i wskazanie odpowiednich punktów końcowych centrum zdarzeń. 

> [!IMPORTANT] 
> Należy pamiętać, że Event Hubs nie zezwala na bezpośredni aliasowanie punktów końcowych przy użyciu rekordów CNAME, co oznacza, że jako odporny na adresy punktów końcowych będzie używany mechanizm wyszukiwania systemu DNS, a nie do bezpośredniego rozpoznawania informacji o adresie IP.

Załóżmy, że jesteś członkiem domeny `example.com` i, dla aplikacji, strefy `test.example.com` . W przypadku dwóch alternatywnych Event Hubs teraz utworzysz dwie dalsze strefy zagnieżdżone i rekord SRV w każdym z nich.

Rekordy SRV są zgodne ze wspólną Konwencją, poprzedzone `_azure_eventhubs._amqp` i zawierają dwa rekordy punktów końcowych: jeden dla AMQP-over-TLS na porcie 5671 i jeden dla AMQP-over-WebSockets na porcie 443, oba wskazujące Event Hubs punkt końcowy przestrzeni nazw odpowiadającej strefie.

| Strefa                   | Rekord SRV                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `eh1.test.example.com` | **`_azure_servicebus._amqp.eh1.test.example.com`**<br>`1 1 5671 eh1-test-example-com.servicebus.windows.net`<br>`2 2 443 eh1-test-example-com.servicebus.windows.net` |
| `eh2.test.example.com` | **`_azure_servicebus._amqp.eh2.test.example.com`**<br>`1 1 5671 eh2-test-example-com.servicebus.windows.net`<br>`2 2 443 eh2-test-example-com.servicebus.windows.net` |

W strefie aplikacji utworzysz wpis CNAME wskazujący strefę podrzędną odpowiadającą głównemu centrum zdarzeń:

| Rekord CNAME                | Alias                    |
| --------------------------- | ------------------------ |
| `eventhub.test.example.com` | `test1.test.example.com` |

Przy użyciu klienta DNS, który umożliwia jawne wykonywanie zapytań dotyczących rekordów CNAME i SRV (Wbudowani klienci języka Java i platformy .NET umożliwiają tylko proste rozpoznawanie nazw na adresy IP), można rozpoznać żądany punkt końcowy. W przypadku [DnsClient.NET](https://dnsclient.michaco.net/), na przykład, funkcja Lookup:

```C#
static string GetEventHubName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_eventhub._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

Funkcja zwraca wartość docelowej nazwy hosta zarejestrowanego dla portu 5671 strefy, która jest obecnie aliasem rekordu CNAME, jak pokazano powyżej.

Wykonanie trybu failover wymaga edycji rekordu CNAME i wskazanie go w strefie alternatywnej.

Zaletą korzystania z systemu DNS, a szczególnie [Azure DNS](../dns/dns-overview.md), jest to, że Azure DNS informacje są replikowane globalnie i w związku z tym odporne na awarie w jednym regionie.

Ta procedura jest podobna do sposobu działania [Event Hubs geograficznego](event-hubs-geo-dr.md) odzyskiwania po awarii, ale w pełni w ramach własnej kontrolki, a także współpracuje z aktywnymi/aktywnymi scenariuszami.

#### <a name="file-share-based-failover-configuration"></a>Konfiguracja trybu failover oparta na udziale plików

Najprostszą alternatywą dla korzystania z usługi DNS w celu udostępniania informacji o punkcie końcowym jest umieszczenie nazwy podstawowego punktu końcowego w pliku z tekstem zwykłym i obsłużynie pliku z infrastruktury, która jest niezawodna w odniesieniu do awarii i nadal zezwala na aktualizacje.

Jeśli infrastruktura witryny sieci Web o wysokiej dostępności jest już uruchomiona z globalną dostępnością i replikacją zawartości, dodanie takiego pliku i ponowne opublikowanie go w przypadku konieczności przełączenia.

> [!CAUTION]
> W ten sposób należy opublikować tylko nazwę punktu końcowego, a nie pełne parametry połączenia, w tym wpisy tajne.

#### <a name="extra-considerations-for-failing-over-consumers"></a>Dodatkowe zagadnienia dotyczące przełączenia klientów do trybu failover

W przypadku użytkowników usługi Event Hub dalsze zagadnienia dotyczące strategii pracy awaryjnej zależą od potrzeb procesora zdarzeń.

Jeśli wystąpi awaria wymagająca ponownego skompilowania systemu, w tym bazy danych, z danych kopii zapasowej, bazy danych są wprowadzane bezpośrednio lub za pośrednictwem przetwarzania pośredniego ze zdarzeń przechowywanych w centrum zdarzeń, należy przywrócić kopię zapasową, a następnie ponownie rozpocząć odtwarzanie zdarzeń w systemie od momentu utworzenia kopii zapasowej bazy danych, a nie od momentu zniszczenia oryginalnego systemu.

Jeśli awaria dotyczy tylko wycinka systemu lub rzeczywiście tylko jednego centrum zdarzeń, która stała się nieosiągalna, prawdopodobnie chcesz kontynuować przetwarzanie zdarzeń z tego samego miejsca, w którym przetwarzanie zostało przerwane.

Aby zrealizować każdy scenariusz i korzystać z procesora zdarzeń odpowiedniego zestawu Azure SDK, utworzysz [nowy magazyn punktów kontrolnych](event-processor-balance-partition-load.md#checkpointing) i udostępnimy początkową pozycję partycji na podstawie _sygnatury czasowej_ , z której chcesz wznowić przetwarzanie.

Jeśli nadal masz dostęp do magazynu punktów kontrolnych centrum zdarzeń, z którego korzystasz, [propagowane metadane](#service-assigned-metadata) pomogą Ci pominąć zdarzenia, które zostały już obsłużone i wznowić z miejsca, w którym zostało przerwane.

## <a name="merge"></a>Merge

Wzorzec scalania zawiera co najmniej jedno zadanie replikacji wskazujące jeden element docelowy, prawdopodobnie jednocześnie z regularnymi producentami, które wysyłają zdarzenia do tego samego obiektu docelowego.

Zmiany tych patters są następujące:

- Co najmniej dwie funkcje replikacji jednocześnie nabywają zdarzenia z oddzielnych źródeł i wysyłają je do tego samego obiektu docelowego.
- Jedna z funkcji replikacji pobierającej zdarzenia ze źródła, gdy element docelowy jest również używany bezpośrednio przez producentów.
- Poprzedni wzorzec, ale zdublowany przez dwa lub więcej Event Hubs, co skutkuje tymi Event Hubs zawierającymi te same strumienie, niezależnie od tego, gdzie są tworzone zdarzenia.

Pierwsze dwie odmiany wzorców są proste i nie różnią się od zwykłych zadań replikacji.

Ostatni scenariusz wymaga ponownego replikowania zdarzeń, które zostały już zreplikowane. Technika jest przedstawiona i wyjaśniona w próbce [EventHubToEventHubMerge](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/code/EventHubMerge) .

## <a name="editor"></a>Edytor

Wzorzec edytora kompiluje się w wzorcu [replikacji](#replication) , ale komunikaty są modyfikowane przed przesłaniem dalej. 

Przykłady dla takich modyfikacji:

- **_Transkodowanie_** — Jeśli zawartość zdarzenia (określana także jako "treść" lub "ładunek") dotarła ze źródła zakodowanego przy użyciu formatu _Apache Avro_ lub pewnego zastrzeżonego formatu serializacji, jednak oczekiwany system posiadający obiekt docelowy to zawartość, która ma być zakodowana w _notacji JSON_ , zadanie replikacji transkodowanej najpierw dekoduje ładunek z _Apache Avro_ do grafu obiektów w pamięci, a następnie serializować ten wykres do formatu _JSON_ dla zdarzenia, które jest przesyłane dalej. Transkodowanie obejmuje również zadania **kompresji zawartości** i dekompresji.
- **_Transformacja_** — zdarzenia zawierające dane strukturalne mogą wymagać zmiany kształtu tych danych w celu łatwiejszego wykorzystania przez użytkowników podrzędnych. Może to dotyczyć takich zadań, jak spłaszczanie zagnieżdżonych struktur, oczyszczanie nadmiarowych elementów danych lub zmiana kształtu ładunku, aby dokładnie dopasować dany schemat.
- **_Przetwarzanie wsadowe_** — zdarzenia mogą być odbierane w partiach (wiele zdarzeń w jednym transferze) ze źródła, ale muszą być przekazywane pojedynczo do miejsca docelowego lub na odwrót. Zadanie może zatem przekazywać wiele zdarzeń na podstawie pojedynczego transferu zdarzeń wejściowych lub agregować zestaw zdarzeń, które są następnie przesyłane razem.
- **_Sprawdzanie poprawności_** — dane zdarzenia ze źródeł zewnętrznych często muszą być sprawdzane pod kątem zgodności z zestawem reguł, zanim będą mogły zostać przesłane dalej. Reguły mogą być wyrażone przy użyciu schematów lub kodu. Zdarzenia, które nie są zgodne, mogą zostać usunięte, z problemem zanotowanym w dziennikach lub mogą być przekazywane do specjalnego docelowego miejsca docelowego, aby można je było dalej obsłużyć.
- **_Wzbogacanie_** danych zdarzeń pochodzących z niektórych źródeł może wymagać wzbogacania z dodatkowym kontekstem, aby można było go używać w systemach docelowych. Może to oznaczać wyszukiwanie danych referencyjnych i osadzenie tych danych ze zdarzeniem lub dodaniem informacji o źródle znanym przez zadanie replikacji, ale nie zawarte w zdarzeniach.
- **_Filtrowanie_** — niektóre zdarzenia przychodzące ze źródła mogą wymagać wstrzymania z elementu docelowego na podstawie pewnej reguły. Filtr testuje zdarzenie względem reguły i odrzuca zdarzenie, jeśli zdarzenie nie jest zgodne z regułą. Filtrowanie powtarzających się zdarzeń przez zaobserwowanie pewnych kryteriów i usunięcie kolejnych zdarzeń z tymi samymi wartościami jest formą filtrowania.
- **_Kryptografia_** — zadanie replikacji może być konieczne do odszyfrowania zawartości przychodzącej ze źródła i/lub zaszyfrowania zawartości przesłanej do miejsca docelowego i/lub może być konieczne zweryfikowanie integralności zawartości i metadanych względem podpisu przeprowadzonego w zdarzeniu lub dołączenie takiego podpisu.
- **_Zaświadczanie_** — zadanie replikacji może dołączyć metadane, potencjalnie chronione przez podpis cyfrowy, do zdarzenia, które zaświadcza, że zdarzenie zostało odebrane za pośrednictwem określonego kanału lub w określonym czasie.
- **_Łączenie łańcuchowe_** — zadanie replikacji może stosować podpisy do strumieni zdarzeń w taki sposób, aby integralność strumienia była chroniona i wykryto brakujące zdarzenia.

Wzorce transformacji, przetwarzania wsadowego i wzbogacania są zwykle najlepiej zaimplementowane przy użyciu [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) zadań. 

Wszystkie te wzorce można zaimplementować przy użyciu Azure Functions, przy użyciu [wyzwalacza Event Hubs](../azure-functions/functions-bindings-event-hubs-trigger.md) do uzyskiwania zdarzeń i [powiązania danych wyjściowych centrum zdarzeń](../azure-functions/functions-bindings-event-hubs-output.md) w celu ich dostarczania.

## <a name="routing"></a>Routing

Wzorzec routingu kompiluje się w wzorcu [replikacji](#replication) , ale zamiast jednego źródła i jednego obiektu docelowego, zadanie replikacji ma wiele obiektów docelowych przedstawionych w tym miejscu w języku C#:

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest1", Connection = "EventHubConnectionAppSetting")] EventHubClient output1,
    [EventHub("dest2", Connection = "EventHubConnectionAppSetting")] EventHubClient output2,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // send to output1 and/or output2 based on criteria
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output1, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2==0 ) ? inputEvent : null;
        });
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output2, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2!=0 ) ? inputEvent : null;
        });
    }
}
```

Funkcja routingu będzie uwzględniać metadane komunikatów i/lub ładunek wiadomości, a następnie wybrać jeden z dostępnych miejsc docelowych do wysłania.

W Azure Stream Analytics można osiągnąć ten sam sposób, definiując wiele danych wyjściowych, a następnie wykonując zapytanie na dane wyjściowe.

```sql
select * into dest1Output from inputSource where Info = 1
select * into dest2Output from inputSource where Info = 2
```


## <a name="log-projection"></a>Projekcja dziennika

Wzorzec projekcji dziennika spłaszcza strumień zdarzeń do indeksowanej bazy danych, a zdarzenia stają się rekordami w bazie danych. Zwykle zdarzenia są dodawane do tej samej kolekcji lub tabeli, a klucz partycji centrum zdarzeń jest stroną klucza podstawowego, który szuka unikatowego rekordu.

Projekcja dziennika może generować Historian szeregów czasowych danych zdarzeń lub Widok kompaktowy, dzięki czemu tylko najnowsze zdarzenie jest zachowywane dla każdego klucza partycji. Kształt docelowej bazy danych jest ostatecznie do Ciebie i Twoich potrzeb Twojej aplikacji. Ten wzorzec jest również nazywany "źródłem zdarzeń".

> [!TIP]
> Można łatwo tworzyć projekcje dzienników w [Azure SQL Database](../stream-analytics/sql-database-output.md) i [Azure Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) w Azure Stream Analytics i należy preferować tę opcję.

Następująca funkcja platformy Azure projektuje zawartość centrum zdarzeń kompaktowego do kolekcji Azure CosmosDB.

```C#
[FunctionName("Eh1ToCosmosDb1Json")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static async Task Eh1ToCosmosDb1Json(
    [EventHubTrigger("eh1", ConsumerGroup = "Eh1ToCosmosDb1", Connection = "Eh1ToCosmosDb1-source-connection")] EventData[] input,
    [CosmosDB(databaseName: "SampleDb", collectionName: "foo", ConnectionStringSetting = "CosmosDBConnection")] IAsyncCollector<object> output,
    ILogger log)
{
    foreach (var ev in input)
    {
        if (!string.IsNullOrEmpty(ev.SystemProperties.PartitionKey))
        {
            var record = new
            {
                id = ev.SystemProperties.PartitionKey,
                data = JsonDocument.Parse(ev.Body),
                properties = ev.Properties
            };
            await output.AddAsync(record);
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

- [Aplikacje Replikator zdarzeń w Azure Functions][1]
- [Replikowanie zdarzeń między Event Hubs][2]
- [Replikowanie zdarzeń do Azure Service Bus][3]

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
