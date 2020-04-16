---
title: Porównanie kolejek usługi Azure Storage i kolejek usługi Service Bus
description: Analizuje różnice i podobieństwa między dwoma typami kolejek oferowanych przez platformę Azure.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 09/04/2019
ms.author: aschhab
ms.openlocfilehash: ffa98e511053edc75fd0e6f25f7b0e21ee9ddda0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414533"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Porównanie kolejek magazynu i kolejek usługi Service Bus
W tym artykule przeanalizowane różnice i podobieństwa między dwoma typami kolejek oferowanych przez platformę Microsoft Azure dzisiaj: kolejki usługi Storage i kolejek usługi Service Bus. Korzystając z tych informacji, można porównać i porównać odpowiednie technologie i być w stanie podjąć bardziej świadomą decyzję o tym, które rozwiązanie najlepiej odpowiada Twoim potrzebom.

## <a name="introduction"></a>Wprowadzenie
Platforma Azure obsługuje dwa typy mechanizmów kolejek: **kolejki magazynu** i kolejki usługi **Service Bus.**

**Kolejki magazynu**, które są częścią infrastruktury [magazynu platformy Azure,](https://azure.microsoft.com/services/storage/) są wyposażone w prosty interfejs GET/PUT/PEEK oparty na rest, zapewniający niezawodne, trwałe wiadomości w ramach i między usługami.

**Kolejki usługi Service Bus** są częścią szerszej infrastruktury [obsługi wiadomości platformy Azure,](https://azure.microsoft.com/services/service-bus/) która obsługuje kolejkowanie, a także publikowanie/subskrybowanie i bardziej zaawansowane wzorce integracji. Aby uzyskać więcej informacji na temat kolejek/tematów/subskrypcji usługi Service Bus, zobacz [omówienie usługi Service Bus](service-bus-messaging-overview.md).

Chociaż obie technologie kolejkowania istnieją jednocześnie, kolejki magazynu zostały wprowadzone jako dedykowany mechanizm magazynowania kolejek utworzony na podstawie usług Azure Storage. Kolejki usługi Service Bus są oparte na szerszej infrastrukturze obsługi wiadomości, zaprojektowanej do integracji aplikacji lub składników aplikacji, które mogą obejmować wiele protokołów komunikacyjnych, kontraktów danych, domen zaufania i/lub środowisk sieciowych.

## <a name="technology-selection-considerations"></a>Zagadnienia dotyczące wyboru technologii
Kolejki magazynu i usługi Service Bus są implementacjami usługi kolejkowania wiadomości obecnie oferowanej przez platformę Microsoft Azure. Każdy z nich ma nieco inny zestaw funkcji, co oznacza, że możesz wybrać jeden lub drugi, lub użyć obu, w zależności od potrzeb konkretnego rozwiązania lub problemu biznesowego / technicznego, który rozwiązujesz.

Przy określaniu, która technologia kolejkowania pasuje do celu danego rozwiązania, architekci rozwiązań i deweloperzy powinni rozważyć te zalecenia. Aby uzyskać więcej informacji, zobacz następną sekcję.

Jako architekt/deweloper rozwiązań **należy rozważyć użycie kolejek magazynu,** gdy:

* Aplikacja musi przechowywać ponad 80 GB wiadomości w kolejce.
* Aplikacja chce śledzić postęp przetwarzania wiadomości wewnątrz kolejki. Jest to przydatne, jeśli proces roboczy przetwarzania wiadomości ulega awarii. Kolejny pracownik może następnie użyć tych informacji, aby kontynuować, od którego poprzedni pracownik został przerwany.
* Wymagane są dzienniki po stronie serwera wszystkich transakcji wykonywanych w kolejkach.

Jako architekt/deweloper rozwiązań **należy rozważyć użycie kolejek usługi Service Bus,** gdy:

* Rozwiązanie musi być w stanie odbierać wiadomości bez konieczności sondowania kolejki. Za pomocą usługi Service Bus można to osiągnąć za pomocą operacji odbierania długiego sondowania przy użyciu protokołów opartych na TCP obsługiwanych przez usługę Service Bus.
* Rozwiązanie wymaga kolejki, aby zapewnić gwarantowaną dostawę zamówioną przez użytkownika (FIFO).
* Rozwiązanie musi być w stanie obsługiwać automatyczne wykrywanie duplikatów.
* Chcesz, aby aplikacja przetwarzała wiadomości jako równoległe strumienie długo działające (wiadomości są skojarzone ze strumieniem przy użyciu [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) właściwości w wiadomości). W tym modelu każdy węzeł w aplikacji zużywającej konkuruje o strumienie, w przeciwieństwie do komunikatów. Gdy strumień jest przekazywanie do węzła zużywającego, węzeł może sprawdzić stan stanu strumienia aplikacji przy użyciu transakcji.
* Rozwiązanie wymaga zachowania transakcyjnego i niepodzielności podczas wysyłania lub odbierania wielu wiadomości z kolejki.
* Aplikacja obsługuje komunikaty, które mogą przekraczać 64 KB, ale prawdopodobnie nie zbliży się do limitu 256 KB.
* Należy do czynienia z wymogiem, aby zapewnić model dostępu oparty na rolach do kolejek i różne prawa/uprawnienia dla nadawców i odbiorców. Aby uzyskać więcej informacji zobacz następujące artykuły:
    - [Uwierzytelnianie przy użyciu tożsamości zarządzanych](service-bus-managed-service-identity.md)
    - [Uwierzytelnianie przy użyciu aplikacji](authenticate-application.md)
* Rozmiar kolejki nie przekroczy 80 GB.
* Chcesz użyć protokołu komunikacji opartej na standardach AMQP 1.0. Aby uzyskać więcej informacji na temat usługi AMQP, zobacz [Omówienie usługi AMQP magistrali usług](service-bus-amqp-overview.md).
* Można wyobrazić sobie ewentualną migrację z komunikacji punkt-punkt opartej na kolejce do wzorca wymiany komunikatów, który umożliwia bezproblemową integrację dodatkowych odbiorników (subskrybentów), z których każdy otrzymuje niezależne kopie niektórych lub wszystkich wiadomości wysłanych do kolejki. Ten ostatni odnosi się do możliwości publikowania/subskrybowania natywnie świadczonych przez usługę Service Bus.
* Rozwiązanie do obsługi wiadomości musi być w stanie obsługiwać gwarancję dostarczania "At-Most-Once" bez konieczności tworzenia dodatkowych składników infrastruktury.
* Chcesz mieć możliwość publikowania i używania partii komunikatów.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Porównywanie kolejek magazynu i kolejek usługi Service Bus
Tabele w poniższych sekcjach zapewniają logiczne grupowanie funkcji kolejki i umożliwiają porównanie, na pierwszy rzut oka, możliwości dostępnych zarówno w kolejkach usługi Azure Storage, jak i w kolejkach usługi Service Bus.

## <a name="foundational-capabilities"></a>Podstawowe możliwości
W tej sekcji porównano niektóre podstawowe możliwości kolejkowania zapewniane przez kolejki magazynu i kolejki usługi Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Gwarancja zamówienia |**Nie** <br/><br>Aby uzyskać więcej informacji, zobacz pierwszą notatkę w sekcji "Informacje dodatkowe".</br> |**Tak - Pierwszy na pierwszy wysięg (FIFO)**<br/><br>(za pomocą sesji wiadomości) |
| Gwarancja dostawy |**Co najmniej raz** |**At-Least-Once** (za pomocą trybu odbioru PeekLock - jest to domyślne) <br/><br/>**At-Most-Once** (przy użyciu receiveAndDelete tryb odbioru) <br/> <br/> Dowiedz się więcej o różnych [trybach odbioru](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Obsługa operacji atomowych |**Nie** |**Tak**<br/><br/> |
| Odbieranie zachowania |**Nieblokujące**<br/><br/>(kończy się natychmiast, jeśli nie znaleziono nowej wiadomości) |**Blokowanie z/bez limitu czasu**<br/><br/>(oferuje długie sondowanie lub ["Technika Komety")](https://go.microsoft.com/fwlink/?LinkId=613759)<br/><br/>**Nieblokujące**<br/><br/>(tylko za pomocą zarządzanego interfejsu API platformy .NET) |
| Interfejs API w stylu wypychaniem |**Nie** |**Tak**<br/><br/>[QueueClient.OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) i [MessageSessionHandler.OnMessage](/dotnet/api/microsoft.servicebus.messaging.messagesessionhandler.onmessage#Microsoft_ServiceBus_Messaging_MessageSessionHandler_OnMessage_Microsoft_ServiceBus_Messaging_MessageSession_Microsoft_ServiceBus_Messaging_BrokeredMessage__) sesji .NET API. |
| Tryb odbierania |**Zajrzeć & dzierżawy** |**Zajrzeć & lock**<br/><br/>**Odbierz & usuń** |
| Tryb wyłącznego dostępu |**Oparte na dzierżawie** |**Oparte na blokadzie** |
| Czas trwania dzierżawy/blokady |**30 sekund (domyślnie)**<br/><br/>**7 dni (maksymalnie)** (można odnowić lub zwolnić dzierżawę wiadomości za pomocą interfejsu API [UpdateMessage).](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) |**60 sekund (domyślnie)**<br/><br/>Blokadę wiadomości można odnowić za pomocą interfejsu API [RenewLock.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) |
| Precyzja dzierżawy/blokady |**Poziom wiadomości**<br/><br/>(każda wiadomość może mieć inną wartość limitu czasu, którą można następnie zaktualizować w razie potrzeby podczas przetwarzania wiadomości, za pomocą interfejsu API [UpdateMessage)](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) |**Poziom kolejki**<br/><br/>(każda kolejka ma dokładność blokady zastosowane do wszystkich jego komunikatów, ale można odnowić blokadę za pomocą interfejsu API [RenewLock.)](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) |
| Odbiór wsadowe |**Tak**<br/><br/>(jawnie określając liczbę wiadomości podczas pobierania wiadomości, maksymalnie 32 wiadomości) |**Tak**<br/><br/>(niejawnie umożliwiające pobieranie właściwości wstępnej lub jawnie za pomocą transakcji) |
| Wysyłanie wsadowe |**Nie** |**Tak**<br/><br/>(poprzez wykorzystanie transakcji lub wsadowania po stronie klienta) |

### <a name="additional-information"></a>Dodatkowe informacje
* Wiadomości w kolejkach magazynu są zazwyczaj first-in-first-out, ale czasami mogą być nie w porządku; na przykład po wygaśnięciu limitu czasu widoczności wiadomości (na przykład w wyniku awarii aplikacji klienckiej podczas przetwarzania). Po upływie limitu czasu widoczności wiadomość staje się ponownie widoczna w kolejce dla innego pracownika, aby go przekierować. W tym momencie nowo widoczna wiadomość może zostać umieszczona w kolejce (do ponownego usunięcia z kolejki) po wiadomości, która została pierwotnie ujmowana w kolejce po niej.
* Gwarantowany wzorzec FIFO w kolejkach usługi Service Bus wymaga użycia sesji obsługi wiadomości. W przypadku awarii aplikacji podczas przetwarzania wiadomości odebranej w trybie **blokady wglądu &** następnym razem, gdy odbiorca kolejki zaakceptuje sesję obsługi wiadomości, rozpocznie się od komunikatu, który nie powiększy się po upływie okresu wygaśnięcia.In the moment that the application crashes while processing a message received in the Peek & Lock mode, the next time a queue receiver accepts a messaging session, it will start with the failed message after its time-to-live (TTL) period expires.
* Kolejki magazynu są przeznaczone do obsługi standardowych scenariuszy kolejkowania, takich jak oddzielenie składników aplikacji w celu zwiększenia skalowalności i tolerancji dla awarii, bilansowania obciążenia i tworzenia przepływów pracy procesu.
* Niespójności w odniesieniu do obsługi wiadomości w kontekście sesji usługi Service Bus można uniknąć za pomocą stanu sesji do przechowywania stanu aplikacji w stosunku do postępu obsługi sekwencji komunikatów sesji i przy użyciu transakcji wokół rozliczania odebranych wiadomości i aktualizowanie stanu sesji. Ten rodzaj funkcji spójności jest czasami oznaczony *dokładnie raz przetwarzania* w produktach innych dostawców, ale błędy transakcji będzie oczywiście powodować wiadomości do ponownego dostarczenia i dlatego termin nie jest dokładnie odpowiednie.
* Kolejki magazynu zapewniają jednolity i spójny model programowania w kolejkach, tabelach i bloków BLOB — zarówno dla deweloperów, jak i dla zespołów operacyjnych.
* Kolejki usługi Service Bus zapewniają obsługę transakcji lokalnych w kontekście jednej kolejki.
* Tryb **odbierania i usuwania** obsługiwany przez usługę Service Bus umożliwia zmniejszenie liczby operacji obsługi wiadomości (i związanego z nimi kosztu) w zamian za obniżoną gwarancję dostarczenia.
* Kolejki magazynu zapewniają dzierżawy z możliwością rozszerzenia dzierżawy dla wiadomości. Dzięki temu pracownicy mogą utrzymywać krótkie dzierżawy wiadomości. W związku z tym jeśli pracownik ulegnie awarii, wiadomość może być szybko przetwarzane ponownie przez innego pracownika. Ponadto pracownik może przedłużyć dzierżawę wiadomości, jeśli musi przetworzyć ją dłużej niż bieżący czas dzierżawy.
* Kolejki magazynu oferują limit czasu widoczności, który można ustawić na umieszczanie w kolejce lub usuwanie z kolejki wiadomości. Ponadto można zaktualizować komunikat z różnymi wartościami dzierżawy w czasie wykonywania i zaktualizować różne wartości w wiadomościach w tej samej kolejce. Limity czasu blokady usługi Service Bus są definiowane w metadanych kolejki; jednak można odnowić blokadę, wywołując [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) metody.
* Maksymalny limit czasu dla operacji odbierania blokowania w kolejkach usługi Service Bus wynosi 24 dni. Jednak limity czasu oparte na rest mają maksymalną wartość 55 sekund.
* Przetwarzanie wsadowe po stronie klienta dostarczane przez usługę Service Bus umożliwia klientowi kolejki wsadowanie wielu komunikatów w jedną operację wysyłania. Przetwarzanie wsadowe jest dostępne tylko dla operacji wysyłania asynchroniczowego.
* Funkcje, takie jak pułap 200 TB kolejek magazynu (więcej podczas wirtualizacji kont) i nieograniczone kolejki sprawiają, że jest to idealna platforma dla dostawców SaaS.
* Kolejki magazynu zapewniają elastyczny i wydajny mechanizm kontroli dostępu delegowanego.

## <a name="advanced-capabilities"></a>Zaawansowane możliwości
W tej sekcji porównano zaawansowane możliwości zapewniane przez kolejki magazynu i kolejki usługi Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Zaplanowane dostarczanie |**Tak** |**Tak** |
| Automatyczne napisy martwe |**Nie** |**Tak** |
| Zwiększanie wartości czasu oczekiwania na żywo w kolejce |**Tak**<br/><br/>(poprzez aktualizację limitu czasu widoczności w miejscu) |**Tak**<br/><br/>(dostarczane za pośrednictwem dedykowanej funkcji API) |
| Obsługa wiadomości Poison |**Tak** |**Tak** |
| Aktualizacja w miejscu |**Tak** |**Tak** |
| Dziennik transakcji po stronie serwera |**Tak** |**Nie** |
| Dane magazynu |**Tak**<br/><br/>**Metryki minut:** zapewnia metryki w czasie rzeczywistym dostępności, TPS, liczby wywołań interfejsu API, liczby błędów i więcej, wszystko w czasie rzeczywistym (zagregowane na minutę i zgłaszane w ciągu kilku minut od tego, co właśnie wydarzyło się w środowisku produkcyjnym. Aby uzyskać więcej informacji, zobacz [Informacje o metrykach analizy magazynu](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Tak**<br/><br/>(zbiorcze zapytania, wywołując [GetQueues)](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues) |
| Zarządzanie stanem |**Nie** |**Tak**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Automatyczne przekazywanie wiadomości |**Nie** |**Tak** |
| Przeczyszczanie, funkcja kolejki |**Tak** |**Nie** |
| Grupy wiadomości |**Nie** |**Tak**<br/><br/>(za pomocą sesji wiadomości) |
| Stan aplikacji na grupę wiadomości |**Nie** |**Tak** |
| Wykrywanie duplikatów |**Nie** |**Tak**<br/><br/>(konfigurowalne po stronie nadawcy) |
| Przeglądanie grup wiadomości |**Nie** |**Tak** |
| Pobieranie sesji wiadomości według identyfikatora |**Nie** |**Tak** |

### <a name="additional-information"></a>Dodatkowe informacje
* Obie technologie kolejkowania umożliwiają zaplanowanie dostarczenia wiadomości w późniejszym czasie.
* Automatyczne przekazywanie kolejek umożliwia tysiącom kolejek automatyczne przekazywanie ich wiadomości do jednej kolejki, z której aplikacja odbierająca zużywa wiadomość. Tego mechanizmu można użyć do osiągnięcia zabezpieczeń, przepływu kontroli i izolowania magazynu między każdym wydawcą wiadomości.
* Kolejki magazynu zapewniają obsługę aktualizowania zawartości wiadomości. Tej funkcji można użyć do utrwalania informacji o stanie i przyrostowych aktualizacji postępu do wiadomości, dzięki czemu mogą być przetwarzane z ostatniego znanego punktu kontrolnego, zamiast zaczynać od podstaw. W kolejkach usługi Service Bus można włączyć ten sam scenariusz za pomocą sesji wiadomości. Sesje umożliwiają zapisywanie i pobieranie stanu przetwarzania aplikacji (przy użyciu [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) i [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* [Martwe napisy](service-bus-dead-letter-queues.md), które są obsługiwane tylko przez kolejki usługi Service Bus, mogą być przydatne do izolowania wiadomości, które nie mogą być przetwarzane pomyślnie przez aplikację odbierającą lub gdy wiadomości nie mogą dotrzeć do miejsca docelowego z powodu wygasłej właściwości czasu wygaśnięcia (TTL). Wartość TTL określa, jak długo wiadomość pozostaje w kolejce. W usłudze Service Bus wiadomość zostanie przeniesiona do specjalnej kolejki o nazwie $DeadLetterQueue po upływie okresu wygaśnięcia.
* Aby znaleźć "poison" wiadomości w kolejkach magazynu, podczas usuwania z kolejki wiadomości aplikacja sprawdza [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) właściwości wiadomości. Jeśli **DequeueCount** jest większa niż dany próg, aplikacja przenosi wiadomość do kolejki "martwej litery" zdefiniowanej przez aplikację.
* Kolejki magazynu umożliwiają uzyskanie szczegółowego dziennika wszystkich transakcji wykonywanych w kolejce, a także zagregowanych metryk. Obie te opcje są przydatne do debugowania i zrozumienia, jak aplikacja używa kolejek magazynu. Są one również przydatne do dostrajania wydajności aplikacji i zmniejszania kosztów korzystania z kolejek.
* Pojęcie "sesje wiadomości" obsługiwane przez usługę Service Bus umożliwia wiadomości, które należą do określonej grupy logicznej, które mają być skojarzone z danym odbiornikiem, co z kolei tworzy koligacji podobnej do sesji między wiadomościami i ich odpowiednich odbiorców. Tę zaawansowaną funkcję można włączyć w usłudze Service Bus, ustawiając właściwość [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) w komunikacie. Odbiorniki mogą następnie nasłuchiwają identyfikator określonej sesji i odbierać wiadomości, które współużytkuje określony identyfikator sesji.
* Funkcja wykrywania duplikacji obsługiwana przez kolejki usługi Service Bus automatycznie usuwa zduplikowane wiadomości wysyłane do kolejki lub tematu na podstawie wartości [messageid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) właściwości.

## <a name="capacity-and-quotas"></a>Zdolności produkcyjne i przydziały
W tej sekcji porównuje kolejki magazynu i kolejek usługi Service Bus z punktu widzenia [pojemności i przydziałów,](service-bus-quotas.md) które mogą mieć zastosowanie.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Maksymalny rozmiar kolejki |**500 TB**<br/><br/>(ograniczona do [pojemności jednego konta magazynu)](../storage/common/storage-introduction.md#queue-storage) |**Od 1 GB do 80 GB**<br/><br/>(zdefiniowane po utworzeniu kolejki i [włączaniu partycjonowania](service-bus-partitioning.md) – zobacz sekcję "Informacje dodatkowe") |
| Maksymalny rozmiar wiadomości |**64 KB**<br/><br/>(48 KB podczas korzystania z kodowania **Base64)**<br/><br/>Platforma Azure obsługuje duże wiadomości, łącząc kolejki i obiekty BLOB — w tym momencie można wyliczyć w kolejce do 200 GB dla pojedynczego elementu. |**256 KB** lub **1 MB**<br/><br/>(w tym zarówno nagłówek, jak i treść, maksymalny rozmiar nagłówka: 64 KB).<br/><br/>Zależy od [warstwy usług](service-bus-premium-messaging.md). |
| Maksymalny czas wygaśnięcia wiadomości |**Infinite** (od wersji api 2017-07-27) |**Czasspan.Max** |
| Maksymalna liczba kolejek |**Nieograniczona liczba** |**10 000**<br/><br/>(na obszar nazw usługi) |
| Maksymalna liczba równoczesnych klientów |**Nieograniczona liczba** |**Nieograniczona liczba**<br/><br/>(100 równoczesnych połączeń ma zastosowanie tylko do komunikacji opartej na protokole TCP) |

### <a name="additional-information"></a>Dodatkowe informacje
* Usługa Service Bus wymusza limity rozmiaru kolejki. Maksymalny rozmiar kolejki jest określony podczas tworzenia kolejki i może mieć wartość od 1 do 80 GB. Jeśli zostanie osiągnięta wartość rozmiaru kolejki ustawiona podczas tworzenia kolejki, dodatkowe wiadomości przychodzące zostaną odrzucone, a kod wywołujący zostanie odebrany przez kod wywołujący. Aby uzyskać więcej informacji na temat przydziałów w usłudze Service Bus, zobacz [Przydziały magistrali usług](service-bus-quotas.md).
* Partycjonowanie nie jest obsługiwane w [warstwie Premium](service-bus-premium-messaging.md). W warstwie Standardowa można utworzyć kolejki usługi Service Bus w rozmiarach 1, 2, 3, 4 lub 5 GB (wartość domyślna to 1 GB). W warstwie Standardowa z włączoną partycjonowaniem (co jest ustawieniem domyślnym) usługa Service Bus tworzy 16 partycji dla każdego określonego GB. W związku z tym, jeśli utworzysz kolejkę o rozmiarze 5 GB, z 16 partycjami maksymalny rozmiar kolejki staje się (5 * 16) = 80 GB. Maksymalny rozmiar podzielonej na partycje kolejki lub tematu można wyświetlić, przeglądając jego wpis w [witrynie Azure portal][Azure portal].
* W przypadku kolejek magazynu, jeśli zawartość wiadomości nie jest bezpieczna dla XML, musi ona być zakodowana w **formacie Base64.** Jeśli **base64**-koduje wiadomość, ładunek użytkownika może być do 48 KB, zamiast 64 KB.
* W kolejkach usługi Service Bus każda wiadomość przechowywana w kolejce składa się z dwóch części: nagłówka i treści. Całkowity rozmiar wiadomości nie może przekraczać maksymalnego rozmiaru wiadomości obsługiwanego przez warstwę usług.
* Gdy klienci komunikują się z kolejkami usługi Service Bus za pomocą protokołu TCP, maksymalna liczba równoczesnych połączeń z jedną kolejką usługi Service Bus jest ograniczona do 100. Numer ten jest współużytkowane przez nadawców i odbiorców. Jeśli ten przydział zostanie osiągnięty, kolejne żądania dotyczące dodatkowych połączeń zostaną odrzucone, a wyjątek zostanie odebrany przez kod wywołujący. Ten limit nie jest nałożony na klientów łączących się z kolejkami przy użyciu interfejsu API opartego na rest.
* Jeśli potrzebujesz więcej niż 10 000 kolejek w jednym obszarze nazw usługi Service Bus, możesz skontaktować się z zespołem pomocy technicznej platformy Azure i poprosić o zwiększenie. Aby przeskalować poza 10 000 kolejek za pomocą usługi Service Bus, można również utworzyć dodatkowe obszary nazw za pomocą [portalu Azure.][Azure portal]

## <a name="management-and-operations"></a>Zarządzanie i operacje
W tej sekcji porównano funkcje zarządzania udostępniane przez kolejki magazynu i kolejki usługi Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Protokół zarządzania |**ODPOCZYNEK przez HTTP/HTTPS** |**ODPOCZYNEK przez HTTPS** |
| Protokół środowiska wykonawczego |**ODPOCZYNEK przez HTTP/HTTPS** |**ODPOCZYNEK przez HTTPS**<br/><br/>**Standard AMQP 1.0 (TCP z TLS)** |
| Interfejs API .NET |**Tak**<br/><br/>(.NET Interfejs API klienta magazynu) |**Tak**<br/><br/>(.NET Service Bus API) |
| Natywny język C++ |**Tak** |**Tak** |
| Interfejs API języka Java |**Tak** |**Tak** |
| PHP API |**Tak** |**Tak** |
| Node.js API |**Tak** |**Tak** |
| Obsługa dowolnych metadanych |**Tak** |**Nie** |
| Reguły nazewnictwa kolejek |**Do 63 znaków**<br/><br/>(Litery w nazwie kolejki muszą być małe litery.) |**Do 260 znaków**<br/><br/>(Ścieżki i nazwy kolejek są niewrażliwe na nie uwzględnianie wielkości liter). |
| Pobierz funkcję długości kolejki |**Tak**<br/><br/>(Przybliżona wartość, jeśli wiadomości wygasną poza czas wygaśnięcia bez usuwania). |**Tak**<br/><br/>(Dokładna wartość punktu w czasie). |
| Zajrzeć, funkcja |**Tak** |**Tak** |

### <a name="additional-information"></a>Dodatkowe informacje
* Kolejki magazynu zapewniają obsługę dowolnych atrybutów, które mogą być stosowane do opisu kolejki w postaci par nazwy/wartości.
* Obie technologie kolejek oferują możliwość wglądu do wiadomości bez konieczności blokowania go, co może być przydatne podczas implementowania narzędzia eksploratora/przeglądarki kolejki.
* Interfejsy API obsługi wiadomości obsługiwane przez usługę Service Bus .NET wykorzystują połączenia TCP w trybie pełnego dupleksu w celu zwiększenia wydajności w porównaniu z restemyduj za pośrednictwem protokołu HTTP i obsługują standardowy protokół AMQP 1.0.
* Nazwy kolejek magazynu mogą mieć długość od 3 do 63 znaków, mogą zawierać małe litery, cyfry i łączniki. Aby uzyskać więcej informacji, zobacz [Nazywanie kolejek i metadanych](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Nazwy kolejek usługi Service Bus mogą mieć długość do 260 znaków i mniej restrykcyjne reguły nazewnictwa. Nazwy kolejek usługi Service Bus mogą zawierać litery, cyfry, kropki, łączniki i podkreślenia.

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja
W tej sekcji omówiono funkcje uwierzytelniania i autoryzacji obsługiwane przez kolejki magazynu i kolejek usługi Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Authentication |**Klucz symetryczny** |**Klucz symetryczny** |
| Model zabezpieczeń |Delegowany dostęp za pośrednictwem tokenów sygnatury dostępu Współdzielonego. |Sas |
| Federacja dostawców tożsamości |**Nie** |**Tak** |

### <a name="additional-information"></a>Dodatkowe informacje
* Każde żądanie do jednej z technologii kolejkowania musi być uwierzytelnione. Kolejki publiczne z dostępem anonimowym nie są obsługiwane. Za pomocą [sygnatury dostępu](service-bus-sas.md)Współdzielonego , można rozwiązać ten scenariusz, publikując tylko do zapisu sygnatury dostępu Współdzielonego, tylko do odczytu, a nawet sas z pełnym dostępem.
* Schemat uwierzytelniania zapewniany przez kolejki magazynu obejmuje użycie klucza symetrycznego, który jest kodem uwierzytelniania wiadomości opartego na mieszaniu (HMAC), obliczonym za pomocą algorytmu SHA-256 i zakodowanym jako ciąg **Base64.** Aby uzyskać więcej informacji na temat odpowiedniego protokołu, zobacz [Uwierzytelnianie dla usług Azure Storage Services](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Kolejki usługi Service Bus obsługują podobny model przy użyciu kluczy symetrycznych. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie podpisu dostępu współdzielonego za pomocą usługi Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Podsumowanie
Dzięki głębszemu zrozumieniu tych dwóch technologii, będziesz mógł podjąć bardziej świadomą decyzję, która technologia kolejki użyć i kiedy. Decyzja o tym, kiedy używać kolejek magazynu lub kolejek usługi Service Bus, zależy wyraźnie od wielu czynników. Czynniki te mogą zależeć w dużej mierze od indywidualnych potrzeb aplikacji i jej architektury. Jeśli aplikacja korzysta już z podstawowych funkcji platformy Microsoft Azure, możesz wybrać kolejki magazynu, zwłaszcza jeśli wymagana jest podstawowa komunikacja i komunikacja między usługami lub potrzebujesz kolejek o rozmiarze większym niż 80 GB.

Ponieważ kolejki usługi Service Bus zapewniają szereg zaawansowanych funkcji, takich jak sesje, transakcje, wykrywanie duplikatów, automatyczne dead-lettering i trwałe możliwości publikowania/subskrybowania, mogą być preferowanym wyborem, jeśli budujesz aplikację hybrydową lub jeśli aplikacja w inny sposób wymaga tych funkcji.

## <a name="next-steps"></a>Następne kroki
Poniższe artykuły zawierają więcej wskazówek i informacji dotyczących korzystania z kolejek magazynu lub kolejek usługi Service Bus.

* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak korzystać z usługi magazynu kolejek](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Najważniejsze wskazówki dotyczące poprawy wydajności przy użyciu obsługi wiadomości obsługiwanych przez usługę Service Bus](service-bus-performance-improvements.md)
* [Wprowadzenie kolejek i tematów w usłudze Azure Service Bus (wpis w blogu)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [Przewodnik dla deweloperów do autobusu serwisowego](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Korzystanie z usługi kolejkowania na platformie Azure](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

