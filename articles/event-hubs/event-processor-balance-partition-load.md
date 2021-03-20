---
title: Równoważenie obciążenia partycji w wielu wystąpieniach — Event Hubs platformy Azure | Microsoft Docs
description: Opisuje sposób równoważenia obciążenia partycji w wielu wystąpieniach aplikacji przy użyciu procesora zdarzeń i zestawu Azure Event Hubs SDK.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: af307058d0eda6b96c0811bccc245c09e2bdd27d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025048"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Równoważenie obciążenia partycji w wielu wystąpieniach aplikacji
Aby skalować aplikację do przetwarzania zdarzeń, można uruchomić wiele wystąpień aplikacji i zrównoważyć obciążenie między sobą. W starszych wersjach [klasy eventprocessorhost](event-hubs-event-processor-host.md) można zrównoważyć obciążenie między wieloma wystąpieniami zdarzeń programu i punktów kontrolnych podczas otrzymywania. W nowszych wersjach (5,0), **EventProcessorClient** (.NET i Java) lub **EventHubConsumerClient** (Python i JavaScript) umożliwiają wykonywanie tych samych czynności. Model programistyczny jest prostszy przy użyciu zdarzeń. Zasubskrybuj zdarzenia, które Cię interesują, rejestrując procedurę obsługi zdarzeń. Jeśli używasz starej wersji biblioteki klienckiej, zapoznaj się z następującymi przewodnikami migracji: [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/migration-guide.md), [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/servicebus/azure-servicebus/migration_guide.md)i [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/migrationguide.md).

W tym artykule opisano przykładowy scenariusz używany przez wiele wystąpień do odczytu zdarzeń z centrum zdarzeń, a następnie udostępniamy szczegółowe informacje o funkcjach klienta procesora zdarzeń, co umożliwia odbieranie zdarzeń z wielu partycji jednocześnie i równoważenie obciążenia z innymi konsumentami korzystającymi z tego samego centrum zdarzeń i grupy odbiorców.

> [!NOTE]
> Klucz do skalowania dla Event Hubs jest pomysłem dla użytkowników z podziałem na partycje. W przeciwieństwie do wzorca [konkurujących odbiorców](/previous-versions/msp-n-p/dn568101(v=pandp.10)) , partycjonowany wzorzec klienta umożliwia wysoką skalowalność, usuwając wąskie gardła rywalizacji i ułatwiając zakończenie równoległości.

## <a name="example-scenario"></a>Przykładowy scenariusz

W przykładowym scenariuszu należy rozważyć firmową firmę zabezpieczeń, która monitoruje 100 000 domy. Co minutę pobiera dane z różnych czujników, takich jak wykrywanie ruchu, czujnik otwartych drzwi/okien, detektory szkła i tak dalej, które są instalowane w każdym domu. Firma udostępnia witrynę sieci Web dla rezydentów do monitorowania aktywności ich domu w czasie niemal rzeczywistym.

Każdy czujnik wypychanie danych do centrum zdarzeń. Centrum zdarzeń jest skonfigurowane z 16 partycjami. W celu korzystania z programu wymagany jest mechanizm, który może odczytywać te zdarzenia, konsolidować je (filtrowanie, agregowanie itd.) i zrzutować agregacji do magazynu obiektów blob, który jest następnie rzutowany na stronę sieci Web przyjaznych dla użytkownika.

## <a name="write-the-consumer-application"></a>Napisz aplikację konsumenta

Podczas projektowania użytkownika w środowisku rozproszonym scenariusz musi obsługiwać następujące wymagania:

1. **Skala:** Utwórz wielu odbiorców, z których każdy konsument przejmuje prawo do odczytu z kilku Event Hubs partycji.
2. **Równoważenie obciążenia:** Dynamiczne zwiększanie lub zmniejszanie liczby klientów. Na przykład, gdy do każdego domu zostanie dodany nowy typ czujnika (na przykład wykrywacz tlenku węgla), liczba zdarzeń rośnie. W takim przypadku operator (człowiek) zwiększa liczbę wystąpień odbiorców. Następnie Pula odbiorców może ponownie zrównoważyć liczbę posiadanych partycji, aby udostępnić obciążenie nowo dodanym klientom.
3. **Bezproblemowe wznowienie w przypadku awarii:** Jeśli odbiorca (**konsument a**) zakończy się niepowodzeniem (na przykład w maszynie wirtualnej, na której znajdują się nieoczekiwane awarie klienta), inni klienci mogą wybrać partycje należące do **konsumenta a** i kontynuować. Ponadto punkt kontynuacji, nazywany punktem *kontrolnym* lub *przesunięciem*, powinien znajdować się w dokładnie określonym punkcie, w którym **odbiorca** nie zakończył się niepowodzeniem lub nieco przed nim.
4. **Korzystaj z zdarzeń:** Mimo że poprzednie trzy punkty zajmują się zarządzaniem konsumentem, musi istnieć kod, który umożliwia korzystanie z tych zdarzeń i jest przydatne w odróżnieniu od siebie. Na przykład Agreguj go i Przekaż do magazynu obiektów BLOB.

