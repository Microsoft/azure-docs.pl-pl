---
title: Wskazówki dotyczące niezawodnych kolekcji
description: Wskazówki i zalecenia dotyczące używania Service Fabric niezawodnych kolekcji w aplikacji Service Fabric platformy Azure.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 63e6de436bdaceed7f1d2a78e8385dd14bfc0ed6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260915"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Wskazówki i zalecenia dotyczące niezawodnych kolekcji na platformie Azure Service Fabric
Ta sekcja zawiera wskazówki dotyczące używania niezawodnego menedżera stanu i niezawodnych kolekcji. Celem jest ułatwienie użytkownikom unikania typowych pułapek.

Wytyczne są zorganizowane w formie prostych zaleceń, które są poprzedzone *postanowieniami*, *należy rozważyć*, *unikać* i *nie*.

* Nie należy modyfikować obiektu typu niestandardowego zwracanego przez operacje odczytu (na przykład `TryPeekAsync` lub `TryGetValueAsync` ). Niezawodne kolekcje, podobnie jak kolekcje współbieżne, zwracają odwołanie do obiektów, a nie kopię.
* Przed zmodyfikowaniem należy skopiować do trybu szczegółowego zwrócony obiekt typu niestandardowego. Ponieważ struktury i typy wbudowane są przekazywane przez wartość, nie trzeba wykonywać na nich głębokiej kopii, chyba że zawierają pola z typem odwołania lub właściwości, które mają zostać zmodyfikowane.
* Nie należy używać `TimeSpan.MaxValue` w przypadku limitów czasu. Do wykrywania zakleszczeniów należy używać limitów czasu.
* Nie należy używać transakcji po jej zatwierdzeniu, przerwaniu lub usunięciu.
* Nie należy używać wyliczenia poza zakresem transakcji, w którym został utworzony.
* Nie należy tworzyć transakcji w instrukcji innej transakcji `using` , ponieważ może to spowodować zakleszczenie.
* Nie twórz niezawodnego stanu z `IReliableStateManager.GetOrAddAsync` i używaj niezawodnego stanu w ramach tej samej transakcji. Powoduje to InvalidOperationException.
* Upewnij się, że `IComparable<TKey>` implementacja jest poprawna. System bierze zależność od `IComparable<TKey>` scalania punktów kontrolnych i wierszy.
* Użyj blokady aktualizacji podczas odczytywania elementu z zamiarem aktualizacji, aby zapobiec określonej klasie zakleszczenii.
* Należy rozważyć utrzymywanie liczby niezawodnych kolekcji na partycję poniżej 1000. Preferuj niezawodne kolekcje z większą liczbą elementów niż bardziej niezawodne kolekcje z mniejszą liczbą elementów.
* Rozważ przechowywanie elementów (na przykład TKey + TValue dla niezawodnego słownika) poniżej 80 kilobajtów: mniejszej niż lepsza. Zmniejsza to stopień użycia sterty dużego obiektu, a także wymagania dotyczące operacji we/wy na dysku i sieci. Często program zmniejsza replikację zduplikowanych danych, gdy jest aktualizowana tylko jedna niewielka część wartości. Typowym sposobem osiągnięcia tego działania w niezawodnym słowniku jest przerwanie wierszy w wielu wierszach.
* Należy rozważyć użycie funkcji tworzenia kopii zapasowych i przywracania w celu odzyskiwania po awarii.
* Unikaj mieszania operacji pojedynczych jednostek i operacji wielojednostkowych ( `GetCountAsync` np `CreateEnumerableAsync` .) w ramach tej samej transakcji z powodu różnych poziomów izolacji.
* Do obsługi InvalidOperationException. Transakcje użytkowników mogą być przerwane przez system z różnych powodów. Na przykład gdy niezawodny Menedżer stanu zmienia swoją rolę poza podstawową lub gdy długotrwała transakcja blokuje obcinanie dziennika transakcyjnego. W takich przypadkach użytkownik może otrzymać InvalidOperationException wskazujący, że ich transakcja została już zakończona. Przy założeniu, że zakończenie transakcji nie zostało zlecone przez użytkownika, najlepszym sposobem obsłużenia tego wyjątku jest pozbycie transakcji, sprawdzenie, czy token anulowania został zasygnalizowani (lub że rola repliki została zmieniona), i jeśli nie zostanie utworzona nowa transakcja i ponów próbę.  

