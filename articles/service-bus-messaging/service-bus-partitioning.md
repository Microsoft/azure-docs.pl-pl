---
title: Tworzenie podzielonych kolejek Azure Service Bus i tematów | Microsoft Docs
description: Opisuje sposób partycjonowania Service Bus kolejek i tematów przy użyciu wielu brokerów komunikatów.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c500a69f853b11437a0dcaa48213fe3a84da53b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97796639"
---
# <a name="partitioned-queues-and-topics"></a>Partycjonowane kolejki i tematy

Azure Service Bus wykorzystuje wielu brokerów komunikatów do przetwarzania komunikatów i wielu magazynów komunikatów do przechowywania komunikatów. Konwencjonalne kolejki lub tematy są obsługiwane przez jednego brokera komunikatów i przechowywane w jednym magazynie obsługi komunikatów. Service Bus *partycje* umożliwiają korzystanie z kolejek i tematów, a także *jednostek obsługi* komunikatów, które mają być partycjonowane dla wielu brokerów komunikatów i magazynów komunikatów. Partycjonowanie oznacza, że ogólna przepływność partycjonowanej jednostki nie jest już ograniczona przez wydajność jednego brokera komunikatów lub magazynu komunikatów. Ponadto tymczasowa awaria magazynu obsługi komunikatów nie powoduje niedostępności partycjonowanej kolejki lub tematu. Partycjonowane kolejki i tematy mogą zawierać wszystkie zaawansowane funkcje Service Bus, takie jak obsługa transakcji i sesji.

> [!NOTE]
> Partycjonowanie jest dostępne podczas tworzenia jednostki dla wszystkich kolejek i tematów w podstawowych lub standardowych jednostkach SKU. Nie jest ona dostępna dla jednostki SKU obsługi komunikatów w warstwie Premium, ale wszystkie wcześniej istniejące partycjonowane jednostki w przestrzeni nazw Premium nadal działają zgodnie z oczekiwaniami.
 
Nie można zmienić opcji partycjonowania dla żadnej istniejącej kolejki lub tematu; opcję można ustawić tylko podczas tworzenia jednostki.

## <a name="how-it-works"></a>Jak to działa

Każda podzielona Kolejka lub temat zawiera wiele partycji. Każda partycja jest przechowywana w innym magazynie komunikatów i obsługiwana przez innego brokera komunikatów. Gdy wiadomość jest wysyłana do kolejki lub tematu partycjonowanego, Service Bus przypisuje komunikat do jednej z partycji. Wybór jest wykonywany losowo przez Service Bus lub przy użyciu klucza partycji, który może określić nadawca.

Gdy klient chce otrzymać komunikat z podzielonej kolejki lub z subskrypcji do podzielonego tematu, Service Bus wysyła zapytanie do wszystkich partycji dla komunikatów, a następnie zwraca pierwszy komunikat uzyskany z dowolnego magazynu komunikatów do odbiorcy. Service Bus buforuje inne komunikaty i zwraca je po odebraniu dodatkowych żądań odebrania. Klient otrzymujący nie wie o partycjonowaniu; zachowanie związane z klientem dla kolejki lub tematu podzielonego na partycje (na przykład odczyt, zakończenie, odkładanie, utraconie, pobieranie z wyprzedzeniem) jest identyczne z zachowaniem zwykłej jednostki.

Operacja wglądu w jednostce niepartycjonowanej zawsze zwraca najstarszą wiadomość, ale nie na partycjonowanej jednostce. Zamiast tego zwraca najstarszy komunikat z jednej z partycji, których Broker komunikatów odpowiedział jako pierwszy. Nie ma gwarancji, że zwracany komunikat jest najstarszym z nich we wszystkich partycjach. 

Nie ma dodatkowych kosztów podczas wysyłania komunikatu do lub otrzymywania komunikatu z podzielonej kolejki lub tematu.

