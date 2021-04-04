---
title: Odbieranie zdarzeń za pomocą hosta procesora zdarzeń — Azure Event Hubs | Microsoft Docs
description: W tym artykule opisano hosta procesora zdarzeń w usłudze Azure Event Hubs, który upraszcza zarządzanie użyciem punktów kontrolnych, dzierżawienia i odczytywania jonu zdarzeń równoległych.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: de5d8f0f8bf9f64a473b18a50434cac83e8e38c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98622066"
---
# <a name="event-processor-host"></a>Host procesora zdarzeń
> [!NOTE]
> Ten artykuł ma zastosowanie do starej wersji usługi Azure Event Hubs SDK. Aby uzyskać aktualną wersję zestawu SDK, zobacz [równoważenia obciążenia partycji w wielu wystąpieniach aplikacji](event-processor-balance-partition-load.md). Aby dowiedzieć się, jak przeprowadzić migrację kodu do nowszej wersji zestawu SDK, zobacz te przewodniki dotyczące migracji. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Skrypt Java](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)

Azure Event Hubs to zaawansowana usługa pozyskiwania danych telemetrycznych, która może służyć do przesyłania strumieniowego milionów zdarzeń przy niskich kosztach. W tym artykule opisano sposób korzystania z zdarzeń pozyskiwanych za pomocą *hosta procesora zdarzeń* (EPH); inteligentny Agent konsumencki, który upraszcza zarządzanie czytnikami punktów kontrolnych, dzierżaw i zdarzeń równoległych.  

Klucz do skalowania dla Event Hubs jest pomysłem dla użytkowników z podziałem na partycje. W przeciwieństwie do wzorca [konkurujących odbiorców](/previous-versions/msp-n-p/dn568101(v=pandp.10)) , partycjonowany wzorzec klienta umożliwia wysoką skalowalność, usuwając wąskie gardła rywalizacji i ułatwiając zakończenie równoległości.

## <a name="home-security-scenario"></a>Scenariusz zabezpieczeń domowych

W przykładowym scenariuszu należy rozważyć firmową firmę zabezpieczeń, która monitoruje 100 000 domy. Co minutę pobiera dane z różnych czujników, takich jak wykrywanie ruchu, czujnik otwartych drzwi/okien, detektory szkła, jak i zainstalowane w każdym domu. Firma udostępnia witrynę sieci Web dla rezydentów do monitorowania aktywności ich domu w czasie niemal rzeczywistym.

Każdy czujnik wypychanie danych do centrum zdarzeń. Centrum zdarzeń jest skonfigurowane z 16 partycjami. W celu korzystania z tego celu wymagany jest mechanizm, który może odczytywać te zdarzenia, konsolidować je (filtrowanie, agregowanie itp.) i zrzutować agregacji do magazynu obiektów blob, który jest następnie rzutowany na stronę sieci Web przyjazną dla użytkownika.

## <a name="write-the-consumer-application"></a>Napisz aplikację konsumenta

Podczas projektowania użytkownika w środowisku rozproszonym scenariusz musi obsługiwać następujące wymagania:

1. **Skala:** Utwórz wielu odbiorców, z których każdy konsument przejmuje prawo do odczytu z kilku Event Hubs partycji.
2. **Równoważenie obciążenia:** Dynamiczne zwiększanie lub zmniejszanie liczby klientów. Na przykład, gdy do każdego domu zostanie dodany nowy typ czujnika (na przykład wykrywacz tlenku węgla), liczba zdarzeń rośnie. W takim przypadku operator (człowiek) zwiększa liczbę wystąpień odbiorców. Następnie Pula odbiorców może ponownie zrównoważyć liczbę posiadanych partycji, aby udostępnić obciążenie nowo dodanym klientom.
3. **Bezproblemowe wznowienie w przypadku awarii:** Jeśli odbiorca (**konsument a**) zakończy się niepowodzeniem (na przykład w maszynie wirtualnej, na której znajdują się nieoczekiwane awarie klienta), inni klienci muszą mieć możliwość pobrania partycji należących do **konsumenta a** i kontynuować. Ponadto punkt kontynuacji, nazywany punktem *kontrolnym* lub *przesunięciem*, powinien znajdować się w dokładnie określonym punkcie, w którym **odbiorca** nie zakończył się niepowodzeniem lub nieco przed nim.
4. **Korzystaj z zdarzeń:** Mimo że poprzednie trzy punkty zajmują się zarządzaniem konsumentem, musi istnieć kod, który umożliwia korzystanie z tych zdarzeń i jest bardziej użyteczny dla niego. na przykład Agreguj go i Przekaż do magazynu obiektów BLOB.

