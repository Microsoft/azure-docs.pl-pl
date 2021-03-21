---
title: Przegląd cyklu życia Reliable Services
description: Dowiedz się więcej o zdarzeniach cyklu życia w aplikacji Reliable Services Service Fabric platformy Azure dla usług stanowych i bezstanowych.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6ea8fa6933052374721d8d205d5b07386c807ae2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784600"
---
# <a name="reliable-services-lifecycle-overview"></a>Przegląd cyklu życia Reliable Services
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java w systemie Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Gdy myślisz o cyklach życia usługi Azure Service Fabric Reliable Services, podstawowe informacje na temat cyklu życia są najważniejsze. Ogólnie rzecz biorąc, cykl życia obejmuje następujące elementy:

- Podczas uruchamiania:
  - Usługi są zbudowane.
  - Usługi mogą tworzyć i zwracać zero lub większą liczbę odbiorników.
  - Wszystkie zwrócone detektory są otwierane, umożliwiając komunikację z usługą.
  - Metoda **RunAsync** usługi jest wywoływana, co umożliwia usłudze wykonywanie długotrwałych zadań lub pracy w tle.
- Podczas zamykania:
  - Token anulowania przesłany do **RunAsync** został anulowany, a detektory są zamknięte.
  - Po zamknięciu odbiorników sam obiekt usługi jest destruktorem.

Istnieją szczegóły dotyczące dokładnej kolejności tych zdarzeń. Kolejność zdarzeń może się nieco zmieniać w zależności od tego, czy niezawodna usługa jest bezstanowa, czy stanowa. Ponadto w przypadku usług stanowych musimy zająć się podstawowym scenariuszem wymiany. Podczas tej sekwencji rola podstawowa jest przekazywana do innej repliki (lub wraca) bez zamykania usługi. Na koniec należy wziąć pod uwagę informacje o błędach lub błędach.

## <a name="stateless-service-startup"></a>Uruchamianie usługi bezstanowej
Cykl życia usługi bezstanowej jest prosty. Oto kolejność zdarzeń:

1. Usługa jest zbudowana.
2. Następnie, równolegle, wykonywane są dwie rzeczy:
    - `StatelessService.CreateServiceInstanceListeners()` jest wywoływana i wszystkie zwrócone detektory są otwarte. `ICommunicationListener.OpenAsync()` jest wywoływana dla każdego odbiornika.
    - `StatelessService.RunAsync()`Metoda usługi jest wywoływana.
3. Jeśli jest obecny, `StatelessService.OnOpenAsync()` wywoływana jest metoda usługi. To wywołanie jest nietypowym przesłonięciem, ale jest dostępne. W tej chwili można uruchomić zadania inicjowania usługi rozszerzonej.

Należy pamiętać, że nie istnieje porządkowanie między wywołaniami do tworzenia i otwierania odbiorników i **RunAsync**. Odbiorniki można otworzyć przed uruchomieniem **RunAsync** . Podobnie można wywołać **RunAsync** przed otwarciem lub nawet skonstruowaniem odbiorników komunikacji. Jeśli wymagana jest jakakolwiek synchronizacja, zostanie ona pozostawiona jako ćwiczenie wdrożenia. Oto kilka typowych rozwiązań:

  - Czasami odbiorniki nie mogą działać, dopóki nie zostaną utworzone inne informacje lub nie będą działać. W przypadku usług bezstanowych to działanie może być zwykle wykonywane w innych lokalizacjach, takich jak następujące: 
    - W konstruktorze usługi.
    - Podczas `CreateServiceInstanceListeners()` wywołania.
    - Jako część konstrukcji odbiornika.
  - Czasami kod w **RunAsync** nie rozpoczyna się, dopóki odbiorniki nie są otwarte. W takim przypadku konieczne jest dodatkowe koordynowanie. Jedno z typowych rozwiązań polega na tym, że w detektorach znajdują się flagi wskazujące, kiedy zostały zakończone. Ta flaga jest następnie zaewidencjonowana w **RunAsync** przed kontynuowaniem rzeczywistej pracy.

## <a name="stateless-service-shutdown"></a>Zamykanie bezstanowej usługi
Aby zamknąć usługę bezstanową, ten sam wzorzec następuje po prostu:

1. Równolegle:
    - Wszystkie otwarte odbiorniki są zamknięte. `ICommunicationListener.CloseAsync()` jest wywoływana dla każdego odbiornika.
    - Token anulowania przeszedł do `RunAsync()` został anulowany. Sprawdzenie właściwości tokenu anulowania `IsCancellationRequested` zwraca wartość true, a jeśli jest wywoływana, `ThrowIfCancellationRequested` Metoda tokenu zgłasza `OperationCanceledException` .
