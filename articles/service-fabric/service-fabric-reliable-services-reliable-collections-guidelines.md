---
title: Wskazówki dotyczące niezawodnych kolekcji
description: Wskazówki i zalecenia dotyczące korzystania z niezawodnych kolekcji sieci szkieletowej usług w aplikacji sieci szkieletowej usług Azure.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: db37067069b2a9eb08009eb6bb373f6fce1cafa9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398540"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Wskazówki i zalecenia dotyczące niezawodnych kolekcji w sieci szkieletowej usług Azure
Ta sekcja zawiera wskazówki dotyczące korzystania z Menedżera stanu niezawodne i niezawodne kolekcje. Celem jest, aby pomóc użytkownikom uniknąć typowych pułapek.

Wytyczne są zorganizowane jako proste zalecenia poprzedzone terminami *Do*, *Consider*, *Avoid* i *Do Not*.

* Nie należy modyfikować obiektu typu niestandardowego zwracanego `TryPeekAsync` `TryGetValueAsync`przez operacje odczytu (na przykład lub ). Niezawodne kolekcje, podobnie jak kolekcje współbieżne, zwracają odwołanie do obiektów, a nie kopię.
* Wykonaj głęboką kopię zwróconego obiektu typu niestandardowego przed zmodyfikowaniem go. Ponieważ struktury i typy wbudowane są przekazywania przez wartość, nie trzeba wykonywać głębokiej kopii na nich, chyba że zawierają pola wpisane odwołanie lub właściwości, które zamierzasz zmodyfikować.
* Nie używaj `TimeSpan.MaxValue` do limitów czasu. Limity czasu powinny służyć do wykrywania zakleszczenia.
* Nie należy używać transakcji po jej zatwierdzeniu, przerwaniu lub usunięciu.
* Nie należy używać wyliczenia poza zakresem transakcji, w który został utworzony.
* Nie należy tworzyć transakcji w `using` instrukcji innej transakcji, ponieważ może to spowodować zakleszczenia.
* Nie należy tworzyć `IReliableStateManager.GetOrAddAsync` niezawodnego stanu z i używać niezawodnego stanu w tej samej transakcji. Powoduje to InvalidOperationException.
* Upewnij się, `IComparable<TKey>` że implementacja jest poprawna. System przyjmuje zależność `IComparable<TKey>` do scalania punktów kontrolnych i wierszy.
* Użyj Update lock podczas odczytywania elementu z zamiarem zaktualizowania go, aby zapobiec określonej klasy zakleszczenia.
* Należy rozważyć utrzymanie liczby niezawodnych kolekcji na partycję jest mniejsza niż 1000. Preferuj niezawodne kolekcje z większą ilością przedmiotów niż bardziej niezawodne kolekcje z mniejszą liczbą przedmiotów.
* Należy rozważyć zachowanie elementów (na przykład TKey + TValue dla niezawodnego słownika) poniżej 80 KB: mniejsze, tym lepiej. Zmniejsza to zużycie sterty dużych obiektów, a także wymagania dotyczące we/wy dysku i sieci. Często zmniejsza replikowanie zduplikowanych danych, gdy aktualizowana jest tylko jedna mała część wartości. Typowym sposobem osiągnięcia tego w reliable dictionary, jest przerwanie wierszy do wielu wierszy.
* Należy rozważyć użycie funkcji tworzenia kopii zapasowych i przywracania, aby mieć odzyskiwanie po awarii.
* Unikaj mieszania operacji pojedynczych jednostek i `GetCountAsync`operacji `CreateEnumerableAsync`wielopodobiennych (np. ) w tej samej transakcji ze względu na różne poziomy izolacji.
* Czy obsłużyć InvalidOperationException. Transakcje użytkownika mogą zostać przerwane przez system z różnych powodów. Na przykład, gdy Menedżer stanu niezawodnego zmienia swoją rolę z podstawowego lub gdy długotrwała transakcja blokuje obcięcie dziennika transakcyjnego. W takich przypadkach użytkownik może otrzymać InvalidOperationException wskazując, że ich transakcja została już zakończona. Zakładając, że zakończenie transakcji nie został poproszony przez użytkownika, najlepszym sposobem obsługi tego wyjątku jest pozbycie się transakcji, sprawdź, czy token anulowania został zasygnalizowany (lub rola repliki została zmieniona), a jeśli nie utworzyć nową transakcję i ponowić próbę.  

Oto kilka rzeczy, o których należy pamiętać:

* Domyślny limit czasu wynosi cztery sekundy dla wszystkich interfejsów API niezawodnej kolekcji. Większość użytkowników powinna używać domyślnego przesuwu.
* Domyślny token `CancellationToken.None` anulowania znajduje się we wszystkich interfejsach API niezawodnej kolekcji.
* Parametr typu klucza *(TKey)* dla słownika `GetHashCode()` `Equals()`niezawodnego musi być poprawnie zaimplementowany i . Klucze muszą być niezmienne.
* Aby osiągnąć wysoką dostępność dla niezawodnych kolekcji, każda usługa powinna mieć co najmniej docelowy i minimalny rozmiar zestawu replik 3.
* Operacje odczytu w pomocniczych może odczytywać wersje, które nie są zatwierdzone kworum.
  Oznacza to, że wersja danych, która jest odczytywana z jednego pomocniczego może być false progressed.
  Odczyty z podstawowego są zawsze stabilne: nigdy nie może być false progressed.
* Bezpieczeństwo/prywatność danych utrzymywanych przez aplikację w wiarygodnym kolekcji jest twoją decyzją i podlega ochronie zapewnianej przez zarządzanie pamięcią masową; Tj. Szyfrowanie dysku systemu operacyjnego może służyć do ochrony danych w stanie spoczynku.  

## <a name="volatile-reliable-collections"></a>Niestabilne niezawodne kolekcje
Decydując się na użycie nietrwałych kolekcji niezawodnych, należy wziąć pod uwagę następujące kwestie:

* ```ReliableDictionary```ma niestabilne wsparcie
* ```ReliableQueue```ma niestabilne wsparcie
* ```ReliableConcurrentQueue```NIE ma wsparcia lotnych
* Utrwalone usługi NIE MOGĄ być niestabilne. Zmiana ```HasPersistedState``` flagi ```false``` na wymaga odtworzenia całej usługi od podstaw
* Usługi nietrwałe nie mogą być utrzymywane. Zmiana ```HasPersistedState``` flagi ```true``` na wymaga odtworzenia całej usługi od podstaw
* ```HasPersistedState```jest config poziomie usług. Oznacza to, że **wszystkie** kolekcje będą zachowywane lub volatile. Nie można mieszać nietrwałych i utrwalonych kolekcji
* Utrata kworum partycji nietrwałej powoduje całkowitą utratę danych
* Tworzenie kopii zapasowych i przywracanie NIE jest dostępne dla usług nietrwałych

## <a name="next-steps"></a>Następne kroki
* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transakcje i blokady](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Zarządzanie danymi
  * [Tworzenie kopii zapasowych i przywracanie](service-fabric-reliable-services-backup-restore.md)
  * [Powiadomienia](service-fabric-reliable-services-notifications.md)
  * [Serializacja i uaktualnianie](service-fabric-application-upgrade-data-serialization.md)
  * [Niezawodna konfiguracja menedżera stanu](service-fabric-reliable-services-configuration.md)
* Inne
  * [Szybki start niezawodnych usług](service-fabric-reliable-services-quick-start.md)
  * [Informacje dla dewelopera dotyczące niezawodnych kolekcji](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