Zamiast tworzyć własne rozwiązanie, Event Hubs udostępnia tę funkcję za pomocą interfejsu [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) oraz klasy [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) .

## <a name="ieventprocessor-interface"></a>IEventProcessor, interfejs

Pierwsze, zużywające aplikacje implementują interfejs  [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) , który ma cztery metody: [openAsync, CloseAsync, ProcessErrorAsync i ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor#methods). Ten interfejs zawiera rzeczywisty kod służący do korzystania z zdarzeń wysyłanych Event Hubs. Poniższy kod pokazuje prostą implementację:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
       Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
       return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
       Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
       return Task.CompletedTask;
     }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
       Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
       return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
       foreach (var eventData in messages)
       {
          var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
             Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
       }
       return context.CheckpointAsync();
    }
}
```

Następnie Utwórz wystąpienie wystąpienia [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) . W zależności od przeciążenia podczas tworzenia wystąpienia [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) w Konstruktorze są używane następujące parametry:

- **hostname:** Nazwa każdego wystąpienia odbiorcy. Każde wystąpienie elementu **klasy eventprocessorhost** musi mieć unikatową wartość tej zmiennej w grupie odbiorców, dlatego nie należy nakodować tej wartości.
- **eventHubPath:** Nazwa centrum zdarzeń.
- **consumerGroupName:** Event Hubs używa **$default** jako nazwy domyślnej grupy odbiorców, ale dobrym sposobem jest utworzenie grupy odbiorców dla określonego aspektu przetwarzania.
- **eventHubConnectionString:** Parametry połączenia z centrum zdarzeń, które można pobrać z Azure Portal. Te parametry połączenia powinny mieć uprawnienia do **nasłuchiwania** w centrum zdarzeń.
- **storageConnectionString:** Konto magazynu używane na potrzeby wewnętrznego zarządzania zasobami.

Na koniec użytkownicy rejestrują wystąpienie [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) za pomocą usługi Event Hubs. Zarejestrowanie klasy procesora zdarzeń z wystąpieniem klasy eventprocessorhost rozpoczyna przetwarzanie zdarzenia. Rejestracja instruuje usługę Event Hubs, aby spodziewać się, że aplikacja konsumencka korzysta z zdarzeń z niektórych partycji i wywołuje kod implementacji [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) za każdym razem, gdy wypycha zdarzenia do użycia. 

> [!NOTE]
> W consumerGroupName jest rozróżniana wielkość liter.  Zmiany w consumerGroupName mogą spowodować odczytanie wszystkich partycji od początku strumienia.

### <a name="example"></a>Przykład

Załóżmy na przykład, że istnieją 5 maszyn wirtualnych przeznaczonych do zużywania zdarzeń oraz prosta aplikacja konsolowa w każdej maszynie wirtualnej, która wykonuje rzeczywiste zużycie. Każda aplikacja konsolowa tworzy jedno wystąpienie [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) i rejestruje je w usłudze Event Hubs.

W tym przykładowym scenariuszu Załóżmy, że 16 partycji są przydzielono do 5 wystąpień **klasy eventprocessorhost** . Niektóre wystąpienia **klasy eventprocessorhost** mogą być właścicielami kilku innych partycji niż inne. Dla każdej partycji, do której należy wystąpienie **klasy eventprocessorhost** , tworzy wystąpienie `SimpleEventProcessor` klasy. W związku z tym istnieje 16 wystąpień `SimpleEventProcessor` ogólnych z jednym przypisanym do każdej partycji.

Poniższa lista zawiera podsumowanie tego przykładu:

- 16 Event Hubs partycjach.
- 5 maszyn wirtualnych, 1 aplikacja konsumencka (na przykład Consumer.exe) w każdej maszynie wirtualnej.
- 5 zarejestrowanych wystąpień EPH, 1 w każdej maszynie wirtualnej przez Consumer.exe.
- 16 `SimpleEventProcessor` obiektów utworzonych przez wystąpienia z 5 EPH.
- 1 aplikacja Consumer.exe może zawierać 4 `SimpleEventProcessor` obiekty, ponieważ wystąpienie 1 EPH może mieć 4 partycje.

## <a name="partition-ownership-tracking"></a>Śledzenie własności partycji

Własność partycji z wystąpieniem EPH (lub odbiorcą) jest śledzona za pomocą konta usługi Azure Storage, które jest dostępne do śledzenia. Możesz wizualizować śledzenie jako prostą tabelę w następujący sposób. Rzeczywistą implementację można zobaczyć, badając obiekty blob w ramach podanego konta magazynu:

| **Nazwa grupy konsumentów** | **Identyfikator partycji** | **Nazwa hosta (właściciel)** | **Czas trwania dzierżawy (lub własności)** | **Przesunięcie na partycję (punkt kontrolny)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | \_VM3 konsumenta | 2018 r – 04-15T01:23:45 | 156 |
| $Default | 1 | \_VM4 konsumenta | 2018 r – 04-15T01:22:13 | 734 |
| $Default | 2 | \_VM0 konsumenta | 2018 r – 04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | \_VM3 konsumenta | 2018 r – 04-15T01:22:56 | 976 |

W tym miejscu każdy host uzyskuje własność partycji przez określony czas (czas trwania dzierżawy). Jeśli host zakończy się niepowodzeniem (maszyna wirtualna zostanie wyłączona), dzierżawa wygaśnie. Inne hosty próbują uzyskać własność partycji, a jeden z nich kończy się powodzeniem. Ten proces resetuje dzierżawę na partycji przy użyciu nowego właściciela. W ten sposób tylko jeden czytelnik w danym momencie może odczytywać daną partycję w obrębie grupy odbiorców.

## <a name="receive-messages"></a>Odbieranie komunikatów

Każde wywołanie [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) dostarcza kolekcję zdarzeń. Jest odpowiedzialny za obsługę tych zdarzeń. Aby upewnić się, że host procesora przetwarza każdy komunikat co najmniej raz, należy napisać swój własny kod Kontynuuj ponawianie próby. Należy zachować ostrożność w przypadku skażonych komunikatów.

Zalecane jest stosunkowo szybkie wykonywanie operacji. oznacza to, że możliwie jak najmniejszej ilości przetwarzania. Zamiast tego należy użyć grup odbiorców. Jeśli zachodzi potrzeba zapisu do magazynu i przeprowadzenia pewnych routingu, lepiej jest używać dwóch grup odbiorców i mieć dwie implementacje [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) , które są uruchamiane oddzielnie.

W pewnym momencie podczas przetwarzania warto śledzić dane, które zostały odczytane i wykonane. Śledzenie ma krytyczne znaczenie, jeśli trzeba będzie ponownie uruchomić odczytywanie, aby nie wrócić do początku strumienia. [Klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) upraszcza to śledzenie przy użyciu *punktów kontrolnych*. Punkt kontrolny jest lokalizacją lub przesunięciem dla danej partycji w ramach danej grupy odbiorców, w tym momencie masz pewność, że przetworzono komunikaty. Oznaczanie punktu kontrolnego w **klasy eventprocessorhost** jest realizowane przez wywołanie metody [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) w obiekcie [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) . Ta operacja jest wykonywana w metodzie [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) , ale można ją również wykonać w [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Tworzenie punktów kontrolnych

Metoda [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) ma dwa przeciążenia: pierwszy, bez parametrów, punkty kontrolne do najwyższego przesunięcia zdarzenia w kolekcji zwróconej przez [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). To przesunięcie jest oznaczeniem "wysokiej wody"; przyjęto założenie, że wszystkie ostatnie zdarzenia zostały przetworzone podczas jego wywoływania. Jeśli używasz tej metody w ten sposób, pamiętaj, że oczekujesz, że będzie ona wywoływana po zwróceniu innego kodu przetwarzania zdarzeń. Drugie Przeciążenie pozwala określić wystąpienie [EVENTDATA](/dotnet/api/microsoft.azure.eventhubs.eventdata) dla punktu kontrolnego. Ta metoda umożliwia użycie innego typu znaku wodnego do tworzenia punktów kontrolnych. Za pomocą tego znaku wodnego można zaimplementować znacznik "niskiego poziomu": najniższe określone sekwencjonowane zdarzenie zostało przetworzone. To Przeciążenie zapewnia elastyczność w zakresie zarządzania przesunięciem.

Gdy punkt kontrolny jest wykonywany, plik JSON z informacjami specyficznymi dla partycji (w konkretnym przesunięciu) jest zapisywana na koncie magazynu dostarczonym w konstruktorze do [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Ten plik jest aktualizowany w sposób ciągły. Należy wziąć pod uwagę konieczność przetworzenia punktów kontrolnych w kontekście. Konto magazynu używane do tworzenia punktów kontrolnych prawdopodobnie nie obsłuży tego obciążenia, ale dokładniejsze punkty kontrolne każdego pojedynczego zdarzenia to wskaźnik wzorca komunikatów umieszczonych w kolejce, dla którego Kolejka Service Bus może być lepszym rozwiązaniem niż centrum zdarzeń. Event Hubs poniżej zawarto "co najmniej raz" dostarczanie na dużą skalę. Dzięki wykorzystaniu systemów podrzędnych idempotentne można łatwo odzyskać z błędów lub ponownych uruchomień, co spowoduje, że te same zdarzenia są odbierane wiele razy.

## <a name="thread-safety-and-processor-instances"></a>Bezpieczeństwo wątków i wystąpienia procesora

Domyślnie [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) jest bezpieczny wątkowo i zachowuje się synchronicznie w odniesieniu do wystąpienia elementu [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Po nadejściu zdarzeń dla partycji [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) jest wywoływana w wystąpieniu **IEventProcessor** dla tej partycji i będzie blokować dalsze wywołania **ProcessEventsAsync** dla partycji. Kolejne komunikaty i wywołania w kolejce **ProcessEventsAsync** w tle, gdy pompa komunikatów będzie działać w tle w innych wątkach. Bezpieczeństwo wątków eliminuje konieczność stosowania kolekcji bezpiecznych dla wątków i znacząco zwiększa wydajność.

## <a name="shut-down-gracefully"></a>Łagodnie Zamknij

Na koniec [klasy eventprocessorhost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) włącza czyste zamknięcie wszystkich czytników partycji i zawsze powinna być wywoływana podczas zamykania wystąpienia [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Niewykonanie tej operacji może spowodować opóźnienia podczas uruchamiania innych wystąpień **klasy eventprocessorhost** ze względu na wygaśnięcie dzierżawy i konflikty epoki. Zarządzanie epoką zostało szczegółowo omówione w sekcji [epoki](#epoch) artykułu. 

## <a name="lease-management"></a>Zarządzanie dzierżawą
Zarejestrowanie klasy procesora zdarzeń z wystąpieniem klasy eventprocessorhost rozpoczyna przetwarzanie zdarzenia. Wystąpienie hosta uzyskuje dzierżawy na niektórych partycjach centrum zdarzeń, które mogą być używane przez inne wystąpienia hosta w taki sposób, aby były zbieżne na parzystej dystrybucji partycji we wszystkich wystąpieniach hosta. W przypadku każdej partycji dzierżawionej wystąpienie hosta tworzy wystąpienie dostarczonej klasy procesora zdarzeń, a następnie odbiera zdarzenia z tej partycji i przekazuje je do wystąpienia procesora zdarzeń. Po dodaniu większej liczby wystąpień i przeniesieniu większej liczby dzierżaw klasy eventprocessorhost ostatecznie równoważy obciążenie wśród wszystkich odbiorców.

Jak wyjaśniono wcześniej, tabela śledzenia znacznie upraszcza charakter automatycznego skalowania [klasy eventprocessorhost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Wystąpienie **klasy eventprocessorhost** rozpoczyna się od wielu dzierżaw, a następnie rozpoczyna odczytywanie zdarzeń. Gdy dzierżawy zbliżają się do wygaśnięcia, **klasy eventprocessorhost** próbuje odnowić je, umieszczając rezerwację. Jeśli dzierżawa jest dostępna do odnowienia, procesor kontynuuje odczyt, ale jeśli nie jest, czytnik jest zamknięty i [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) jest wywoływana. **CloseAsync** to dobry moment, aby przeprowadzić końcowe czyszczenie tej partycji.

**Klasy eventprocessorhost** zawiera właściwość [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) . Ta właściwość umożliwia kontrolę nad zarządzaniem dzierżawą. Ustaw te opcje przed zarejestrowaniem implementacji [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) .

## <a name="control-event-processor-host-options"></a>Sterowanie opcjami hosta procesora zdarzeń

Ponadto jedno Przeciążenie [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) przyjmuje obiekt [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) jako parametr. Użyj tego parametru, aby kontrolować zachowanie [klasy eventprocessorhost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) . [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) definiuje cztery właściwości i jedno zdarzenie:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): maksymalny rozmiar kolekcji, która ma zostać odebrana w wywołaniu [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Ten rozmiar nie jest minimalny, tylko maksymalny rozmiar. Jeśli jest mniej komunikatów do odebrania, **ProcessEventsAsync** wykonuje się za pomocą tak dużo, jak były dostępne.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): wartość używana przez podstawowy kanał AMQP do określenia górnego limitu liczby komunikatów, które powinien odebrać klient. Ta wartość powinna być większa lub równa [maxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Jeśli ten parametr ma **wartość true**, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) jest wywoływana, gdy bazowe wywołanie do odbierania zdarzeń na partycji przekracza limit czasu. Ta metoda jest przydatna do podejmowania akcji opartych na czasie w okresach braku aktywności na partycji.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): umożliwia określenie wskaźnika funkcji lub wyrażenia lambda, które jest wywoływane, aby zapewnić początkowe przesunięcie, gdy czytnik rozpocznie Odczytywanie partycji. Bez określenia tego przesunięcia czytnik zaczyna się od najstarszego zdarzenia, chyba że plik JSON z przesunięciami został już zapisany na koncie magazynu dostarczonym do konstruktora [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) . Ta metoda jest przydatna, gdy chcesz zmienić zachowanie uruchamiania czytnika. Gdy ta metoda jest wywoływana, parametr obiektu zawiera identyfikator partycji, dla której czytelnik jest uruchamiany.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): umożliwia otrzymywanie powiadomień o wszelkich podstawowych wyjątkach występujących w [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Jeśli rzeczy nie działają zgodnie z oczekiwaniami, to zdarzenie jest dobrym miejscem, aby rozpocząć wyszukiwanie.

## <a name="epoch"></a>EPOCH

Oto jak działa Epoka odbioru:

### <a name="with-epoch"></a>Z epoką
Epoka jest unikatowym identyfikatorem (wartość epoki) używaną przez usługę, aby wymusić własność partycji/dzierżawy. Można utworzyć odbiornik oparty na epoki przy użyciu metody [CreateEpochReceiver](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver) . Ta metoda tworzy odbiornik oparty na epoki. Odbiorca jest tworzony dla określonej partycji centrum zdarzeń z określonej grupy odbiorców.

Funkcja Epoka zapewnia użytkownikom możliwość zapewnienia, że w dowolnym momencie w grupie odbiorców istnieje tylko jeden odbiornik z następującymi regułami:

- Jeśli nie ma żadnych istniejących odbiorników w grupie odbiorców, użytkownik może utworzyć odbiorcę z dowolną wartością epoki.
- Jeśli istnieje odbiornik z wartością epoki E1 i zostanie utworzony nowy odbiornik z wartością epoki E2, gdzie E1 <= E2, odbiorca z E1 zostanie odłączony automatycznie, odbiornik z E2 zostanie utworzony pomyślnie.
- Jeśli istnieje odbiornik z wartością epoki E1 i nowy odbiorca jest tworzony z wartością epoki E2, gdzie E1 > E2, a następnie utworzenie E2 z zakończeniem się niepowodzeniem z powodu błędu: odbiornik z epoką.

### <a name="no-epoch"></a>Brak epoki
Tworzysz odbiornik oparty na protokole epoki przy użyciu metody [OnReceive](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver) . 

W przypadku przetwarzania strumieniowego istnieją pewne scenariusze, w których użytkownicy mogą utworzyć wielu odbiorników w pojedynczej grupie odbiorców. Aby wspierać takie scenariusze, firma Microsoft może utworzyć odbiorcę bez epoki. w takim przypadku zezwolimy na maksymalnie 5 współbieżnych odbiorców w grupie konsumentów.

### <a name="mixed-mode"></a>Tryb mieszany
Nie zalecamy użycia aplikacji, gdy tworzysz odbiornik z epoką, a następnie przełączasz się do opcji No-Epoka lub vice versa w tej samej grupie odbiorców. Jednak w przypadku wystąpienia tego zachowania usługa obsługuje je przy użyciu następujących reguł:

- Jeśli istnieje już odbiornik z epoką E1 i aktywnie odbieramy zdarzenia i zostanie utworzony nowy odbiorca bez epoki, tworzenie nowego odbiornika zakończy się niepowodzeniem. Odbiorniki epok zawsze mają pierwszeństwo w systemie.
- Jeśli został już utworzony odbiornik z epoką E1 i nastąpiło odłączenie, a nowy odbiorca jest tworzony bez epoki na nowym MessagingFactory, tworzenie nowego odbiorcy powiedzie się. W tym miejscu występuje zastrzeżenia, które system wykrywa "odłączenie odbiorcy" po ~ 10 minut.
- Jeśli istnieje co najmniej jeden odbiornik, który został utworzony bez epoki, a nowy odbiorca jest tworzony z epoką E1, wszystkie stare odbiorniki są rozłączone.


> [!NOTE]
> Zalecamy używanie różnych grup odbiorców dla aplikacji korzystających z epoki i dla tych, które nie używają epoki, aby uniknąć błędów. 


## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już hosta procesora zdarzeń, zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o Event Hubs:

- Rozpoczynanie pracy z usługą Event Hubs
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Przewodnik programowania Event Hubs](event-hubs-programming-guide.md)
* [Availability and consistency in Event Hubs](event-hubs-availability-and-consistency.md) (Dostępność i spójność w usłudze Event Hubs)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykłady Event Hubs w witrynie GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)