2. Po `CloseAsync()` zakończeniu dla każdego odbiornika i `RunAsync()` zakończeniu tej `StatelessService.OnCloseAsync()` metody usługa jest wywoływana, jeśli jest obecna.  OnCloseAsync jest wywoływana, gdy wystąpienie usługi bezstanowej zostanie bezpiecznie zamknięte. Taka sytuacja może wystąpić, gdy trwa uaktualnianie kodu usługi, wystąpienie usługi jest przenoszone z powodu równoważenia obciążenia lub wykryto błąd przejściowy. Przesłonięcie jest nietypowe `StatelessService.OnCloseAsync()` , ale może służyć do bezpiecznego zamykania zasobów, zatrzymania przetwarzania w tle, kończenia zapisywania zewnętrznego stanu lub zamykania istniejących połączeń.
3. Po `StatelessService.OnCloseAsync()` zakończeniu obiekt usługi jest destruktorem.

## <a name="stateful-service-startup"></a>Uruchamianie usługi stanowej
Usługi stanowe mają podobny wzorzec do usług bezstanowych, z uwzględnieniem kilku zmian. W celu uruchomienia usługi stanowej kolejność zdarzeń jest następująca:

1. Usługa jest zbudowana.
2. `StatefulServiceBase.OnOpenAsync()` jest wywoływana. To wywołanie nie jest zwykle zastępowane w usłudze.
3. Następujące czynności są wykonywane równolegle:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` jest wywoływana. 
      - Jeśli usługa jest usługą podstawową, wszystkie zwrócone detektory są otwarte. `ICommunicationListener.OpenAsync()` jest wywoływana dla każdego odbiornika.
      - Jeśli usługa jest usługą dodatkową, otwierane są tylko te detektory oznaczone jako `ListenOnSecondary = true` . Posiadanie odbiorników, które są otwarte na serwerze pomocniczym, jest rzadziej.
    - Jeśli usługa jest obecnie podstawową, `StatefulServiceBase.RunAsync()` wywoływana jest metoda usługi.
4. Po zakończeniu wszystkich wywołań odbiornika repliki `OpenAsync()` i wywołaniu jest `RunAsync()` `StatefulServiceBase.OnChangeRoleAsync()` wywoływana. To wywołanie nie jest zwykle zastępowane w usłudze.

Podobnie jak w przypadku usług bezstanowych, nie ma koordynacji między kolejnością tworzenia i otwierania odbiorników oraz gdy wywoływana jest **RunAsync** . W przypadku konieczności koordynacji rozwiązania są znacznie takie same. Istnieje jeden dodatkowy przypadek dla usługi stanowej. Załóżmy, że wywołania, które docierają do odbiorników komunikacji, wymagają informacji przechowywanych wewnątrz niektórych [niezawodnych kolekcji](service-fabric-reliable-services-reliable-collections.md).

   > [!NOTE]  
   > Ponieważ odbiorniki komunikacji mogą zostać otwarte przed możliwym do odczytu i zapisywalnymi kolekcjami, a przed rozpoczęciem **RunAsync** , konieczne jest wykonanie pewnej dodatkowej koordynacji. Najprostszym i najbardziej typowym rozwiązaniem jest określenie, że odbiorniki komunikacji zwracają kod błędu, którego klient używa do ponowienia próby wykonania żądania.

## <a name="stateful-service-shutdown"></a>Wyłączenie usługi stanowej
Podobnie jak w przypadku usług bezstanowych, zdarzenia cyklu życia podczas zamykania są takie same jak podczas uruchamiania, ale odwrócone. Gdy usługa stanowa jest zamykana, wystąpią następujące zdarzenia:

1. Równolegle:
    - Wszystkie otwarte odbiorniki są zamknięte. `ICommunicationListener.CloseAsync()` jest wywoływana dla każdego odbiornika.
    - Token anulowania przeszedł do `RunAsync()` został anulowany. Sprawdzenie właściwości tokenu anulowania `IsCancellationRequested` zwraca wartość true, a jeśli jest wywoływana, `ThrowIfCancellationRequested` Metoda tokenu zgłasza `OperationCanceledException` .
2. Po `CloseAsync()` zakończeniu każdego odbiornika i `RunAsync()` zakończeniu działania usługi `StatefulServiceBase.OnChangeRoleAsync()` jest wywoływana. To wywołanie nie jest zwykle zastępowane w usłudze.

   > [!NOTE]  
   > Konieczność oczekiwania na zakończenie **RunAsync** jest konieczna tylko wtedy, gdy ta replika jest repliką podstawową.

3. Po `StatefulServiceBase.OnChangeRoleAsync()` zakończeniu metody `StatefulServiceBase.OnCloseAsync()` wywoływana jest metoda. To wywołanie jest nietypowym przesłonięciem, ale jest dostępne.
3. Po `StatefulServiceBase.OnCloseAsync()` zakończeniu obiekt usługi jest destruktorem.

## <a name="stateful-service-primary-swaps"></a>Podstawowe swapy usługi stanowej
Podczas gdy usługa stanowa jest uruchomiona, tylko repliki podstawowe tych usług stanowych mają otwarte odbiorniki komunikacji i metodę **RunAsync** . Repliki pomocnicze są konstruowane, ale nie są wyświetlane żadne dalsze wywołania. Podczas gdy usługa stanowa jest uruchomiona, replika, która jest obecnie podstawową, może ulec zmianie w wyniku optymalizacji awarii lub klastra. Co to oznacza zdarzenia cyklu życia, które może zobaczyć replika? Zachowanie replika stanowa jest zależne od tego, czy jest to replika obniżana lub podwyższana podczas wymiany.

### <a name="for-the-primary-thats-demoted"></a>Dla podstawowego, który został obniżony
W przypadku repliki podstawowej, która została obniżona, Service Fabric potrzebuje tej repliki, aby zatrzymać przetwarzanie komunikatów i zakończyć wszystkie działania w tle. W związku z tym ten krok wygląda podobnie do momentu wyłączenia usługi. Jedną z różnic polega na tym, że usługa nie jest zainstalowana lub ZAMKNIĘTA, ponieważ pozostanie jako pomocnicza. Następujące interfejsy API są wywoływane:

1. Równolegle:
    - Wszystkie otwarte odbiorniki są zamknięte. `ICommunicationListener.CloseAsync()` jest wywoływana dla każdego odbiornika.
    - Token anulowania przeszedł do `RunAsync()` został anulowany. Sprawdzenie właściwości tokenu anulowania `IsCancellationRequested` zwraca wartość true, a jeśli jest wywoływana, `ThrowIfCancellationRequested` Metoda tokenu zgłasza `OperationCanceledException` .
2. Po `CloseAsync()` zakończeniu każdego odbiornika i `RunAsync()` zakończeniu działania usługi `StatefulServiceBase.OnChangeRoleAsync()` jest wywoływana. To wywołanie nie jest zwykle zastępowane w usłudze.

### <a name="for-the-secondary-thats-promoted"></a>Dla elementu pomocniczego, który został podwyższony
Podobnie, Service Fabric potrzebuje repliki pomocniczej, która została podwyższona, aby rozpocząć nasłuchiwanie komunikatów w sieci i uruchamianie wszelkich zadań w tle potrzebnych do ukończenia. W związku z tym ten proces wygląda następująco po utworzeniu usługi, z tą różnicą, że sama replika już istnieje. Następujące interfejsy API są wywoływane:

1. Równolegle:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` jest wywoływana i wszystkie zwrócone detektory są otwarte. `ICommunicationListener.OpenAsync()` jest wywoływana dla każdego odbiornika.
    - `StatefulServiceBase.RunAsync()`Metoda usługi jest wywoływana.