## <a name="event-processor-or-consumer-client"></a>Procesor zdarzeń lub klient klienta

Nie musisz tworzyć własnego rozwiązania, aby spełnić te wymagania. Te funkcje są oferowane przez zestawy SDK platformy Azure Event Hubs. W zestawach SDK platformy .NET lub Java można używać klienta procesora zdarzeń (EventProcessorClient), a także w języku Python i JavaScript SDK, używając EventHubConsumerClient. W starej wersji zestawu SDK była to host procesora zdarzeń (klasy eventprocessorhost), który obsługuje te funkcje.

W przypadku większości scenariuszy produkcyjnych zaleca się używanie klienta procesora zdarzeń do odczytu i przetwarzania zdarzeń. Klient procesora ma zapewnić niezawodne środowisko do przetwarzania zdarzeń we wszystkich partycjach centrum zdarzeń w sposób bezpieczny i odporny na uszkodzenia, a jednocześnie zapewniając możliwość tworzenia punktów kontrolnych postępu. Klienci procesora zdarzeń mogą współpracować wspólnie w kontekście grupy odbiorców dla danego centrum zdarzeń. Klienci będą automatycznie zarządzać dystrybucją i zrównoważeniem pracy, ponieważ wystąpienia stają się dostępne lub niedostępne dla grupy.

## <a name="partition-ownership-tracking"></a>Śledzenie własności partycji

Wystąpienie procesora zdarzeń zwykle jest właścicielem i przetwarza zdarzenia z co najmniej jednej partycji. Własność partycji jest równomiernie dystrybuowana wśród wszystkich aktywnych wystąpień procesora zdarzeń skojarzonych z połączeniem centrum zdarzeń i grupy konsumentów. 

Każdy procesor zdarzeń otrzymuje unikatowy identyfikator i oświadczenia własność partycji przez dodanie lub zaktualizowanie wpisu w magazynie punktów kontrolnych. Wszystkie wystąpienia procesora zdarzeń komunikują się z tym magazynem okresowo, aby zaktualizować własny stan przetwarzania oraz poznać inne aktywne wystąpienia. Te dane są następnie używane do zrównoważenia obciążenia między aktywnymi procesorami. Nowe wystąpienia mogą przyłączyć się do puli przetwarzania w celu skalowania w górę. Gdy wystąpienia przechodzą z powodu awarii lub skalowania w dół, własność partycji jest bezpiecznie przekazywana do innych aktywnych procesorów.

Rekordy własności partycji w magazynie punktów kontrolnych śledzą Event Hubs przestrzeni nazw, nazwy centrum zdarzeń, grupy odbiorców, identyfikatora procesora zdarzeń (nazywanego również właścicielem), identyfikatora partycji i czas ostatniej modyfikacji.



