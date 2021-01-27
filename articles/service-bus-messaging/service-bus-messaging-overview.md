---
title: Omówienie obsługi komunikatów w usłudze Azure Service Bus | Microsoft Docs
description: Ten artykuł zawiera ogólne omówienie Azure Service Bus w pełni zarządzanego brokera komunikatów integracji przedsiębiorstwa.
ms.topic: overview
ms.date: 11/20/2020
ms.openlocfilehash: 7453e8dd300ad754fb58489f059670af209314ab
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881605"
---
# <a name="what-is-azure-service-bus"></a>Co to jest Azure Service Bus?
Microsoft Azure Service Bus to w pełni zarządzany Broker komunikatów przedsiębiorstwa z kolejkami komunikatów i tematami publikowania/subskrybowania. Service Bus służy do rozdzielania aplikacji i usług od siebie, co zapewnia następujące korzyści:

- Równoważenie obciążenia między konkurującymi pracownikami
- Bezpieczne kierowanie i Transferowanie danych oraz kontrolowanie między granicami usług i aplikacji
- Koordynowanie działań transakcyjnych, które wymagają wysokiego stopnia niezawodności 

## <a name="overview"></a>Omówienie
Dane są przesyłane między różnymi aplikacjami i usługami przy użyciu *komunikatów*. Komunikat jest kontenerem z metadanymi i zawiera dane. Dane mogą być dowolnym rodzajem informacji, w tym dane strukturalne kodowane przy użyciu wspólnych formatów, takich jak następujące: JSON, XML, Apache Avro, zwykły tekst.

Niektóre typowe scenariusze obsługi komunikatów:

* *Obsługa komunikatów*. Przenieś dane biznesowe, takie jak zamówienia sprzedaży lub zakupu, dzienniki lub przesunięcia spisu.
* Rozdzielanie *aplikacji*. Zwiększ niezawodność i skalowalność aplikacji i usług. Producenci i konsumenci nie muszą być w trybie online ani nie mogą być w tym samym czasie dostępne. [Obciążenie jest bilansowane](/azure/architecture/patterns/queue-based-load-leveling) w taki sposób, że skoki ruchu nie overtax usługi. 
* *Równoważenie obciążenia*. Zezwalaj wielu [konkurującym odbiorcom](/azure/architecture/patterns/competing-consumers) na odczytywanie z kolejki w tym samym czasie, przez każdy bezpieczny dostęp do konkretnych komunikatów. 
* *Tematy i subskrypcje*. Włączenie 1:*n* relacji między [wydawcami i subskrybentami](/azure/architecture/patterns/publisher-subscriber), co umożliwia subskrybentom wybranie określonych komunikatów z opublikowanego strumienia komunikatów.
* *Transakcje*. Umożliwia wykonywanie kilku operacji w zakresie transakcji niepodzielnych. Na przykład następujące operacje można wykonać w zakresie transakcji.  

    1. Uzyskaj komunikat z jednej kolejki.
    2. Wyślij wyniki przetwarzania do co najmniej jednej innej kolejki.
    3. Przenieś komunikat wejściowy z oryginalnej kolejki. 
    
    Wyniki stają się widoczne dla odbiorców podrzędnych tylko po powodzeniu, w tym pomyślne rozliczanie komunikatu wejściowego, co pozwala na semantykę przetwarzania tylko raz. Ten model transakcji jest niezawodną podstawą wzorca [transakcji kompensacyjnych](/azure/architecture/patterns/compensating-transaction) w kontekście większego rozwiązania. 
* *Sesje komunikatów*. Zaimplementuj międzyskalowe koordynowanie przepływów pracy i transferów, które wymagają dokładnej kolejności komunikatów lub odroczenia komunikatów.

Jeśli znasz inne brokerów komunikatów, takich jak Apache ActiveMQ, koncepcje Service Bus są podobne do tego, co znasz. Ponieważ Service Bus to oferta typu "platforma jako usługa" (PaaS), kluczowa różnica polega na tym, że nie trzeba martwić się o następujące działania. Platforma Azure zajmuje się tymi potrzebami. 

- Umieszczanie dzienników i Zarządzanie miejscem na dysku
- Obsługa kopii zapasowych
- Zachowywanie poprawek systemów operacyjnych i produktów
- Nie martw się o awarie sprzętu 
- Przechodzenie w tryb failover do komputera rezerwowego

## <a name="compliance-with-standards-and-protocols"></a>Zgodność ze standardami i protokołami