2. Po zakończeniu wszystkich wywołań odbiornika repliki `OpenAsync()` i wywołaniu jest `RunAsync()` `StatefulServiceBase.OnChangeRoleAsync()` wywoływana. To wywołanie nie jest zwykle zastępowane w usłudze.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Typowe problemy występujące podczas zamykania usługi stanowej i podstawowego obniżania poziomu
Service Fabric zmienia podstawową usługę stanową z różnych powodów. Najczęściej jest to ponowne [zrównoważenie klastra](service-fabric-cluster-resource-manager-balancing.md) i [uaktualnienie aplikacji](service-fabric-application-upgrade.md). Podczas tych operacji (jak również podczas normalnego zamykania usługi, podobnie jak w przypadku usunięcia usługi), ważne jest, aby usługa była zgodna z usługą `CancellationToken` . 

Usługi, które nie obsługują czyszczenia anulowania mogą napotkać kilka problemów. Te operacje są wolne, ponieważ Service Fabric czeka, aż usługi przestaną być bezpieczne. Może to ostatecznie prowadzić do nieudanych uaktualnień, których przekroczenie limitu czasu i wycofanie. Niepowodzenie honorowania tokenu anulowania może również spowodować niezrównoważone klastry. Klastry stają się niezrównoważone, ponieważ węzły uzyskują gorącą, ale nie można zrównoważyć ich w innym miejscu. 

