---
title: Replikacja komunikatów i międzyregionowa Federacja Azure Service Bus | Microsoft Docs
description: Ten artykuł zawiera Omówienie replikacji zdarzeń i federacji między regionami przy użyciu Azure Service Bus.
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: e47f633fcd9248eab6f47936aa7c45877decc1fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880831"
---
# <a name="message-replication-and-cross-region-federation"></a>Replikacja komunikatów i federacja między regionami

W obszarze przestrzenie nazw Program Azure Service Bus obsługuje [Tworzenie topologii kolejek połączonych i subskrypcji tematu przy użyciu funkcji autoprzesyłania dalej](service-bus-auto-forwarding.md) , aby umożliwić implementację różnych wzorców routingu. Na przykład można zapewnić partnerom dedykowane kolejki, do których mają uprawnienia do wysyłania lub odbierania, które mogą być tymczasowo zawieszone w razie potrzeby i elastycznie połączyć je z innymi jednostkami, które są prywatne dla aplikacji. Można również tworzyć złożone topologie routingu z wieloma etapami lub tworzyć kolejki w stylu skrzynek pocztowych, które opróżniają subskrypcje podobne do kolejki i umożliwiają większej pojemności magazynu na subskrybenta. 

Wiele zaawansowanych rozwiązań wymaga również replikowania komunikatów między granicami przestrzeni nazw w celu zaimplementowania tych i innych wzorców. Komunikaty mogą być przesyłane między obszarami nazw skojarzonymi z wieloma różnymi dzierżawcami aplikacji lub wieloma różnymi regionami świadczenia usługi Azure. 

