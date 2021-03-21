---
title: Transakcje i tryby blokowania w niezawodnych kolekcjach
description: Usługa Azure Service Fabric niezawodny Menedżer stanu i niezawodne transakcje kolekcji oraz blokowanie.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: 5d2cbb517ea5ca45697cd9124b82e9ef13dd32db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784346"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transakcje i tryby blokowania w usłudze Azure Service Fabric niezawodne Kolekcje

## <a name="transaction"></a>Transakcja

Transakcja to sekwencja operacji wykonywanych w ramach pojedynczej jednostki logicznej pracy. Wykazuje właściwości typowego [kwasu](https://en.wikipedia.org/wiki/ACID) (*niepodzielności*, *spójności*, *izolacji*, *trwałości*) transakcji bazy danych:

* **Niepodzielność**: transakcja musi być niepodzielną jednostką pracy. Innymi słowy, wszystkie modyfikacje danych są wykonywane lub żadne z nich nie są wykonywane.
* **Spójność**: po zakończeniu transakcji należy pozostawić wszystkie dane w spójnym stanie. Wszystkie wewnętrzne struktury danych muszą być poprawne na końcu transakcji.
* **Izolacja**: modyfikacje dokonane przez współbieżne transakcje muszą być odizolowane od zmian wprowadzonych przez inne współbieżne transakcje. Poziom izolacji użyty dla operacji w ramach metody [ITransaction](/dotnet/api/microsoft.servicefabric.data.itransaction) jest określany przez [IReliableState](/dotnet/api/microsoft.servicefabric.data.ireliablestate) wykonujący operację.
* **Trwałość**: po zakończeniu transakcji jego skutki mają stałe miejsce w systemie. Modyfikacje są zachowywane nawet w przypadku awarii systemu.

### <a name="isolation-levels"></a>Poziomy izolacji

Poziom izolacji definiuje stopień odizolowania transakcji od modyfikacji dokonanych przez inne transakcje.
Istnieją dwa poziomy izolacji, które są obsługiwane w niezawodnych kolekcjach:

* **Powtarzający się odczyt**: określa, że instrukcje nie mogą odczytywać danych, które zostały zmodyfikowane, ale nie zostały jeszcze zatwierdzone przez inne transakcje i że żadne inne transakcje nie mogą modyfikować danych odczytanych przez bieżącą transakcję do momentu zakończenia bieżącej transakcji.
* **Migawka**: określa, że dane odczytane przez żadną instrukcję w transakcji to niespójna w pełni funkcjonalna wersja danych, które istniały na początku transakcji.
  Transakcja może rozpoznać tylko modyfikacje danych, które zostały zatwierdzone przed rozpoczęciem transakcji.
  Modyfikacje danych wprowadzone przez inne transakcje po rozpoczęciu bieżącej transakcji nie są widoczne dla instrukcji wykonywanych w bieżącej transakcji.
  Jest to efekt, tak jakby instrukcje w transakcji uzyskują migawkę zatwierdzonych danych, które istniały na początku transakcji.
  Migawki są spójne dla niezawodnych kolekcji.

Niezawodne kolekcje automatycznie wybierają poziom izolacji, który ma być używany dla danej operacji odczytu, w zależności od operacji i roli repliki w momencie tworzenia transakcji.
Poniżej znajduje się tabela, która przedstawia ustawienia domyślne na poziomie izolacji dla niezawodnych operacji słownika i kolejki.

| Operacja \ rola | Podstawowe | Pomocniczy |
| --- |:--- |:--- |
| Odczyt pojedynczej jednostki |Odczyt powtarzalny |Snapshot |
| Wyliczenie, liczba |Snapshot |Snapshot |

> [!NOTE]
> Typowe przykłady operacji pojedynczych jednostek to `IReliableDictionary.TryGetValueAsync` , `IReliableQueue.TryPeekAsync` .
> 

Zarówno niezawodny słownik, jak i niezawodna Kolejka obsługują *odczytywanie zapisów*.
Innymi słowy, każdy zapis w ramach transakcji będzie widoczny dla następującego odczytu, który należy do tej samej transakcji.

## <a name="locks"></a>Blokady

W niezawodnych kolekcjach wszystkie transakcje implementują rygorystyczne dwie fazy blokowania: transakcja nie zwalnia blokad, które uzyskały do momentu zakończenia transakcji z przerwaniem lub zatwierdzeniem.

Niezawodny słownik używa blokowania na poziomie wierszy dla wszystkich operacji pojedynczych jednostek.
Niezawodna wymiana transakcji w ramach współbieżności dla rygorystycznej transakcyjnej właściwości FIFO.
Niezawodna Kolejka używa blokad na poziomie operacji, umożliwiając jedną transakcję z `TryPeekAsync` i/lub `TryDequeueAsync` i jedną transakcję `EnqueueAsync` jednocześnie.
Należy pamiętać, że w celu zachowania FIFO, jeśli `TryPeekAsync` lub `TryDequeueAsync` kiedykolwiek obserwuje, że niezawodna kolejka jest pusta, również zostanie zablokowana `EnqueueAsync` .

Operacje zapisu zawsze pobierają blokady na wyłączność.
W przypadku operacji odczytu blokowanie zależy od kilku czynników:

- Wszystkie operacje odczytu wykonane przy użyciu izolacji migawki są wolne od blokady.
- Wszystkie powtarzalne operacje odczytu domyślnie pobierają blokady udostępnione.
- Jednak dla każdej operacji odczytu, która obsługuje powtarzanie odczytu, użytkownik może poprosił o blokadę aktualizacji zamiast blokady udostępnionej.
Blokada aktualizacji jest asymetryczną blokadą używaną do zapobiegania wspólnej formie zakleszczenia, która występuje, gdy wiele transakcji blokuje zasoby na potrzeby potencjalnych aktualizacji w późniejszym czasie.

Macierz zgodności blokad można znaleźć w poniższej tabeli:

| Żądanie \ przyznane | Brak | Udostępniona | Aktualizacja | Klucz |
| --- |:--- |:--- |:--- |:--- |
| Udostępniona |Brak konfliktu |Brak konfliktu |Konflikt |Konflikt |
| Aktualizacja |Brak konfliktu |Brak konfliktu |Konflikt |Konflikt |
| Klucz |Brak konfliktu |Konflikt |Konflikt |Konflikt |

Argument timeout w niezawodnych kolekcjach interfejsów API jest używany do wykrywania zakleszczenia.
Na przykład dwie transakcje (T1 i T2) próbują odczytywać i aktualizować K1.
Istnieje możliwość zakleszczenia, ponieważ oba te elementy kończą się z blokadą udostępnioną.
W takim przypadku jedna lub obie operacje przekroczą limit czasu. W tym scenariuszu blokada aktualizacji może uniemożliwić takie zakleszczenie.

## <a name="next-steps"></a>Następne kroki

* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Powiadomienia Reliable Services](service-fabric-reliable-services-notifications.md)
* [Reliable Services kopii zapasowej i przywracania (odzyskiwanie po awarii)](service-fabric-reliable-services-backup-restore.md)
* [Konfiguracja niezawodnego menedżera stanu](service-fabric-reliable-services-configuration.md)
* [Dokumentacja dla deweloperów dla niezawodnych kolekcji](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