Poniżej przedstawiono niektóre kwestie, o których należy pamiętać:

* Domyślny limit czasu wynosi cztery sekundy dla wszystkich niezawodnych interfejsów API kolekcji. Większość użytkowników powinna używać domyślnego limitu czasu.
* Domyślny token anulowania to `CancellationToken.None` wszystkie niezawodne interfejsy API kolekcji.
* Parametr typu klucza (*TKey*) dla niezawodnego słownika musi poprawnie implementować `GetHashCode()` i `Equals()` . Klucze muszą być niezmienne.
* Aby uzyskać wysoką dostępność dla niezawodnych kolekcji, każda usługa powinna mieć co najmniej docelowy i minimalny rozmiar zestawu replik 3.
* Operacje odczytu na serwerze pomocniczym mogą odczytywać wersje, które nie są zatwierdzone przez kworum.
  Oznacza to, że wersja danych odczytana z pojedynczej pomocniczej może być fałszywa.
  Odczyty z podstawowego są zawsze stabilne: nigdy nie może być fałszywie zaawansowana.
* Bezpieczeństwo/prywatność danych utrwalonych przez aplikację w niezawodnej kolekcji to decyzja i podlegająca ochronie zapewnianej przez Zarządzanie magazynem; co. Szyfrowanie dysków systemu operacyjnego może służyć do ochrony danych przechowywanych w usłudze Rest.
* `ReliableDictionary` Wyliczenie używa posortowanej struktury danych uporządkowanej według klucza. Aby zwiększyć efektywność wyliczania, zatwierdzenia są dodawane do tymczasowej tabeli podręcznej i później przenoszone do głównego posortowanej struktury danych post. Dodaje/aktualizuje/usuwa środowisko uruchomieniowe najlepszego przypadku o (1) i najgorszym czasie wykonywania operacji o wartości o (log n), w przypadku sprawdzania poprawności obecności klucza. Pobieranie może mieć wartość O (1) lub O (log n) w zależności od tego, czy czytasz od ostatniego zatwierdzenia, czy ze starszego zatwierdzenia.

## <a name="volatile-reliable-collections"></a>Niezawodne kolekcje nietrwałe
Podczas decydowania o korzystaniu z niezawodnych kolekcji nietrwałych należy wziąć pod uwagę następujące kwestie:

* ```ReliableDictionary``` czy obsługa nietrwałych
* ```ReliableQueue``` czy obsługa nietrwałych
* ```ReliableConcurrentQueue``` nie ma obsługi nietrwałej
* Utrwalone usługi nie mogą być nietrwałe. Zmiana ```HasPersistedState``` flagi ```false``` wymaga ponownego utworzenia całej usługi od podstaw
* Nietrwałe usługi nie mogą zostać utrwalone. Zmiana ```HasPersistedState``` flagi ```true``` wymaga ponownego utworzenia całej usługi od podstaw
* ```HasPersistedState``` jest konfiguracją poziomu usługi. Oznacza to, że **wszystkie** kolekcje zostaną utrwalone lub nietrwałe. Nie można mieszać nietrwałych i utrwalonych kolekcji
* Utrata partycji nietrwałej kworum powoduje utratę danych
* Tworzenie kopii zapasowych i przywracanie nie jest dostępne dla usług lotnych

## <a name="next-steps"></a>Następne kroki
* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transakcje i blokady](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Zarządzanie danymi
  * [Tworzenie kopii zapasowej i przywracanie](service-fabric-reliable-services-backup-restore.md)
  * [Powiadomienia](service-fabric-reliable-services-notifications.md)
  * [Serializacja i uaktualnienie](service-fabric-application-upgrade-data-serialization.md)
  * [Konfiguracja niezawodnego menedżera stanu](service-fabric-reliable-services-configuration.md)
* Inne
  * [Reliable Services — Szybki Start](service-fabric-reliable-services-quick-start.md)
  * [Dokumentacja dla deweloperów dla niezawodnych kolekcji](/dotnet/api/microsoft.servicefabric.data.collections?view=azure-dotnet#microsoft_servicefabric_data_collections)