Podstawowy protokół komunikacyjny dla Service Bus to [Advanced Messaging Queueing Protocol (AMQP) 1,0](service-bus-amqp-overview.md), otwarty standard ISO/IEC. Dzięki temu klienci mogą pisać aplikacje, które działają przed Service Bus i lokalnymi brokerami, takimi jak ActiveMQ lub RabbitMQ. [Przewodnik dotyczący protokołu AMQP](service-bus-amqp-protocol-guide.md) zawiera szczegółowe informacje, w przypadku których chcesz skompilować takie streszczenie.

[Service Bus Premium](service-bus-premium-messaging.md) jest w pełni zgodna z interfejsem API usług Java/Dżakarta EE w [języku Java (JMS) 2,0](how-to-use-java-message-service-20.md) . Service Bus Standard obsługuje podzbiór JMS 1,1, który koncentruje się na kolejkach. JMS to wspólne streszczenie dla brokerów komunikatów i integruje się z wieloma aplikacjami i strukturami, w tym ze popularną strukturą sprężyny. Aby przełączać się od innych brokerów do Azure Service Bus, wystarczy ponownie utworzyć topologię kolejek i tematów oraz zmienić zależności dostawcy i konfigurację klienta. Aby zapoznać się z przykładem, zobacz [Przewodnik migracji ActiveMQ](migrate-jms-activemq-to-servicebus.md).

## <a name="concepts-and-terminology"></a>Pojęcia i terminologia 
W tej części omówiono koncepcje i terminologię Service Bus.

### <a name="namespaces"></a>Przestrzenie nazw
Przestrzeń nazw jest kontenerem dla wszystkich składników obsługi komunikatów. W jednej przestrzeni nazw może znajdować się wiele kolejek i tematów, a przestrzenie nazw często pełnią rolę kontenerów aplikacji. 

Przestrzeń nazw można porównać do "serwera" w terminologii innych brokerów, ale koncepcje nie są bezpośrednio równoważne. Przestrzeń nazw Service Bus jest własnym wycinkem pojemności dużego klastra składającym się z dziesiątek wszystkich aktywnych maszyn wirtualnych. Może on opcjonalnie obejmować trzy [strefy dostępności platformy Azure](../availability-zones/az-overview.md). Dzięki temu można uzyskać wszystkie korzyści z dostępności i niezawodności, które umożliwiają uruchamianie brokera komunikatów na ogromną skalę. Nie musisz martwić się o złożone złożoności. Service Bus to komunikat "bezserwerowy".

### <a name="queues"></a>Kolejki
Komunikaty są wysyłane do *kolejek* i odbierane z nich. Kolejki przechowują komunikaty do momentu, gdy aplikacja odbierająca będzie mogła je odbierać i przetwarzać.

