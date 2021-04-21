---
title: Tworzenie partycjonowanych Azure Service Bus i tematów | Microsoft Docs
description: Opisuje sposób partycjonowania Service Bus i tematów przy użyciu wielu brokerów komunikatów.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: bc41bcf31102b19dd35f62452b956faf4f029551
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750918"
---
# <a name="partitioned-queues-and-topics"></a>Partycjonowane kolejki i tematy

Azure Service Bus wielu brokerów komunikatów do przetwarzania komunikatów i wielu magazynów obsługi komunikatów do przechowywania komunikatów. Konwencjonalne kolejki lub temat są obsługiwane przez jednego brokera komunikatów i przechowywane w jednym magazynie obsługi komunikatów. Service Bus *partycje* umożliwiają partycjonowanie kolejek i tematów lub jednostek obsługi komunikatów w wielu brokerach komunikatów i magazynach obsługi komunikatów.  Partycjonowanie oznacza, że ogólna przepływność partycjonowanej jednostki nie jest już ograniczona przez wydajność pojedynczego brokera komunikatów lub magazynu komunikatów. Ponadto tymczasowa niedostępność magazynu obsługi komunikatów nie powoduje niedostępności partycjonowanych kolejek ani tematu. Partycjonowane kolejki i tematy mogą zawierać wszystkie zaawansowane Service Bus, takie jak obsługa transakcji i sesji.

> [!NOTE]
> Partycjonowanie jest dostępne podczas tworzenia jednostki dla wszystkich kolejek i tematów w podstawowych lub standardowych jednostkach SKU. Nie jest ona dostępna w przypadku jednostki SKU obsługi komunikatów Premium, ale wszystkie wcześniej partycjonowane jednostki w przestrzeniach nazw Premium nadal działają zgodnie z oczekiwaniami.
 
Nie można zmienić opcji partycjonowania w żadnej istniejącej kolejce lub temacie. Tę opcję można ustawić tylko podczas tworzenia jednostki.

## <a name="how-it-works"></a>Jak to działa

Każda partycjonowana kolejka lub temat składa się z wielu partycji. Każda partycja jest przechowywana w innym magazynie obsługi komunikatów i obsługiwane przez innego brokera komunikatów. Gdy komunikat jest wysyłany do partycjonowanych kolejki lub tematu, Service Bus przypisuje komunikat do jednej z partycji. Wybór jest wybierany losowo przez Service Bus lub przy użyciu klucza partycji, który może określić nadawca.

Gdy klient chce odbierać komunikat z kolejki podzielonej na partycje lub z subskrypcji do tematu podzielonego na partycje, program Service Bus wysyła zapytanie do wszystkich partycji o komunikaty, a następnie zwraca pierwszy komunikat uzyskany z dowolnego magazynu obsługi komunikatów do odbiorcy. Service Bus buforuje inne komunikaty i zwraca je, gdy odbiera dodatkowe żądania odbierania. Klient odbierający nie wie o partycjonowania; Zachowanie partycjonowanych kolejek lub tematów (na przykład odczytu, ukończenia, odroczenie, zakleszczenie, pobieranie z wyprzedzeniem) po stronie klienta jest identyczne z zachowaniem zwykłej jednostki.

Operacja podglądu dla jednostki nieu partitioned zawsze zwraca najstarszy komunikat, ale nie w jednostce podzielonej na partycje. Zamiast tego zwraca najstarszy komunikat w jednej z partycji, których broker komunikatów odpowiedział jako pierwszy. Nie ma gwarancji, że zwrócony komunikat jest najstarszy we wszystkich partycjach. 

Nie ma żadnych dodatkowych kosztów podczas wysyłania komunikatu do partycjonowanych kolejek lub tematu lub odbierania komunikatu z tej kolejki.

> [!NOTE]
> Operacja podglądu zwraca najstarszy komunikat z partycji na podstawie jej numeru sekwencji. W przypadku jednostek podzielonych na partycje numer sekwencji jest wystawiany względem partycji. Aby uzyskać więcej informacji, zobacz [Sekwencjonowanie komunikatów i znaczniki czasu](../service-bus-messaging/message-sequencing.md).

