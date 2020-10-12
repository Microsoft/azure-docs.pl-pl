---
title: Usługi stanowe testowania jednostkowego na platformie Azure Service Fabric
description: Poznaj koncepcje i praktyki testowania jednostkowego Service Fabric usług stanowych.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 12e8a47d9685dee12594f4e2afaa848d9688d185
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75433906"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Testy jednostkowe usług stanowych w Service Fabric

W tym artykule omówiono koncepcje i praktyki testowania jednostkowego Service Fabric usług stanowych. Testy jednostkowe w ramach Service Fabric nie zapełnią swoich zagadnień ze względu na fakt, że kod aplikacji aktywnie działa w wielu różnych kontekstach. W tym artykule opisano praktyki, które są używane do zapewnienia, że kod aplikacji jest objęty każdym z różnych kontekstów, które można uruchomić.

## <a name="unit-testing-and-mocking"></a>Testowanie jednostkowe i ich symulacja
Testy jednostkowe w kontekście tego artykułu są zautomatyzowanymi testami, które mogą być wykonywane w kontekście modułu uruchamiającego testy, takiego jak MSTest lub NUnit. Testy jednostkowe w tym artykule nie wykonują operacji na zasobach zdalnych, takich jak baza danych lub interfejs API RESTFul. Te zasoby zdalne powinny być makietą. Imitacja w kontekście tego artykułu spowoduje sfałszowanie, zapisanie i kontrolowanie wartości zwracanych dla zasobów zdalnych.

### <a name="service-fabric-considerations"></a>Zagadnienia dotyczące Service Fabric
Testowanie jednostkowe Service Fabric stanowej usługi ma kilka zagadnień. Po pierwsze kod usługi jest wykonywany na wielu węzłach, ale pod różnymi rolami. Testy jednostkowe powinny oszacować kod w każdej roli w celu osiągnięcia pełnego pokrycia. Różne role to podstawowa, aktywna pomocnicza, pomocnicza wartość bezczynna i nieznana. Rola brak zazwyczaj nie wymaga specjalnego pokrycia, ponieważ Service Fabric uważa, że ta rola jest równa void lub null. Po drugie każdy węzeł zmieni swoją rolę w danym punkcie. Aby osiągnąć pełny zakres, należy przetestować ścieżkę wykonywania kodu ze zmianami roli.

## <a name="why-unit-test-stateful-services"></a>Dlaczego testy jednostkowe są usługi stanowe? 
Usługi stanowe testowania jednostkowego mogą pomóc w rozwróceniu niektórych typowych błędów, które nie powinny być przechwytywane przez konwencjonalne aplikacje lub testy jednostkowe specyficzne dla domeny. Jeśli na przykład usługa stanowa ma dowolny stan w pamięci, testowanie tego typu może sprawdzić, czy ten stan w pamięci jest zsynchronizowany dla każdej repliki. Ten typ testowania może również sprawdzić, czy usługa stanowa reaguje na tokeny anulowania, które zostały odpowiednio przesłane przez Service Fabric aranżację. W przypadku wyzwolenia operacji anulowania usługa powinna zatrzymać wszystkie długotrwałe operacje wykonywane i/lub asynchroniczne.  

## <a name="common-practices"></a>Typowe praktyki