> [!NOTE]
> Operacja wglądu zwraca najstarszy komunikat z partycji na podstawie jego numeru sekwencyjnego. W przypadku jednostek partycjonowanych numer sekwencyjny jest wystawiany względem partycji. Aby uzyskać więcej informacji, zobacz [sekwencjonowanie komunikatów i sygnatury czasowe](../service-bus-messaging/message-sequencing.md).

## <a name="enable-partitioning"></a>Włącz partycjonowanie

Aby używać podzielonych kolejek i tematów w Azure Service Bus, należy użyć zestawu Azure SDK w wersji 2,2 lub nowszej albo określić `api-version=2013-10` lub później w żądaniach HTTP.

### <a name="standard"></a>Standardowa

W warstwie Standardowa obsługa komunikatów można tworzyć Service Bus kolejki i tematy w rozmiarze 1, 2, 3, 4 lub 5 GB (wartość domyślna to 1 GB). Po włączeniu partycjonowania Service Bus tworzy 16 kopii (16 partycji) jednostki, każdy z określonych rozmiarów. W związku z tym, jeśli utworzysz kolejkę o rozmiarze 5 GB, a w przypadku 16 partycji zostanie osiągnięty maksymalny rozmiar kolejki (5 \* 16) = 80 GB. Możesz zobaczyć maksymalny rozmiar kolejki lub tematu partycjonowanego, przeglądając jego wpis na [Azure Portal][Azure portal], w bloku **Przegląd** dla tej jednostki.

### <a name="premium"></a>Premium

W przestrzeni nazw warstwy Premium partycjonowanie jednostek nie jest obsługiwane. Można jednak nadal tworzyć Service Bus kolejki i tematy w 1, 2, 3, 4, 5, 10, 20, 40 lub 80 GB rozmiaru (wartość domyślna to 1 GB). Możesz wyświetlić rozmiar kolejki lub tematu, przeglądając jego wpis na [Azure Portal][Azure portal], w bloku **Przegląd** dla tej jednostki.

### <a name="create-a-partitioned-entity"></a>Tworzenie jednostki partycjonowanej

Istnieje kilka sposobów tworzenia partycjonowanej kolejki lub tematu. Podczas tworzenia kolejki lub tematu z aplikacji można włączyć partycjonowanie dla kolejki lub tematu, odpowiednio ustawiając właściwość [QueueDescription. EnablePartitioning][QueueDescription.EnablePartitioning] lub [TopicDescription. EnablePartitioning][TopicDescription.EnablePartitioning] na **wartość true**. Te właściwości muszą być ustawione w momencie tworzenia kolejki lub tematu i są dostępne tylko w starszej bibliotece [windowsazure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) . Jak wspomniano wcześniej, nie można zmienić tych właściwości dla istniejącej kolejki lub tematu. Na przykład:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Alternatywnie można utworzyć partycjonowaną kolejkę lub temat w [Azure Portal][Azure portal]. Podczas tworzenia kolejki lub tematu w portalu opcja **Włącz partycjonowanie** w kolejce lub oknie dialogowym **Tworzenie** tematu jest domyślnie zaznaczona. Tę opcję można wyłączyć tylko w jednostce warstwy Standardowa; partycjonowanie warstwy Premium nie jest obsługiwane, a pole wyboru nie ma żadnego efektu. 

## <a name="use-of-partition-keys"></a>Korzystanie z kluczy partycji

Gdy komunikat jest przewidziany do kolejki lub tematu, Service Bus sprawdza obecność klucza partycji. Jeśli go znajdzie, wybiera partycję opartą na tym kluczu. Jeśli nie znajdzie klucza partycji, wybiera partycję opartą na wewnętrznym algorytmie.

### <a name="using-a-partition-key"></a>Korzystanie z klucza partycji

Niektóre scenariusze, takie jak sesje lub transakcje, wymagają, aby komunikaty były przechowywane w określonej partycji. Wszystkie te scenariusze wymagają użycia klucza partycji. Wszystkie komunikaty, które używają tego samego klucza partycji, są przypisane do tej samej partycji. Jeśli partycja jest tymczasowo niedostępna, Service Bus zwraca błąd.