## <a name="enable-partitioning"></a>Włączanie partycjonowania

Aby używać partycjonowanych kolejek i tematów z Azure Service Bus, użyj zestawu Azure SDK w wersji 2.2 lub nowszej albo określ lub później `api-version=2013-10` w żądaniach HTTP.

### <a name="standard"></a>Standardowa (Standard)

W warstwie Standardowa można tworzyć kolejki i tematy Service Bus w rozmiarach 1, 2, 3, 4 lub 5 GB (wartość domyślna to 1 GB). Po włączeniu partycjonowania Service Bus 16 kopii (16 partycji) jednostki, z których każda ma określony rozmiar. W związku z tym, jeśli utworzysz kolejkę o rozmiarze 5 GB, z 16 partycjami maksymalny rozmiar kolejki staje się (5 \* 16) = 80 GB. Maksymalny rozmiar partycjonowanych kolejek lub tematów można zobaczyć, patrząc na jego wpis  w Azure Portal [,][Azure portal]w bloku Przegląd dla tej jednostki.

### <a name="premium"></a>Premium

W przestrzeni nazw w warstwie Premium partycjonowanie jednostek nie jest obsługiwane. Można jednak nadal tworzyć Service Bus i tematy o rozmiarach 1, 2, 3, 4, 5, 10, 20, 40 lub 80 GB (wartość domyślna to 1 GB). Rozmiar kolejki lub tematu można wyświetlić, patrząc na jego wpis w  Azure Portal [w][Azure portal]bloku Przegląd dla tej jednostki.

### <a name="create-a-partitioned-entity"></a>Tworzenie jednostki podzielonej na partycje

Istnieje kilka sposobów tworzenia partycjonowanych kolejki lub tematu. Podczas tworzenia kolejki lub tematu z aplikacji można włączyć partycjonowanie dla kolejki lub tematu, odpowiednio ustawiając właściwość [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] lub [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] na **wartość true**. Te właściwości należy ustawić podczas tworzenia kolejki lub tematu i są dostępne tylko w starszej bibliotece [WindowsAzure.ServiceBus.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) Jak wspomniano wcześniej, nie można zmienić tych właściwości w istniejącej kolejce lub temacie. Na przykład:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Alternatywnie możesz utworzyć partycjonowane kolejki lub temat w Azure Portal [.][Azure portal] Podczas tworzenia kolejki lub tematu w  portalu opcja Włącz partycjonowanie w oknie dialogowym Tworzenie kolejki lub tematu jest zaznaczona domyślnie.  Tę opcję można wyłączyć tylko w jednostce w warstwie Standardowa. w warstwie Premium partycjonowanie nie jest obsługiwane, a pole wyboru nie ma żadnego efektu. 

## <a name="use-of-partition-keys"></a>Korzystanie z kluczy partycji

Gdy komunikat znajduje się w kolejce lub temacie podzielonym na partycje, Service Bus sprawdza obecność klucza partycji. Jeśli znajdzie jedną z nich, wybiera partycję na podstawie tego klucza. Jeśli nie znajdzie klucza partycji, wybierze partycję na podstawie algorytmu wewnętrznego.

### <a name="using-a-partition-key"></a>Używanie klucza partycji

Niektóre scenariusze, takie jak sesje lub transakcje, wymagają przechowywania komunikatów w określonej partycji. Wszystkie te scenariusze wymagają użycia klucza partycji. Wszystkie komunikaty, które używają tego samego klucza partycji, są przypisywane do tej samej partycji. Jeśli partycja jest tymczasowo niedostępna, Service Bus zwraca błąd.

W zależności od scenariusza jako klucz partycji są używane różne właściwości komunikatu:

**SessionId:** jeśli komunikat ma ustawioną właściwość [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) Service Bus używa **wartości SessionID** jako klucza partycji. Dzięki temu wszystkie komunikaty należące do tej samej sesji są obsługiwane przez tego samego brokera komunikatów. Sesje Service Bus zagwarantować kolejność komunikatów, a także spójność stanów sesji.