W poniższej sekcji przedstawiono najczęstsze praktyki testowania jednostkowego usługi stanowej. Zalecane jest również, aby warstwa imitacji była ściśle wyrównana do Service Fabric aranżacji i zarządzania stanem. Funkcja [servicefabric. makiety](https://www.nuget.org/packages/ServiceFabric.Mocks/) w postaci 3.3.0 lub nowszej jest jedną z tych bibliotek, która zapewnia zalecaną funkcję tworzenia i postępuje zgodnie z poniższymi wskazówkami.

### <a name="arrangement"></a>Szkic

#### <a name="use-multiple-service-instances"></a>Korzystanie z wielu wystąpień usługi
Testy jednostkowe powinny wykonywać wiele wystąpień usługi stanowej. Symuluje to, co się dzieje w klastrze, gdzie Service Fabric Inicjuje obsługę wielu replik z uruchomioną usługą w różnych węzłach. Każde z tych wystąpień będzie jednak wykonywane w innym kontekście. Podczas wykonywania testu każde wystąpienie powinno być zależeć od konfiguracji roli oczekiwanej w klastrze. Na przykład jeśli oczekuje się, że usługa ma docelowy rozmiar repliki 3, Service Fabric będzie inicjować trzy repliki w różnych węzłach. Jednym z nich jest podstawowa, a druga dwie są aktywne.

W większości przypadków ścieżka wykonywania usługi różni się nieco dla każdej z tych ról. Jeśli na przykład usługa nie powinna akceptować żądań z aktywnej pomocniczej usługi, może ona sprawdzić, czy jest to przypadek, aby zgłosić wyjątek, który wskazuje, że żądanie zostało podjęte na serwerze pomocniczym. Przetestowanie tej sytuacji w wielu wystąpieniach będzie możliwe.

Ponadto, jeśli wiele wystąpień umożliwia testom przełączanie ról każdego z tych wystąpień w celu sprawdzenia, czy odpowiedzi są spójne, pomimo zmiany roli.

#### <a name="mock-the-state-manager"></a>Makieta menedżera stanu
Menedżer stanu powinien być traktowany jako zasób zdalny i z tego powodu. Podczas imitacji menedżera stanu musi być używany magazyn w pamięci do śledzenia informacji zapisanych w Menedżerze Stanów, tak aby można go było odczytać i zweryfikować. Prostym sposobem na osiągnięcie tego jest utworzenie wystąpień makiety dla każdego typu niezawodnych kolekcji. W ramach tych imitacji należy użyć typu danych, który jest ściśle wyrównany do operacji wykonywanych względem tej kolekcji. Poniżej przedstawiono kilka sugerowanych typów danych dla każdej niezawodnej kolekcji

- IReliableDictionary<TKey, TValue>-> system. Collections. współbieżne. ConcurrentDictionary<TKey, TValue>
- IReliableQueue \<T> — > system. Collections. Generic. Queue\<T>
- IReliableConcurrentQueue \<T> — > system. Collections. współbieżne. ConcurrentQueue\<T>

#### <a name="many-state-manager-instances-single-storage"></a>Wiele wystąpień Menedżera stanu, pojedynczy magazyn
Jak wspomniano wcześniej, Menedżer stanu i niezawodne kolekcje powinny być traktowane jako zasób zdalny. W związku z tym te zasoby powinny i zostaną zamakietne w ramach testów jednostkowych. Jednak w przypadku uruchamiania wielu wystąpień usługi stanowej będzie to wyzwanie, aby zapewnić, że każdy z nich zostanie zsynchronizowany przez różne wystąpienia usługi stanowej. Gdy usługa stanowa jest uruchomiona w klastrze, Service Fabric dba o utrzymywanie zgodności każdego menedżera stanu repliki pomocniczej z repliką podstawową. W związku z tym testy powinny zachowywać się tak samo, aby mogły symulować zmiany ról.

Prostą metodą tej synchronizacji jest użycie pojedynczego wzorca dla obiektu źródłowego, który przechowuje dane zapisane w każdej niezawodnej kolekcji. Na przykład jeśli usługa stanowa używa programu `IReliableDictionary<string, string>` . Menedżer stanu makiety powinien zwrócić makietę `IReliableDictionary<string, string>` . Ten makieta może używać `ConcurrentDictionary<string, string>` do śledzenia par klucz/wartość, które są zapisywane. `ConcurrentDictionary<string, string>`Powinien być pojedynczym używanym przez wszystkie wystąpienia menedżerów Stanów, które zostały przesłane do usługi.

#### <a name="keep-track-of-cancellation-tokens"></a>Śledź tokeny anulowania
Tokeny anulowania są istotnie, ale ogólnie niespotykanym aspektem usług stanowych. Gdy Service Fabric uruchamia replikę podstawową dla usługi stanowej, zostanie podany token anulowania. Ten token anulowania jest przeznaczony do sygnalizowania usługi po jej usunięciu lub obniżeniu do innej roli. Usługa stanowa powinna zatrzymać wszystkie operacje długotrwałe lub asynchroniczne, aby Service Fabric mógł ukończyć przepływ pracy zmiany roli.

Podczas uruchamiania testów jednostkowych wszystkie tokeny anulowania, które są dostarczane do RunAsync, ChangeRoleAsync, OpenAsync i CloseAsync, powinny być przechowywane podczas wykonywania testu. Przechowywanie na te tokeny umożliwi testowi zasymulowanie wyłączenia lub obniżenia działania usługi oraz zweryfikowanie odpowiedniej odpowiedzi usługi.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Kompleksowe testowanie kompleksowych zasobów zdalnych
Testy jednostkowe powinny być wykonywane ze zbyt dużą ilością kodu aplikacji, która może zmienić stan usługi stanowej. Zaleca się, aby testy były bardziej kompleksowe. Jedyne ślady, które istnieją, służą do rejestrowania, symulowania i/lub weryfikowania interakcji z zasobami zdalnymi. Obejmuje to interakcje z menedżerem stanu i niezawodnymi kolekcjami. Poniższy fragment kodu stanowi przykład Gherkin dla testu, który pokazuje kompleksowe testowanie:

```
    Given stateful service named "fabric:/MyApp/MyService" is created
    And a new replica is created as "Primary" with id "111"
    And a new replica is created as "IdleSecondary" with id "222"
    And a new replica is created as "IdleSecondary" with id "333"
    And all idle secondary replicas are promoted to active secondary
    When a request is made to add the an employee "John Smith"
    And the active secondary replica "222" is promoted to primary
    And a request is made to get all employees
    Then the request should should return the "John Smith" employee
```

Ten test potwierdza, że dane przechwytywane z jednej repliki są dostępne w replice pomocniczej po podwyższeniu poziomu do podstawowej. Przy założeniu, że niezawodna kolekcja jest magazynem zapasowym danych pracownika, nie można wychwycić potencjalnej awarii, która może zostać przechwycona przez ten test, jeśli kod aplikacji nie został wykonany `CommitAsync` w transakcji w celu zapisania nowego pracownika. W takim przypadku drugie żądanie pobrania pracowników nie zwróci pracownika dodanego przez pierwsze żądanie.

### <a name="acting"></a>Pośrednicząc
#### <a name="mimic-service-fabric-replica-orchestration"></a>Naśladowanie Service Fabric aranżacji repliki
Podczas zarządzania wieloma wystąpieniami usług testy powinny inicjować i dzielić te usługi w taki sam sposób jak w przypadku Service Fabric aranżacji. Na przykład gdy usługa jest tworzona w nowej replice podstawowej, Service Fabric wywoła CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync i RunAsync. Zdarzenia cyklu życia są udokumentowane w następujących artykułach:

- [Uruchamianie usługi stanowej](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Wyłączenie usługi stanowej](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Podstawowe swapy usługi stanowej](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Uruchom zmiany roli repliki
Testy jednostkowe powinny zmieniać role wystąpień usługi w taki sam sposób, jak w przypadku aranżacji Service Fabric. Komputer stanu roli jest udokumentowany w następującym artykule:

[Maszyna stanu repliki](service-fabric-concepts-replica-lifecycle.md#replica-role)

Symulowanie zmian ról jest jednym z bardziej krytycznych aspektów testowania i może odkrywać problemy, w których stan repliki nie jest spójny ze sobą. Niespójny stan repliki może wystąpić z powodu przechowywania stanu w pamięci w zmiennych wystąpieniach statycznych lub na poziomie klasy. Przykładami mogą być tokeny anulowania, wyliczenia i obiekty/wartości konfiguracji. Zapewni to również, że usługa szanuje tokeny anulowania podane podczas RunAsync, aby umożliwić zmianę roli. Symulowanie zmian ról może również obejmować problemy, które mogą wystąpić, jeśli kod nie zostanie zapisany, aby umożliwić RunAsync wiele razy.

#### <a name="cancel-cancellation-tokens"></a>Anulowanie tokenów anulowania
Powinny istnieć testy jednostkowe, w których token anulowania podany dla RunAsync został anulowany. Pozwoli to testowi na sprawdzenie, czy usługa jest bezpiecznie zamykana. Podczas tego zamykania należy zatrzymać wszystkie długotrwałe operacje wykonywane lub asynchroniczne. Przykładem długotrwałego procesu, który może istnieć w usłudze, jest taka, która nasłuchuje komunikatów w niezawodnym kolejce. Może ona istnieć bezpośrednio w RunAsync lub wątku w tle. Implementacja powinna uwzględniać logikę kończenia operacji, jeśli ten token anulowania został anulowany.

Jeśli usługi stanowe korzystają z dowolnej pamięci podręcznej lub stanu w pamięci, która powinna istnieć tylko na serwerze podstawowym, należy ją usunąć. Jest to konieczne, aby upewnić się, że ten stan jest spójny, jeśli węzeł ponownie stanie się później. Testowanie anulowania zezwoli na sprawdzenie, czy ten stan zostanie prawidłowo usunięty.

#### <a name="execute-requests-against-multiple-replicas"></a>Żądania wykonania dla wielu replik
Testy potwierdzenia powinny wykonywać te same żądania względem różnych replik. W przypadku sparowania ze zmianami ról można odzyskać problemy ze spójnością. Przykładowy test może wykonać następujące czynności:
1. Wykonaj żądanie zapisu względem bieżącego elementu podstawowego
2. Wykonaj żądanie odczytu, które zwraca dane zapisywane w kroku 1 względem bieżącego elementu podstawowego
3. Podnieś poziom pomocniczy do podstawowego. Powinno to również obniżyć poziom bieżącego podstawowego do pomocniczego
4. Wykonaj to samo żądanie odczytu z kroku 2 dla nowego pomocniczego.

W ostatnim kroku test może postanowić, że zwrócone dane są spójne. Potencjalnym problemem, który może się nie pokryć, jest to, że dane zwracane przez usługę mogą znajdować się w pamięci, ale są ostatecznie obsługiwane przez niezawodną kolekcję. Dane znajdujące się w pamięci mogą nie zostać prawidłowo zsynchronizowane z tym, co istnieje w niezawodnej kolekcji.

Dane znajdujące się w pamięci są zwykle używane do tworzenia indeksów pomocniczych lub agregacji danych, które istnieją w niezawodnej kolekcji.

### <a name="asserting"></a>Zostanie poproszony
#### <a name="ensure-responses-match-across-replicas"></a>Upewnij się, że odpowiedzi są zgodne między replikami
Testy jednostkowe powinny mieć na celu potwierdzenie, że odpowiedź dla danego żądania jest spójna w wielu replikach po przejściu na podstawową. Może to powodować potencjalne problemy, w przypadku których dane podane w odpowiedzi nie są obsługiwane przez niezawodną kolekcję lub są przechowywane w pamięci bez mechanizmu synchronizowania danych między replikami. Dzięki temu usługa będzie wysyłać spójne odpowiedzi po Service Fabric zrównoważenia lub przełączeniu w tryb failover do nowej repliki podstawowej.

#### <a name="verify-service-respects-cancellation"></a>Weryfikacja anulowania usługi
Długotrwałe lub asynchroniczne procesy, które powinny zostać zakończone, gdy token anulowania zostanie anulowany powinien zostać zweryfikowany, że faktycznie zakończy się po anulowaniu. Zapewni to, że mimo że repliki zmienią role, procesy, które nie są przeznaczone do uruchamiania w replice innej niż podstawowa, zostaną zatrzymane przed zakończeniem przejścia. Może to również obejmować problemy, w których taki proces blokuje zmianę roli lub żądanie zamknięcia z Service Fabric od zakończenia.

#### <a name="verify-which-replicas-should-serve-requests"></a>Sprawdź, które repliki powinny obpracować żądania
Testy powinny mieć na celu potwierdzenie oczekiwanego zachowania, jeśli żądanie jest kierowane do repliki innej niż podstawowa. Service Fabric zapewnia możliwość obsługi żądań przez repliki pomocnicze. Jednak operacje zapisu w niezawodnych kolekcjach mogą wystąpić tylko w replice podstawowej. Jeśli aplikacja zaproponuje tylko repliki podstawowe do obsługi żądań lub tylko podzbiór żądań może być obsługiwany przez pomocniczą, wówczas testy powinny mieć wpływ na oczekiwane zachowanie zarówno dla przypadków dodatnich, jak i ujemnych. Negatywny przypadek, w którym żądanie jest kierowane do repliki, która nie powinna obsłużyć żądania, a dodatnia jest odwrotna.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [jednostkowo przetestować usługi stanowe](service-fabric-how-to-unit-test-stateful-services.md).
