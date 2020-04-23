---
title: Często zadawane pytania — Usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: Ten artykuł zawiera listę często zadawanych pytań (CZĘSTO ZADAWANE PYTANIA) dla usługi Azure Event Hubs i ich odpowiedzi.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/02/2019
ms.author: shvija
ms.openlocfilehash: 7f6e1896c97c96cd484d15fb9e6a3056e5c5d6b2
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086372"
---
# <a name="event-hubs-frequently-asked-questions"></a>Centra zdarzeń często zadawane pytania

## <a name="general"></a>Ogólne

### <a name="what-is-an-event-hubs-namespace"></a>Co to jest obszar nazw Centrum zdarzeń?
Obszar nazw jest kontenerem zakresu dla tematów Centrum zdarzeń/platformy Kafka. To daje unikalny [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Obszar nazw służy jako kontener aplikacji, który może pomieścić wiele Event Hub/Kafka Tematy. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Kiedy utworzyć nową przestrzeń nazw a używać istniejącego obszaru nazw?
Alokacje zdolności produkcyjnych[(jednostki przepływności (TUs)](#throughput-units)są rozliczane na poziomie obszaru nazw. Obszar nazw jest również skojarzony z regionem.

Można utworzyć nowy obszar nazw zamiast przy użyciu istniejącego w jednym z następujących scenariuszy: 

- Potrzebujesz Centrum zdarzeń skojarzonego z nowym regionem.
- Potrzebujesz Centrum zdarzeń skojarzonego z inną subskrypcją.
- Potrzebujesz Centrum zdarzeń z odrębną alokacją zdolności produkcyjnych (czyli zapotrzebowanie na pojemność obszaru nazw z dodanym centrum zdarzeń przekroczy próg 40 TU i nie chcesz przechodzić do dedykowanego klastra)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Jaka jest różnica między warstwami Event Hubs Basic i Standard?

Warstwa Standardowa usługi Azure Event Hubs zawiera funkcje wykraczające poza to, co jest dostępne w warstwie Podstawowa. W standardzie znajdują się następujące funkcje:

* Dłuższe zatrzymywanie zdarzeń
* Dodatkowe połączenia pośredniczące, z opłatą za nadliżenie więcej niż liczba w zestawie
* Więcej niż jedna [grupa konsumentów](event-hubs-features.md#consumer-groups)
* [Przechwytywania](event-hubs-capture-overview.md)
* [Integracja kafka](event-hubs-for-kafka-ecosystem-overview.md)

Aby uzyskać więcej informacji na temat warstw cenowych, w tym usługi Event Hubs Dedicated, zobacz [szczegóły dotyczące cen centrum zdarzeń](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Gdzie są dostępne usługi Azure Event Hubs?

Usługa Azure Event Hubs jest dostępna we wszystkich obsługiwanych regionach platformy Azure. Aby uzyskać listę, odwiedź stronę [regionów platformy Azure.](https://azure.microsoft.com/regions/)  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Czy mogę używać jednego połączenia AMQP do wysyłania i odbierania z wielu centrów zdarzeń?

Tak, tak długo, jak wszystkie centra zdarzeń znajdują się w tej samej przestrzeni nazw.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Jaki jest maksymalny okres przechowywania zdarzeń?

Warstwa Standard usługi Event Hubs obsługuje obecnie maksymalny okres przechowywania wynoszący siedem dni. Centra zdarzeń nie są przeznaczone jako stały magazyn danych. Okresy przechowywania większe niż 24 godziny są przeznaczone dla scenariuszy, w których jest wygodne do odtwo nieodtwonie strumienia zdarzeń do tych samych systemów; na przykład, aby szkolić lub weryfikować nowy model uczenia maszynowego na istniejących danych. Jeśli potrzebujesz przechowywania wiadomości po upływie siedmiu dni, włączenie [przechwytywania zdarzeń w](event-hubs-capture-overview.md) centrum zdarzeń pobiera dane z centrum zdarzeń do wybranego konta usługi Storage lub usługi Azure Data Lake Service. Włączenie przechwytywania wiąże się z obciążeniem w oparciu o zakupione jednostki przepływności.

Można skonfigurować okres przechowywania przechwyconych danych na koncie magazynu. Funkcja **zarządzania cyklem życia** usługi Azure Storage oferuje bogate zasady oparte na regułach dla kont magazynu ogólnego przeznaczenia w wersji 2 i obiektów blob. Użyj zasad, aby przejść dane do odpowiednich warstw dostępu lub wygaśnie po zakończeniu cyklu życia danych. Aby uzyskać więcej informacji, zobacz [Zarządzanie cyklem życia magazynu obiektów Blob platformy Azure](../storage/blobs/storage-lifecycle-management-concepts.md). 

### <a name="how-do-i-monitor-my-event-hubs"></a>Jak monitorować centra zdarzeń?
Usługa Event Hubs emituje wyczerpujące metryki, które zapewniają stan zasobów do [usługi Azure Monitor.](../azure-monitor/overview.md) Umożliwiają one również ocenę ogólnej kondycji usługi Usługi Centrum zdarzeń nie tylko na poziomie obszaru nazw, ale także na poziomie jednostki. Dowiedz się, jakie monitorowanie jest oferowane w [usłudze Azure Event Hubs.](event-hubs-metrics-azure-monitor.md)

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Jakie porty są potrzebne do otwarcia na zaporze? 
Do wysyłania i odbierania wiadomości za pomocą usługi Azure Service Bus można używać następujących protokołów:

- Zaawansowane usługi kolejkowania Protocol (AMQP)
- HTTP
- Apache Kafka

Zobacz poniższą tabelę dla portów wychodzących, które należy otworzyć, aby używać tych protokołów do komunikowania się z usługą Azure Event Hubs. 

| Protocol (Protokół) | Porty | Szczegóły | 
| -------- | ----- | ------- | 
| AMQP | 5671 i 5672 | Zobacz [przewodnik po protokole protokołu AMQP](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Zobacz [Korzystanie z centrów zdarzeń z aplikacji platformy Kafka](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Jakie adresy IP są potrzebne do umieszczenia na białej liście?
Aby znaleźć odpowiednie adresy IP do białej listy połączeń, wykonaj następujące kroki:

1. Uruchom następujące polecenie z wiersza polecenia: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Zanotuj adres `Non-authoritative answer`IP zwrócony w pliku . Tylko wtedy, gdy zmieni się, jeśli przywrócisz obszar nazw do innego klastra.

Jeśli używasz nadmiarowości strefy dla obszaru nazw, musisz wykonać kilka dodatkowych kroków: 

1. Najpierw uruchom nslookup w obszarze nazw.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Zanotuj nazwę w sekcji **odpowiedzi nieuzyskanych,** która jest w jednym z następujących formatów: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Uruchom nslookup dla każdego z przyrostkami s1, s2 i s3, aby uzyskać adresy IP wszystkich trzech wystąpień uruchomionych w trzech strefach dostępności, 

## <a name="apache-kafka-integration"></a>Integracja Apache Kafka

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Jak zintegrować istniejącą aplikację Platformy Kafka z Centrum zdarzeń?
Usługa Event Hubs udostępnia punkt końcowy platformy Kafka, który może być używany przez istniejące aplikacje oparte na platformie Apache Kafka. Zmiana konfiguracji jest wszystko, co jest wymagane do korzystania z usługi PaaS Kafka. Stanowi alternatywę dla uruchamiania własnego klastra platformy Kafka. Usługa Event Hubs obsługuje apache kafka 1.0 i nowsze wersje klienta i współpracuje z istniejącymi aplikacjami, narzędziami i strukturami platformy Kafka. Aby uzyskać więcej informacji, zobacz [Centra zdarzeń dla repozytorium platformy Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Jakie zmiany konfiguracji należy wykonać, aby moja istniejąca aplikacja rozmawiała z centrum zdarzeń?
Aby połączyć się z centrum zdarzeń, musisz zaktualizować konfiguracje klientów platformy Kafka. Odbywa się to przez utworzenie obszaru nazw Centrum zdarzeń i uzyskanie [ciągu połączenia](event-hubs-get-connection-string.md). Zmień plik bootstrap.servers, aby wskazać numer FQDN centrów zdarzeń i port na 9093. Zaktualizuj sasl.jaas.config, aby skierować klienta platformy Kafka do punktu końcowego usługi Event Hubs (który jest uzyskanym parametryem połączenia), z poprawnym uwierzytelnianiem, jak pokazano poniżej:

bootstrap.servers={TWOJE. EVENTHUBS. FQDN}:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule wymaganego login="$ConnectionString" password="{YOUR. EVENTHUBS. Połączenia. CIĄG}";

Przykład:

bootstrap.servers=dummynamespace.servicebus.windows.net:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule wymagane username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/; SharedAccessKeyName=DummyAccessKeyName; SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=";

Uwaga: Jeśli sasl.jaas.config nie jest obsługiwaną konfiguracją w ramach, znajdź konfiguracje, które są używane do ustawiania nazwy użytkownika i hasła SASL i użyj ich zamiast tego. Ustaw nazwę użytkownika na $ConnectionString i hasło do ciągu połączenia usługi Event Hubs.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Jaki jest rozmiar wiadomości/zdarzeń w centrach zdarzeń?
Maksymalny rozmiar wiadomości dozwolony dla centrów zdarzeń wynosi 1 MB.

## <a name="throughput-units"></a>Jednostki przepływności

### <a name="what-are-event-hubs-throughput-units"></a>Co to są jednostki przepływności centrów zdarzeń?
Przepływność w centrach zdarzeń definiuje ilość danych w mega bajtach lub liczbę (w tysiącach) zdarzeń 1 KB, które przychodzące i wychodzące za pośrednictwem centrów zdarzeń. Ta przepustowość jest mierzona w jednostkach przepływności (TU). Zakup TUs przed rozpoczęciem korzystania z usługi Usługi Centrum zdarzeń. Można jawnie wybrać usługi TU centrów zdarzeń za pomocą szablonów Portalu lub Menedżera zasobów centrum zdarzeń. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Czy jednostki przepływności mają zastosowanie do wszystkich centrów zdarzeń w obszarze nazw?
Tak, jednostki przepływności (TUs) mają zastosowanie do wszystkich centrów zdarzeń w obszarze nazw Centrum zdarzeń. Oznacza to, że zakup tus na poziomie obszaru nazw i są współużytkowane przez centra zdarzeń w tej przestrzeni nazw. Każda jednostka robocza uprawnia obszar nazw do następujących funkcji:

- Do 1 MB na sekundę zdarzeń przychodzących (zdarzenia wysyłane do centrum zdarzeń), ale nie więcej niż 1000 zdarzeń transferu danych przychodzących, operacji zarządzania lub kontroli wywołań interfejsu API na sekundę.
- Do 2 MB na sekundę zdarzeń wychodzących (zdarzenia używane z centrum zdarzeń), ale nie więcej niż 4096 zdarzeń wychodzących.
- Do 84 GB miejsca na zdarzenia (wystarczająca dla domyślnego 24-godzinnego okresu przechowywania).

### <a name="how-are-throughput-units-billed"></a>Jak są rozliczane jednostki przepływności?
Jednostki przepływności (TUs) są rozliczane co godzinę. Rozliczenia są oparte na maksymalnej liczbie jednostek wybranych w danej godzinie. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Jak zoptymalizować użycie jednostek przepływności?
Można uruchomić tak niskie, jak jedna jednostka przepływności (TU) i włączyć [automatyczne nadmuchiwane](event-hubs-auto-inflate.md). Funkcja automatycznego nadmuchiwania pozwala rozwijać swoje TUs wraz ze wzrostem ruchu / ładowności. Można również ustawić górny limit liczby tk.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Jak działa funkcja Automatycznego zawyżania centrów zdarzeń?
Funkcja automatycznego nadmuchiwane umożliwia skalowanie jednostek przepływności (TUs). Oznacza to, że można rozpocząć od zakupu niskich TUs i auto-nadmuchać skaluje się tus jak twój wnikanie wzrasta. Zapewnia ekonomiczną opcję i pełną kontrolę nad liczbą procesorów telekomunikacyjnych do zarządzania. Ta funkcja jest tylko **skalowaną** funkcją i można całkowicie kontrolować skalowanie w dół liczby obiektów tu, aktualizując go. 

Można rozpocząć od jednostek o niskiej przepływności (TUs), na przykład 2 jednostek TU. Jeśli przewidujesz, że ruch może wzrosnąć do 15 procesorów TU, włącz funkcję automatycznego nadmuchiwanie w obszarze nazw i ustaw maksymalny limit na 15 procesorów TU. Teraz możesz automatycznie rozwijać swoje tus w miarę wzrostu ruchu.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Czy po włączeniu funkcji automatycznego nadmuchiania wiąże się koszt?
Z tą funkcją nie są skojarzone **żadne koszty.** 

### <a name="how-are-throughput-limits-enforced"></a>Jak są wymuszane limity przepływności?
Jeśli całkowita przepływność przychodząca lub całkowita szybkość zdarzeń przychodzących we wszystkich centrach zdarzeń w obszarze nazw przekracza zagregowane przydziały jednostek przepływności, nadawcy są ograniczani i otrzymują błędy wskazujące, że przydział transferu przychodzącego ruchu przychodzącego został przekroczony.

Jeśli całkowita przepływność wychodząca lub całkowita szybkość ruchu wychodzącego wszystkich węzłów zdarzeń w obszarze nazw przekracza zagregowane przydziały jednostek przepływności, odbiorniki są ograniczane i otrzymują błędy wskazujące, że przydział ruchu wychodzącego został przekroczony. Przydziały ruchu przychodzącego i wychodzącego są wymuszane oddzielnie, dzięki czemu żaden nadawca nie może powodować spowolnienia zużycia zdarzeń ani nie może uniemożliwić wysyłania zdarzeń do centrum zdarzeń.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Czy istnieje limit liczby jednostek przepływności (TUs), które można zarezerwować/wybrać?
W przypadku oferty z wieloma dzierżawami jednostki przepływności mogą wzrosnąć do 40 jednostek TU (można wybrać do 20 jednostek TU w portalu i podnieść bilet pomocy technicznej, aby podnieść go do 40 jednostek TU w tej samej przestrzeni nazw). Poza 40 procesorami plików TU, centra zdarzeń oferują model oparty na zasobach/pojemnościach o nazwie **klastry dedykowane centrów zdarzeń.** Dedykowane klastry są sprzedawane w jednostkach pojemności (CU).

## <a name="dedicated-clusters"></a>Dedykowane klastry

### <a name="what-are-event-hubs-dedicated-clusters"></a>Co to są klastry usługi Event Hubs — warstwa Dedykowana?
Klastry dedykowane usługi Event Hubs oferują wdrożenia z jedną dzierżawą dla klientów o najbardziej wymagających wymaganiach. Ta oferta tworzy klaster oparty na pojemności, który nie jest powiązany przez jednostki przepływności. Oznacza to, że można użyć klastra do pozyskiwania i przesyłania strumieniowego danych zgodnie z użyciem procesora CPU i pamięci klastra. Aby uzyskać więcej informacji, zobacz [Klastry dedykowane centrów zdarzeń](event-hubs-dedicated-overview.md).

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>Jaka jest efektywność pojedynczej jednostki wydajności?
W przypadku dedykowanego klastra, ile można spożywać i przesyłać strumieniowo, zależy od różnych czynników, takich jak producenci, konsumenci, szybkość, z jaką połykasz i przetwarzasz, i wiele więcej. 

Poniższa tabela przedstawia wyniki testów porównawczych, które osiągnęliśmy podczas naszych testów:

| Kształt ładowności | Odbiorniki | Przepustowość transferu ruchu przychodzącego| Komunikaty przychodzące | Przepustowość ruchu wychodzącego | Wiadomości wychodzące | Całkowita liczba TU | TUs na CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Partie 100x1KB | 2 | 400 MB/s | 400k wiadomości/s | 800 MB/s | 800k wiadomości/s | 400 tus | 100 tus | 
| Partie 10x10KB | 2 | 666 MB/s | 66,6 tys. | 1,33 GB/s | 133k wiadomości/s | 666 tus | 166 tus |
| Partie 6x32KB | 1 | 1,05 GB/s | Komunikaty 34k /s | 1,05 GB/s | Komunikaty 34k/s | 1000 tus | 250 tUs |

W badaniu zastosowano następujące kryteria:

- Użyto dedykowanego klastra centrum zdarzeń z czterema jednostkami pojemności (CU). 
- Centrum zdarzeń używane do pozyskiwania miał 200 partycji. 
- Dane, które zostały pozyskane zostały odebrane przez dwie aplikacje odbiornika odbierające ze wszystkich partycji.

Wyniki dają wyobrażenie o tym, co można osiągnąć za pomocą dedykowanego klastra centrum zdarzeń. Ponadto klaster dedykacji jest wyposażony w funkcję Przechwytywanie centrów zdarzeń włączone dla scenariuszy mikro-partii i długoterminowego przechowywania.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Jak utworzyć dedykowany klaster centrów zdarzeń?
Utworzony klaster dedykowany centrum zdarzeń można utworzyć, przesyłając [żądanie zwiększenia przydziału pomocy technicznej](https://portal.azure.com/#create/Microsoft.Support) lub kontaktując się z [zespołem Centrum zdarzeń](mailto:askeventhubs@microsoft.com). Zwykle trwa około dwóch tygodni, aby uzyskać klastra wdrożony i przekazany do użycia przez Ciebie. Ten proces jest tymczasowy, dopóki pełna samoobsługa nie zostanie udostępniona za pośrednictwem witryny Azure portal.

## <a name="best-practices"></a>Najlepsze rozwiązania

### <a name="how-many-partitions-do-i-need"></a>Ile partycji potrzebuję?
Liczba partycji jest określana podczas tworzenia i musi należeć do zakresu od 2 do 32. Liczba partycji nie jest zmieniana, dlatego należy wziąć pod uwagę skalę długoterminową podczas ustawiania liczby partycji. Partycje stanowią mechanizm organizacji danych powiązany z równoległością podrzędną wymaganą w aplikacjach korzystających z tych danych. Liczba partycji w centrum zdarzeń jest bezpośrednio związana z oczekiwaną liczbą jednoczesnych czytników. Aby uzyskać więcej informacji na temat partycji, zobacz [Partycje](event-hubs-features.md#partitions).

Możesz ustawić go jako najwyższą możliwą wartość, która jest 32, w momencie tworzenia. Należy pamiętać, że posiadanie więcej niż jednej partycji spowoduje zdarzenia wysyłane do wielu partycji bez zachowywania kolejności, chyba że skonfigurujesz nadawców tylko wysłać do jednej partycji z 32 pozostawiając pozostałe partycje 31 nadmiarowe. W pierwszym przypadku musisz przeczytać zdarzenia we wszystkich partycjach 32. W tym ostatnim przypadku nie ma oczywistych dodatkowych kosztów oprócz dodatkowej konfiguracji, którą musisz wykonać na hostie procesora zdarzeń.

Centra zdarzeń jest przeznaczony do zezwalania na jeden czytnik partycji na grupę odbiorców. W większości przypadków użycia domyślne ustawienie czterech partycji jest wystarczające. Jeśli chcesz skalować przetwarzanie zdarzeń, warto rozważyć dodanie dodatkowych partycji. Nie ma określonego limitu przepływności na partycji, jednak agregująca przepływność w obszarze nazw jest ograniczona przez liczbę jednostek przepływności. W miarę zwiększania liczby jednostek przepływności w obszarze nazw można zwiększyć liczbę jednostek przepływności w obszarze nazw, można zwiększyć dodatkowe partycje, aby umożliwić równoczesnych czytników do osiągnięcia ich maksymalnej przepływności.

Jednak jeśli masz model, w którym aplikacja ma koligacji do określonej partycji, zwiększenie liczby partycji nie może być żadnych korzyści dla Ciebie. Aby uzyskać więcej informacji, zobacz [dostępność i spójność](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Cennik

### <a name="where-can-i-find-more-pricing-information"></a>Gdzie mogę znaleźć więcej informacji o cenach?

Aby uzyskać pełne informacje na temat cenowania centrów zdarzeń, zobacz [szczegóły cen centrum zdarzeń](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Czy istnieje opłata za zachowanie zdarzeń w centrach zdarzeń przez ponad 24 godziny?

Warstwa Standard usługi Event Hubs zezwala na okresy przechowywania wiadomości dłuższe niż 24 godziny, maksymalnie przez siedem dni. Jeśli rozmiar całkowitej liczby przechowywanych zdarzeń przekracza limit magazynowania dla liczby wybranych jednostek przepływności (84 GB na jednostkę przepływności), rozmiar przekraczający limit jest naliczany według opublikowanej szybkości magazynowania obiektów Blob platformy Azure. Limit magazynowania w każdej jednostce przepływności pokrywa wszystkie koszty magazynowania dla okresów przechowywania 24 godzin (domyślnie), nawet jeśli jednostka przepływności jest używana do maksymalnego limitu transferu danych przychodzących.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Jak obliczany i naliczany jest rozmiar magazynu usługi Event Hubs?

Całkowity rozmiar wszystkich przechowywanych zdarzeń, w tym wszelkie wewnętrzne obciążenie dla nagłówków zdarzeń lub struktur magazynu dysków we wszystkich centrach zdarzeń, jest mierzony przez cały dzień. Na koniec dnia obliczany jest szczytowy rozmiar magazynu. Dzienny limit magazynowania jest obliczany na podstawie minimalnej liczby jednostek przepływności wybranych w ciągu dnia (każda jednostka przepływności zapewnia limit 84 GB). Jeśli całkowity rozmiar przekracza obliczony dzienny limit do magazynowania, nadmiar magazynu jest rozliczany przy użyciu stawek magazynu obiektów Blob platformy Azure (w szybkości **magazynu lokalnie nadmiarowego** magazynu).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Jak obliczane są zdarzenia przychodzące usługi Event Hubs?

Każde zdarzenie wysłane do centrum zdarzeń jest liczone jako wiadomość podlegający rozliczaniu. *Zdarzenie przychodzącego* jest definiowane jako jednostka danych, która jest mniejsza lub równa 64 KB. Każde zdarzenie o rozmiarze mniejszym lub równym 64 KB jest uważane za jedno zdarzenie podlegające rozliczaniu. Jeśli zdarzenie jest większa niż 64 KB, liczba zdarzeń rozliczanych jest obliczana zgodnie z rozmiarem zdarzenia, w wielokrotności 64 KB. Na przykład zdarzenie o masie 8 KB wysyłane do centrum zdarzeń jest rozliczane jako jedno zdarzenie, ale wiadomość o masie 96 KB wysłana do centrum zdarzeń jest rozliczana jako dwa zdarzenia.

Zdarzenia używane z Centrum zdarzeń, a także operacje zarządzania i wywołania kontroli, takie jak punkty kontrolne, nie są liczone jako zdarzenia transferu danych przychodzących, ale są naliczane do limitu jednostki przepływności.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Czy usługi pośrednictwa dotyczą usług Event Hubs?

Opłaty za połączenie mają zastosowanie tylko wtedy, gdy używany jest protokół AMQP. Nie ma żadnych opłat za połączenie za wysyłanie zdarzeń przy użyciu protokołu HTTP, niezależnie od liczby systemów lub urządzeń wysyłających. Jeśli planujesz używać usługi AMQP (na przykład w celu osiągnięcia bardziej efektywnego przesyłania strumieniowego zdarzeń lub włączenia komunikacji dwukierunkowej w scenariuszach poleceń i kontroli IoT), zobacz stronę [informacji o cenach centrum zdarzeń,](https://azure.microsoft.com/pricing/details/event-hubs/) aby uzyskać szczegółowe informacje o liczbie połączeń uwzględnionych w każdej warstwie usług.

### <a name="how-is-event-hubs-capture-billed"></a>Jak rozliczane jest przechwytywanie zdarzeń?

Przechwytywanie jest włączone, gdy w dowolnym centrum zdarzeń w obszarze nazw jest włączona opcja Przechwytywanie. Przechwytywanie centrów zdarzeń jest rozliczane co godzinę za zakupioną jednostkę przepływności. Ponieważ liczba jednostek przepływności jest zwiększana lub zmniejszana, rozliczenia Przechwytywanie zdarzeń odzwierciedlają te zmiany w przyrostach całych godzin. Aby uzyskać więcej informacji na temat rozliczeń przechwytywania centrów zdarzeń, zobacz [Informacje o cenach w Centrach zdarzeń](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Czy otrzymuję naliczone naliczanie na konto magazynu wybranego w uchwyceniu usługi Event Hubs?

Usługa Capture używa konta magazynu, które udostępniasz po włączeniu w centrum zdarzeń. Ponieważ jest to twoje konto magazynu, wszelkie zmiany dla tej konfiguracji są rozliczane do subskrypcji platformy Azure.

## <a name="quotas"></a>Przydziały

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Czy istnieją przydziały skojarzone z centrum zdarzeń?

Aby uzyskać listę wszystkich przydziałów centrum zdarzeń, zobacz [przydziały](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Dlaczego nie mogę utworzyć obszaru nazw po usunięciu jej z innej subskrypcji? 
Po usunięciu obszaru nazw z subskrypcji, poczekaj 4 godziny przed odtworzeniem go o tej samej nazwie w innej subskrypcji. W przeciwnym razie może zostać `Namespace already exists`wyświetlony następujący komunikat o błędzie: . 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Jakie są wyjątki generowane przez centra zdarzeń i ich sugerowane akcje?

Aby uzyskać listę możliwych wyjątków w centrach zdarzeń, zobacz [Omówienie wyjątków](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Usługa Event Hubs obsługuje dwa [typy dzienników diagnostycznych](event-hubs-diagnostic-logs.md) — przechwytywanie dzienników błędów i dzienników operacyjnych — oba są reprezentowane w json i można je włączyć za pośrednictwem witryny Azure portal.

### <a name="support-and-sla"></a>Wsparcie i umowy SLA

Pomoc techniczna dla Centrów zdarzeń jest dostępna za pośrednictwem [forów społecznościowych.](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus) Pomoc techniczna w zakresie rozliczeń i zarządzania subskrypcjami jest świadczona bezpłatnie.

Aby dowiedzieć się więcej o naszej umowie SLA, zobacz stronę [Umowy dotyczące poziomu usług.](https://azure.microsoft.com/support/legal/sla/)

## <a name="next-steps"></a>Następne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie Centrum zdarzeń](event-hubs-create.md)
* [Centra zdarzeń Automatyczne nadmuchiwane](event-hubs-auto-inflate.md)
