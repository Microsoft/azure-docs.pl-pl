---
title: Omówienie funkcji — Azure Event Hubs | Microsoft Docs
description: Ten artykuł zawiera szczegółowe informacje o funkcjach i terminologii Event Hubs platformy Azure.
ms.topic: article
ms.date: 02/19/2021
ms.openlocfilehash: 8bb63bfdbeb5b875b1e461fbd93fb48dcbb43054
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739079"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Funkcje i terminologia w usłudze Azure Event Hubs

Azure Event Hubs to skalowalna usługa przetwarzania zdarzeń, która pobiera i przetwarza duże ilości zdarzeń i danych, z małymi opóźnieniami i wysoką niezawodnością. Zobacz [co to jest Event Hubs?](./event-hubs-about.md) , aby zapoznać się z ogólnym omówieniem.

W tym artykule opisano informacje zawarte w [artykule Omówienie](./event-hubs-about.md)i przedstawiono techniczne i szczegółowe informacje dotyczące Event Hubs składników i funkcji.

> [!TIP]
> [Obsługa protokołu dla klientów **Apache Kafka**](event-hubs-for-kafka-ecosystem-overview.md)  (wersje >= 1,0) zawiera punkty końcowe sieci, które umożliwiają aplikacjom skompilowanym przez aplikacje używanie Apache Kafka z dowolnym klientem do korzystania z Event Hubs. Większość istniejących aplikacji Kafka można po prostu zmienić konfigurację, aby wskazywały na przestrzeń nazw centrum zdarzeń, a nie serwer ładowania początkowego klastra Kafka. 
>
>Z punktu widzenia kosztów, nakładu pracy i niezawodności usługa Azure Event Hubs to świetna alternatywa dla wdrażania i obsługi własnych klastrów Kafka i dozorcy oraz ofert Kafka-as-Service, które nie są natywne dla platformy Azure. 
>
> Oprócz uzyskania tych samych podstawowych funkcji programu Apache Kafka brokera można także uzyskać dostęp do funkcji centrum zdarzeń platformy Azure, takich jak automatyczne tworzenie i archiwizowanie przy użyciu [Event Hubs przechwytywania](event-hubs-capture-overview.md), automatycznego skalowania i równoważenia, odzyskiwania po awarii, obsługi stref dostępności neutralnych kosztów, elastycznej i zabezpieczonej integracji sieci oraz obsługi wieloprotokołowej, w tym protokołu AMQP-over-WebSockets (przyjazny dla zapory).


## <a name="namespace"></a>Przestrzeń nazw
Przestrzeń nazw Event Hubs zawiera zintegrowane punkty końcowe sieci DNS oraz zakres funkcji kontroli dostępu i zarządzania integracją sieci, takich jak [filtrowanie adresów IP](event-hubs-ip-filtering.md), [punkt końcowy usługi sieci wirtualnej](event-hubs-service-endpoints.md)i [link prywatny](private-link-service.md) , a także jest kontenerem zarządzania dla jednego z wielu wystąpień centrów zdarzeń (lub tematów w Kafka sprzężeniem).

## <a name="event-publishers"></a>Wydawcy zdarzeń

Każda jednostka, która wysyła dane do centrum zdarzeń, jest *wydawcą zdarzeń* (z synonimem używanym przez *producenta zdarzeń*). Wydawcy zdarzeń mogą publikować zdarzenia przy użyciu protokołów HTTPS lub AMQP 1,0 lub protokołu Kafka. Wydawcy zdarzeń używają Azure Active Directory autoryzacja przy użyciu tokenów JWT wystawionych przez OAuth2 lub token sygnatury dostępu współdzielonego (SAS) określony dla centrum zdarzeń.

### <a name="publishing-an-event"></a>Publikowanie zdarzenia

