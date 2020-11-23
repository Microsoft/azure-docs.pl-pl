---
title: Omówienie obsługi komunikatów w usłudze Azure Service Bus | Microsoft Docs
description: Ten artykuł zawiera ogólne omówienie Azure Service Bus, a w pełni zarządzanego brokera komunikatów integracji przedsiębiorstwa.
ms.topic: overview
ms.date: 11/20/2020
ms.openlocfilehash: febb25474f84819b0afc9ab1f9af96e93489ab54
ms.sourcegitcommit: 1d366d72357db47feaea20c54004dc4467391364
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95415290"
---
# <a name="what-is-azure-service-bus"></a>Co to jest Azure Service Bus?

Usługa Microsoft Azure Service Bus jest w pełni zarządzanym brokerem komunikatów do integracji przedsiębiorstw. Service Bus może oddzielić aplikacje i usługi. Service Bus oferuje niezawodną i bezpieczną platformę do asynchronicznego transferu danych i stanu.

Dane są przesyłane między różnymi aplikacjami i usługami przy użyciu *komunikatów*. Komunikat jest w formacie binarnym i może zawierać kod JSON, XML lub tylko tekst. Aby uzyskać więcej informacji, zobacz [Integration Services](https://azure.com/integration).

Niektóre typowe scenariusze obsługi komunikatów:

* *Obsługa komunikatów*. Przenieś dane biznesowe, takie jak zamówienia sprzedaży lub zakupu, dzienniki lub przesunięcia spisu.
* Rozdzielanie *aplikacji*. Zwiększ niezawodność i skalowalność aplikacji i usług. Klient i usługa nie muszą być w trybie online w tym samym czasie.
* *Tematy i subskrypcje*. Włącz 1:*n* relacji między wydawcami a subskrybentami.
* *Sesje komunikatów*. Implementuj przepływy pracy, które wymagają porządkowania komunikatów lub odroczenia komunikatów.

## <a name="namespaces"></a>Namespaces

Przestrzeń nazw jest kontenerem dla wszystkich składników obsługi komunikatów. W jednej przestrzeni nazw może znajdować się wiele kolejek i tematów, a przestrzenie nazw często pełnią rolę kontenerów aplikacji.

## <a name="queues"></a>Kolejki

Komunikaty są wysyłane do *kolejek* i odbierane z nich. Kolejki przechowują komunikaty do momentu, gdy aplikacja odbierająca będzie mogła je odbierać i przetwarzać.

![Kolejka](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Komunikaty w kolejkach są uporządkowane i oznaczane jako sygnatura czasowa przybycia. Po zaakceptowaniu komunikat jest bezpiecznie przechowywany w magazynie nadmiarowym. Komunikaty są dostarczane w trybie *ściągania* , podczas gdy żąda się tylko dostarczania komunikatów.

## <a name="topics"></a>Tematy

Do wysyłania i odbierania komunikatów można również używać *tematów*. Kolejka jest często używana do komunikacji typu punkt-punkt, natomiast tematy są przydatne w scenariuszach publikowania/subskrypcji.

![Temat](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Tematy mogą mieć wiele niezależnych subskrypcji. Subskrybent tematu może otrzymywać kopie wszystkich komunikatów wysłanych do tego tematu. Subskrypcje są nazwanymi jednostkami. Subskrypcje są utrwalane, ale mogą wygasnąć lub usuwać.

Użytkownik może nie chcieć, aby poszczególne subskrypcje odbierali wszystkie komunikaty wysyłane do tematu. W takim przypadku można użyć *zasad* i *filtrów* do definiowania warunków wyzwalających *Akcje* opcjonalne. Można filtrować określone komunikaty i ustawiać lub modyfikować właściwości komunikatów. Aby uzyskać więcej informacji, zobacz [temat filtry i akcje tematu](topic-filters.md).

## <a name="advanced-features"></a>Funkcje zaawansowane

Service Bus obejmuje zaawansowane funkcje, które umożliwiają rozwiązywanie bardziej złożonych problemów z obsługą komunikatów. W poniższych sekcjach opisano niektóre z tych funkcji.

### <a name="message-sessions"></a>Sesje komunikatów

Aby utworzyć pierwszą gwarancję (FIFO) w Service Bus, użyj sesji. Sesje komunikatów umożliwiają wspólną i uporządkowaną obsługę niepowiązanych sekwencji powiązanych komunikatów. Aby uzyskać więcej informacji, zobacz [sesje komunikatów: pierwszy w, pierwszy na zewnątrz (FIFO)](message-sessions.md).

### <a name="autoforwarding"></a>Autoprzesyłanie dalej

Funkcja autoprzesyłania dalej łańcuchuje kolejkę lub subskrypcję do innej kolejki lub tematu. Muszą one być częścią tego samego obszaru nazw. W przypadku automatycznego przesyłania dalej Service Bus automatycznie usuwa komunikaty z kolejki lub subskrypcji i umieszcza je w innej kolejce lub temacie. Aby uzyskać więcej informacji, zobacz Tworzenie [łańcucha Service Bus jednostek z funkcją autoprzesyłania dalej](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Kolejka utraconych wiadomości

Service Bus obsługuje kolejkę utraconych wiadomości (DLQ). DLQ przechowuje komunikaty, których nie można dostarczyć do żadnego odbiornika. Przechowuje komunikaty, których nie można przetworzyć. Service Bus umożliwia usuwanie komunikatów z DLQ i sprawdzanie ich. Aby uzyskać więcej informacji, zobacz [omówienie Service Bus kolejki utraconych wiadomości](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Zaplanowane dostarczanie

Można przesłać komunikaty do kolejki lub tematu w celu opóźnienia przetwarzania. Można zaplanować, aby zadanie stało się dostępne do przetwarzania przez system w określonym czasie. Aby uzyskać więcej informacji, zobacz [zaplanowane wiadomości](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Odraczanie komunikatów

Klient z kolejką lub subskrypcją może odroczyć pobieranie komunikatu do późniejszego czasu. To odroczenie może być spowodowane szczególnymi okolicznościami aplikacji. Komunikat pozostaje w kolejce lub subskrypcji, ale jest ustawiony. Aby uzyskać więcej informacji, zobacz [odroczenie komunikatu](message-deferral.md).

### <a name="batching"></a>Przetwarzanie wsadowe

Dzielenie na partie po stronie klienta umożliwia klientowi kolejki lub tematu opóźnienie wysłania komunikatu na pewien czas. Jeśli klient wysyła dodatkowe komunikaty w tym okresie, przesyła komunikaty w jednej partii. Aby uzyskać więcej informacji, zobacz [Przetwarzanie wsadowe po stronie klienta](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Transakcje

Grupy transakcji grupuje dwie lub więcej operacji jednocześnie do *zakresu wykonywania*. Service Bus obsługuje operacje grupowania w ramach pojedynczej jednostki obsługi komunikatów w ramach jednej transakcji. Jednostką wiadomości może być kolejka, temat lub subskrypcja. Aby uzyskać więcej informacji, zobacz [Omówienie przetwarzania transakcji Service Bus](service-bus-transactions.md).

### <a name="filtering-and-actions"></a>Filtrowanie i akcje

Subskrybenci mogą zdefiniować, które komunikaty chcą odbierać z tematu. Komunikaty te są określone w formie co najmniej jednej nazwanej reguły subskrypcji. Dla każdego warunku reguły dopasowywania subskrypcja tworzy kopię wiadomości, która może być inaczej oznaczona adnotacją dla każdej reguły dopasowywania. Aby uzyskać więcej informacji, zobacz [temat filtry i akcje tematu](topic-filters.md).

### <a name="autodelete-on-idle"></a>Autodelete przy bezczynności

Autodelete przy bezczynności umożliwia określenie interwału bezczynności, po upływie którego Kolejka zostanie automatycznie usunięta. Minimalny czas trwania wynosi 5 minut. Aby uzyskać więcej informacji, zobacz [Właściwość QueueDescription. AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle).

### <a name="duplicate-detection"></a>Wykrywanie duplikatów

Błąd może spowodować, że klient ma wątpliwości dotyczące wyniku operacji wysyłania. Wykrywanie duplikatów umożliwia nadawcy ponowne wysłanie tego samego komunikatu. Inna opcja dotyczy kolejki lub tematu, aby odrzucić wszystkie zduplikowane kopie. Aby uzyskać więcej informacji, zobacz [Wykrywanie duplikatów](duplicate-detection.md).

### <a name="geo-disaster-recovery"></a>Geograficzne odzyskiwanie po awarii

Gdy w regionach platformy Azure lub centrach danych wystąpi przestój, funkcja rozproszonego odzyskiwania po awarii w trybie geograficznym umożliwia przetwarzanie danych w celu kontynuowania działania w innym regionie lub Datacenter. Aby uzyskać więcej informacji, zobacz [Azure Service Bus geograficznie z odzyskiwaniem po awarii](service-bus-geo-dr.md).

### <a name="security"></a>Zabezpieczenia

Service Bus obsługuje standardowe protokoły [AMQP 1,0](service-bus-amqp-overview.md) i [http/REST](/rest/api/servicebus/) oraz ich odpowiednie funkcje zabezpieczeń, w tym zabezpieczenia na poziomie transportu (TLS). Klienci mogą mieć autoryzację dostępu przy użyciu Service Bus natywnego modelu [sygnatury dostępu współdzielonego](service-bus-sas.md) lub [Azure Active Directory](service-bus-authentication-and-authorization.md) zabezpieczeń opartych na rolach, używając zwykłych kont usług lub tożsamości zarządzanych platformy Azure. 

W celu ochrony przed niechcianym ruchem Service Bus zapewnia szereg [funkcji zabezpieczeń sieci](network-security.md), w tym zapory filtrowania IP i integrację z platformą Azure i lokalnymi sieciami wirtualnymi.

## <a name="client-libraries"></a>Biblioteki klienta

W pełni obsługiwane Service Bus biblioteki klienckie są dostępne za pośrednictwem zestawu Azure SDK.

- [Azure Service Bus dla platformy .NET](https://docs.microsoft.com/dotnet/api/overview/azure/service-bus?view=azure-dotnet&preserve-view=true)
- [Biblioteki Azure Service Bus dla języka Java](https://docs.microsoft.com/java/api/overview/azure/servicebus?view=azure-java-stable&preserve-view=true)
- [Dostawca Azure Service Bus dla języka Java JMS 2,0](how-to-use-java-message-service-20.md)
- [Moduły Azure Service Bus dla języków JavaScript i TypeScript](https://docs.microsoft.com/javascript/api/overview/azure/service-bus?view=azure-node-latest&preserve-view=true)
- [Biblioteki Azure Service Bus dla języka Python](https://docs.microsoft.com/python/api/overview/azure/servicebus?view=azure-python&preserve-view=true)

[Azure Service Bus "podstawowy protokół to AMQP 1,0](service-bus-amqp-overview.md) i może być używany z dowolnego klienta zgodnego protokołu AMQP 1,0. Kilku klientów AMQP Open Source ma przykłady, które jawnie demonstrują Service Bus współdziałania. Zapoznaj się z [przewodnikiem po protokole AMQP 1,0](service-bus-amqp-protocol-guide.md) , aby dowiedzieć się, jak używać funkcji Service Bus "bezpośrednio z klientami AMQP 1,0.

| Język | Biblioteka |
| --- | --- |
| Java | [Apache Qpid Proton-J](https://qpid.apache.org/proton/index.html) |
| C/C++ |[Azure UAMQP C](https://github.com/azure/azure-uamqp-c/), [Apache Qpid Proton-C](https://qpid.apache.org/proton/index.html) |
| Python |[Azure uAMQP for Python](https://github.com/azure/azure-uamqp-python/), [Apache Qpid Proton Python](https://qpid.apache.org/releases/qpid-proton-0.32.0/proton/python/docs/overview.html) |
| PHP | [Azure uAMQP dla języka PHP](https://github.com/vsouz4/azure-uamqp-php/) |
| Ruby | [Apache Qpid Proton Ruby](https://github.com/apache/qpid-proton/tree/master/ruby) |
| Przejdź | [Azure go AMQP](https://github.com/Azure/go-amqp), [Apache Qpid Proton go](https://github.com/apache/qpid-proton/tree/master/go/examples)
| C#/F #/VB | [AMQP .NET Lite](https://github.com/Azure/amqpnetlite), [Apache NMS AMQP](https://github.com/apache/activemq-nms-amqp)|
| JavaScript/węzeł | [Rhea](https://github.com/grs/rhea) |

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