**PartitionKey:** jeśli komunikat ma właściwość [PartitionKey,](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) ale nie ustawiono właściwości [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) Service Bus używa wartości właściwości [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) jako klucza partycji. Jeśli komunikat ma ustawione właściwości [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) i [PartitionKey,](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) obie właściwości muszą być identyczne. Jeśli właściwość [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) jest ustawiona na inną wartość niż właściwość [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) Service Bus zwraca wyjątek nieprawidłowej operacji. Właściwość [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) powinna być używana, jeśli nadawca wysyła komunikaty transakcyjne bez powiadomienia o sesji. Klucz partycji zapewnia, że wszystkie komunikaty wysyłane w ramach transakcji są obsługiwane przez tego samego brokera obsługi komunikatów.

**MessageId:** jeśli kolejka lub temat ma właściwość [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) ustawioną na **wartość true** i właściwości [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) lub [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) nie są ustawione, wartość właściwości [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) służy jako klucz partycji. (Biblioteki Microsoft .NET i AMQP automatycznie przypisz identyfikator komunikatu, jeśli aplikacja wysyłająca tego nie robi). W takim przypadku wszystkie kopie tego samego komunikatu są obsługiwane przez tego samego brokera komunikatów. Ten identyfikator umożliwia Service Bus wykrywania i eliminowania zduplikowanych komunikatów. Jeśli właściwość [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) nie jest ustawiona na wartość **true,** Service Bus właściwość [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) nie jest uwzględniana jako klucz partycji.

### <a name="not-using-a-partition-key"></a>Nie korzystasz z klucza partycji

W przypadku braku klucza partycji Service Bus komunikaty są dystrybuowane w sposób okrężny do wszystkich partycji partycjonowanych kolejki lub tematu. Jeśli wybrana partycja nie jest dostępna, Service Bus przypisze komunikat do innej partycji. W ten sposób operacja wysyłania powiedzie się pomimo tymczasowej niedostępności magazynu obsługi komunikatów. Jednak nie osiągniesz gwarantowanej kolejności zapewnianej przez klucz partycji.

Aby uzyskać bardziej szczegółowe omówienie kompromisu między dostępnością (bez klucza partycji) i spójnością (przy użyciu klucza partycji), zobacz [ten artykuł.](../event-hubs/event-hubs-availability-and-consistency.md) Te informacje dotyczą w równym stopniu partycjonowanych Service Bus jednostek.

Aby zapewnić Service Bus wystarczająco dużo czasu na kolejkowanie komunikatu do innej partycji, wartość [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) określona przez klienta, który wysyła komunikat, musi być większa niż 15 sekund. Zaleca się ustawienie właściwości [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) na wartość domyślną 60 sekund.

Klucz partycji "przypina" komunikat do określonej partycji. Jeśli magazyn obsługi komunikatów przechowujący tę partycję jest niedostępny, Service Bus zwraca błąd. W przypadku braku klucza partycji Service Bus wybrać inną partycję i operacja zakończy się pomyślnie. W związku z tym zaleca się, aby nie po podać klucza partycji, chyba że jest to wymagane.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Tematy zaawansowane: używanie transakcji z jednostkami podzielonymi na partycje

