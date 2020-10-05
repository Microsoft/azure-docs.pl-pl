---
title: Omówienie obsługi komunikatów w usłudze Azure Service Bus | Microsoft Docs
description: Ten artykuł zawiera ogólne omówienie Azure Service Bus, a w pełni zarządzanego brokera komunikatów integracji przedsiębiorstwa.
ms.topic: overview
ms.date: 06/23/2020
ms.openlocfilehash: 478dd0debb5117e76cf8d0ab6599dcf363c12ab3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87501478"
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

Użytkownik może nie chcieć, aby poszczególne subskrypcje odbierali wszystkie komunikaty wysyłane do tematu. W takim przypadku można użyć *zasad* i *filtrów* do definiowania warunków wyzwalających *Akcje*opcjonalne. Można filtrować określone komunikaty i ustawiać lub modyfikować właściwości komunikatów. Aby uzyskać więcej informacji, zobacz [temat filtry i akcje tematu](topic-filters.md).

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

### <a name="security-protocols"></a>Protokoły zabezpieczeń
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

Service Bus obsługuje protokoły zabezpieczeń, takie jak [sygnatury dostępu współdzielonego](service-bus-sas.md) (SAS), [Kontrola dostępu oparta na rolach na platformie Azure (RBAC)](authenticate-application.md) i [zarządzane tożsamości dla zasobów platformy Azure](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Geograficzne odzyskiwanie po awarii

Jeśli w regionach lub centrach danych Azure dojdzie do przestoju, geograficzne odzyskiwanie po awarii umożliwia kontynuowanie przetwarzania danych w innym regionie lub centrum danych. Aby uzyskać więcej informacji, zobacz [Azure Service Bus geograficznie z odzyskiwaniem po awarii](service-bus-geo-dr.md).

### <a name="security"></a>Zabezpieczenia

Usługa Service Bus obsługuje standardowe protokoły [AMQP 1.0](service-bus-amqp-overview.md) i [HTTP/REST](/rest/api/servicebus/).

## <a name="client-libraries"></a>Biblioteki klienta

Service Bus obsługuje biblioteki klienckie dla [platform .NET](https://github.com/Azure/azure-service-bus-dotnet/tree/master), [Java](https://github.com/Azure/azure-service-bus-java/tree/master)i [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client).

## <a name="integration"></a>Integracja

Usługa Service Bus w pełni integruje się z następującymi usługami platformy Azure:

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z obsługi komunikatów usługi Service Bus, zobacz następujące artykuły:

* Aby porównać usługi Azure Messaging, zobacz [porównanie usług](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* Wypróbuj Przewodniki Szybki Start dla [platformy .NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md)lub [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Aby zarządzać zasobami Service Bus, zobacz [eksplorator Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Aby dowiedzieć się więcej o warstwach Standardowa i Premium oraz ich cenach, zobacz [Service Bus Cennik](https://azure.microsoft.com/pricing/details/service-bus/).
* Aby dowiedzieć się więcej o wydajności i opóźnieniu dla warstwy Premium, zobacz [Obsługa komunikatów](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722)w warstwie Premium.