Ponieważ usługi są stanowe, prawdopodobnie korzystają z [niezawodnych kolekcji](service-fabric-reliable-services-reliable-collections.md). W Service Fabric, gdy podstawowa jest obniżana, jeden z pierwszych rzeczy jest taki, że dostęp do zapisu do podstawowego stanu jest odwołany. Prowadzi to do drugiego zestawu problemów, które mogą wpływać na cykl usługi. Kolekcje zwracają wyjątki na podstawie chronometrażu i tego, czy replika jest przenoszona, czy wyłączona. Te wyjątki powinny być prawidłowo obsługiwane. Wyjątki zgłoszone przez Service Fabric należą do trwałych kategorii [( `FabricException` )](/dotnet/api/system.fabric.fabricexception) i przejściowych [( `FabricTransientException` )](/dotnet/api/system.fabric.fabrictransientexception) . Stałe wyjątki powinny być rejestrowane i zgłaszane, podczas gdy przejściowe wyjątki mogą być ponawiane na podstawie jakiejś logiki ponawiania.

Obsługa wyjątków, które są używane `ReliableCollections` w połączeniu z zdarzeniami cyklu życia usługi, jest ważną częścią testowania i weryfikacji niezawodnej usługi. Zalecamy, aby zawsze uruchamiać usługę pod obciążeniem podczas przeprowadzania uaktualnień i [testowania chaos](service-fabric-controlled-chaos.md) przed wdrożeniem w środowisku produkcyjnym. Te podstawowe kroki pomagają upewnić się, że usługa jest prawidłowo zaimplementowana i prawidłowo obsługuje zdarzenia cyklu życia.


## <a name="notes-on-the-service-lifecycle"></a>Uwagi dotyczące cyklu życia usługi
  - Obie `RunAsync()` metody i `CreateServiceReplicaListeners/CreateServiceInstanceListeners` wywołania są opcjonalne. Usługa może mieć jedną z nich, obie lub nie. Na przykład jeśli usługa wykonuje całą swoją działania w odpowiedzi na wywołania użytkowników, nie ma potrzeby jej implementacji `RunAsync()` . Wymagane są tylko odbiorniki komunikacji i ich skojarzony kod. Podobnie tworzenie i zwracanie odbiorników komunikacji jest opcjonalne, ponieważ usługa może tylko pracować w tle i dlatego musi tylko wdrożyć `RunAsync()` .
  - Poprawne zakończenie usługi `RunAsync()` powiodło się i powrót z niej. Zakończenie nie jest warunkiem błędu. Zakończenie `RunAsync()` wskazuje, że działanie w tle usługi zostało zakończone. W przypadku niezawodnych usług `RunAsync()` jest wywoływana ponownie, jeśli replika zostanie obniżona z podstawowej do pomocniczej, a następnie podwyższona do podstawowej.
  - Jeśli usługa kończy działanie z programu, zwracając `RunAsync()` jakiś nieoczekiwany wyjątek, stanowi to niepowodzenie. Obiekt usługi jest wyłączony i raportowany jest błąd kondycji.
  - Chociaż nie ma limitu czasu na zwracaniu z tych metod, natychmiast utracisz możliwość zapisu w niezawodnych kolekcjach i dlatego nie można ukończyć żadnej rzeczywistej pracy. Zalecamy, aby podczas otrzymywania żądania anulowania w miarę jak najszybciej wrócić do programu. Jeśli usługa nie odpowiada na te wywołania interfejsu API w rozsądnym czasie, Service Fabric może wymusić zakończenie usługi. Zwykle dzieje się to tylko podczas uaktualniania aplikacji lub podczas usuwania usługi. Ten limit czasu wynosi domyślnie 15 minut.
  - Błędy w `OnCloseAsync()` ścieżce powodują `OnAbort()` wywoływanie, co stanowi najlepszą okazję do oczyszczenia i zwolnienia wszelkich zażądanych zasobów. Jest to zwykle wywoływane w przypadku wykrycia trwałego błędu w węźle lub gdy Service Fabric nie może niezawodnie zarządzać cyklem życia wystąpienia usługi ze względu na błędy wewnętrzne.
  - `OnChangeRoleAsync()` jest wywoływana, gdy replika usługi stanowej zmienia rolę, na przykład na podstawową lub pomocniczą. Repliki podstawowe mają stan zapisu (można tworzyć i zapisywać w niezawodnych kolekcjach). Repliki pomocnicze uzyskują stan odczytu (mogą odczytywać tylko z istniejących niezawodnych kolekcji). Większość pracy w usłudze stanowej jest wykonywana w replice podstawowej. Repliki pomocnicze mogą wykonywać walidację, generowanie raportów, wyszukiwanie danych lub inne zadania tylko do odczytu.

## <a name="next-steps"></a>Następne kroki
- [Wprowadzenie do Reliable Services](service-fabric-reliable-services-introduction.md)
- [Reliable Services Szybki Start](service-fabric-reliable-services-quick-start.md)
- [Repliki i wystąpienia](service-fabric-concepts-replica-lifecycle.md)