![Kolejka](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Komunikaty w kolejkach są uporządkowane i oznaczane jako sygnatura czasowa przybycia. Po zaakceptowaniu przez brokera ten komunikat jest zawsze przechowywany trwale w magazynie ze potrójnym nadmiarowym rozłożeniem w strefach dostępności, jeśli przestrzeń nazw jest włączona. Service Bus nigdy nie pozostawia komunikatów w pamięci lub w magazynie nietrwałym po ich zgłoszeniu do klienta jako zaakceptowane.

Komunikaty są dostarczane w trybie *ściągania* , podczas gdy żąda się tylko dostarczania komunikatów. W przeciwieństwie do modelu sondowania w przypadku niektórych innych kolejek chmurowych operacja ściągania może być długotrwała i wykonana tylko wtedy, gdy wiadomość jest dostępna. 

### <a name="topics"></a>Tematy

Do wysyłania i odbierania komunikatów można również używać *tematów*. Kolejka jest często używana do komunikacji typu punkt-punkt, natomiast tematy są przydatne w scenariuszach publikowania/subskrypcji.

![Temat](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Tematy mogą mieć wiele niezależnych subskrypcji, które dołączają do tematu i w inny sposób działają podobnie jak kolejki po stronie odbiornika. Subskrybent tematu może otrzymywać kopie wszystkich komunikatów wysłanych do tego tematu. Subskrypcje są nazwanymi jednostkami. Subskrypcje są domyślnie trwałe, ale można je skonfigurować do wygaśnięcia, a następnie automatycznie usuwać. Za pośrednictwem interfejsu API JMS, Service Bus Premium umożliwia również tworzenie nietrwałych subskrypcji istniejących na czas trwania połączenia.

Reguły można definiować w ramach subskrypcji. Reguła subskrypcji ma *Filtr* służący do definiowania warunku, który ma być kopiowany do subskrypcji, oraz opcjonalną *akcję* , która może modyfikować metadane komunikatów. Aby uzyskać więcej informacji, zobacz [temat filtry i akcje tematu](topic-filters.md). Ta funkcja jest przydatna w następujących scenariuszach:

- Nie chcesz, aby subskrypcja otrzymywała wszystkie komunikaty wysyłane do tematu.
- Chcesz oznaczyć wiadomości z dodatkowymi metadanymi podczas przekazywania ich przez subskrypcję.

## <a name="advanced-features"></a>Funkcje zaawansowane

Service Bus obejmuje zaawansowane funkcje, które umożliwiają rozwiązywanie bardziej złożonych problemów z obsługą komunikatów. W poniższych sekcjach opisano niektóre z tych funkcji.

### <a name="message-sessions"></a>Sesje komunikatów

Aby utworzyć pierwszą gwarancję (FIFO) w Service Bus, użyj sesji. Sesje komunikatów umożliwiają wyłączną, uporządkowaną obsługę niepowiązanych sekwencji komunikatów. Aby umożliwić obsługę sesji w systemach o wysokiej dostępności, funkcja sesji umożliwia również przechowywanie stanu sesji, co pozwala na bezpieczne przechodzenie między programami obsługi. Aby uzyskać więcej informacji, zobacz [sesje komunikatów: pierwszy w, pierwszy na zewnątrz (FIFO)](message-sessions.md).

### <a name="autoforwarding"></a>Autoprzesyłanie dalej

Funkcja autoprzesyłania dalej łańcuchuje kolejkę lub subskrypcję do innej kolejki lub tematu w tej samej przestrzeni nazw. W przypadku korzystania z tej funkcji Service Bus automatycznie przenosi komunikaty z kolejki lub subskrypcji do docelowej kolejki lub tematu. Wszystkie takie ruchy są wykonywane w sposób niefunkcjonalny. Aby uzyskać więcej informacji, zobacz Tworzenie [łańcucha Service Bus jednostek z funkcją autoprzesyłania dalej](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Kolejka utraconych wiadomości

Wszystkie kolejki Service Bus i subskrypcje tematów mają skojarzoną kolejkę utraconych wiadomości (DLQ). DLQ przechowuje komunikaty, które spełniają następujące kryteria: 

- Nie mogą zostać dostarczone pomyślnie do żadnego odbiornika.
- Przekroczono limit czasu.
- Są one jawnie Sidelined przez aplikację otrzymującą. 

Komunikaty w kolejce utraconych wiadomości są adnotacjami z przyczyną, dlaczego zostały umieszczone w tym miejscu. Kolejka utraconych wiadomości ma specjalny punkt końcowy, ale w przeciwnym razie działa podobnie jak w przypadku każdej kolejki regularnej. Aplikacja lub narzędzie może przeglądać DLQ lub usunąć z niego kolejkę. Możesz również przekazywać je do kolejki utraconych wiadomości. Aby uzyskać więcej informacji, zobacz [omówienie Service Bus kolejki utraconych wiadomości](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Zaplanowane dostarczanie

Można przesłać komunikaty do kolejki lub tematu do opóźnionego przetwarzania, ustawiając czas, kiedy komunikat stanie się dostępny do użycia. Zaplanowanych wiadomości można także anulować. Aby uzyskać więcej informacji, zobacz [zaplanowane wiadomości](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Odraczanie komunikatów

Klient z kolejką lub subskrypcją może odroczyć pobieranie odebranego komunikatu do późniejszego czasu. Komunikat mógł zostać ogłoszony z oczekiwaną kolejnością i klient chce czekać, aż odbierze kolejny komunikat. Komunikaty odroczone pozostają w kolejce lub subskrypcji i muszą zostać ponownie aktywowane jawnie przy użyciu numeru sekwencyjnego przypisanego do usługi. Aby uzyskać więcej informacji, zobacz [odroczenie komunikatu](message-deferral.md).

### <a name="batching"></a>Przetwarzanie wsadowe

Tworzenie wsadowe po stronie klienta umożliwia klientowi kolejki lub tematu gromadzenie zestawu komunikatów i przekazywanie ich razem. Często wykonywane jest zaoszczędzenie przepustowości lub zwiększenie przepływności. Aby uzyskać więcej informacji, zobacz [Przetwarzanie wsadowe po stronie klienta](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Transakcje

Grupy transakcji grupuje dwie lub więcej operacji jednocześnie do *zakresu wykonywania*. Service Bus pozwala grupować operacje na wielu jednostkach obsługi komunikatów w ramach jednej transakcji. Jednostką wiadomości może być kolejka, temat lub subskrypcja. Aby uzyskać więcej informacji, zobacz [Omówienie przetwarzania transakcji Service Bus](service-bus-transactions.md).

### <a name="autodelete-on-idle"></a>Autodelete przy bezczynności

Autodelete w trybie bezczynności umożliwia określenie interwału bezczynności, po upływie którego automatycznie zostanie usunięta subskrypcja kolejki lub tematu. Minimalny czas trwania wynosi 5 minut. Aby uzyskać więcej informacji, zobacz [Właściwość QueueDescription. AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle).

### <a name="duplicate-detection"></a>Wykrywanie duplikatów
Funkcja wykrywania duplikatów umożliwia nadawcy ponowne wysłanie tego samego komunikatu i dla brokera, aby porzucić potencjalne duplikaty. Wykrywanie duplikatów opiera się na śledzeniu `message-id` Właściwości komunikatu, co oznacza, że aplikacja musi mieć ostrożność, aby użyć tej samej wartości przy ponownym wysyłaniu wiadomości, która może być bezpośrednio uzyskana z niektórych kontekstów specyficznych dla aplikacji. Aby uzyskać więcej informacji, zobacz [Wykrywanie duplikatów](duplicate-detection.md).

### <a name="geo-disaster-recovery"></a>Geograficzne odzyskiwanie po awarii

W przypadku przestoju w regionie platformy Azure funkcja odzyskiwania po awarii umożliwia przetwarzanie danych w celu kontynuowania działania w innym regionie lub centrum danych. Funkcja utrzymuje strukturalne dublowanie przestrzeni nazw dostępnej w regionie pomocniczym i umożliwia tożsamość przestrzeni nazw do przełączenia do pomocniczej przestrzeni nazw. Już ogłoszone komunikaty pozostają w poprzedniej głównej przestrzeni nazw do odzyskiwania po podłączeniu odcinka dostępności. Aby uzyskać więcej informacji, zobacz [Azure Service Bus geograficznie z odzyskiwaniem po awarii](service-bus-geo-dr.md).

### <a name="security"></a>Zabezpieczenia

Service Bus obsługuje standardowe protokoły [AMQP 1,0](service-bus-amqp-overview.md) i [http lub REST](/rest/api/servicebus/) oraz ich odpowiednie funkcje zabezpieczeń, w tym zabezpieczenia na poziomie transportu (TLS). Klienci mogą uzyskać dostęp za pomocą [sygnatury dostępu współdzielonego](service-bus-sas.md) lub [Azure Active Directory](service-bus-authentication-and-authorization.md) zabezpieczenia oparte na rolach. 

W celu ochrony przed niechcianym ruchem Service Bus udostępnia [funkcje zabezpieczeń](network-security.md) , takie jak Zapora IP i integracja z sieciami wirtualnymi. 

## <a name="client-libraries"></a>Biblioteki klienta

W pełni obsługiwane Service Bus biblioteki klienckie są dostępne za pośrednictwem zestawu Azure SDK.

- [Azure Service Bus dla platformy .NET](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Biblioteki Azure Service Bus dla języka Java](/java/api/overview/azure/servicebus?preserve-view=true)
- [Dostawca Azure Service Bus dla języka Java JMS 2,0](how-to-use-java-message-service-20.md)
- [Moduły Azure Service Bus dla języków JavaScript i TypeScript](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Biblioteki Azure Service Bus dla języka Python](/python/api/overview/azure/servicebus?preserve-view=true)

[Azure Service Bus "podstawowy protokół to AMQP 1,0](service-bus-amqp-overview.md) i może być używany z dowolnego klienta zgodnego protokołu AMQP 1,0. Kilku klientów AMQP Open Source ma przykłady, które jawnie demonstrują Service Bus współdziałania. Zapoznaj się z [przewodnikiem po protokole AMQP 1,0](service-bus-amqp-protocol-guide.md) , aby dowiedzieć się, jak używać funkcji Service Bus "bezpośrednio z klientami AMQP 1,0.

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="integration"></a>Integracja

Service Bus w pełni integrują się z wieloma usługami firmy Microsoft i platformy Azure, na przykład:

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Power Platform](https://powerplatform.microsoft.com/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z obsługi komunikatów usługi Service Bus, zobacz następujące artykuły:

* Aby porównać usługi Azure Messaging, zobacz [porównanie usług](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* Wypróbuj Przewodniki Szybki Start dla [platformy .NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md)lub [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Aby zarządzać zasobami Service Bus, zobacz [eksplorator Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Aby dowiedzieć się więcej o warstwach Standardowa i Premium oraz ich cenach, zobacz [Service Bus Cennik](https://azure.microsoft.com/pricing/details/service-bus/).
* Aby dowiedzieć się więcej o wydajności i opóźnieniu dla warstwy Premium, zobacz [Obsługa komunikatów](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722)w warstwie Premium.