---
title: Porównanie kolejek usług Azure Storage i Service Bus
description: Analizuje różnice i podobieństwa między dwoma typami kolejek oferowanych przez platformę Azure.
ms.topic: article
ms.date: 11/04/2020
ms.openlocfilehash: 31992aa2012009c51cbeae78010ae8ced65fc872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96928311"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Porównanie kolejek magazynu i kolejek usługi Service Bus
W tym artykule przeanalizowano różnice i podobieństwa między dwoma typami kolejek oferowanych przez Microsoft Azure: kolejki magazynu i kolejki Service Bus. Korzystając z tych informacji, można podejmować bardziej świadome decyzje dotyczące tego, które rozwiązanie najlepiej odpowiada Twoim potrzebom.

## <a name="introduction"></a>Wprowadzenie
Platforma Azure obsługuje dwa typy mechanizmów kolejki: **kolejki magazynu** i **kolejki Service Bus**.

**Kolejki magazynu** są częścią infrastruktury [usługi Azure Storage](https://azure.microsoft.com/services/storage/) . Umożliwiają one przechowywanie dużej liczby komunikatów. Użytkownik uzyskuje dostęp do komunikatów z dowolnego miejsca na świecie za pośrednictwem uwierzytelnionych połączeń przy użyciu protokołu HTTP lub HTTPS. Komunikat w kolejce może mieć rozmiar do 64 KB. Kolejka może zawierać miliony komunikatów, do łącznego limitu pojemności konta magazynu. Kolejki są często używane do tworzenia zaległości prac do przetwarzania asynchronicznego. Aby uzyskać więcej informacji, zobacz [co to są kolejki usługi Azure Storage](../storage/queues/storage-queues-introduction.md).

**Kolejki Service Bus** są częścią szerszej infrastruktury [usługi Azure Messaging](https://azure.microsoft.com/services/service-bus/) , która obsługuje Kolejkowanie, publikowanie/subskrybowanie i bardziej zaawansowane wzorce integracji. Są one przeznaczone do integrowania aplikacji lub składników aplikacji, które mogą obejmować wiele protokołów komunikacyjnych, kontraktów danych, domen zaufania lub środowisk sieciowych. Więcej informacji o Service Bus kolejkach/tematach/subskrypcjach znajduje się w temacie [Service Bus Queues, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md).


## <a name="technology-selection-considerations"></a>Zagadnienia dotyczące wyboru technologii
Kolejki magazynu i kolejki Service Bus mają nieco inny zestaw funkcji. Możesz wybrać jedno lub oba, w zależności od potrzeb danego rozwiązania.

Podczas określania, która technologia kolejkowania pasuje do przeznaczenia danego rozwiązania, architektzy rozwiązań i deweloperzy powinni wziąć pod uwagę te zalecenia. 

### <a name="consider-using-storage-queues"></a>Rozważ użycie kolejek magazynu
Jako architekt rozwiązań/deweloper należy **rozważyć użycie kolejek usługi Storage** w przypadku:

* Aplikacja musi przechowywać ponad 80 gigabajtów komunikatów w kolejce.
* Aplikacja chce śledzić postęp przetwarzania komunikatu w kolejce. Jest to przydatne, jeśli proces roboczy przetwarza komunikat awarii. Inny proces roboczy może następnie użyć tych informacji, aby kontynuować, gdzie poprzedni proces roboczy został pozostawiony.
* Wymagane są dzienniki po stronie serwera dla wszystkich transakcji wykonywanych względem kolejek.

### <a name="consider-using-service-bus-queues"></a>Rozważ użycie kolejek Service Bus
Jako architekt rozwiązań/deweloper należy **rozważyć użycie kolejek Service Bus** , gdy:

* Twoje rozwiązanie musi odbierać komunikaty bez konieczności sondowania kolejki. Service Bus można osiągnąć za pomocą długotrwałej operacji odbioru przy użyciu protokołów opartych na protokole TCP, które Service Bus obsługiwane.
* Twoje rozwiązanie wymaga, aby Kolejka zapewniała zagwarantowane dostarczenie pierwszego początku (FIFO).
* Twoje rozwiązanie musi obsługiwać automatyczne wykrywanie duplikatów.
* Aplikacja ma przetwarzać komunikaty jako Parallel długotrwałe strumienie (komunikaty są skojarzone ze strumieniem przy użyciu właściwości [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) w komunikacie). W tym modelu każdy węzeł w aplikacji zużywanej konkuruje o strumienie, w przeciwieństwie do komunikatów. Gdy strumień jest przyznany w węźle zużywanym, węzeł może przeanalizować stan strumienia aplikacji przy użyciu transakcji.
* Twoje rozwiązanie wymaga transakcyjnego zachowania i niepodzielności podczas wysyłania lub otrzymywania wielu komunikatów z kolejki.
* Aplikacja obsługuje komunikaty, które mogą przekraczać 64 KB, ale najkorzystniej nie zbliża się do limitu 256 KB.
* Istnieje wymóg, aby zapewnić model dostępu oparty na rolach dla kolejek i różne prawa/uprawnienia dla nadawców i odbiorników. Aby uzyskać więcej informacji, zobacz następujące artykuły:
    - [Uwierzytelnianie przy użyciu tożsamości zarządzanych](service-bus-managed-service-identity.md)
    - [Uwierzytelnianie przy użyciu aplikacji](authenticate-application.md)
* Rozmiar kolejki nie będzie większy niż 80 GB.
* Chcesz użyć protokołu obsługi komunikatów opartych na standardach AMQP 1,0. Aby uzyskać więcej informacji na temat AMQP, zobacz [Service Bus AMQP Overview](service-bus-amqp-overview.md).
* Envision z punktu widzenia komunikacji punkt-punktowego z kolejką na potrzeby przesyłania komunikatów publikowania i subskrybowania. Ten wzorzec umożliwia integrację dodatkowych odbiorników (subskrybentów). Każdy odbiornik odbiera niezależne kopie niektórych lub wszystkich komunikatów wysyłanych do kolejki. 
* Rozwiązanie do obsługi komunikatów musi obsługiwać gwarancję dostarczenia "at-najwyżej raz" bez konieczności tworzenia dodatkowych składników infrastruktury.
* Twoje rozwiązanie musi publikować partie komunikatów i korzystać z nich.

## <a name="compare-storage-queues-and-service-bus-queues"></a>Porównaj kolejki magazynu i kolejki Service Bus
Tabele w poniższych sekcjach zawierają logiczne grupowanie funkcji kolejki. Umożliwiają one szybkie porównanie możliwości dostępnych zarówno w kolejkach usługi Azure Storage, jak i w kolejkach Service Bus.

## <a name="foundational-capabilities"></a>Funkcje podstawowe
W tej sekcji porównano niektóre podstawowe możliwości kolejkowania zapewniane przez kolejki magazynu i kolejki Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Gwarancja porządkowania |**Nie** <br/><br>Aby uzyskać więcej informacji, zobacz pierwszą uwagę w sekcji [Informacje dodatkowe](#additional-information) .</br> | **Tak — pierwszy w pierwszej kolejności (FIFO)**<br/><br>(za pomocą [sesji komunikatów](message-sessions.md)) |
| Gwarancja dostarczania |**Co najmniej raz** |**Co najmniej raz** (przy użyciu trybu odbierania PeekLock. Jest to ustawienie domyślne) <br/><br/>**Co najwyżej raz** (przy użyciu trybu odbierania ReceiveAndDelete) <br/> <br/> Dowiedz się więcej na temat różnych [trybów odbierania](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Obsługa niepodzielnych operacji |**Nie** |**Tak**<br/><br/> |
| Zachowanie odbierania |**Bez blokowania**<br/><br/>(wykonuje natychmiast, jeśli nie zostanie znaleziony nowy komunikat) |**Blokowanie z limitem czasu lub bez niego**<br/><br/>(oferuje długotrwałe sondowanie lub ["technikę" Comet "](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Bez blokowania**<br/><br/>(tylko w przypadku korzystania z zarządzanego interfejsu API platformy .NET) |
| Interfejs API w stylu wypychania |**Nie** |**Tak**<br/><br/>[QueueClient. OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) i [MessageSessionHandler. OnMessage](/dotnet/api/microsoft.servicebus.messaging.messagesessionhandler.onmessage#Microsoft_ServiceBus_Messaging_MessageSessionHandler_OnMessage_Microsoft_ServiceBus_Messaging_MessageSession_Microsoft_ServiceBus_Messaging_BrokeredMessage__) Sessions .NET API. |
| Tryb odbioru |**Wgląd w & dzierżawy** |**Wgląd & blokadę**<br/><br/>**Odbierz & usunąć** |
| Wyłączny tryb dostępu |**Oparte na dzierżawie** |**Na podstawie blokady** |
| Czas trwania dzierżawy/blokady |**30 sekund (wartość domyślna)**<br/><br/>**7 dni (maksymalnie) (maksymalnie)** (można odnowić lub zwolnić dzierżawę wiadomości za pomocą interfejsu API [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) ). |**60 sekund (wartość domyślna)**<br/><br/>Blokadę wiadomości można odnowić przy użyciu interfejsu API [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) . |
| Precyzja dzierżawy/blokady |**Poziom komunikatu**<br/><br/>Każdy komunikat może mieć inną wartość limitu czasu, którą można następnie zaktualizować w razie konieczności podczas przetwarzania komunikatu przy użyciu interfejsu API [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) . |**Poziom kolejki**<br/><br/>(Każda kolejka ma dokładnooć blokady zastosowana do wszystkich swoich komunikatów, ale można odnowić blokadę przy użyciu interfejsu API [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) ). |
| Odbierane wsadowo |**Tak**<br/><br/>(jawnie Określanie liczby komunikatów podczas pobierania komunikatów, maksymalnie 32 komunikatów) |**Tak**<br/><br/>(niejawnie włączenie właściwości poprzedzającego pobranie lub jawne użycie transakcji) |
| Wysyłanie wsadowe |**Nie** |**Tak**<br/><br/>(w przypadku korzystania z transakcji lub wykonywania operacji wsadowych po stronie klienta) |

### <a name="additional-information"></a>Dodatkowe informacje
* Komunikaty w kolejkach magazynu są zwykle pierwszym wyjściem, ale czasami mogą być poza kolejnością. Na przykład po upływie limitu czasu widoczności dla komunikatu, ponieważ aplikacja kliencka uległa awarii podczas przetwarzania komunikatu. Po upływie limitu czasu widoczności komunikat zostaje ponownie widoczny w kolejce dla innego procesu roboczego, aby go usunąć. W tym momencie nowo widoczna wiadomość może zostać umieszczona w kolejce w celu ponownego odkolejkowania.
* W kolejkach Service Bus gwarantowana wzorzec FIFO wymaga użycia sesji obsługi komunikatów. Jeśli aplikacja ulegnie awarii podczas przetwarzania komunikatu odebranego w trybie **wglądu &** , następnym razem, gdy odbiornik kolejki akceptuje sesję obsługi komunikatów, rozpocznie się z komunikatem o błędzie po upływie okresu czasu wygaśnięcia komunikatu.
* Kolejki magazynu są przeznaczone do obsługi standardowych scenariuszy kolejkowania, takich jak następujące:
    - Oddziel składniki aplikacji, aby zwiększyć skalowalność i odporność na awarie
    - Wyrównywanie obciążenia
    - Kompilowanie przepływów pracy procesów.
* Niespójności w odniesieniu do obsługi komunikatów w kontekście Service Bus sesji można uniknąć przy użyciu stanu sesji w celu przechowywania stanu aplikacji względem postępu obsługi sekwencji komunikatów sesji oraz przy użyciu transakcji dotyczących rozliczania odebranych komunikatów i aktualizowania stanu sesji. Ten rodzaj funkcji spójności jest czasami oznaczany *dokładnie po przetworzeniu* w produktach innych dostawców. Wszelkie błędy transakcji spowodują, że komunikaty zostaną dostarczone i dlatego nie jest to dokładnie odpowiednie.
* Kolejki magazynu zapewniają jednolity i spójny model programowania między kolejkami, tabelami i obiektami BLOB — zarówno dla deweloperów, jak i dla zespołów operacyjnych.
* Kolejki Service Bus zapewniają obsługę lokalnych transakcji w kontekście pojedynczej kolejki.
* Tryb **odbioru i usuwania** obsługiwany przez Service Bus umożliwia zredukowanie liczby operacji obsługi komunikatów (i powiązanego kosztu) w programie Exchange w celu obniżenia poziomu gwarancji dostarczania.
* Kolejki magazynu zapewniają dzierżawy z możliwością rozszerania dzierżaw dla komunikatów. Ta funkcja umożliwia procesom roboczym zachowanie krótkich dzierżaw w przypadku komunikatów. Tak więc w przypadku awarii procesu roboczego komunikat może być szybko przetwarzany przez inny proces roboczy. Ponadto proces roboczy może wydłużyć dzierżawę w wiadomości, jeśli musi przetworzyć ją dłużej niż bieżący czas dzierżawy.
* Kolejki magazynu oferują wartość limitu czasu widoczności, którą można ustawić przy umieszczenie lub wykorzystaniu komunikatu. Ponadto można aktualizować komunikat z różnymi wartościami dzierżawy w czasie wykonywania i aktualizować różne wartości między wiadomościami w tej samej kolejce. Limity czasu blokady Service Bus są zdefiniowane w metadanych kolejki. Można jednak odnowić blokadę, wywołując metodę [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) .
* Maksymalny limit czasu dla operacji odbierania blokowania w kolejkach Service Bus wynosi 24 dni. Limity czasu REST mają jednak wartość maksymalną 55 sekund.
* Przetwarzanie wsadowe po stronie klienta zapewniane przez Service Bus umożliwia klientowi kolejki przetwarzanie wsadowe wielu komunikatów w ramach jednej operacji wysyłania. Przetwarzanie wsadowe jest dostępne tylko dla asynchronicznych operacji wysyłania.
* Funkcje, takie jak limit 200-TB kolejek magazynu (więcej podczas wirtualizacji kont) i nieograniczoną liczbę kolejek sprawiają, że jest idealnym platformą dla dostawców SaaS.
* Kolejki magazynu zapewniają elastyczny i wydajny mechanizm kontroli dostępu delegowanego.

## <a name="advanced-capabilities"></a>Zaawansowane możliwości
W tej sekcji porównano zaawansowane możliwości zapewniane przez kolejki magazynu i kolejki Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Zaplanowane dostarczanie |**Tak** |**Tak** |
| Automatyczna litera utraconych wiadomości |**Nie** |**Tak** |
| Zwiększanie wartości czasu wygaśnięcia kolejki |**Tak**<br/><br/>(za pośrednictwem aktualizacji w miejscu limitu czasu widoczności) |**Tak**<br/><br/>(udostępniane za pośrednictwem dedykowanej funkcji interfejsu API) |
| Obsługa skażonych komunikatów |**Tak** |**Tak** |
| Aktualizacja w miejscu |**Tak** |**Tak** |
| Dziennik transakcji po stronie serwera |**Tak** |**Nie** |
| Metryki magazynu |**Tak**<br/><br/>**Metryki minut** zapewniają metryki w czasie rzeczywistym na potrzeby dostępności, TPS, liczby wywołań interfejsu API, liczby błędów i nie tylko. Są one w czasie rzeczywistym agregowane na minutę i raportowane w ciągu kilku minut od tego, co się stało w środowisku produkcyjnym. Aby uzyskać więcej informacji, zobacz [Informacje o analityka magazynu metrykach](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Tak**<br/><br/>(zapytania zbiorcze przez wywołanie metody [Getqueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Zarządzanie stanem |**Nie** |**Tak**<br/><br/>[Microsoft. ServiceBus. Messaging. EntityStatus. Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. ServiceBus. Messaging. EntityStatus. disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. ServiceBus. Messaging. EntityStatus. SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. ServiceBus. Messaging. EntityStatus. ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Autoprzesyłanie komunikatów |**Nie** |**Tak** |
| Funkcja przeczyszczania kolejki |**Tak** |**Nie** |
| Grupy komunikatów |**Nie** |**Tak**<br/><br/>(za pomocą sesji obsługi komunikatów) |
| Stan aplikacji na grupę komunikatów |**Nie** |**Tak** |
| Wykrywanie duplikatów |**Nie** |**Tak**<br/><br/>(możliwe do skonfigurowania po stronie nadawcy) |
| Przeglądanie grup komunikatów |**Nie** |**Tak** |
| Pobieranie sesji komunikatów według identyfikatora |**Nie** |**Tak** |

### <a name="additional-information"></a>Dodatkowe informacje
* Obie technologie kolejkowania umożliwiają zaplanowanie dostarczania wiadomości w późniejszym czasie.
* Kolejkowanie autoprzesyłania dalej umożliwia tysiącom kolejek przekazanie wiadomości do pojedynczej kolejki, z której aplikacja otrzymująca korzysta z wiadomości. Tego mechanizmu można użyć do osiągnięcia zabezpieczeń, przepływu sterowania i izolowania magazynu między każdym wydawcą wiadomości.
* Kolejki magazynu zapewniają obsługę aktualizowania zawartości komunikatów. Za pomocą tej funkcji można zachować informacje o stanie i przyrostowe aktualizacje postępu w komunikacie, aby można było je przetworzyć z ostatniego znanego punktu kontrolnego, zamiast zaczynać od zera. Za pomocą kolejek Service Bus można włączyć ten sam scenariusz przy użyciu sesji komunikatów. Sesje umożliwiają zapisywanie i pobieranie stanu przetwarzania aplikacji (przy użyciu funkcji [setstate](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) i [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* Kolejki Service Bus obsługują [utraconą literę](service-bus-dead-letter-queues.md). Może być przydatne w przypadku izolowania komunikatów, które spełniają następujące kryteria:
    - Nie można pomyślnie przetworzyć komunikatów przez aplikację otrzymującą 
    - Komunikaty nie mogą dotrzeć do swojego miejsca docelowego z powodu wygasłej właściwości czasu wygaśnięcia (TTL). Wartość czasu wygaśnięcia określa, jak długo komunikat pozostaje w kolejce. Za pomocą Service Bus wiadomość zostanie przeniesiona do specjalnej kolejki o nazwie $DeadLetterQueue po upływie okresu TTL.
* Aby znaleźć komunikaty "trujące" w kolejkach magazynu, podczas usuwania z kolejki komunikatu aplikacja analizuje Właściwość [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) komunikatu. Jeśli **DequeueCount** jest większa niż dany próg, aplikacja przenosi komunikat do kolejki "utraconych" aplikacji.
* Kolejki magazynu umożliwiają uzyskanie szczegółowego dziennika wszystkich transakcji wykonywanych względem kolejki oraz zagregowanych metryk. Obie te opcje są przydatne w debugowaniu i zrozumieniu, jak Twoja aplikacja korzysta z kolejek magazynu. Są one również przydatne do dostrajania wydajności aplikacji i zmniejszania kosztów korzystania z kolejek.
* Sesje komunikatów obsługiwane przez Service Bus umożliwiają włączenie komunikatów należących do grupy logicznej, które mają być skojarzone z odbiornikiem. Tworzy koligację podobną do sesji między komunikatami i ich odpowiednimi odbiornikami. Możesz włączyć tę funkcję zaawansowaną w Service Bus przez ustawienie właściwości [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) w komunikacie. Odbiorcy mogą następnie nasłuchiwać określonego identyfikatora sesji i odbierać komunikaty, które współużytkują określony identyfikator sesji.
* Funkcja wykrywania duplikacji w kolejkach Service Bus automatycznie usuwa zduplikowane komunikaty wysyłane do kolejki lub tematu na podstawie wartości właściwości [MessageID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) .

## <a name="capacity-and-quotas"></a>Pojemność i limity przydziału
W tej sekcji porównano kolejki magazynu i kolejki Service Bus z perspektywy [pojemności i przydziałów](service-bus-quotas.md) , które mogą być stosowane.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Maksymalny rozmiar kolejki |**500 TB**<br/><br/>(ograniczone do [jednej pojemności konta magazynu](../storage/common/storage-introduction.md#queue-storage)) |**1 GB do 80 GB**<br/><br/>(zdefiniowane podczas tworzenia kolejki i [włączania partycjonowania](service-bus-partitioning.md) — Zobacz sekcję "informacje dodatkowe"). |
| Maksymalny rozmiar komunikatu |**64 KB**<br/><br/>(48 KB przy użyciu kodowania **Base64** )<br/><br/>Platforma Azure obsługuje duże komunikaty przez połączenie kolejek i obiektów BLOB — w tym momencie można umieścić w kolejce do 200 GB dla pojedynczego elementu. |**256 KB** lub **1 MB**<br/><br/>(w tym nagłówek i treść, maksymalny rozmiar nagłówka: 64 KB).<br/><br/>Zależy od [warstwy usług](service-bus-premium-messaging.md). |
| Maksymalny czas wygaśnięcia komunikatu |**Nieskończony** (interfejs API w wersji 2017-07-27 lub nowszej) |**TimeSpan. Max** |
| Maksymalna liczba kolejek |**Nieograniczona liczba** |**10 000**<br/><br/>(na przestrzeń nazw usługi) |
| Maksymalna liczba jednoczesnych klientów |**Nieograniczona liczba** |**5000** |

### <a name="additional-information"></a>Dodatkowe informacje
* Service Bus wymusza limity rozmiaru kolejki. Maksymalny rozmiar kolejki jest określany podczas tworzenia kolejki. Może wynosić od 1 GB do 80 GB. Jeśli rozmiar kolejki osiągnie ten limit, dodatkowe komunikaty przychodzące zostaną odrzucone, a obiekt wywołujący otrzyma wyjątek. Aby uzyskać więcej informacji na temat przydziałów w Service Bus, zobacz [Service Bus przydziałów](service-bus-quotas.md).
* Partycjonowanie nie jest obsługiwane w [warstwie Premium](service-bus-premium-messaging.md). W warstwie Standardowa można tworzyć Service Bus kolejki i tematy w 1 (wartość domyślna), 2, 3, 4 lub 5 GB. Po włączeniu partycjonowania Service Bus tworzy 16 kopii (16 partycji) jednostki, każdy z określonych rozmiarów. W związku z tym, jeśli utworzysz kolejkę o rozmiarze 5 GB, a w przypadku 16 partycji zostanie osiągnięty maksymalny rozmiar kolejki (5 * 16) = 80 GB.  Możesz zobaczyć maksymalny rozmiar kolejki lub tematu partycjonowanego w [Azure Portal][Azure portal].
* W przypadku kolejek magazynu, jeśli zawartość wiadomości nie jest bezpieczna w formacie XML, musi być zakodowana w **formacie base64** . W przypadku kodowania **Base64** komunikat, ładunek użytkownika może mieć do 48 KB, a nie 64 KB.
* W przypadku kolejek Service Bus każdy komunikat przechowywany w kolejce składa się z dwóch części: nagłówka i treści. Łączny rozmiar wiadomości nie może przekroczyć maksymalnego rozmiaru komunikatu obsługiwanego przez warstwę usług.
* Gdy klienci komunikują się z kolejkami Service Bus za pośrednictwem protokołu TCP, Maksymalna liczba jednoczesnych połączeń z pojedynczą kolejką Service Bus jest ograniczona do 100. Ta liczba jest udostępniana między nadawcami i odbiornikami. Jeśli limit przydziału zostanie osiągnięty, żądania dodatkowych połączeń zostaną odrzucone i zostanie odebrany wyjątek przez wywoływany kod. Ten limit nie jest nakładany na klientów nawiązujących połączenie z kolejkami za pomocą interfejsu API opartego na interfejsie REST.
* Jeśli potrzebujesz więcej niż 10 000 kolejek w pojedynczej przestrzeni nazw Service Bus, możesz skontaktować się z zespołem pomocy technicznej systemu Azure i poprosić o zwiększenie. Aby skalować poza kolejki 10 000 przy użyciu Service Bus, można również utworzyć dodatkowe przestrzenie nazw przy użyciu [Azure Portal][Azure portal].

## <a name="management-and-operations"></a>Zarządzanie i operacje
W tej sekcji porównano funkcje zarządzania udostępniane przez kolejki magazynu i kolejki Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Protokół zarządzania |**REST za pośrednictwem protokołu HTTP/HTTPS** |**REST za pośrednictwem protokołu HTTPS** |
| Protokół uruchomieniowy |**REST za pośrednictwem protokołu HTTP/HTTPS** |**REST za pośrednictwem protokołu HTTPS**<br/><br/>**AMQP 1,0 Standard (TCP z TLS)** |
| Interfejs API .NET |**Tak**<br/><br/>(Interfejs API klienta usługi Storage) |**Tak**<br/><br/>(.NET Service Bus API) |
| Natywny język C++ |**Tak** |**Tak** |
| Interfejs API języka Java |**Tak** |**Tak** |
| INTERFEJS API PHP |**Tak** |**Tak** |
| Interfejs API Node.js |**Tak** |**Tak** |
| Dowolna Obsługa metadanych |**Tak** |**Nie** |
| Reguły nazewnictwa kolejek |**Do 63 znaków**<br/><br/>(Litery w nazwie kolejki muszą być małymi literami). |**Do 260 znaków**<br/><br/>(W ścieżkach i nazwach kolejek nie jest rozróżniana wielkość liter). |
| Pobierz funkcję długości kolejki |**Tak**<br/><br/>(Przybliżona wartość, Jeśli komunikaty wygasną poza czasem wygaśnięcia, bez usuwania). |**Tak**<br/><br/>(Dokładnie wartość wskazywana przez punkt w czasie). |
| Peek — funkcja |**Tak** |**Tak** |

### <a name="additional-information"></a>Dodatkowe informacje
* Kolejki magazynu zapewniają obsługę dowolnych atrybutów, które można zastosować do opisu kolejki, w postaci par nazwa/wartość.
* Obie technologie kolejki oferują możliwość wglądu w komunikat bez konieczności jego zablokowania, co może być przydatne podczas implementowania Eksploratora kolejki/narzędzia przeglądarki.
* Interfejsy API komunikatów obsługiwanych przez brokera programu Service Bus .NET używają pełnych połączeń TCP, aby zwiększyć wydajność w porównaniu do usługi REST za pośrednictwem protokołu HTTP i obsługują protokół AMQP 1,0 Standard.
* Nazwy kolejek magazynu mogą składać się z co najwyżej 3-63 znaków, mogą zawierać małe litery, cyfry i łączniki. Aby uzyskać więcej informacji, zobacz [nazywanie kolejek i metadanych](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Nazwy kolejek Service Bus mogą mieć długość do 260 znaków i mają mniej restrykcyjne reguły nazewnictwa. Nazwy kolejek Service Bus mogą zawierać litery, cyfry, kropki, łączniki i podkreślenia.

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja
W tej sekcji omówiono funkcje uwierzytelniania i autoryzacji obsługiwane przez kolejki magazynu i kolejki Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Authentication |**Klucz symetryczny** |**Klucz symetryczny** |
| Model zabezpieczeń |Delegowany dostęp za pośrednictwem tokenów SAS. |SAS |
| Federacja dostawcy tożsamości |**Nie** |**Tak** |

### <a name="additional-information"></a>Dodatkowe informacje
* Każde żądanie dotyczące każdej z technologii kolejkowania musi zostać uwierzytelnione. Kolejki publiczne z dostępem anonimowym nie są obsługiwane. Korzystając z [sygnatury dostępu współdzielonego](service-bus-sas.md), można rozwiązać ten scenariusz, publikując sygnaturę dostępu współdzielonego tylko do odczytu, czyli SAS, do której istnieje wiele SAS.
* Schemat uwierzytelniania dostarczany przez kolejki magazynu obejmuje użycie klucza symetrycznego. Ten klucz jest oparty na skrócie kod uwierzytelniania wiadomości (HMAC), obliczony przy użyciu algorytmu SHA-256 i zakodowany jako ciąg **Base64** . Aby uzyskać więcej informacji na temat odpowiedniego protokołu, zobacz [uwierzytelnianie dla usług Azure Storage](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Kolejki Service Bus obsługują podobny model przy użyciu kluczy symetrycznych. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie sygnatury dostępu współdzielonego za pomocą Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Podsumowanie
Dzięki dokładniejszemu zrozumieniu tych dwóch technologii można podejmować bardziej świadome decyzje dotyczące technologii kolejek, która ma być używana. Podejmowanie decyzji o tym, kiedy należy używać kolejek usługi Storage lub kolejek Service Bus, zależy od wielu czynników. Te czynniki mogą zależeć od indywidualnych potrzeb aplikacji i jej architektury. 

Być może wolisz wybrać kolejki magazynu z przyczyn takich jak następujące:

- Jeśli aplikacja używa już podstawowych możliwości programu Microsoft Azure 
- Jeśli wymagana jest podstawowa komunikacja i obsługa komunikatów między usługami 
- Wymagane kolejki, które mogą mieć rozmiar większy niż 80 GB

Kolejki Service Bus udostępniają wiele zaawansowanych funkcji, takich jak następujące. Z tego względu mogą być preferowane wybór, jeśli tworzysz aplikację hybrydową lub jeśli aplikacja wymaga tych funkcji w inny sposób.

- [Sesje](message-sessions.md)
- [Transakcje](service-bus-transactions.md)
- [Wykrywanie duplikatów](duplicate-detection.md)
- [Automatyczne utraconych wiadomości](service-bus-dead-letter-queues.md)
- [Trwałe możliwości publikowania i subskrybowania](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) 



## <a name="next-steps"></a>Następne kroki
Poniższe artykuły zawierają dodatkowe wskazówki i informacje dotyczące korzystania z kolejek usługi Storage lub kolejek Service Bus.

* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak korzystać z usługi Queue Storage](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Najlepsze rozwiązania dotyczące ulepszeń wydajności przy użyciu Service Bus komunikatów obsługiwanych przez brokera](service-bus-performance-improvements.md)
* [Wprowadzenie do kolejek i tematów w Azure Service Bus (wpis w blogu)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [Przewodnik dewelopera do Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Korzystanie z usługi kolejkowania na platformie Azure](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

