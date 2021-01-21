---
title: Service Bus asynchroniczne komunikaty | Microsoft Docs
description: Dowiedz się, w jaki sposób Azure Service Bus obsługuje asynchronism za pośrednictwem mechanizmu magazynu i przesyłania dalej z kolejkami, tematami i subskrypcjami.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: ea09b809a19cfe7c7f024b059589748ff9e297d4
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631627"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Wzorce asynchronicznej obsługi komunikatów i wysoka dostępność

Asynchroniczne komunikaty można zaimplementować na różne sposoby. Za pomocą kolejek, tematów i subskrypcji Azure Service Bus obsługuje asynchronism za pośrednictwem mechanizmu magazynu i przesyłania dalej. W normalnej operacji (synchronicznej) wysyłanie komunikatów do kolejek i tematów oraz odbieranie komunikatów z kolejek i subskrypcji. Aplikacje, które zapisujesz, zależą od tych, które są zawsze dostępne. Gdy kondycja jednostki ulegnie zmianie, ze względu na różne okoliczności, trzeba zapewnić ograniczoną jednostkę możliwości, która może spełnić większość potrzeb.

Aplikacje zwykle używają asynchronicznych wzorców obsługi komunikatów w celu umożliwienia wielu scenariuszy komunikacji. Można tworzyć aplikacje, w których klienci mogą wysyłać wiadomości do usług, nawet jeśli usługa nie jest uruchomiona. W przypadku aplikacji, które napotykają na rozerwanie komunikacji, kolejka może pomóc w obciążeniu obciążenia, zapewniając w ten sposób miejsce do komunikacji buforowej. Na koniec można skorzystać z prostego, ale efektywnego modułu równoważenia obciążenia do dystrybucji komunikatów na wielu maszynach.

Aby zapewnić dostępność którejkolwiek z tych jednostek, należy wziąć pod uwagę różne sposoby, w których te jednostki mogą być niedostępne dla trwałego systemu obsługi komunikatów. Ogólnie mówiąc, zobaczymy, że jednostka staje się niedostępna dla aplikacji, które zapisujemy na następujące różne sposoby:

* Nie można wysłać komunikatów.
* Nie można odebrać komunikatów.
* Nie można zarządzać jednostkami (tworzenie, pobieranie, aktualizowanie i usuwanie jednostek).
* Nie można skontaktować się z usługą.

Dla każdego z tych błędów istnieją różne tryby awarii, które umożliwiają aplikacji kontynuowanie pracy na pewnym poziomie ograniczonej funkcjonalności. Na przykład system, który może wysyłać wiadomości, ale ich nie odbierać, nadal może otrzymywać zamówienia od klientów, ale nie może przetwarzać tych zamówień. W tym temacie omówiono potencjalne problemy, które mogą wystąpić, oraz sposób, w jaki te problemy zostały skorygowane. W Service Bus wprowadzono wiele środków zaradczych, które należy zadecydować, a w tym temacie omówiono także reguły dotyczące korzystania z tych zasad zaradczych.

## <a name="reliability-in-service-bus"></a>Niezawodność w Service Bus
Istnieje kilka sposobów obsługi problemów z komunikatami i jednostkami, a istnieją wskazówki dotyczące odpowiedniego zastosowania tych środków zaradczych. Aby zrozumieć wytyczne, należy najpierw zrozumieć, co może się nie powieść w Service Bus. Ze względu na projektowanie systemów Azure wszystkie te problemy są zwykle skracane. Na wysokim poziomie różne przyczyny niedostępności są wyświetlane w następujący sposób:

* Ograniczanie z systemu zewnętrznego, od którego zależy Service Bus. Ograniczanie przepływów odbywa się z interakcji z magazynem i zasobami obliczeniowymi.
* Problem dotyczący systemu, w którym zależą Service Bus. Na przykład dana część magazynu może napotkać problemy.
* Niepowodzenie Service Bus w pojedynczym podsystemie. W takiej sytuacji węzeł obliczeniowy może przejść w stan niespójny i uruchomić go ponownie, co spowoduje, że wszystkie jednostki, które obsługują, równoważenia obciążenia z innymi węzłami. To z kolei może spowodować krótkotrwały czas przetwarzania komunikatów.
* Niepowodzenie Service Bus w centrum danych platformy Azure. Jest to "błąd krytyczny", podczas którego system jest nieosiągalny przez wiele minut lub kilka godzin.