Twoje rozwiązanie będzie obsługiwać wiele przestrzeni nazw Service Bus w różnych regionach i replikować komunikaty między kolejkami i tematami, a także umożliwia wymianę komunikatów ze źródłami i obiektami docelowymi, takimi jak [azure Event Hubs](../event-hubs/event-hubs-about.md), [azure IoT Hub](../iot-fundamentals/iot-introduction.md)lub [Apache Kafka](https://kafka.apache.org). 

Te scenariusze są skoncentrowane na tym artykule. 

## <a name="federation-patterns"></a>Wzorce Federacji

Istnieje wiele potencjalnych powodów, dla których może być konieczne przenoszenie komunikatów między Service Bus jednostkami, takimi jak kolejki lub tematy, lub między Service Bus i innymi źródłami i obiektami docelowymi. 

W porównaniu z podobnym zestawem wzorców dla [Event Hubs](../service-bus-messaging/service-bus-federation-overview.md), Federacja dla jednostek przypominających kolejki jest bardziej złożona, ponieważ kolejki komunikatów oferują klientom wyłączny dostęp za pośrednictwem jednego komunikatu, oczekuje się zachowania kolejności przybycia w przypadku dostarczania komunikatów oraz dla brokera w celu koordynowania rzetelnej dystrybucji komunikatów między [konkurującymi klientami](/azure/architecture/patterns/competing-consumers). 

Istnieją praktyczne przeszkody, włącznie z ograniczeniami [theorem Cap](https://en.wikipedia.org/wiki/CAP_theorem), które utrudniają udostępnienie ujednoliconego widoku kolejki, która jest jednocześnie dostępna w wielu regionach, a co pozwala na dystrybuowanie w regionie, [konkurujących odbiorców](/azure/architecture/patterns/competing-consumers) do podejmowania wyłącznego prawa własności do komunikatów. Taka kolejka oparta na lokalizacji geograficznej wymagała pełnej spójnej replikacji nie tylko w przypadku komunikatów, ale również stanu dostarczania wszystkich komunikatów przed udostępnieniem komunikatów dla klientów. Celem pełnej spójności dla hipotetycznej kolejki rozproszonej w regionie jest bezpośredni konflikt z najważniejszym celem, który praktycznie wszystkie Azure Service Bus klienci mają rozważać scenariusze federacyjne: maksymalną dostępność i niezawodność rozwiązań. 

Wzorce przedstawione w tym miejscu koncentrują się na dostępności i niezawodności, a także mają na celu najlepsze uniknięcie utraty informacji oraz duplikowania obsługi komunikatów. 

### <a name="resiliency-against-regional-availability-events"></a>Odporność na zdarzenia dostępności regionalnej 

Chociaż maksymalna dostępność i niezawodność są najwyższymi priorytetami operacyjnymi Service Bus, istnieje wiele sposobów, w których producent lub odbiorca może być nieosiągalny do przyznanego "podstawowego" Service Bus ze względu na problemy z siecią lub rozpoznawaniem nazw lub miejsce, w którym Service Bus jednostka może faktycznie przestać odpowiadać lub zwracać błędy. Wydzielony procesor komunikatów może być również niedostępny.

Takie warunki nie są "katastrofalne" w taki sposób, że zachodzi konieczność porzucenia wdrożenia regionalnego, jak to możliwe w sytuacji odzyskiwania po awarii, ale w przypadku niektórych aplikacji mogą mieć już wpływ zdarzenia dostępności, które nie przekraczają kilku minut, a nawet sekund. Azure Service Bus jest często używana w środowiskach chmury hybrydowej i z klientami, którzy znajdują się na granicy sieci, na przykład w sklepach detalicznych, restauracjach, gałęziach bankowych, miejscach produkcyjnych, logistycznych i portach lotniczych. Istnieje możliwość, że problem z routingiem sieciowym lub przeciążeniem ma wpływ na zdolność jednej lokacji do osiągnięcia przypisanego do niego punktu końcowego Service Bus, gdy pomocniczy punkt końcowy w innym regionie może być osiągalny. W tym samym czasie, systemy przetwarzania komunikatów przychodzących z tych lokacji mogą nadal mieć niezakłócony dostęp zarówno do podstawowego, jak i pomocniczego punktu końcowego Service Bus. 

Istnieje wiele praktycznych przykładów takich aplikacji w chmurze hybrydowej i brzegowej z niską tolerancją biznesową w przypadku problemów z routingiem sieciowym lub przejściowych problemów z dostępnością jednostki Service Bus. Obejmują one przetwarzanie płatności w witrynach handlu detalicznego, na przykład na bramach portów lotniczych, a także za pomocą zamówień na telefon komórkowy w restauracjach, a wszystko to jest błyskawiczne i kompletne.

W tej kategorii omówiono trzy różne dystrybuowane wzorce: replikacja "All-Active", replikacja "Active-pasywna" i replikacja "spillover". 

#### <a name="all-active-replication"></a>All-Active replikację

Wzorzec replikacji "All-Active" umożliwia dostęp do aktywnej repliki tego samego logicznego tematu (lub kolejki) w wielu obszarach nazw (i regionach), a także dla wszystkich komunikatów, które mają być dostępne we wszystkich replikach, niezależnie od tego, gdzie zostały one poddane do kolejki. Wzorzec zazwyczaj zachowuje kolejność komunikatów względem dowolnego wydawcy. 

![Cały aktywny wzorzec](media/service-bus-federation-overview/mirrored-topics.jpg)

Jak pokazano na ilustracji, wzorzec zazwyczaj jest pochyleniem w Service Bus tematy. Jeden temat dla każdej przestrzeni nazw, który uczestniczy w schemacie replikacji. Każdy z tych tematów ma jedną "subskrypcję replikacji" dla każdego z innych tematów, do których są replikowane komunikaty. Na powyższej ilustracji po prostu mamy parę tematów, a tym samym jedną subskrypcję replikacji dla odpowiedniego tematu. W scenariuszu z trzema przestrzeniami nazw *{N1, N2, N3}*, temat w przestrzeni nazw *N1* ma dwie subskrypcje replikacji, jeden dla odpowiedniego tematu w *N2* i jeden dla odpowiedniego tematu w *N3*. 

Każda subskrypcja replikacji ma regułę, która łączy wyrażenie filtru SQL ( `replicated <> 1` ) i akcję SQL ( `set replicated = 1` ). Filtr reguły gwarantuje, że tylko komunikaty, w których nie jest ustawiona właściwość niestandardowa `replication` lub nie mają wartości, `1` kwalifikują się do tej subskrypcji, a akcja ustawia dokładną właściwość na wartość `1` w każdym z wybranych komunikatów po raz kolejny. Ten efekt polega na tym, że gdy komunikat jest kopiowany do odpowiedniego tematu, nie kwalifikuje się do replikacji w odwrotnym kierunku i w związku z tym unikamy komunikatów odbijających się między replikami.

Subskrypcję z odpowiednią regułą można łatwo dodać do dowolnego tematu przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli

az servicebus topic subscription rule create --resource-group myresourcegroup \
   --namespace mynamespace --topic-name mytopic 
   --subscription-name replication --name replication \
   --action-sql-expression "set replication = 1" \
   --filter-sql-expression "replication IS NULL"
```

Aby modelować kolejkę, każdy temat jest ograniczony do tylko jednej zwykłej subskrypcji (innej niż subskrypcje replikacji), którą wszyscy odbiorcy używają.

> Model "All-Active Replication" umieszcza kopię każdej wiadomości wysyłanej do dowolnego tematu w poszczególnych tematach. Oznacza to, że kod aplikacji w każdym regionie będzie widział i przetwarzał wszystkie komunikaty.
> Ten wzorzec jest odpowiedni dla scenariuszy, w których dane są udostępniane w wielu regionach, lub w przypadku, gdy jest to zwykle wymagane. Jeśli musisz przetworzyć każdy komunikat tylko raz, tak jak w przypadku zwykłej kolejki, należy wziąć pod uwagę jeden z następujących dwóch wzorców.  

#### <a name="active-passive-replication"></a>Active-Passive replikację

Wzorzec replikacji "Active-pasywny" to odmiana poprzedniego wzorca, w której tylko jeden z tych tematów ("podstawowy") jest aktywnie używany przez aplikację do wysyłania i otrzymywania komunikatów, a komunikaty są replikowane do pomocniczego tematu w przypadku, gdy podstawowy temat może stać się niedostępny lub nieosiągalny. 

![Aktywny wzorzec pasywny](media/service-bus-federation-overview/mirrored-topics-passive.jpg)

Kluczowa różnica między tym wzorcem i wcześniejszym wzorcem polega na tym, że replikacja jest jednokierunkowe z tematu podstawowego do pomocniczego tematu. Pomocniczy temat nigdy nie stanie się podstawowym, ale jest opcją tworzenia kopii zapasowej, gdy podstawowy temat nie może być używany. 

Przy użyciu tego wzorca jest podejmowana próba zminimalizowania przetwarzania duplikatów. Podczas replikacji `TimeToLive` Właściwość Message jest ustawiana na czas trwania replikowanych komunikatów, która odzwierciedla oczekiwany czas, w którym awaria podstawowa będzie prowadzić do przejścia w tryb failover. Jeśli na przykład scenariusz przypadków użycia wymaga przełączenia konsumenta do pomocniczej wartości w ciągu maksymalnie 1 minuty od momentu pobrania komunikatów z podstawowego startu, należy uzyskać dostęp do wszystkich dostępnych komunikatów, które nie były dostępne w podstawowym, ale minimalną liczbę komunikatów, które zostały już przetworzone z lokalizacji głównej przed wystąpieniem problemów. Jeśli ustawimy `TimeToLive` dwa razy ten okres, 2 minuty podczas replikacji ( `set sys.TimeToLive = '0:2:0'` w akcji reguły), pomocnicza będzie przechowywać komunikaty przez 2 minuty i odrzucać starsze. Oznacza to, że gdy odbiornik przełączy się na pomocniczą, może szybko odczytywać i odrzucać komunikaty starsze niż ostatnio przetwarzane, a następnie przetwarzać z pierwszego komunikatu, który jeszcze nie widział. Rzeczywisty czas przechowywania będzie zależeć od konkretnego przypadku użycia i szybko, aby można było przełączyć się na pomocniczą aplikację. `TimeToLive`Ustawienie jest honorowane z zakresu od kilku sekund do dni. 

Gdy aplikacja korzysta z pomocniczej usługi, może ona również być opublikowana bezpośrednio w temacie dodatkowym, który następnie działa jak każdy zwykły temat. Po przełączeniu się do pomocniczego odbiorca będzie widzieć mieszany komunikat i komunikaty opublikowane bezpośrednio do pomocniczego. W związku z tym aplikacja powinna najpierw przełączyć publikowanie z powrotem do podstawowego i nadal zezwala na opróżnianie komunikatów publikowanych lokalnie przed przełączeniem konsumenta z powrotem do pomocniczej. Ze względu na to, że replikacja jest wznawiana automatycznie po ponownym udostępnieniu podstawowego, odbiorca również otrzymuje nowe komunikaty publikowane w tym czasie, chociaż z nieco większym opóźnieniem. 

> Ten wzorzec jest odpowiedni dla scenariuszy, w których komunikaty powinny być przetwarzane tylko raz. Aplikacja musi współpracować w śledzeniu komunikatów przetwarzanych z poziomu podstawowego, ponieważ będzie ona znajdować duplikaty czasu trwania okna trybu failover w pomocniczej i ponownie znajdować duplikaty podczas przełączania z powrotem.
> Kryterium cofania duplikatów powinno być najlepszym rozwiązaniem w przypadku aplikacji `MessageId` . `EnqueuedTimeUtc`Wartość jest również odpowiednia jako wskaźnik znaku wodnego, ale aplikacja musi zezwolić na pewien czas dryfu zegara (kilka sekund) między podstawową i pomocniczą, tak jak w przypadku każdego systemu rozproszonego.


#### <a name="spillover-replication"></a>Replikacja spillover

Wzorzec replikacji "spillover" umożliwia aktywne/aktywne korzystanie z wielu jednostek Service Bus w wielu regionach w celu zaradzenia sobie z scenariuszem, w którym Service Bus jest w dobrej kondycji, ale *odbiorca* zostanie przeciążony liczbą oczekujących komunikatów lub jest niedostępny. Przyczyną może być to, że baza danych będąca procesem tworzenia kopii zapasowej może być powolna lub niedostępna. Ten wzorzec działa z zwykłymi kolejkami i subskrypcjami tematów.  

![Replikacja spillover](media/service-bus-federation-overview/spillover.jpg)  

Jak pokazano na ilustracji, wzorzec replikacji spillover replikuje komunikaty z kolejki [utraconych](service-bus-dead-letter-queues.md) wiadomości skojarzonych z kolejką lub subskrypcją do sparowanej kolejki lub tematu w innej przestrzeni nazw. 

W przypadku niepowodzenia awarii dwie przestrzenie nazw są używane równolegle, a każdy z nich otrzymuje podzestaw całego ruchu komunikatów i skojarzonych z nimi klientów obsługujących ten podzbiór. Gdy jeden z użytkowników zacznie przedstawiać wysokie stawki błędów lub przestaje działać, odpowiednie komunikaty zakończą się w kolejce wiadomości utraconych przez przekroczenie liczby dostaw lub wygaśnie. Zadania związane z replikacją będą następnie wybierane i ponownie umieszczane w kolejce w sparowanych kolejkach, gdzie są następnie przedstawiane jako przewidywany konsument w dobrej kondycji. 

Jeśli przetwarzanie musi odbywać się w określonym terminie, to `TimeToLive` dla kolejki i/lub komunikatów należy ustawić takie, że przetwarzanie może nadal odbywać się w czasie przez pomocniczą spillover, na przykład `TimeToLive` może być ustawiona na połowę dozwolonego czasu.

Podobnie jak w przypadku [wzorca "All-Active"](#all-active-replication), aplikacja może dodać wskaźnik do komunikatu, czy wiadomość została już zreplikowana, aby nie odbijać między parę kolejek, ale nie jest umieszczana w pomocniczej kolejce, która działa jako kolejka utraconych wiadomości dla wzorca złożonego.

> Ten wzorzec jest odpowiedni do scenariuszy, w których najważniejszym zagadnieniem jest obronność przed problemami z dostępnością w konsumentów lub zasobach, na których polegają Klienci, a także do redystrybucji ruchu w jednej z sparowanych kolejek. Zapewnia również ochronę przed jedną z przestrzeni nazw, która staje się niedostępna, jeśli konsumenci odczytają obie kolejki, ale opóźnienie replikacji narzucone przez `TimeToLive` wygaśnięcie może spowodować, że komunikaty w tym przedziale czasu mają być aktywne w niedostępnym obszarze nazw. 

### <a name="latency-optimization"></a>Optymalizacja opóźnienia 

Tematy są używane do dystrybuowania informacji do wielu użytkowników. W niektórych przypadkach, szczególnie w przypadku użytkowników z szeroką dystrybucją geograficzną, może być korzystne replikowanie komunikatów z tematu do tematu w pomocniczej przestrzeni nazw bliżej klientów.

![Optymalizacja opóźnienia](media/service-bus-federation-overview/latency-optimization.jpg)  

Na przykład podczas udostępniania danych między regionalnymi centrami kontynentalnemi bardziej wydajne jest przeniesienie informacji tylko raz między centrami, a klienci uzyskują kopię danych z tych centrów. 

Transfery replikacji można wykonywać w partiach, które konsumenci często uzyskują i rozliczą komunikaty po jednym. Przy opóźnieniu sieci podstawowej wynoszącym 100 ms między, powiedzmy, Ameryka Północna i Europa, każdy komunikat zajmuje więcej niż 200 MS czasu na przetworzenie dwóch operacji rundy do jednostki zdalnej w celu uzyskania i rozliczenia komunikatów w porównaniu do jednostki w tym samym regionie. 

### <a name="validation-reduction-and-enrichment"></a>Sprawdzanie poprawności, zmniejszenie i wzbogacanie

Komunikaty mogą być przesyłane do kolejki Service Bus lub tematu przez klientów zewnętrznych względem własnych rozwiązań.

![Sprawdzanie poprawności, zmniejszenie, wzbogacanie](media/service-bus-federation-overview/validation.jpg)  

Takie komunikaty mogą wymagać sprawdzenia zgodności z danym schematem oraz niezgodnych komunikatów, które mają zostać porzucone lub utracone. Niektóre komunikaty mogą być ograniczone do złożoności przez pominięcie danych, a niektóre mogą być wzbogacane przez dodanie danych na podstawie wyszukiwania danych referencyjnych. Operacje można wykonywać za pomocą funkcji niestandardowych w zadaniu replikacji. 

### <a name="stream-to-queue-replication"></a>Przesyłanie strumieniowe do replikacji kolejki

Usługa Azure Event Hubs to idealne rozwiązanie do obsługi ekstremalnych woluminów zdarzeń przychodzących. Nie Event Hubs ani podobnych aparatów, takich jak Apache Kafka, udostępniają [konkurencyjny model odbiorców](/azure/architecture/patterns/competing-consumers) , w którym wielu odbiorców może obsługiwać komunikaty z tego samego źródła jednocześnie bez ryzyka przetwarzania duplikatów, a wreszcie rozliczać te komunikaty po ich przetworzeniu. 

![Integracja](media/service-bus-federation-overview/hub-to-queue.jpg)

Strumień służący do kolejek replikacji przesyła zawartość pojedynczej partycji centrum zdarzeń lub zawartość pełnego centrum zdarzeń do kolejki Service Bus, z której można je przetworzyć bezpiecznie i transakcyjnie oraz z konkurencyjnymi klientami. Ta replikacja umożliwia również korzystanie z wszystkich innych Service Bus funkcji dla tych komunikatów, w tym routingu z tematami i demultipleksowanie oparte na sesji. 

### <a name="consolidation-and-normalization"></a>Konsolidacja i normalizacja 

Rozwiązania globalne często składają się z regionalnych miejsc, które są w dużej mierze niezależne, z uwzględnieniem ich własnych możliwości przetwarzania, ale perspektywy Supra-regionalne i globalne będą wymagały integracji danych i w związku z tym centralnej konsolidacji tych samych danych komunikatów, które są oceniane w odpowiednich regionalnych regionach dla lokalnego perspektywy. 

![Potrzeby](media/service-bus-federation-overview/merge.jpg)

Normalizacja jest częścią scenariusza konsolidacji, w której dwie lub więcej przychodzących sekwencji komunikatów mają ten sam rodzaj informacji, ale z różnymi strukturami lub różnymi metodami kodowania, a komunikaty muszą być transkodowane lub przekształcone przed ich użyciem. 

Normalizacja może również obejmować zadania kryptograficzne, takie jak odszyfrowywanie kompleksowych ładunków szyfrowanych i ponowne szyfrowanie z użyciem różnych kluczy i algorytmów dla odbiorców dla klientów podrzędnych. 

### <a name="splitting-and-routing"></a>Dzielenie i Routing

Tematy Service Bus i ich reguły subskrypcji są często używane do filtrowania strumienia komunikatów dla określonych odbiorców, a następnie odbiorca uzyskuje przefiltrowany zestaw z subskrypcji. 

![Dzielenie](media/service-bus-federation-overview/split.jpg)

W systemie globalnym, w którym odbiorcy tych komunikatów są dystrybuowane globalnie lub należą do różnych aplikacji, replikacja może być używana do transferowania wiadomości z takiej subskrypcji do kolejki lub tematu w innej przestrzeni nazw, z której są używane.

### <a name="replication-applications-in-azure-functions"></a>Replikacja aplikacji w Azure Functions

Wdrożenie powyższych wzorców wymaga skalowalnego i niezawodnego środowiska wykonawczego dla zadań replikacji, które mają zostać skonfigurowane i uruchomione. Na platformie Azure środowisko uruchomieniowe najlepiej dopasowane do zadań bezstanowych jest [Azure Functions](../azure-functions/functions-overview.md). 

Azure Functions można uruchomić w ramach [tożsamości zarządzanej platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) , tak aby zadania replikacji można zintegrować z regułami kontroli dostępu opartymi na rolach dla usług źródłowych i docelowych bez konieczności zarządzania kluczami tajnymi wzdłuż ścieżki replikacji. W przypadku źródeł i elementów docelowych replikacji, które wymagają jawnych poświadczeń, Azure Functions mogą przechowywać wartości konfiguracyjne tych poświadczeń w odniesieniu do magazynu kontrolowanego dostępem w ramach [Azure Key Vault](../key-vault/general/overview.md).

Ponadto Azure Functions pozwala na bezpośrednie integrowanie zadań replikacji z sieciami wirtualnymi platformy Azure i [punktami końcowymi usługi](../virtual-network/virtual-network-service-endpoints-overview.md) dla wszystkich usług Azure Messaging oraz łatwe zintegrowane z [Azure monitor](../azure-monitor/overview.md).

Co najważniejsze, Azure Functions wstępnie skompilowane, skalowalne wyzwalacze i powiązania wyjściowe dla [Event Hubs platformy Azure](../azure-functions/functions-bindings-service-bus.md), [platformy azure IoT Hub](../azure-functions/functions-bindings-event-iot.md), [Azure Service Bus](../azure-functions/functions-bindings-service-bus.md), [Azure Event Grid](../azure-functions/functions-bindings-event-grid.md)i [platformy Azure queue storage](../azure-functions/functions-bindings-storage-queue.md), niestandardowe rozszerzenia dla [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)i [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension). Większość wyzwalaczy dynamicznie dostosowuje się do potrzeb dotyczących przepływności przez skalowanie liczby współbieżnie wykonywanych wystąpień w górę i w dół w oparciu o udokumentowane metryki. 

Zgodnie z planem użycia Azure Functions prekompilowane wyzwalacze mogą nawet skalować w dół do zera, a żadne komunikaty nie są dostępne do replikacji, co oznacza, że nie ponosisz żadnych kosztów, aby zachować konfigurację gotową do skalowania kopii zapasowej. Kluczowym minusem korzystania z planu zużycia jest to, że opóźnienie zadań replikacji "wznawianie" z tego stanu jest znacznie wyższe niż w przypadku planów hostingu, w których działa infrastruktura.  

W przeciwieństwie do wszystkich tych, najpopularniejszych aparatów replikacji na potrzeby obsługi komunikatów i zdarzeń, takich jak [narzędzia mirrormaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) Apache Kafka wymaga zapewnienia środowiska hostingu i samodzielnego skalowania aparatu replikacji. Obejmuje to Konfigurowanie i integrację funkcji zabezpieczeń i sieci oraz ułatwianie przepływu danych monitorowania, a następnie nadal nie istnieje możliwość dodawania niestandardowych zadań replikacji do przepływu. 

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono szereg wzorców Federacji i wyjaśniono rolę Azure Functions jako środowiska uruchomieniowego replikacji zdarzeń i komunikatów na platformie Azure. 

Następnie można zapoznać się z artykułem jak skonfigurować aplikację replikatora przy użyciu Azure Functions, a następnie jak replikować przepływy zdarzeń między Event Hubs i różnymi systemami obsługi zdarzeń i komunikatów:

- [Replikacja aplikacji w Azure Functions](service-bus-federation-replicator-functions.md)
- [Replikowanie zdarzeń między jednostkami Service Bus](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)
- [Kierowanie zdarzeń do usługi Azure Event Hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)
- [Pozyskiwanie zdarzeń z usługi Azure Event Hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus)

[1]: ./media/service-bus-auto-forwarding/IC628632.gif