W zależności od scenariusza są używane różne właściwości komunikatów jako klucz partycji:

**Identyfikator sesji**: Jeśli komunikat ma ustawioną właściwość [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) , Service Bus używa **identyfikatora SessionID** jako klucza partycji. W ten sposób wszystkie komunikaty należące do tej samej sesji są obsługiwane przez ten sam brokera komunikatów. Sesje umożliwiają Service Bus w celu zagwarantowania kolejności komunikatów oraz spójności stanów sesji.

**PartitionKey**: Jeśli komunikat ma właściwość [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) , ale nie ustawiono właściwości [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) , Service Bus używa wartości właściwości [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) jako klucza partycji. Jeśli komunikat ma ustawioną właściwość [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) i [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) , obie właściwości muszą być identyczne. Jeśli właściwość [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) ma ustawioną inną wartość niż Właściwość [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) , Service Bus zwraca wyjątek nieprawidłowej operacji. Właściwość [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) powinna zostać użyta, jeśli nadawca wyśle komunikaty transakcyjne nieobsługujących sesji. Klucz partycji gwarantuje, że wszystkie komunikaty wysyłane w ramach transakcji są obsługiwane przez tego samego brokera obsługi komunikatów.

**MessageID**: Jeśli kolejka lub temat ma właściwość [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) ustawioną na **wartość true** , a właściwości [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) lub [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) nie są ustawione, a następnie wartość właściwości [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid) służy jako klucz partycji. (Biblioteki Microsoft .NET i AMQP automatycznie przypisują identyfikator wiadomości, jeśli aplikacja wysyłająca nie). W takim przypadku wszystkie kopie tego samego komunikatu są obsługiwane przez ten sam brokera komunikatów. Ten identyfikator umożliwia Service Bus wykrywania i eliminowania zduplikowanych komunikatów. Jeśli właściwość [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) nie jest ustawiona na **wartość true**, Service Bus nie uwzględnia właściwości [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid) jako klucza partycji.

### <a name="not-using-a-partition-key"></a>Nie używa klucza partycji

W przypadku braku klucza partycji Service Bus dystrybuuje komunikaty w sposób okrężny do wszystkich partycji podzielonej kolejki lub tematu. Jeśli wybrana partycja jest niedostępna, Service Bus przypisze komunikat do innej partycji. W ten sposób operacja wysyłania kończy się niepowodzeniem pomimo tymczasowej niedostępności magazynu komunikatów. Nie można jednak zapewnić gwarantowanej kolejności, w której znajduje się klucz partycji.

Aby zapoznać się z bardziej szczegółowym omówieniem kompromisu między dostępnością (bez klucza partycji) i spójnością (za pomocą klucza partycji), zobacz [ten artykuł](../event-hubs/event-hubs-availability-and-consistency.md). Te informacje są stosowane zarówno do partycjonowania Service Bus jednostek.

Aby dać Service Bus wystarczająco dużo czasu na umieszczenie komunikatu w kolejce na inną partycję, wartość [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) określona przez klienta wysyłającego komunikat musi być większa niż 15 sekund. Zaleca się, aby właściwość [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) została ustawiona na wartość domyślną 60 sekund.

Klucz partycji "przypina" do określonej partycji. Jeśli magazyn komunikatów, który przechowuje tę partycję, jest niedostępny, Service Bus zwraca błąd. W przypadku braku klucza partycji Service Bus można wybrać inną partycję, a operacja powiedzie się. W związku z tym zaleca się, aby nie podawać klucza partycji, chyba że jest to wymagane.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Tematy zaawansowane: używanie transakcji z podzielonymi jednostkami

