---
title: Porównanie kolejek usług Azure Storage i Service Bus
description: Analizuje różnice i podobieństwa między dwoma typami kolejek oferowanych przez platformę Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a64000741de68518dd459b105a093ccf4cb6ab7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85337641"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Porównanie kolejek magazynu i kolejek usługi Service Bus
Ten artykuł analizuje różnice i podobieństwa między dwoma typami kolejek oferowanych przez Microsoft Azure Dzisiaj: kolejkami magazynu i kolejkami Service Bus. Korzystając z tych informacji, można porównać i rozróżnić odpowiednie technologie i mieć możliwość podejmowania bardziej świadomej decyzji o tym, które rozwiązanie najlepiej odpowiada Twoim potrzebom.

## <a name="introduction"></a>Wprowadzenie
Platforma Azure obsługuje dwa typy mechanizmów kolejki: **kolejki magazynu** i **kolejki Service Bus**.

**Kolejki magazynu**, które są częścią infrastruktury [usługi Azure Storage](https://azure.microsoft.com/services/storage/) , oferują prosty, oparty na REST interfejs get/put/Peek, zapewniając niezawodne, trwałe komunikaty w ramach i między usługami.

**Kolejki Service Bus** są częścią szerszej infrastruktury [usługi Azure Messaging](https://azure.microsoft.com/services/service-bus/) , która obsługuje Kolejkowanie, a także publikowanie/subskrybowanie i bardziej zaawansowane wzorce integracji. Więcej informacji o Service Bus kolejkach/tematach/subskrypcjach znajduje się w temacie [omówienie Service Bus](service-bus-messaging-overview.md).

Chociaż obie technologie kolejkowania istnieją jednocześnie, kolejki magazynu zostały najpierw wprowadzone jako dedykowany mechanizm magazynu kolejki oparty na usługach Azure Storage. Kolejki Service Bus są zbudowane na podstawie szerszej infrastruktury obsługi komunikatów zaprojektowanej do integrowania aplikacji lub składników aplikacji, które mogą obejmować wiele protokołów komunikacyjnych, kontraktów danych, domen zaufania i/lub środowisk sieciowych.

## <a name="technology-selection-considerations"></a>Zagadnienia dotyczące wyboru technologii
Kolejki magazynu i kolejki Service Bus są implementacjami usługi kolejkowania komunikatów oferowaną obecnie przez Microsoft Azure. Każdy z nich ma nieco inny zestaw funkcji, co oznacza, że można wybrać jedną lub drugą lub użyć obu, w zależności od potrzeb konkretnego rozwiązania lub problemu biznesowego, który rozwiązujesz.

Podczas określania, która technologia kolejkowania pasuje do przeznaczenia dla danego rozwiązania, architektzy rozwiązań i deweloperzy powinni wziąć pod uwagę te zalecenia. Aby uzyskać więcej informacji, zobacz następną sekcję.

Jako architekt rozwiązań/deweloper należy **rozważyć użycie kolejek usługi Storage** w przypadku:

* Aplikacja musi przechowywać ponad 80 GB komunikatów w kolejce.
* Aplikacja chce śledzić postęp przetwarzania komunikatu w kolejce. Jest to przydatne, jeśli proces roboczy przetwarza komunikat awarii. Kolejny proces roboczy może następnie użyć tych informacji, aby kontynuować, gdzie poprzedni proces roboczy został pozostawiony.
* Wymagane są dzienniki po stronie serwera dla wszystkich transakcji wykonywanych względem kolejek.

Jako architekt rozwiązań/deweloper należy **rozważyć użycie kolejek Service Bus** , gdy:

* Twoje rozwiązanie musi mieć możliwość otrzymywania komunikatów bez konieczności sondowania kolejki. Za pomocą Service Bus można to osiągnąć za pomocą długotrwałej operacji odbioru przy użyciu protokołów opartych na protokole TCP obsługiwanych przez Service Bus.
* Twoje rozwiązanie wymaga, aby Kolejka zapewniała zagwarantowane dostarczenie pierwszego początku (FIFO).
* Twoje rozwiązanie musi być w stanie obsługiwać automatyczne wykrywanie duplikatów.
* Aplikacja ma przetwarzać komunikaty jako Parallel długotrwałe strumienie (komunikaty są skojarzone ze strumieniem przy użyciu właściwości [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) w komunikacie). W tym modelu każdy węzeł w aplikacji zużywanej konkuruje o strumienie, w przeciwieństwie do komunikatów. Gdy strumień jest przyznany w węźle zużywanym, węzeł może przeanalizować stan strumienia aplikacji przy użyciu transakcji.
* Twoje rozwiązanie wymaga transakcyjnego zachowania i niepodzielności podczas wysyłania lub otrzymywania wielu komunikatów z kolejki.
* Aplikacja obsługuje komunikaty, które mogą przekraczać 64 KB, ale prawdopodobnie nie zbliża się do limitu 256 KB.
* Istnieje wymóg, aby zapewnić model dostępu oparty na rolach dla kolejek i różne prawa/uprawnienia dla nadawców i odbiorników. Aby uzyskać więcej informacji zobacz następujące artykuły:
    - [Uwierzytelnianie przy użyciu tożsamości zarządzanych](service-bus-managed-service-identity.md)
    - [Uwierzytelnianie przy użyciu aplikacji](authenticate-application.md)
* Rozmiar kolejki nie przekroczy 80 GB.
* Chcesz użyć protokołu obsługi komunikatów opartych na standardach AMQP 1,0. Aby uzyskać więcej informacji na temat AMQP, zobacz [Service Bus AMQP Overview](service-bus-amqp-overview.md).
* Istnieje możliwość Envision migracji z komunikacji punkt-punkt do wzorca wymiany komunikatów, który umożliwia bezproblemową integrację dodatkowych odbiorników (subskrybentów), z których każdy otrzymuje niezależne kopie niektórych lub wszystkich komunikatów wysyłanych do kolejki. Drugie odnosi się do możliwości publikowania/subskrybowania natywnie udostępniane przez Service Bus.
* Rozwiązanie do obsługi komunikatów musi być w stanie obsługiwać gwarancję dostarczenia "at-najwyżej raz" bez konieczności tworzenia dodatkowych składników infrastruktury.
* Chcesz mieć możliwość publikowania i używania partii komunikatów.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Porównywanie kolejek magazynu i kolejek Service Bus
Tabele w poniższych sekcjach zawierają logiczne grupowanie funkcji kolejki oraz pozwalają na szybkie porównanie możliwości dostępnych zarówno w kolejkach usługi Azure Storage, jak i w kolejkach Service Bus.

## <a name="foundational-capabilities"></a>Funkcje podstawowe
W tej sekcji porównano niektóre podstawowe możliwości kolejkowania zapewniane przez kolejki magazynu i kolejki Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Gwarancja porządkowania |**Nie** <br/><br>Aby uzyskać więcej informacji, zobacz pierwszą uwagę w sekcji "dodatkowe informacje".</br> |**Tak — pierwszy w pierwszej kolejności (FIFO)**<br/><br>(za pomocą sesji obsługi komunikatów) |
| Gwarancja dostarczania |**Co najmniej raz** |**Co najmniej raz** (przy użyciu trybu odbierania PeekLock — jest to ustawienie domyślne) <br/><br/>**Co najwyżej raz** (przy użyciu trybu odbierania ReceiveAndDelete) <br/> <br/> Dowiedz się więcej na temat różnych [trybów odbierania](service-bus-queues-topics-subscriptions.md#receive-modes)  |
| Obsługa niepodzielnych operacji |**Nie** |**Tak**<br/><br/> |
| Zachowanie odbierania |**Bez blokowania**<br/><br/>(wykonuje natychmiast, jeśli nie zostanie znaleziony nowy komunikat) |**Blokowanie z limitem czasu/bez**<br/><br/>(oferuje długotrwałe sondowanie lub ["technikę" Comet "](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Bez blokowania**<br/><br/>(tylko w przypadku korzystania z zarządzanego interfejsu API platformy .NET) |
| Interfejs API w stylu wypychania |**Nie** |**Tak**<br/><br/>[QueueClient. OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) i [MessageSessionHandler. OnMessage](/dotnet/api/microsoft.servicebus.messaging.messagesessionhandler.onmessage#Microsoft_ServiceBus_Messaging_MessageSessionHandler_OnMessage_Microsoft_ServiceBus_Messaging_MessageSession_Microsoft_ServiceBus_Messaging_BrokeredMessage__) Sessions .NET API. |
| Tryb odbioru |**Wgląd w & dzierżawy** |**Wgląd & blokadę**<br/><br/>**Odbierz & usunąć** |
| Wyłączny tryb dostępu |**Oparte na dzierżawie** |**Na podstawie blokady** |
| Czas trwania dzierżawy/blokady |**30 sekund (wartość domyślna)**<br/><br/>**7 dni (maksymalnie) (maksymalnie)** (można odnowić lub zwolnić dzierżawę wiadomości za pomocą interfejsu API [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) ). |**60 sekund (wartość domyślna)**<br/><br/>Blokadę wiadomości można odnowić przy użyciu interfejsu API [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) . |
| Precyzja dzierżawy/blokady |**Poziom komunikatu**<br/><br/>(każdy komunikat może mieć inną wartość limitu czasu, którą można następnie zaktualizować w razie konieczności podczas przetwarzania komunikatu przy użyciu interfejsu API [UpdateMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.updatemessage) ). |**Poziom kolejki**<br/><br/>(Każda kolejka ma dokładnooć blokady zastosowana do wszystkich swoich komunikatów, ale można odnowić blokadę przy użyciu interfejsu API [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) ). |
| Odbierane wsadowo |**Tak**<br/><br/>(jawnie Określanie liczby komunikatów podczas pobierania komunikatów, maksymalnie 32 komunikatów) |**Tak**<br/><br/>(niejawnie włączenie właściwości poprzedzającego pobranie lub jawne użycie transakcji) |
| Wysyłanie wsadowe |**Nie** |**Tak**<br/><br/>(w przypadku korzystania z transakcji lub wykonywania operacji wsadowych po stronie klienta) |

### <a name="additional-information"></a>Dodatkowe informacje
* Komunikaty w kolejkach magazynu są zwykle pierwszym wyjściem, ale czasami mogą być poza kolejnością. na przykład po upływie limitu czasu wyświetlania komunikatu (na przykład w wyniku awarii aplikacji klienta podczas przetwarzania). Po upływie limitu czasu widoczności komunikat zostaje ponownie widoczny w kolejce dla innego procesu roboczego, aby go usunąć. W tym momencie nowo widoczny komunikat może zostać umieszczony w kolejce (do ponownego odkolejkowania) po komunikacie, który był pierwotnie przemieszczony po nim.
* W kolejkach Service Bus gwarantowana wzorzec FIFO wymaga użycia sesji obsługi komunikatów. W przypadku awarii aplikacji podczas przetwarzania komunikatu odebranego w trybie **wglądu & tryb blokowania** , gdy odbiornik kolejki akceptuje sesję obsługi komunikatów, rozpocznie się z komunikatem o błędzie po upływie okresu czasu wygaśnięcia (TTL).
* Kolejki magazynu zostały zaprojektowane z myślą o obsłudze standardowych scenariuszy kolejkowania, takich jak składniki aplikacji do oddzielania, aby zwiększyć skalowalność i odporność na awarie, obciążać i kompilować przepływy pracy procesów.
* Niespójności w odniesieniu do obsługi komunikatów w kontekście Service Bus sesji można uniknąć przy użyciu stanu sesji w celu przechowywania stanu aplikacji względem postępu obsługi sekwencji komunikatów sesji oraz przy użyciu transakcji dotyczących rozliczania odebranych komunikatów i aktualizowania stanu sesji. Ten rodzaj funkcji spójności jest czasami oznaczany *dokładnie jednokrotnym przetwarzaniem* w produktach innych dostawców, ale niepowodzenia transakcji spowodują, że komunikaty zostaną ponownie dostarczone i w związku z tym termin nie jest dokładnie odpowiedni.
* Kolejki magazynu zapewniają jednolity i spójny model programowania między kolejkami, tabelami i obiektami BLOB — zarówno dla deweloperów, jak i dla zespołów operacyjnych.
* Kolejki Service Bus zapewniają obsługę lokalnych transakcji w kontekście pojedynczej kolejki.
* Tryb **odbioru i usuwania** obsługiwany przez Service Bus umożliwia zredukowanie liczby operacji obsługi komunikatów (i powiązanego kosztu) w programie Exchange w celu obniżenia poziomu gwarancji dostarczania.
* Kolejki magazynu zapewniają dzierżawy z możliwością rozszerania dzierżaw dla komunikatów. Dzięki temu procesy robocze mogą obsługiwać krótkie dzierżawy komunikatów. W takim przypadku w przypadku awarii procesu roboczego komunikat może być szybko przetwarzany przez inny proces roboczy. Ponadto proces roboczy może wydłużyć dzierżawę w wiadomości, jeśli musi przetworzyć ją dłużej niż bieżący czas dzierżawy.
* Kolejki magazynu oferują wartość limitu czasu widoczności, którą można ustawić przy umieszczenie lub wykorzystaniu komunikatu. Ponadto można aktualizować komunikat z różnymi wartościami dzierżawy w czasie wykonywania i aktualizować różne wartości między komunikatami w tej samej kolejce. Limity czasu blokady Service Bus są zdefiniowane w metadanych kolejki; można jednak odnowić blokadę, wywołując metodę [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) .
* Maksymalny limit czasu dla operacji odbierania blokowania w kolejkach Service Bus wynosi 24 dni. Limity czasu REST mają jednak wartość maksymalną 55 sekund.
* Przetwarzanie wsadowe po stronie klienta zapewniane przez Service Bus umożliwia klientowi kolejki przetwarzanie wsadowe wielu komunikatów w ramach jednej operacji wysyłania. Przetwarzanie wsadowe jest dostępne tylko dla asynchronicznych operacji wysyłania.
* Funkcje, takie jak limit 200 TB kolejek magazynu (więcej podczas wirtualizacji kont) i nieograniczoną liczbę kolejek sprawiają, że jest to idealna platforma dla dostawców SaaS.
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
| Metryki magazynu |**Tak**<br/><br/>**Metryki minut**: udostępniają metryki w czasie rzeczywistym dotyczące dostępności, TPS, liczby wywołań interfejsu API, liczby błędów i nie tylko w czasie rzeczywistym (zagregowane na minutę i raportowane w ciągu kilku minut od tego, co się stało w środowisku produkcyjnym). Aby uzyskać więcej informacji, zobacz [Informacje o analityka magazynu metrykach](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Tak**<br/><br/>(zapytania zbiorcze przez wywołanie metody [Getqueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Zarządzanie stanem |**Nie** |**Tak**<br/><br/>[Microsoft. ServiceBus. Messaging. EntityStatus. Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. ServiceBus. Messaging. EntityStatus. disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. ServiceBus. Messaging. EntityStatus. SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft. ServiceBus. Messaging. EntityStatus. ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Funkcja autoprzekazywania komunikatów |**Nie** |**Tak** |
| Funkcja przeczyszczania kolejki |**Tak** |**Nie** |
| Grupy komunikatów |**Nie** |**Tak**<br/><br/>(za pomocą sesji obsługi komunikatów) |
| Stan aplikacji na grupę komunikatów |**Nie** |**Tak** |
| Wykrywanie duplikatów |**Nie** |**Tak**<br/><br/>(możliwe do skonfigurowania po stronie nadawcy) |
| Przeglądanie grup komunikatów |**Nie** |**Tak** |
| Pobieranie sesji komunikatów według identyfikatora |**Nie** |**Tak** |

### <a name="additional-information"></a>Dodatkowe informacje
* Obie technologie kolejkowania umożliwiają zaplanowanie dostarczania wiadomości w późniejszym czasie.
* Funkcja autoprzekazywania kolejki umożliwia tysiącom kolejek przekazanie wiadomości do pojedynczej kolejki, z której aplikacja otrzymująca korzysta z wiadomości. Tego mechanizmu można użyć do osiągnięcia zabezpieczeń, przepływu sterowania i izolowania magazynu między każdym wydawcą wiadomości.
* Kolejki magazynu zapewniają obsługę aktualizowania zawartości komunikatów. Za pomocą tej funkcji można zachować informacje o stanie i przyrostowe aktualizacje postępu w komunikacie, aby można było je przetworzyć z ostatniego znanego punktu kontrolnego, zamiast zaczynać od zera. Za pomocą kolejek Service Bus można włączyć ten sam scenariusz przy użyciu sesji komunikatów. Sesje umożliwiają zapisywanie i pobieranie stanu przetwarzania aplikacji (przy użyciu funkcji [setstate](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) i [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* [Utracona](service-bus-dead-letter-queues.md)wiadomość, która jest obsługiwana tylko przez kolejki Service Bus, może być przydatna do izolowania komunikatów, które nie mogą zostać przetworzone pomyślnie przez aplikację otrzymującą lub gdy wiadomości nie mogą dotrzeć do swojego miejsca docelowego ze względu na wygasłą właściwość czasu wygaśnięcia (TTL). Wartość czasu wygaśnięcia określa, jak długo komunikat pozostaje w kolejce. Za pomocą Service Bus wiadomość zostanie przeniesiona do specjalnej kolejki o nazwie $DeadLetterQueue po upływie okresu TTL.
* Aby znaleźć komunikaty "trujące" w kolejkach magazynu, podczas usuwania z kolejki komunikatu aplikacja analizuje Właściwość [DequeueCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage.dequeuecount) komunikatu. Jeśli **DequeueCount** jest większa niż dany próg, aplikacja przenosi komunikat do kolejki "utraconych" aplikacji.
* Kolejki magazynu umożliwiają uzyskanie szczegółowego dziennika wszystkich transakcji wykonywanych względem kolejki, a także zagregowanych metryk. Obie te opcje są przydatne w debugowaniu i zrozumieniu, jak Twoja aplikacja korzysta z kolejek magazynu. Są one również przydatne do dostrajania wydajności aplikacji i zmniejszania kosztów korzystania z kolejek.
* Koncepcja "sesji komunikatów" obsługiwana przez Service Bus umożliwia skojarzenie komunikatów należących do określonej grupy logicznej z danym odbiornikiem, co z kolei tworzy koligację podobną do sesji między komunikatami a ich odpowiednimi odbiornikami. Możesz włączyć tę funkcję zaawansowaną w Service Bus przez ustawienie właściwości [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) w komunikacie. Odbiorcy mogą następnie nasłuchiwać określonego identyfikatora sesji i odbierać komunikaty, które współużytkują określony identyfikator sesji.
* Funkcje wykrywania duplikacji obsługiwane przez kolejki Service Bus automatycznie usuwają zduplikowane komunikaty wysyłane do kolejki lub tematu na podstawie wartości właściwości [MessageID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) .

## <a name="capacity-and-quotas"></a>Pojemność i limity przydziału
W tej sekcji porównano kolejki magazynu i kolejki Service Bus z perspektywy [pojemności i przydziałów](service-bus-quotas.md) , które mogą być stosowane.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Maksymalny rozmiar kolejki |**500 TB**<br/><br/>(ograniczone do [jednej pojemności konta magazynu](../storage/common/storage-introduction.md#queue-storage)) |**1 GB do 80 GB**<br/><br/>(zdefiniowane podczas tworzenia kolejki i [włączania partycjonowania](service-bus-partitioning.md) — Zobacz sekcję "informacje dodatkowe"). |
| Maksymalny rozmiar komunikatu |**64 KB**<br/><br/>(48 KB przy użyciu kodowania **Base64** )<br/><br/>Platforma Azure obsługuje duże komunikaty przez połączenie kolejek i obiektów BLOB — w tym momencie można umieścić w kolejce do 200 GB dla pojedynczego elementu. |**256 KB** lub **1 MB**<br/><br/>(w tym nagłówek i treść, maksymalny rozmiar nagłówka: 64 KB).<br/><br/>Zależy od [warstwy usług](service-bus-premium-messaging.md). |
| Maksymalny czas wygaśnięcia komunikatu |**Nieskończony** (w przypadku interfejsu API w wersji 2017-07-27) |**TimeSpan. Max** |
| Maksymalna liczba kolejek |**Nieograniczona liczba** |**10 000**<br/><br/>(na przestrzeń nazw usługi) |
| Maksymalna liczba jednoczesnych klientów |**Nieograniczona liczba** |**Nieograniczona liczba**<br/><br/>(100 limit połączeń współbieżnych dotyczy tylko komunikacji opartej na protokole TCP) |

### <a name="additional-information"></a>Dodatkowe informacje
* Service Bus wymusza limity rozmiaru kolejki. Maksymalny rozmiar kolejki jest określany podczas tworzenia kolejki i może mieć wartość z przedziału od 1 do 80 GB. Jeśli zostanie osiągnięta wartość rozmiaru kolejki ustawioną podczas tworzenia kolejki, dodatkowe komunikaty przychodzące zostaną odrzucone i zostanie odebrany wyjątek przez wywoływany kod. Aby uzyskać więcej informacji na temat przydziałów w Service Bus, zobacz [Service Bus przydziałów](service-bus-quotas.md).
* Partycjonowanie nie jest obsługiwane w [warstwie Premium](service-bus-premium-messaging.md). W warstwie Standardowa można utworzyć kolejki Service Bus w rozmiarze 1, 2, 3, 4 lub 5 GB (wartość domyślna to 1 GB). W warstwie Standardowa z włączonym partycjonowaniem (co jest ustawieniem domyślnym) Service Bus tworzy 16 partycji dla każdej określonej GB. W związku z tym, jeśli utworzysz kolejkę o rozmiarze 5 GB, a w przypadku 16 partycji zostanie osiągnięty maksymalny rozmiar kolejki (5 * 16) = 80 GB. Możesz zobaczyć maksymalny rozmiar kolejki lub tematu partycjonowanego, przeglądając jego wpis na [Azure Portal][Azure portal].
* W przypadku kolejek magazynu, jeśli zawartość wiadomości nie jest bezpieczna w formacie XML, musi być zakodowana w **formacie base64** . W przypadku kodowania **Base64**komunikat, ładunek użytkownika może mieć do 48 KB, a nie 64 KB.
* W przypadku kolejek Service Bus każdy komunikat przechowywany w kolejce składa się z dwóch części: nagłówka i treści. Łączny rozmiar wiadomości nie może przekroczyć maksymalnego rozmiaru komunikatów obsługiwanego przez warstwę usług.
* Gdy klienci komunikują się z kolejkami Service Bus za pośrednictwem protokołu TCP, Maksymalna liczba jednoczesnych połączeń z pojedynczą kolejką Service Bus jest ograniczona do 100. Ta liczba jest udostępniana między nadawcami i odbiornikami. Jeśli limit przydziału zostanie osiągnięty, kolejne żądania dla dodatkowych połączeń zostaną odrzucone i zostanie odebrany wyjątek przez wywoływany kod. Ten limit nie jest nakładany na klientów nawiązujących połączenie z kolejkami za pomocą interfejsu API opartego na interfejsie REST.
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
* Interfejsy API komunikatów obsługiwanych przez brokera programu Service Bus .NET wykorzystują pełne połączenia TCP, aby zwiększyć wydajność w porównaniu do usługi REST za pośrednictwem protokołu HTTP i obsługują protokół AMQP 1,0 Standard.
* Nazwy kolejek magazynu mogą składać się z co najwyżej 3-63 znaków, mogą zawierać małe litery, cyfry i łączniki. Aby uzyskać więcej informacji, zobacz [nazywanie kolejek i metadanych](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Nazwy kolejek Service Bus mogą mieć długość do 260 znaków i mają mniej restrykcyjne reguły nazewnictwa. Nazwy kolejek Service Bus mogą zawierać litery, cyfry, kropki, łączniki i podkreślenia.

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja
W tej sekcji omówiono funkcje uwierzytelniania i autoryzacji obsługiwane przez kolejki magazynu i kolejki Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Authentication |**Klucz symetryczny** |**Klucz symetryczny** |
| Model zabezpieczeń |Delegowany dostęp za pośrednictwem tokenów SAS. |SYGNATUR |
| Federacja dostawcy tożsamości |**Nie** |**Tak** |

### <a name="additional-information"></a>Dodatkowe informacje
* Każde żądanie dotyczące każdej z technologii kolejkowania musi zostać uwierzytelnione. Kolejki publiczne z dostępem anonimowym nie są obsługiwane. Korzystając z [sygnatury dostępu współdzielonego](service-bus-sas.md), można rozwiązać ten scenariusz, publikując sygnaturę dostępu współdzielonego tylko do odczytu, czyli SAS, do której istnieje wiele SAS.
* Schemat uwierzytelniania dostarczany przez kolejki magazynu obejmuje użycie klucza symetrycznego, który jest kod uwierzytelniania wiadomości oparty na skrótach (HMAC), obliczany przy użyciu algorytmu SHA-256 i zakodowany jako ciąg **Base64** . Aby uzyskać więcej informacji na temat odpowiedniego protokołu, zobacz [uwierzytelnianie dla usług Azure Storage](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Kolejki Service Bus obsługują podobny model przy użyciu kluczy symetrycznych. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie sygnatury dostępu współdzielonego za pomocą Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Podsumowanie
Dzięki dokładniejszemu zrozumieniu tych dwóch technologii będziesz mieć możliwość podejmowania bardziej świadomej decyzji o tym, której technologii kolejki użyć i kiedy. Podejmowanie decyzji o tym, kiedy należy używać kolejek usługi Storage lub kolejek Service Bus, zależy od wielu czynników. Te czynniki mogą zależeć od indywidualnych potrzeb aplikacji i jej architektury. Jeśli aplikacja korzysta już z podstawowych możliwości Microsoft Azure, warto wybrać kolejno pozycje kolejki magazynu, zwłaszcza jeśli wymagana jest podstawowa komunikacja i obsługa komunikatów między usługami lub potrzebna jest większa liczba kolejek niż 80 GB.

Ponieważ kolejki Service Bus udostępniają wiele zaawansowanych funkcji, takich jak sesje, transakcje, wykrywanie duplikatów, automatyczne utracone wiadomości i trwałe funkcje publikowania/subskrybowania, mogą one być preferowanym wyborem w przypadku kompilowania aplikacji hybrydowej lub w przypadku, gdy aplikacja wymaga tych funkcji.

## <a name="next-steps"></a>Następne kroki
Poniższe artykuły zawierają dodatkowe wskazówki i informacje dotyczące korzystania z kolejek usługi Storage lub kolejek Service Bus.

* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak korzystać z usługi Queue Storage](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Najlepsze rozwiązania dotyczące ulepszeń wydajności przy użyciu Service Bus komunikatów obsługiwanych przez brokera](service-bus-performance-improvements.md)
* [Wprowadzenie do kolejek i tematów w Azure Service Bus (wpis w blogu)](https://www.serverless360.com/blog/azure-service-bus-queues-vs-topics)
* [Przewodnik dewelopera do Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Korzystanie z usługi kolejkowania na platformie Azure](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com