| Przestrzeń nazw usługi Event Hubs               | Nazwa centrum zdarzeń | **Grupa konsumentów** | Właściciel                                | Identyfikator partycji | Godzina ostatniej modyfikacji  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | odbiorca    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | odbiorca    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | odbiorca    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | odbiorca    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Każde wystąpienie procesora zdarzeń uzyskuje własność partycji i zaczyna przetwarzanie partycji od ostatniego znanego [punktu kontrolnego](# Checkpointing). Jeśli procesor ulegnie awarii (zamykanie maszyny wirtualnej), inne wystąpienia wykrywają je, sprawdzając czas ostatniej modyfikacji. Inne wystąpienia próbują uzyskać własność partycji, które wcześniej należały do nieaktywnego wystąpienia, i magazyn punktów kontrolnych gwarantuje, że tylko jedno z wystąpień powiodło się w przypadku zajmowania własności partycji. Tak więc w dowolnym momencie istnieje co najwyżej jeden procesor, który odbiera zdarzenia z partycji.

## <a name="receive-messages"></a>Odbieranie komunikatów

Podczas tworzenia procesora zdarzeń należy określić funkcje, które będą przetwarzać zdarzenia i błędy. Każde wywołanie funkcji, która przetwarza zdarzenia, zapewnia pojedyncze zdarzenie z określonej partycji. Jest odpowiedzialny za obsługę tego zdarzenia. Aby upewnić się, że konsument przetwarza każdy komunikat co najmniej raz, należy napisać własny kod z logiką ponawiania. Należy zachować ostrożność w przypadku skażonych komunikatów.

Zalecamy, aby można było stosunkowo szybko pracować. Oznacza to, że możliwie jak najmniejszej ilości przetwarzania. Jeśli zachodzi potrzeba zapisu do magazynu i przeprowadzenia pewnych routingu, lepiej jest użyć dwóch grup konsumenckich i dwóch procesorów zdarzeń.

## <a name="checkpointing"></a>Tworzenie punktów kontrolnych

*Punkt kontrolny* jest procesem, przez który procesor zdarzeń oznacza lub zatwierdza pozycję ostatniego pomyślnie przetworzonego zdarzenia w ramach partycji. Oznaczanie punktu kontrolnego jest zwykle wykonywane w ramach funkcji, która przetwarza zdarzenia i występuje w odniesieniu do partycji w obrębie grupy odbiorców. 

Jeśli procesor zdarzeń rozłącza połączenie z partycji, inne wystąpienie może wznowić przetwarzanie partycji w punkcie kontrolnym, który został wcześniej przekazany przez ostatni procesor tej partycji w tej grupie odbiorców. Po nawiązaniu połączenia z procesorem przekazuje przesunięcie do centrum zdarzeń, aby określić lokalizację, w której ma zostać rozpoczęte odczytywanie. W ten sposób można użyć punktów kontrolnych do obu oznaczania zdarzeń jako "ukończone" przez aplikacje podrzędne i zapewnienia odporności, gdy procesor zdarzeń ulegnie awarii. Istnieje możliwość powrotu do starszych danych przez określenie niższego przesunięcia z tego procesu tworzenia punktów kontrolnych. 

Gdy punkt kontrolny jest wykonywany do oznaczania zdarzenia jako przetworzone, wpis w magazynie punktów kontrolnych jest dodawany lub aktualizowany z przesunięciem i numerem sekwencyjnym zdarzenia. Użytkownicy powinni określić częstotliwość aktualizowania punktu kontrolnego. Aktualizacja po każdym pomyślnie przetworzonym zdarzeniu może mieć wpływ na wydajność i koszty, ponieważ wyzwala operację zapisu w źródłowym magazynie punktów kontrolnych. Ponadto punkt kontrolny każdego pojedynczego zdarzenia ma wskaźnik wzorca komunikatów umieszczonych w kolejce, dla którego Kolejka Service Bus może być lepszym rozwiązaniem niż centrum zdarzeń. Event Hubs poniżej zawarto "co najmniej raz" dostarczanie na dużą skalę. Dzięki wykorzystaniu systemów podrzędnych idempotentne można łatwo odzyskać z błędów lub ponownych uruchomień, co spowoduje, że te same zdarzenia są odbierane wiele razy.

> [!NOTE]
> Jeśli używasz platformy Azure Blob Storage jako magazynu punktów kontrolnych w środowisku obsługującym inną wersję zestawu SDK magazynu obiektów BLOB niż te, które są zwykle dostępne na platformie Azure, musisz użyć kodu, aby zmienić wersję interfejsu API usługi magazynu na określoną wersję obsługiwaną przez to środowisko. Na przykład jeśli używasz [Event Hubs w centrum Azure Stack w wersji 2002](/azure-stack/user/event-hubs-overview), najwyższa dostępna wersja usługi Storage to wersja 2017-11-09. W takim przypadku należy użyć kodu, aby docelowa wersja interfejsu API usługi Storage do 2017-11-09. Aby zapoznać się z przykładem dotyczącym konkretnej wersji interfejsu API usługi Storage, zobacz następujące przykłady w witrynie GitHub: 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) lub  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

## <a name="thread-safety-and-processor-instances"></a>Bezpieczeństwo wątków i wystąpienia procesora

Domyślnie funkcja, która przetwarza zdarzenia jest wywoływana sekwencyjnie dla danej partycji. Kolejne zdarzenia i wywołania tej funkcji z tej samej kolejki partycji w tle, gdy pompa zdarzeń będzie działać w tle w innych wątkach. Zdarzenia z różnych partycji można przetwarzać współbieżnie, a wszystkie Stany udostępnione, do których dostęp odbywa się między partycjami, muszą być synchronizowane.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące przewodniki szybki start:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