Komunikaty wysyłane w ramach transakcji muszą określać klucz partycji. Klucz może mieć jedną z następujących właściwości: [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)lub [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid). Wszystkie komunikaty wysyłane jako część tej samej transakcji muszą określać ten sam klucz partycji. Jeśli podjęto próbę wysłania komunikatu bez klucza partycji w ramach transakcji, Service Bus zwraca wyjątek nieprawidłowej operacji. Jeśli spróbujesz wysłać wiele komunikatów w ramach tej samej transakcji, które mają różne klucze partycji, Service Bus zwraca wyjątek nieprawidłowej operacji. Na przykład:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

W przypadku ustawienia dowolnej z właściwości, które służą jako klucz partycji, Service Bus przypina komunikat do określonej partycji. To zachowanie występuje niezależnie od tego, czy transakcja jest używana. Zaleca się, aby nie określać klucza partycji, jeśli nie jest to konieczne.

## <a name="using-sessions-with-partitioned-entities"></a>Korzystanie z sesji z jednostkami partycjonowanymi

Aby wysłać wiadomość transakcyjną do tematu lub kolejki z obsługą sesji, komunikat musi mieć ustawioną właściwość [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) . Jeśli właściwość [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) jest również określona, musi być taka sama jak Właściwość [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) . Jeśli różnią się one, Service Bus zwraca wyjątek nieprawidłowej operacji.

W przeciwieństwie do zwykłych (niepartycjonowanych) kolejek lub tematów nie jest możliwe użycie pojedynczej transakcji do wysyłania wielu komunikatów do różnych sesji. Jeśli podjęto próbę, Service Bus zwraca nieprawidłowy wyjątek operacji. Na przykład:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatyczne przekazywanie komunikatów przy użyciu jednostek partycjonowanych

Service Bus obsługuje automatyczne przekazywanie komunikatów z, do lub między jednostkami partycjonowanymi. Aby włączyć automatyczne przekazywanie komunikatów, ustaw właściwość [QueueDescription. Prześlij dalej][QueueDescription.ForwardTo] w kolejce źródłowej lub subskrypcji. Jeśli komunikat określa klucz partycji ([SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)lub [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid)), ten klucz partycji jest używany dla jednostki docelowej.

## <a name="considerations-and-guidelines"></a>Zagadnienia i wskazówki
* **Funkcje wysokiej spójności**: Jeśli jednostka używa takich funkcji jak sesje, wykrywanie duplikatów lub jawna kontrola klucza partycjonowania, operacje obsługi komunikatów są zawsze kierowane do określonej partycji. Jeśli dowolna z partycji będzie mieć duży ruch lub kondycja magazynu jest zła, te operacje kończą się niepowodzeniem i dostępność zostanie zmniejszona. Ogólnie, spójność jest nadal znacznie wyższa niż jednostki niepartycjonowane; występują problemy, w przeciwieństwie do całego ruchu. Aby uzyskać więcej informacji, zobacz [Omówienie dostępności i spójności](../event-hubs/event-hubs-availability-and-consistency.md).
* **Zarządzanie**: operacje, takie jak tworzenie, aktualizowanie i usuwanie, muszą być wykonywane na wszystkich partycjach jednostki. Jeśli jakakolwiek partycja ma złą kondycję, może to spowodować błędy tych operacji. W przypadku operacji get informacje takie jak liczby komunikatów muszą być agregowane ze wszystkich partycji. Jeśli jakakolwiek partycja ma złą kondycję, stan dostępności jednostki jest raportowany jako ograniczony.
* **Scenariusze komunikatów o małym nasileniu**: w przypadku takich scenariuszy, zwłaszcza w przypadku korzystania z protokołu HTTP, może być konieczne wykonanie wielu operacji odbioru w celu uzyskania wszystkich komunikatów. W przypadku żądań Receive fronton wykonuje odbieranie na wszystkich partycjach i buforuje wszystkie odebrane odpowiedzi. Kolejne żądanie odbioru dla tego samego połączenia może korzystać z tej pamięci podręcznej, a opóźnienia odbierania będą mniejsze. Jeśli jednak masz wiele połączeń lub używasz protokołu HTTP, który ustanawia nowe połączenie dla każdego żądania. W związku z tym nie ma żadnej gwarancji, że będzie on miał miejsce w tym samym węźle. Jeśli wszystkie istniejące komunikaty są zablokowane i znajdują się w pamięci podręcznej w innym frontonie, operacja Receive zwraca **wartość null**. Komunikaty ostatecznie wygasną i można je odebrać ponownie. Zalecana jest metoda Keep-Alive protokołu HTTP. W przypadku korzystania z partycjonowania w scenariuszach o małej ilości operacje odbioru mogą trwać dłużej niż oczekiwano. Dlatego zalecamy, aby nie używać partycjonowania w tych scenariuszach. Usuń istniejące partycjonowane jednostki i utwórz je ponownie z podziałem na partycje w celu zwiększenia wydajności.
* **Przeglądanie/wgląd komunikatów**: dostępne tylko w starszej bibliotece [windowsazure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) . [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) nie zawsze zwraca liczbę komunikatów określonych we właściwości [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) . Istnieją dwie typowe przyczyny tego zachowania. Jedną z przyczyn jest to, że zagregowany rozmiar kolekcji komunikatów przekracza maksymalny rozmiar 256 KB. Kolejną przyczyną jest to, że jeśli dla kolejki lub tematu [Właściwość EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) ma **wartość true**, partycja może nie mieć wystarczającej liczby komunikatów, aby zakończyć żądaną liczbę komunikatów. Ogólnie rzecz biorąc, jeśli aplikacja chce otrzymywać określoną liczbę komunikatów, należy wielokrotnie wywoływać [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) do momentu uzyskania tej liczby komunikatów lub nie ma więcej komunikatów do wglądu. Aby uzyskać więcej informacji, w tym przykłady kodu, zapoznaj się z dokumentacją interfejsu API [QueueClient. PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) lub [SubscriptionClient. PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) .