> [!NOTE]
> Termin **Magazyn** może oznaczać zarówno usługę Azure Storage, jak i SQL Azure.
> 
> 

Service Bus zawiera wiele środków zaradczych związanych z tymi problemami. W poniższych sekcjach omówiono każdy problem i odpowiednie środki zaradcze.

### <a name="throttling"></a>Ograniczanie przepływności
Dzięki Service Bus ograniczanie przepustowości umożliwia zarządzanie szybkością komunikatów. Każdy pojedynczy węzeł Service Bus ma wiele jednostek. Każda z tych jednostek wykonuje wymagania dotyczące systemu pod względem procesora CPU, pamięci, magazynu i innych aspektów. Gdy którykolwiek z tych aspektów wykryje użycie, które przekracza zdefiniowane wartości progowe, Service Bus może odmówić określonego żądania. Obiekt wywołujący otrzymuje [wyjątek serverbusyexception][ServerBusyException] i ponawia próbę po 10 sekundach.

Jako środek zaradczy, kod musi odczytać błąd i zatrzymać wszystkie ponowne próby komunikatu przez co najmniej 10 sekund. Ponieważ przyczyną błędu może być wiele elementów aplikacji klienta, oczekuje się, że każdy element niezależnie wykonuje logikę ponawiania. Kod może zmniejszyć prawdopodobieństwo ograniczenia przepustowości przez włączenie partycjonowania dla kolejki lub tematu.

### <a name="issue-for-an-azure-dependency"></a>Problem z zależnością platformy Azure
Inne składniki na platformie Azure mogą czasami mieć problemy z usługą. Na przykład gdy system, który Service Bus jest uaktualniany, system może tymczasowo obniżyć możliwości. Aby obejść te rodzaje problemów, Service Bus regularnie badać i implementować środki zaradcze. Zostaną wyświetlone efekty uboczne tych środków zaradczych. Aby na przykład obsłużyć przejściowe problemy z magazynem, Service Bus implementuje System, który pozwala na spójne działanie operacji wysyłania komunikatów. Ze względu na charakter środków zaradczych wysłany komunikat może potrwać do 15 minut w kolejce lub subskrypcji, której dotyczy problem, i będzie gotowy do operacji odbierania. Ogólnie mówiąc, większość jednostek nie będzie napotykać tego problemu. Jednakże uwzględniając liczbę jednostek w Service Bus na platformie Azure, takie ograniczenie jest czasami potrzebne dla małego podzbioru Service Bus klientów.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Niepowodzenie Service Bus w pojedynczym podsystemie
W przypadku każdej aplikacji okoliczności mogą spowodować, że wewnętrzny składnik Service Bus stanie się niespójny. Po wykryciu tego Service Bus zbiera dane z aplikacji, aby pomóc w diagnozowaniu tego, co się stało. Po zebraniu danych aplikacja zostanie uruchomiona ponownie w celu uzyskania spójnego stanu. Ten proces odbywa się dość szybko, a wyniki w jednostce, która jest niedostępna przez maksymalnie kilka minut, chociaż typowe czasy są znacznie krótsze.

W takich przypadkach aplikacja kliencka generuje wyjątek [System. TimeoutException][System.TimeoutException] lub [messagingexception][MessagingException] . Service Bus zawiera środki zaradcze dla tego problemu w postaci zautomatyzowanej logiki ponowienia klienta. Po ponownym przekroczeniu okresu ponowienia próby, gdy wiadomość nie zostanie dostarczona, można zapoznać się z innymi wymienionymi w artykule dotyczącymi [obsługi awarii i katastrof][handling outages and disasters].

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz już podstawy asynchronicznej obsługi komunikatów w Service Bus, Przeczytaj więcej szczegółowych informacji na temat [obsługi awarii i katastrof][handling outages and disasters].

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: /dotnet/api/system.timeoutexception
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: /dotnet/api/system.timespan.zero
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: /dotnet/api/system.unauthorizedaccessexception
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN
[handling outages and disasters]: service-bus-outages-disasters.md