Komunikaty wysyłane w ramach transakcji muszą określać klucz partycji. Klucz może być jedną z następujących właściwości: [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)lub [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Wszystkie komunikaty wysyłane w ramach tej samej transakcji muszą określać ten sam klucz partycji. Jeśli spróbujemy wysłać komunikat bez klucza partycji w ramach transakcji, Service Bus zwraca wyjątek nieprawidłowej operacji. Jeśli próbujesz wysłać wiele komunikatów w ramach tej samej transakcji, które mają różne klucze partycji, Service Bus zwraca wyjątek nieprawidłowej operacji. Na przykład:

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

Jeśli dowolna z właściwości, które służą jako klucz partycji, Service Bus przypina komunikat do określonej partycji. To zachowanie występuje niezależnie od tego, czy jest używana transakcja. Zaleca się, aby nie określać klucza partycji, jeśli nie jest to konieczne.

## <a name="using-sessions-with-partitioned-entities"></a>Używanie sesji z jednostkami podzielonymi na partycje

Aby wysłać komunikat transakcyjny do tematu lub kolejki z informacjami o sesji, komunikat musi mieć ustawioną właściwość [SessionId.](/dotnet/api/microsoft.azure.servicebus.message.sessionid) Jeśli określono również właściwość [PartitionKey,](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) musi być taka sama jak [właściwość SessionId.](/dotnet/api/microsoft.azure.servicebus.message.sessionid) Jeśli się różnią, Service Bus zwraca wyjątek nieprawidłowej operacji.

W przeciwieństwie do zwykłych (nie partycjonowanych) kolejek lub tematów nie można użyć jednej transakcji do wysyłania wielu komunikatów do różnych sesji. W przypadku próby Service Bus zwraca wyjątek nieprawidłowej operacji. Na przykład:

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

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatyczne przekazywanie komunikatów za pomocą jednostek partycjonowanych

Service Bus obsługuje automatyczne przekazywanie komunikatów z, do lub między partycjonowanych jednostek. Aby włączyć automatyczne przekazywanie komunikatów, ustaw właściwość [QueueDescription.ForwardTo][QueueDescription.ForwardTo] w subskrypcji lub kolejce źródłowej. Jeśli komunikat określa klucz partycji[(SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)lub [MessageId),](/dotnet/api/microsoft.azure.servicebus.message.messageid)ten klucz partycji jest używany dla jednostki docelowej.

## <a name="considerations-and-guidelines"></a>Zagadnienia i wskazówki
* **Funkcje wysokiej** spójności: jeśli jednostka używa funkcji, takich jak sesje, wykrywanie duplikatów lub jawna kontrola klucza partycjonowania, operacje obsługi komunikatów są zawsze kierowane do określonej partycji. Jeśli którakolwiek z partycji ma duży ruch lub podstawowy magazyn jest w złej kondycji, te operacje nie powiodą się i dostępność zostanie ograniczona. Ogólnie rzecz ujmuje się, że spójność jest nadal znacznie wyższa niż w przypadku jednostek niez partycjonowanych. występują problemy tylko dla podzbioru ruchu, w przeciwieństwie do całego ruchu. Aby uzyskać więcej informacji, zobacz [omówienie dostępności i spójności](../event-hubs/event-hubs-availability-and-consistency.md).
* **Zarządzanie:** Operacje, takie jak tworzenie, aktualizowanie i usuwanie, muszą być wykonywane na wszystkich partycjach jednostki. Jeśli jakakolwiek partycja jest w złej kondycji, może to spowodować błędy dla tych operacji. W przypadku operacji Get informacje, takie jak liczba komunikatów, muszą być agregowane ze wszystkich partycji. Jeśli dowolna partycja jest w złej kondycji, stan dostępności jednostki jest zgłaszany jako ograniczony.
* **Scenariusze z małą** ilością komunikatów: w takich scenariuszach, szczególnie w przypadku korzystania z protokołu HTTP, może być trzeba wykonać wiele operacji odbierania, aby uzyskać wszystkie komunikaty. W przypadku żądań odbierania fronton wykonuje odbieranie na wszystkich partycjach i buforuje wszystkie odebrane odpowiedzi. Kolejne żądanie odbierania w tym samym połączeniu będzie korzystać z tego buforowania, a opóźnienia odbierania będą mniejsze. Jeśli jednak masz wiele połączeń lub używasz protokołu HTTP, to ustanawia nowe połączenie dla każdego żądania. W związku z tym nie ma gwarancji, że trafi on w tym samym węźle. Jeśli wszystkie istniejące komunikaty są zablokowane i buforowane w innym frontonie, operacja odbierania zwraca wartość **null**. Komunikaty wygasają i można je ponownie odbierać. Zaleca się utrzymanie aktywności protokołu HTTP. W przypadku korzystania z partycjonowania w scenariuszach z małą ilościami operacje odbierania mogą trwać dłużej niż oczekiwano. W związku z tym nie zalecamy używania partycjonowania w tych scenariuszach. Usuń wszystkie istniejące partycjonowane jednostki i utwórz je ponownie z wyłączonym partycjonowaniem, aby zwiększyć wydajność.
* **Przeglądanie/podgląd komunikatów:** dostępne tylko w starszej [bibliotece WindowsAzure.ServiceBus.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) nie zawsze zwraca liczbę komunikatów określoną we właściwości [MessageCount.](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) Istnieją dwie typowe przyczyny tego zachowania. Jednym z powodów jest to, że zagregowany rozmiar kolekcji komunikatów przekracza maksymalny rozmiar 256 KB. Innym powodem jest to, że jeśli kolejka lub temat ma właściwość [EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) ustawioną na wartość **true,** partycja może nie mieć wystarczającej liczby komunikatów, aby ukończyć żądaną liczbę komunikatów. Ogólnie rzecz biorąc, jeśli aplikacja chce odbierać określoną liczbę komunikatów, powinna wielokrotnie wywołać usługę [PeekBatch,](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) dopóki nie otrzyma tej liczby komunikatów lub nie będzie więcej komunikatów do podglądu. Aby uzyskać więcej informacji, w tym przykłady kodu, zobacz dokumentację interfejsu API [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) lub [SubscriptionClient.PeekBatch.](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch)

## <a name="latest-added-features"></a>Najnowsze dodane funkcje

* Dodawanie lub usuwanie reguły jest teraz obsługiwane w przypadku jednostek podzielonych na partycje. Operacje te nie są obsługiwane w przypadku transakcji innych niż jednostki nieuparte na partycjach. 
* Obecnie jest obsługiwany program AMQP do wysyłania i odbierania komunikatów do i z jednostki podzielonej na partycje.
* Usługa AMQP jest teraz obsługiwana w przypadku następujących [operacji:](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch)wysyłanie wsadowe, [](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch)odbieranie wsadowe, [](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive)odbieranie według numeru sekwencji, [](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek) [podgląd,](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock)odnawianie [blokady,](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync)zaplanowany [komunikat,](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync)anulowanie zaplanowanego komunikatu, [dodawanie](/dotnet/api/microsoft.azure.servicebus.ruledescription) [reguły,](/dotnet/api/microsoft.azure.servicebus.ruledescription)usuwanie [reguły,](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock)blokada odnawiania [sesji,](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate)ustawianie stanu [sesji,](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate)uzyskiwanie stanu sesji i [wyliczanie sesji.](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions)

## <a name="partitioned-entities-limitations"></a>Ograniczenia jednostek partycjonowanych

Obecnie Service Bus nakłada następujące ograniczenia na partycjonowane kolejki i tematy:

* Partycjonowane kolejki i tematy nie są obsługiwane w warstwie obsługi komunikatów Premium. Sesje są obsługiwane w warstwie Premier przy użyciu sessionid. 
* Partycjonowane kolejki i tematy nie obsługują wysyłania komunikatów należących do różnych sesji w ramach jednej transakcji.
* Obecnie w usłudze Service Bus można utworzyć maksymalnie 100 partycjonowanych kolejek lub tematów w każdej przestrzeni nazw. Każda partycjonowana kolejka lub temat wlicza się do limitu przydziału 10 000 jednostek na przestrzeń nazw (nie dotyczy warstwy Premium).

## <a name="next-steps"></a>Następne kroki
Partycjonowanie można włączyć przy użyciu Azure Portal, programu PowerShell, interfejsu wiersza polecenia, Resource Manager szablonu, .NET, Java, Python i JavaScript. Aby uzyskać więcej informacji, zobacz [Włączanie partycjonowania](enable-partitions.md). 

Zapoznaj się z podstawowymi pojęciami specyfikacji obsługi komunikatów AMQP 1.0 w przewodniku po protokole [AMQP 1.0.](service-bus-amqp-protocol-guide.md)

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: ./service-bus-amqp-protocol-guide.md