## <a name="latest-added-features"></a>Najnowsze dodane funkcje

* Dodawanie lub usuwanie reguły jest teraz obsługiwane w przypadku partycjonowanych jednostek. W przypadku niepodzielonych jednostek operacje te nie są obsługiwane w ramach transakcji. 
* AMQP jest teraz obsługiwane na potrzeby wysyłania i otrzymywania komunikatów do i z partycjonowanej jednostki.
* Funkcja AMQP jest teraz obsługiwana dla następujących operacji: [wysyłanie wsadowe](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [odbieranie wsadowe](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [odbieranie według numeru sekwencyjnego](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [wgląd](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [odnowienie blokady](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [komunikat harmonogramu](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), [Anulowanie zaplanowanego komunikatu](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [Dodawanie reguły](/dotnet/api/microsoft.azure.servicebus.ruledescription), [Usuwanie reguły](/dotnet/api/microsoft.azure.servicebus.ruledescription), [Zablokuj odnowienie](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock)sesji, [Ustawianie stanu sesji](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [pobieranie stanu sesji](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate)i [wyliczanie sesji](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Ograniczenia jednostek partycjonowanych

Obecnie Service Bus nakładają następujące ograniczenia dotyczące podzielonych kolejek i tematów:

* Partycjonowane kolejki i tematy nie są obsługiwane w warstwie obsługi komunikatów Premium. Sesje są obsługiwane w warstwie Premium przy użyciu identyfikatora sesji. 
* Partycjonowane kolejki i tematy nie obsługują wysyłania komunikatów należących do różnych sesji w ramach jednej transakcji.
* Obecnie w usłudze Service Bus można utworzyć maksymalnie 100 partycjonowanych kolejek lub tematów w każdej przestrzeni nazw. Każda kolejka podzielona na partycje lub temat ma do przydziału liczby jednostek 10 000 na przestrzeń nazw (nie dotyczy warstwy Premium).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z podstawowymi pojęciami dotyczącymi specyfikacji komunikatów AMQP 1,0 w [przewodniku po protokole AMQP 1,0](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: ./service-bus-amqp-protocol-guide.md