Można opublikować zdarzenie za pośrednictwem AMQP 1,0, protokołu Kafka lub HTTPS. Usługa Event Hubs zapewnia interfejsy [API REST](/rest/api/eventhub/) i [.NET](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [JavaScript](event-hubs-node-get-started-send.md)i [go](event-hubs-go-get-started-send.md) w celu publikowania zdarzeń w centrum zdarzeń. W przypadku innych środowisk uruchomieniowych i platform można używać dowolnego klienta protokołu AMQP 1.0, na przykład [Apache Qpid](https://qpid.apache.org/). 

Decyzja o korzystaniu z protokołu AMQP lub HTTPS jest specyficzna dla scenariusza użycia. Protokół AMQP wymaga ustanowienia trwałego gniazda dwukierunkowego oprócz protokołu TLS lub SSL/ TLS. AMQP dysponuje wyższymi kosztami sieci podczas inicjowania sesji, jednak protokół HTTPS wymaga dodatkowych obciążeń protokołu TLS dla każdego żądania. AMQP ma znacznie wyższą wydajność dla często wydawców i może osiągać znacznie mniejsze opóźnienia, gdy są używane z asynchronicznym kodem publikacji.

Możesz publikować zdarzenia pojedynczo lub zbiorczo. Jedna publikacja ma limit 1 MB, niezależnie od tego, czy jest to pojedyncze zdarzenie, czy partia. Publikowanie zdarzeń większych niż ten próg zostanie odrzucone. 

Event Hubs przepływność jest skalowana przy użyciu partycji i alokacji jednostek przepływności (patrz poniżej). Najlepszym rozwiązaniem dla wydawców jest pozostawanie nieznanego modelu partycjonowania wybranego dla centrum zdarzeń i określenie tylko *klucza partycji* , który jest używany do spójnego przypisywania powiązanych zdarzeń do tej samej partycji.

![Klucze partycji](./media/event-hubs-features/partition_keys.png)

Event Hubs zapewnia, że wszystkie zdarzenia współużytkujące wartość klucza partycji są przechowywane razem i dostarczane w kolejności przybycia. Jeśli klucze partycji są używane wraz z zasadami wydawcy, to tożsamość wydawcy i wartość klucza partycji muszą być zgodne. W przeciwnym razie wystąpi błąd.

### <a name="event-retention"></a>Przechowywanie zdarzeń

Opublikowane zdarzenia są usuwane z centrum zdarzeń na podstawie konfigurowalnych zasad przechowywania opartych na czasie. Oto kilka istotnych kwestii:

- Wartość **Domyślna** i **najkrótszy** możliwy okres przechowywania to **1 dzień (24 godziny)**.
- W przypadku Event Hubs **Standard** maksymalny okres przechowywania wynosi **7 dni**. 
- W przypadku Event Hubs **dedykowany** maksymalny okres przechowywania to **90 dni**.
- Jeśli zmienisz okres przechowywania, dotyczy to wszystkich komunikatów, w tym komunikatów, które znajdują się już w centrum zdarzeń. 

> [!NOTE]
> Event Hubs to aparat strumienia zdarzeń w czasie rzeczywistym i nie jest przeznaczony do użycia zamiast bazy danych i/lub jako trwały magazyn dla nieskończonie przechowywanych strumieni zdarzeń. 
> 
> Im większa jest historia strumienia zdarzeń, tym bardziej potrzebne będą indeksy pomocnicze w celu znalezienia określonego wycinka historycznego danego strumienia. Inspekcja ładunków zdarzeń i indeksowania nie znajduje się w zakresie funkcji Event Hubs (lub Apache Kafka). Bazy danych i wyspecjalizowane sklepy analityczne, takie jak [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md), [Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) i [Azure Synapse](../synapse-analytics/overview-what-is.md) , są w związku z tym znacznie lepiej dopasowane do przechowywania wydarzeń historycznych.
>
> [Przechwytywanie Event Hubs](event-hubs-capture-overview.md) jest integrowane bezpośrednio z usługą Azure Blob Storage i Azure Data Lake Storage i za pomocą tej integracji umożliwia również [przepływ zdarzeń bezpośrednio do usługi Azure Synapse](store-captured-data-data-warehouse.md).
>
> Jeśli chcesz użyć wzorca określania [źródła zdarzeń](/azure/architecture/patterns/event-sourcing) dla aplikacji, należy wyrównać strategię migawki z limitami przechowywania Event Hubs. Nie należy dążyć do odbudowywania widoków z nieprzetworzonych zdarzeń, zaczynając od początku czasu. Należy pamiętać, aby zamienić tę strategię, gdy aplikacja jest w trakcie produkcji przez pewien czas i jest dobrze użyta, a Twój Konstruktor projekcji ma być zmieniany przez lata zdarzeń zmiany podczas próby przechwycenia do najnowszych i bieżących zmian. 


### <a name="publisher-policy"></a>Zasady wydawcy

Usługa Event Hubs umożliwia szczegółową kontrolę nad wydawcami zdarzeń za pomocą *zasad wydawcy*. Zasady wydawcy to funkcje środowiska uruchomieniowego zaprojektowane w celu ułatwienia działania dużej liczby niezależnych wydawców zdarzeń. Dzięki zasadom wydawcy każdy wydawca używa swojego unikatowego identyfikatora podczas publikowania zdarzeń w centrum zdarzeń przy użyciu następującego mechanizmu:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>
```

Nie jest konieczne wcześniejsze tworzenie nazw wydawców, ale muszą one być zgodne z tokenem sygnatury dostępu współdzielonego użytym podczas publikowania zdarzenia w celu zapewnienia niezależnych tożsamości wydawcy. Podczas używania zasad wydawcy wartość **PartitionKey** jest ustawiana na nazwę wydawcy. Aby zapewnić prawidłowe działanie, te wartości muszą być zgodne.

## <a name="capture"></a>Przechwytywanie

[Przechwytywanie Event Hubs](event-hubs-capture-overview.md) umożliwia automatyczne przechwytywanie danych przesyłanych strumieniowo w Event Hubs i zapisywanie ich w wybranym przez siebie koncie usługi BLOB Storage lub koncie usług Azure Data Lake. Możesz włączyć Przechwytywanie z Azure Portal i określić minimalny rozmiar i przedział czasu, aby przechwycić. Za pomocą funkcji przechwytywania Event Hubs należy określić własne konto i kontener platformy Azure Blob Storage lub Azure Data Lake konto usługi, z którego korzysta się do przechowywania przechwyconych danych. Przechwycone dane są zapisywane w formacie Apache Avro.

## <a name="partitions"></a>Partycje
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>Tokeny sygnatur dostępu współdzielonego

Usługa Event Hubs używa *sygnatur dostępu współdzielonego*, które są dostępne na poziomie przestrzeni nazw i centrum zdarzeń. Token sygnatury dostępu współdzielonego jest generowany na podstawie klucza sygnatury dostępu współdzielonego i jest skrótem SHA adresu URL zakodowanym w określonym formacie. Przy użyciu nazwy klucza (zasady) i tokenu usługa Event Hubs może ponownie wygenerować skrót i w ten sposób uwierzytelnić nadawcę. Zwykle tokeny sygnatury dostępu współdzielonego dla wydawców zdarzeń są tworzone jedynie z uprawnieniami do **wysyłania** w określonym centrum zdarzeń. Ten mechanizm adresu URL tokenu sygnatury dostępu współdzielonego stanowi podstawę do identyfikacji wydawcy wprowadzoną w ramach zasad wydawcy. Aby uzyskać więcej informacji na temat pracy z sygnaturą dostępu współdzielonego, zobacz [Shared Access Signature Authentication with Service Bus](../service-bus-messaging/service-bus-sas.md) (Uwierzytelnianie za pomocą sygnatury dostępu współdzielonego przy użyciu usługi Service Bus).

## <a name="event-consumers"></a>Odbiorcy zdarzeń

Każda jednostka, która odczytuje dane zdarzenia z centrum zdarzeń, jest *odbiorcą zdarzenia*. Wszyscy odbiorcy usługi Event Hubs nawiązują połączenie za pomocą sesji protokołu AMQP 1.0, w ramach której zdarzenia są dostarczane, gdy tylko staną się dostępne. Klient nie musi sondować dostępności danych.

### <a name="consumer-groups"></a>Grupy odbiorców

Mechanizm publikowania/subskrypcji usługi Event Hubs jest włączany za pomocą *grup odbiorców*. Grupa odbiorców stanowi widok (stan, pozycja lub przesunięcie) całego centrum zdarzeń. Dzięki grupom odbiorców wiele aplikacji odbiorczych może mieć osobny widok strumienia zdarzeń i niezależnie odczytywać strumień we własnym tempie i przy użyciu własnego przesunięcia.

W architekturze przetwarzania strumieni każda aplikacja podrzędna odpowiada grupie odbiorców. Jeśli chcesz zapisać dane zdarzenia do magazynu długoterminowego, to ta aplikacja edytora magazynu odpowiada grupie odbiorców. Przetwarzanie złożonych zdarzeń może być wtedy wykonywane przez inną, oddzielną grupę odbiorców. Dostęp do partycji można uzyskać tylko za pośrednictwem grupy odbiorców. W centrum zdarzeń zawsze istnieje domyślna grupa odbiorców, a w przypadku centrum zdarzeń warstwy Standardowa można utworzyć maksymalnie 20 grup odbiorców.

Na partycji dla każdej grupy odbiorców może znajdować się maksymalnie 5 współbieżnych czytników. jednak **zaleca się, aby tylko jeden aktywny odbiornik został skonfigurowany na partycji dla każdej grupy odbiorców**. W ramach jednej partycji każdy czytelnik otrzymuje wszystkie komunikaty. Jeśli masz wielu czytników na tej samej partycji, przetwórz zduplikowane komunikaty. Musisz obsłużyć to w kodzie, który nie może być prosty. Jest to jednak prawidłowe podejście w niektórych scenariuszach.

Niektórzy klienci oferowani przez zestawy SDK platformy Azure są inteligentnymi agentami konsumenckimi, którzy automatycznie zarządzają szczegółami, aby zapewnić, że każda partycja ma jeden czytnik i że są odczytywane wszystkie partycje centrum zdarzeń. Dzięki temu kod może skupić się na przetwarzaniu zdarzeń odczytywanych z centrum zdarzeń, aby można było zignorować wiele szczegółów partycji. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z partycją](#connect-to-a-partition).

W poniższych przykładach przedstawiono Konwencję identyfikatora URI grupy odbiorców:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>
```

Na poniższym rysunku przedstawiono architekturę przetwarzania strumienia usługi Event Hubs:

![Architektura Event Hubs](./media/event-hubs-about/event_hubs_architecture.svg)

### <a name="stream-offsets"></a>Przesunięcia strumienia

*Przesunięcie* to pozycja zdarzenia w partycji. Przesunięcie można traktować jako kursor po stronie klienta. Przesunięcie to numer bajtu zdarzenia. To przesunięcie umożliwi odbiorcy zdarzeń (czytnikowi) określenie punktu w strumieniu zdarzeń, od którego ma zostać rozpoczęte odczytywanie zdarzeń. Przesunięcie można określić jako sygnaturę czasową lub wartość przesunięcia. Odbiorcy są zobowiązani do przechowywania własnych wartości przesunięcia poza usługą Event Hubs. W ramach partycji każde zdarzenie zawiera przesunięcie.

![Przesunięcie partycji](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Tworzenie punktów kontrolnych

*Tworzenie punktów kontrolnych* jest procesem, za pomocą którego czytniki oznaczają lub zatwierdzają swoją pozycję w sekwencji zdarzeń partycji. Odpowiedzialność za tworzenie punktów kontrolnych spoczywa na odbiorcy i odbywa się dla każdej partycji w ramach grupy odbiorców. Ta odpowiedzialność oznacza, że dla każdej grupy odbiorców każdy czytnik partycji musi śledzić swoją bieżącą pozycję w strumieniu zdarzeń i może poinformować usługi, gdy uzna, że strumień danych jest pełny.

Jeśli czytnik rozłączy się od partycji, po swoim ponownym połączeniu rozpoczyna odczyt punktu kontrolnego, który został wcześniej przesłany przez ostatni czytnik tej partycji w danej grupie odbiorców. Po nawiązaniu połączenia z czytnikiem przekazuje przesunięcie do centrum zdarzeń, aby określić lokalizację, w której ma zostać rozpoczęte odczytywanie. W ten sposób można użyć procesu tworzenia punktów kontrolnych zarówno do oznaczenia zdarzeń jako „ukończone” przez aplikacje podrzędne, jak i zapewnienia odporności zdarzenia na pracę w trybie failover między czytnikami działającymi na różnych komputerach. Istnieje możliwość powrotu do starszych danych przez określenie niższego przesunięcia od tego procesu tworzenia punktów kontrolnych. Dzięki temu mechanizmowi tworzenie punktów kontrolnych zapewnia zarówno odporność na pracę w trybie failover, jak i powtarzanie strumienia zdarzeń.

> [!IMPORTANT]
> Przesunięcia są udostępniane przez usługę Event Hubs. Odbiorca jest odpowiedzialny za tworzenie punktów kontrolnych podczas przetwarzania zdarzeń.

> [!NOTE]
> Jeśli używasz platformy Azure Blob Storage jako magazynu punktów kontrolnych w środowisku obsługującym inną wersję zestawu SDK magazynu obiektów BLOB niż te, które są zwykle dostępne na platformie Azure, musisz użyć kodu, aby zmienić wersję interfejsu API usługi magazynu na określoną wersję obsługiwaną przez to środowisko. Na przykład jeśli używasz [Event Hubs w centrum Azure Stack w wersji 2002](/azure-stack/user/event-hubs-overview), najwyższa dostępna wersja usługi Storage to wersja 2017-11-09. W takim przypadku należy użyć kodu, aby docelowa wersja interfejsu API usługi Storage do 2017-11-09. Aby zapoznać się z przykładem dotyczącym konkretnej wersji interfejsu API usługi Storage, zobacz następujące przykłady w witrynie GitHub: 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) lub  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

### <a name="common-consumer-tasks"></a>Typowe zadania odbiorców

Wszyscy klienci Event Hubs nawiązują połączenie za pośrednictwem sesji AMQP 1,0, dwukierunkowego kanału komunikacyjnego z obsługą stanu. Każda partycja zawiera sesję protokołu AMQP 1.0, która ułatwia transport zdarzeń posegregowanych według partycji.

#### <a name="connect-to-a-partition"></a>Nawiązywanie połączenia z partycją

W przypadku nawiązywania połączenia z partycjami często używany jest mechanizm dzierżawienia w celu koordynowania połączeń czytnika z określonymi partycjami. W ten sposób można dla każdej partycji w grupie odbiorców mieć tylko jeden aktywny czytnik. Tworzenie punktów kontrolnych, dzierżawienie i zarządzanie czytnikami są uproszczone przy użyciu klientów w ramach zestawów SDK Event Hubs, które pełnią rolę inteligentnych agentów konsumenckich. Są to:

- [EventProcessorClient](/dotnet/api/azure.messaging.eventhubs.eventprocessorclient) dla platformy .NET
- [EventProcessorClient](/java/api/com.azure.messaging.eventhubs.eventprocessorclient) dla języka Java
- [EventHubConsumerClient](/python/api/azure-eventhub/azure.eventhub.aio.eventhubconsumerclient) dla języka Python
- [EventHubConsumerClient](/javascript/api/@azure/event-hubs/eventhubconsumerclient) dla języka JavaScript/TypeScript

#### <a name="read-events"></a>Zdarzenia odczytywania

Po otwarciu sesji i linku protokołu AMQP 1.0 dla określonej partycji zdarzenia są dostarczane do klienta protokołu AMQP 1.0 przez usługę Event Hubs. Ten mechanizm dostarczania zapewnia wyższą przepływność i mniejsze opóźnienia niż mechanizmy oparte na ściąganiu, na przykład HTTP GET. Podczas wysyłania zdarzeń do klienta każde wystąpienie danych zdarzenia zawiera ważne metadane, takie jak przesunięcie i numer sekwencji, które są używane do ułatwienia tworzenia punktów kontrolnych sekwencji zdarzeń.

Dane zdarzenia:
* Przesunięcie
* Numer sekwencyjny
* Treść
* Właściwości użytkownika
* Właściwości systemu

Zarządzanie przesunięciem jest Twoim obowiązkiem.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

- Rozpoczynanie pracy z usługą Event Hubs
    - [.NET](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Przewodnik programowania Event Hubs](event-hubs-programming-guide.md)
* [Availability and consistency in Event Hubs](event-hubs-availability-and-consistency.md) (Dostępność i spójność w usłudze Event Hubs)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykłady Event Hubs](event-hubs-samples.md)