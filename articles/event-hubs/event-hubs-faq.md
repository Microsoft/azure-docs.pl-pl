---
title: Często zadawane pytania — Azure Event Hubs | Microsoft Docs
description: Ten artykuł zawiera listę często zadawanych pytań dotyczących usługi Azure Event Hubs i ich odpowiedzi.
ms.topic: article
ms.date: 10/27/2020
ms.openlocfilehash: 051122c2030683eb2f3c57191dbbfa3bfd2bf6b7
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789373"
---
# <a name="event-hubs-frequently-asked-questions"></a>Event Hubs często zadawane pytania

## <a name="general"></a>Ogólne

### <a name="what-is-an-event-hubs-namespace"></a>Co to jest Event Hubs przestrzeń nazw?
Przestrzeń nazw jest kontenerem określania zakresu dla tematów centrum zdarzeń/Kafka. Zapewnia unikatową [nazwę FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Przestrzeń nazw służy jako kontener aplikacji, który może mieć wiele tematów centrów zdarzeń/Kafka. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Kiedy należy utworzyć nową przestrzeń nazw zamiast używać istniejącej przestrzeni nazw?
Alokacje wydajności ([jednostki przepływności (TUs)](#throughput-units)) są rozliczane na poziomie przestrzeni nazw. Przestrzeń nazw jest również skojarzona z regionem.

Możesz chcieć utworzyć nową przestrzeń nazw zamiast używać istniejącej w jednym z następujących scenariuszy: 

- Potrzebujesz centrum zdarzeń skojarzonego z nowym regionem.
- Potrzebujesz centrum zdarzeń skojarzonego z inną subskrypcją.
- Potrzebujesz centrum zdarzeń z odrębną alokacją pojemności (to oznacza, że pojemność przestrzeni nazw z dodanym centrum zdarzeń przekroczy próg 40 jednostek PRZEPŁYWNOŚCI i nie chcesz go wyszukać w przypadku dedykowanego klastra)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Jaka jest różnica między warstwami Event Hubs podstawowa i Standardowa?

Warstwa standardowa platformy Azure Event Hubs udostępnia funkcje wykraczające poza dostępność w warstwie Podstawowa. W standardzie są dostępne następujące funkcje:

* Dłuższe przechowywanie zdarzeń
* Dodatkowe połączenia obsługiwane przez brokera z opłatą za użycie nadwyżkowe za więcej niż uwzględniona liczba
* Więcej niż jedna [Grupa odbiorców](event-hubs-features.md#consumer-groups)
* [Przechwytywanie](event-hubs-capture-overview.md)
* [Integracja Kafka](event-hubs-for-kafka-ecosystem-overview.md)

Aby uzyskać więcej informacji na temat warstw cenowych, w tym Event Hubs — warstwa Dedykowana, zobacz [szczegóły cennika Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Gdzie jest dostępna usługa Azure Event Hubs?

Usługa Azure Event Hubs jest dostępna we wszystkich obsługiwanych regionach platformy Azure. Listę można znaleźć na stronie [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions/) .  

### <a name="can-i-use-a-single-advanced-message-queuing-protocol-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Czy można używać jednego połączenia Advanced Message Queuing Protocol (AMQP) do wysyłania i odbierania z wielu centrów zdarzeń?

Tak, o ile wszystkie Centra zdarzeń znajdują się w tej samej przestrzeni nazw.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Jaki jest maksymalny okres przechowywania zdarzeń?

Warstwa standardowa Event Hubs obecnie obsługuje maksymalny okres przechowywania wynoszący siedem dni. Centra zdarzeń nie są przeznaczone do stałego magazynu danych. Okresy przechowywania powyżej 24 godzin są przeznaczone dla scenariuszy, w których jest wygodne powtarzanie strumienia zdarzeń w tych samych systemach. Na przykład, aby przeprowadzić uczenie lub zweryfikować nowy model uczenia maszynowego na istniejących danych. Jeśli potrzebujesz przechowywania komunikatów przez siedem dni, włączenie [funkcji przechwytywania Event Hubs](event-hubs-capture-overview.md) w centrum zdarzeń powoduje pobranie danych z centrum zdarzeń do konta magazynu lub konta usługi Azure Data Lake. Włączenie funkcji przechwytywania spowoduje naliczenie opłaty na podstawie zakupionych jednostek przepływności.

Możesz skonfigurować okres przechowywania danych przechwyconych na koncie magazynu. Funkcja **zarządzania cyklem życia** usługi Azure Storage oferuje rozbudowane zasady oparte na regułach dla kont ogólnego przeznaczenia w wersji 2 i BLOB Storage. Użyj zasad, aby przenieść dane do odpowiednich warstw dostępu lub wygasnąć po zakończeniu cyklu życia danych. Aby uzyskać więcej informacji, zobacz [Zarządzanie cyklem życia usługi Azure Blob Storage](../storage/blobs/storage-lifecycle-management-concepts.md). 

### <a name="how-do-i-monitor-my-event-hubs"></a>Jak mogę monitorować moje Event Hubs?
Event Hubs emituje wyczerpujące metryki, które zapewniają stan zasobów do [Azure monitor](../azure-monitor/overview.md). Pozwalają również ocenić ogólną kondycję usługi Event Hubs nie tylko na poziomie przestrzeni nazw, ale również na poziomie jednostki. Dowiedz się więcej na temat monitorowania oferowanego w [usłudze Azure Event Hubs](event-hubs-metrics-azure-monitor.md).

### <a name="where-does-azure-event-hubs-store-customer-data"></a><a name="in-region-data-residency"></a>Gdzie usługa Azure Event Hubs przechować dane klienta?
Usługa Azure Event Hubs przechowuje dane klientów. Te dane są automatycznie przechowywane przez Event Hubs w jednym regionie, więc ta usługa automatycznie spełnia wymagania dotyczące miejsca zamieszkania danych regionu, w tym te określone w [Centrum zaufania](https://azuredatacentermap.azurewebsites.net/).

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Jakie porty muszę otworzyć na zaporze? 
Za pomocą następujących protokołów można Azure Service Bus wysyłać i odbierać komunikaty:

- AMQP
- HTTP
- Apache Kafka

Zapoznaj się z poniższą tabelą dla portów wychodzących, które należy otworzyć, aby używać tych protokołów do komunikowania się z usługą Azure Event Hubs. 

| Protokół | Porty | Szczegóły | 
| -------- | ----- | ------- | 
| AMQP | 5671 i 5672 | Zobacz [Przewodnik po protokole AMQP](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Zobacz [używanie Event Hubs z aplikacji Kafka](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-allow"></a>Jakie adresy IP muszę zezwalać?
Aby znaleźć odpowiednie adresy IP, które mają zostać dodane do listy dozwolonych połączeń, wykonaj następujące kroki:

1. Uruchom następujące polecenie w wierszu polecenia: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Zanotuj adres IP zwrócony w `Non-authoritative answer` . 

Jeśli używasz **nadmiarowości strefy** dla przestrzeni nazw, musisz wykonać kilka dodatkowych czynności: 

1. Najpierw uruchom polecenie nslookup w przestrzeni nazw.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Zanotuj nazwę w sekcji **Nieautorytatywna odpowiedź** , która znajduje się w jednym z następujących formatów: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Uruchom polecenie nslookup dla każdego z sufiksów S1, S2 i S3, aby uzyskać adresy IP wszystkich trzech wystąpień uruchomionych w trzech strefach dostępności, 

    > [!NOTE]
    > Adres IP zwrócony przez `nslookup` polecenie nie jest statycznym adresem IP. Jednak pozostaje stałą do momentu usunięcia lub przeniesienia bazowego wdrożenia do innego klastra.

### <a name="where-can-i-find-client-ip-sending-or-receiving-messages-to-my-namespace"></a>Gdzie można znaleźć adresy IP klienta wysyłające lub otrzymujące komunikaty do mojej przestrzeni nazw?
Najpierw włącz [filtrowanie adresów IP](event-hubs-ip-filtering.md) w przestrzeni nazw. 

Następnie Włącz dzienniki diagnostyczne dla [zdarzeń połączeń sieci wirtualnej Event Hubs](event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) , wykonując instrukcje podane w temacie [Włączanie dzienników diagnostycznych](event-hubs-diagnostic-logs.md#enable-diagnostic-logs). Zobaczysz adres IP, dla którego odmówiono nawiązania połączenia.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="apache-kafka-integration"></a>Integracja Apache Kafka

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Jak mogę zintegrować istniejącą aplikację Kafka z usługą Event Hubs?
Event Hubs udostępnia punkt końcowy Kafka, który może być używany przez istniejące aplikacje Apache Kafka na podstawie. Konieczna jest zmiana konfiguracji, która jest wymagana do korzystania z programu PaaS Kafka. Stanowi alternatywę dla uruchamiania własnego klastra Kafka. Event Hubs obsługuje Apache Kafka 1,0 i nowszych wersji klienta i współpracuje z istniejącymi aplikacjami Kafka, narzędziami i strukturami. Aby uzyskać więcej informacji, zobacz [Event Hubs dla repozytorium Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Jakie zmiany konfiguracji należy wykonać, aby moja istniejąca aplikacja mogła komunikować się z Event Hubs?
Aby połączyć się z centrum zdarzeń, należy zaktualizować konfiguracje klienta Kafka. Jest to realizowane przez utworzenie przestrzeni nazw Event Hubs i uzyskanie [parametrów połączenia](event-hubs-get-connection-string.md). Zmień wartość Bootstrap. Servers, aby wskazywała Event Hubs FQDN i port do 9093. Zaktualizuj sasl.jaas.config, aby skierować klienta Kafka do punktu końcowego Event Hubs (czyli uzyskanych parametrów połączenia) z prawidłowym uwierzytelnianiem, jak pokazano poniżej:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

Przykład:

```properties
bootstrap.servers=dummynamespace.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXX";
```
Uwaga: Jeśli sasl.jaas.config nie jest obsługiwaną konfiguracją w Twojej strukturze, Znajdź konfiguracje, które są używane do ustawiania nazwy użytkownika i hasła SASL, i Użyj zamiast nich. Ustaw nazwę użytkownika na $ConnectionString i hasło do parametrów połączenia Event Hubs.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Jaki jest rozmiar komunikatu/zdarzenia dla Event Hubs?
Maksymalny rozmiar komunikatu dozwolony dla Event Hubs wynosi 1 MB.

## <a name="throughput-units"></a>Jednostki przepływności

### <a name="what-are-event-hubs-throughput-units"></a>Co to są jednostki przepływności Event Hubs?
Przepływność w Event Hubs definiuje ilość danych w bajtach lub liczbę (w tysiącach) zdarzeń 1-KB, które przechodzą i wychodzące przez Event Hubs. Ta przepływność jest mierzona w jednostkach przepływności (TUs). Kup TUs przed rozpoczęciem korzystania z usługi Event Hubs. Można jawnie wybrać Event Hubs TUs za pomocą szablonów Menedżer zasobów lub Event Hubs. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Czy jednostki przepływności dotyczą wszystkich centrów zdarzeń w przestrzeni nazw?
Tak, jednostki przepływności (TUs) stosują się do wszystkich centrów zdarzeń w przestrzeni nazw Event Hubs. Oznacza to, że użytkownik kupuje TUs na poziomie przestrzeni nazw i jest udostępniany między centrami zdarzeń w tej przestrzeni nazw. Każdy jednostek PRZEPŁYWNOŚCI uprawnia przestrzeń nazw do następujących możliwości:

- Do 1 MB na sekundę zdarzeń związanych z transferem danych przychodzących (zdarzeń wysyłanych do centrum zdarzeń), ale nie więcej niż 1000 zdarzeń związanych z transferem danych przychodzących, operacji zarządzania lub wywołań interfejsu API kontroli na sekundę.
- Do 2 MB na sekundę zdarzeń związanych z ruchem wychodzącym (zdarzenia używane z centrum zdarzeń), ale nie więcej niż 4096 zdarzeń wychodzących.
- Do 84 GB miejsca w magazynie zdarzeń (wystarczy dla domyślnego okresu przechowywania 24-godzinnego).

### <a name="how-are-throughput-units-billed"></a>Jak są rozliczane jednostki przepływności?
Jednostki przepływności (TUs) są rozliczane godzinowo. Opłaty są naliczane na podstawie maksymalnej liczby jednostek, która została wybrana w danej godzinie. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Jak mogę zoptymalizować użycie w jednostkach przepływności?
Możesz rozpocząć od jednej jednostki przepływności (jednostek PRZEPŁYWNOŚCI) i włączyć funkcję [autodostrajania](event-hubs-auto-inflate.md). Funkcja autostartu pozwala rozwijać TUs w miarę wzrostu ruchu sieciowego. Można również ustawić górny limit liczby TUs.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Jak działa funkcja autorozdęcie Event Hubs?
Funkcja automatycznego rozszerzania umożliwia skalowanie jednostek przepływności (TUs). Oznacza to, że możesz zacząć od zakupu niskich TUs, a funkcja autodostrajania skaluje swój TUs w miarę wzrostu ruchu przychodzącego. Zapewnia ekonomiczne opcje i pełną kontrolę nad liczbą TUs do zarządzania. Ta funkcja jest funkcją **tylko do skalowania w górę** i można całkowicie kontrolować skalowanie liczby TUs przez ich aktualizację. 

Możesz zacząć od jednostek o niskiej przepływności (TUs), na przykład 2 TUs. Jeśli przewidujesz, że ruch może wzrosnąć do 15 TUs, Włącz funkcję autorozszerzania w przestrzeni nazw i ustaw maksymalny limit na 15 TUs. Teraz możesz rozwijać TUs automatycznie w miarę wzrostu ruchu.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Czy podczas włączania funkcji autostartu jest koszt skojarzony?
**Brak kosztów** skojarzonych z tą funkcją. 

### <a name="how-are-throughput-limits-enforced"></a>Jak są wymuszane limity przepływności?
Jeśli całkowita przepływność transferu danych przychodzących **lub całkowita szybkość** zdarzeń związanych z transferem danych przychodzących we wszystkich centrach zdarzeń w przestrzeni nazw przekroczy zagregowany limit jednostek przepływności, nadawcy są ograniczane i odbierane są błędy wskazujące, że przekroczony został przydział transferu danych przychodzących.

Jeśli całkowita przepływność transferu danych wychodzących **lub całkowita szybkość** zdarzeń związanych z transferem danych wychodzących we wszystkich centrach zdarzeń w przestrzeni nazw przekroczy zagregowany limit jednostek przepływności, odbiorniki są ograniczane, ale nie są generowane błędy ograniczania. 

Limity przydziałów ruchu przychodzącego i wychodzącego są wymuszane oddzielnie, dzięki czemu żaden nadawca nie może spowodować spowolnienia użycia zdarzenia, a odbiorca nie może uniemożliwić wysyłania zdarzeń do centrum zdarzeń.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-reservedselected"></a>Czy istnieje ograniczenie liczby jednostek przepływności, które mogą być zarezerwowane lub wybrane?

Podczas tworzenia przestrzeni nazw w warstwie Podstawowa lub standardowa w Azure Portal można wybrać do 20 TUs dla przestrzeni nazw. Aby podnieść poziom do **dokładnie** 40 TUs, Prześlij  [żądanie pomocy technicznej](../azure-portal/supportability/how-to-create-azure-support-request.md).  

1. Na stronie **przestrzeń nazw magistrali zdarzeń** wybierz pozycję **nowe żądanie obsługi** w menu po lewej stronie. 
1. Na stronie **nowe żądanie obsługi** wykonaj następujące czynności:
    1. Aby uzyskać **Podsumowanie** , opisz problem za pomocą kilku wyrazów. 
    1. W obszarze **typ problemu** wybierz pozycję **przydział** . 
    1. W przypadku **problemu z podtypem** wybierz pozycję **Żądaj zwiększenia lub zmniejszenia jednostki przepływności** . 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-throughput-units.png" alt-text="Strona Support request":::

Poza 40 TUs, Event Hubs oferuje model oparty na zasobach i pojemności nazywany klastrami Event Hubs — warstwa Dedykowana. Dedykowane klastry są sprzedawane w jednostkach pojemności. Aby uzyskać więcej informacji, zobacz [Event Hubs — warstwa dedykowana — przegląd](event-hubs-dedicated-overview.md).

## <a name="dedicated-clusters"></a>Dedykowane klastry

### <a name="what-are-event-hubs-dedicated-clusters"></a>Co to są klastry usługi Event Hubs — warstwa Dedykowana?
Klastry Event Hubs — warstwa Dedykowana oferują wdrożenia z jedną dzierżawą dla klientów z najbardziej wymaganymi wymaganiami. Ta oferta kompiluje klaster oparty na pojemności, który nie jest powiązany z jednostkami przepływności. Oznacza to, że można użyć klastra do pozyskiwania i przesyłania strumieniowego danych zgodnie z obciążeniem procesora i pamięci klastra. Aby uzyskać więcej informacji, zobacz [Event Hubs — warstwa dedykowana klastrów](event-hubs-dedicated-overview.md).

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Jak mogę utworzyć klaster Event Hubs — warstwa Dedykowana?
Aby uzyskać instrukcje krok po kroku i więcej informacji na temat konfigurowania dedykowanego klastra Event Hubs, zobacz [Przewodnik Szybki Start: tworzenie dedykowanego Event Hubs klastra przy użyciu Azure Portal](event-hubs-dedicated-cluster-create-portal.md). 


[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]


## <a name="partitions"></a>Partycje

### <a name="how-many-partitions-do-i-need"></a>Ile partycji potrzebuję?
Liczba partycji jest określana podczas tworzenia i musi należeć do zakresu od 1 do 32. Liczba partycji nie jest zmieniana, dlatego należy rozważyć długoterminową skalę podczas ustawiania liczby partycji. Partycje stanowią mechanizm organizacji danych powiązany z równoległością podrzędną wymaganą w aplikacjach korzystających z tych danych. Liczba partycji w centrum zdarzeń jest bezpośrednio związana z oczekiwaną liczbą jednoczesnych czytników. Aby uzyskać więcej informacji o partycjach, zobacz [partycje](event-hubs-features.md#partitions).

Może być konieczne ustawienie najwyższej możliwej wartości, która jest 32 w momencie tworzenia. Należy pamiętać, że z więcej niż jedną partycją będzie można wysyłać zdarzenia do wielu partycji bez zachowywania kolejności, chyba że skonfigurowano nadawców tylko do jednej partycji z 32, pozostawiając pozostałe 31 partycji. W poprzednim przypadku trzeba będzie odczytywać zdarzenia ze wszystkich partycji 32. W tym drugim przypadku nie ma żadnych oczywistych dodatkowych kosztów poza dodatkową konfiguracją, którą należy wykonać na hoście procesora zdarzeń.

Event Hubs jest zaprojektowana tak, aby zezwalała na pojedynczy czytnik partycji dla każdej grupy odbiorców. W większości przypadków użycia wystarcza domyślnie cztery partycje. Jeśli chcesz skalować przetwarzanie zdarzeń, warto rozważyć dodanie dodatkowych partycji. Nie ma określonego limitu przepływności dla partycji, ale agregowana przepływność w przestrzeni nazw jest ograniczona przez liczbę jednostek przepływności. W miarę zwiększania liczby jednostek przepływności w przestrzeni nazw mogą być potrzebne dodatkowe partycje pozwalające współbieżnym czytelnikom na osiąganie ich maksymalnej przepływności.

Jeśli jednak masz model, w którym aplikacja ma koligację z określoną partycją, zwiększenie liczby partycji może nie mieć żadnej korzyści. Aby uzyskać więcej informacji, zobacz [dostępność i spójność](event-hubs-availability-and-consistency.md).

### <a name="increase-partitions"></a>Zwiększ partycje
Możesz poprosić o zwiększenie liczby partycji do 40 (dokładne) przez przesłanie żądania pomocy technicznej. 

1. Na stronie **przestrzeń nazw magistrali zdarzeń** wybierz pozycję **nowe żądanie obsługi** w menu po lewej stronie. 
1. Na stronie **nowe żądanie obsługi** wykonaj następujące czynności:
    1. Aby uzyskać **Podsumowanie** , opisz problem za pomocą kilku wyrazów. 
    1. W obszarze **typ problemu** wybierz pozycję **przydział** . 
    1. W przypadku **problemu z podtypem** wybierz pozycję **Żądaj zmiany partycji** . 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-increase-partitions.png" alt-text="Strona Support request":::

Liczbę partycji można zwiększyć do dokładnie 40. W takim przypadku liczbę TUs należy również zwiększyć do 40. Jeśli zdecydujesz się później obniżyć limit jednostek PRZEPŁYWNOŚCI z powrotem do <= 20, maksymalny limit partycji zostanie również obniżony do 32. 

Zmniejszenie partycji nie ma wpływu na istniejące Centra zdarzeń, ponieważ partycje są stosowane na poziomie centrum zdarzeń i są niezmienne po utworzeniu centrum. 

## <a name="pricing"></a>Cennik

### <a name="where-can-i-find-more-pricing-information"></a>Gdzie można znaleźć więcej informacji o cenach?

Aby uzyskać pełne informacje na temat cennika Event Hubs, zobacz [szczegóły cennika Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Czy jest naliczana opłata za przechowywanie zdarzeń Event Hubs przez ponad 24 godziny?

Warstwa standardowa Event Hubs zezwala na okresy przechowywania komunikatów dłużej niż 24 godziny przez maksymalnie siedem dni. Jeśli całkowita liczba przechowywanych zdarzeń przekracza limit magazynu dla liczby wybranych jednostek przepływności (84 GB na jednostkę przepływności), rozmiar przekraczający limit jest naliczany według opublikowanej szybkości magazynu obiektów blob platformy Azure. Dopuszczalna ilość miejsca do magazynowania w poszczególnych jednostkach przepływności obejmuje wszystkie koszty przechowywania przez okres 24 godzin (domyślnie), nawet jeśli jednostka przepływności jest używana do maksymalnego limitu transferu danych przychodzących.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Jak jest obliczany i naliczany rozmiar magazynu Event Hubs?

Całkowity rozmiar wszystkich przechowywanych zdarzeń, w tym dowolnych wewnętrznych nakładów dla nagłówków zdarzeń lub struktur magazynów dyskowych we wszystkich centrach zdarzeń, jest mierzony przez cały dzień. Na koniec dnia jest obliczana szczytowa wielkość magazynu. Dzienny limit magazynu jest obliczany na podstawie minimalnej liczby jednostek przepływności wybranych w ciągu dnia (każda jednostka przepływności zapewnia przynależność do 84 GB). Jeśli całkowity rozmiar przekracza obliczony dzienny limit magazynu, nadmiarowy magazyn jest rozliczany przy użyciu stawek za magazyn obiektów blob platformy Azure (za **Magazyn lokalnie nadmiarowy** ).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Jak Event Hubs są obliczane zdarzenia związane z transferem danych przychodzących?

Każde zdarzenie wysyłane do centrum zdarzeń jest traktowane jako komunikat rozliczany. *Zdarzenie* przychodzące jest zdefiniowane jako jednostka danych, która jest mniejsza lub równa 64 KB. Każde zdarzenie o rozmiarze mniejszym lub równym 64 KB jest traktowane jako jedno konto rozliczane. Jeśli zdarzenie jest większe niż 64 KB, liczba zdarzeń rozliczanych jest obliczana zgodnie z rozmiarem zdarzenia (wielokrotność 64 KB). Na przykład zdarzenie 8 KB wysyłane do centrum zdarzeń jest rozliczane jako jedno zdarzenie, ale komunikat o 96 KB wysłany do centrum zdarzeń jest rozliczany jako dwa zdarzenia.

Zdarzenia używane z centrum zdarzeń, a także operacje zarządzania i wywołania kontrolne, takie jak punkty kontrolne, nie są wliczane do płatnych zdarzeń związanych z transferem danych przychodzących, ale są naliczane do limitu jednostek przepływności.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Czy opłaty za połączenia obsługiwane przez brokera dotyczą Event Hubs?

Opłaty za połączenia są stosowane tylko wtedy, gdy używany jest protokół AMQP. Nie ma opłat za połączenia związane z wysyłaniem zdarzeń przy użyciu protokołu HTTP, niezależnie od liczby systemów lub urządzeń wysyłających. Jeśli planujesz używać AMQP (na przykład w celu osiągnięcia bardziej wydajnego przesyłania strumieniowego zdarzeń lub umożliwienia dwukierunkowej komunikacji w scenariuszach poleceń i kontroli IoT), zobacz stronę [Informacje o cenach Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) , aby uzyskać szczegółowe informacje o liczbie połączeń uwzględnionych w poszczególnych warstwach usług.

### <a name="how-is-event-hubs-capture-billed"></a>Jak jest rozliczany Event Hubs przechwycenia?

Przechwytywanie jest włączone, gdy dowolne centrum zdarzeń w przestrzeni nazw ma włączoną opcję przechwytywania. Event Hubs przechwytywania jest rozliczana godzinowo za zakupioną jednostkę przepływności. W miarę zwiększania lub zmniejszania liczby jednostek przepływności, Event Hubs przechwycenia uwzględnia te zmiany w pełnych godzinach. Aby uzyskać więcej informacji na temat rozliczeń Event Hubs przechwytywania, zobacz [Event Hubs informacji o cenach](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Czy jest naliczana opłata za konto magazynu wybrane do przechwycenia Event Hubs?

Funkcja przechwytywania korzysta z konta magazynu, które jest dostępne, gdy jest włączone w centrum zdarzeń. Ponieważ jest to Twoje konto magazynu, wszelkie zmiany tej konfiguracji są rozliczane w ramach subskrypcji platformy Azure.

## <a name="quotas"></a>Przydziały

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Czy istnieją przydziały skojarzone z Event Hubs?

Aby uzyskać listę wszystkich przydziałów Event Hubs, zobacz [przydziały](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Dlaczego nie mogę utworzyć przestrzeni nazw po usunięciu jej z innej subskrypcji? 
Po usunięciu przestrzeni nazw z subskrypcji poczekaj 4 godziny, a następnie utwórz ją z tą samą nazwą w innej subskrypcji. W przeciwnym razie może zostać wyświetlony następujący komunikat o błędzie: `Namespace already exists` . 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Jakie są wyjątki generowane przez Event Hubs i ich sugerowane akcje?

Listę możliwych wyjątków Event Hubs można znaleźć w temacie [wyjątki — Omówienie](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Event Hubs obsługuje dwa typy [dzienników diagnostycznych](event-hubs-diagnostic-logs.md) — przechwytywanie dzienników błędów i dzienników operacyjnych — obie są reprezentowane w formacie JSON i można je włączyć za pomocą Azure Portal.

### <a name="support-and-sla"></a>Pomoc techniczna i Umowa SLA

Pomoc techniczna dla Event Hubs jest dostępna za pomocą strony pytań i odpowiedzi [Microsoft&pytań i odpowiedzi na Azure Service Bus](/answers/topics/azure-service-bus.html). Pomoc dotycząca rozliczeń i subskrypcji jest świadczona bezpłatnie.

Aby dowiedzieć się więcej o umowie SLA, zobacz stronę [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/) .

## <a name="azure-stack-hub"></a>Azure Stack Hub

### <a name="how-can-i-target-a-specific-version-of-azure-storage-sdk-when-using-azure-blob-storage-as-a-checkpoint-store"></a>Jak można kierować określoną wersję zestawu SDK usługi Azure Storage w przypadku korzystania z usługi Azure Blob Storage jako magazynu punktów kontrolnych?
Jeśli uruchomisz ten kod w Azure Stack centrum, wystąpią błędy środowiska uruchomieniowego, chyba że zostanie wybrana wersja interfejsu API określonej usługi Storage. Jest to spowodowane tym, że zestaw SDK Event Hubs korzysta z najnowszego dostępnego interfejsu API usługi Azure Storage dostępnego na platformie Azure, który może nie być dostępny na platformie Azure Stack Hub. Azure Stack Hub może obsługiwać inną wersję zestawu SDK obiektów blob magazynu, która jest zazwyczaj dostępna na platformie Azure. Jeśli używasz magazynu blogów platformy Azure jako magazynu punktów kontrolnych, sprawdź [obsługiwaną wersję interfejsu API usługi Azure Storage dla kompilacji centrum Azure Stack](/azure-stack/user/azure-stack-acs-differences?#api-version) i wybierz tę wersję w kodzie. 

Na przykład jeśli korzystasz z programu Azure Stack Hub w wersji 2005, najwyższa dostępna wersja usługi Storage to wersja 2019-02-02. Domyślnie Biblioteka klienta zestawu SDK Event Hubs używa najwyższej dostępnej wersji na platformie Azure (2019-07-07 w momencie wydania zestawu SDK). W takim przypadku, oprócz kroków opisanych w tej sekcji, należy również dodać kod docelowy interfejsu API usługi Storage w wersji 2019-02-02. Aby zapoznać się z przykładem dotyczącym konkretnej wersji interfejsu API usługi Storage, zobacz następujące przykłady dla języków C#, Java, Python i JavaScript/TypeScript.  

Aby zapoznać się z przykładem dotyczącym konkretnej wersji interfejsu API usługi Storage w kodzie, zobacz następujące przykłady w witrynie GitHub: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)
- Python — [synchroniczne](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [asynchroniczne](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) i [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts)

## <a name="next-steps"></a>Następne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs](./event-hubs-about.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs autodostrajania](event-hubs-auto-inflate.md)
