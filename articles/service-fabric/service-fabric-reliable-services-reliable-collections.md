---
title: Wprowadzenie do niezawodnych kolekcji
description: Service Fabric usługi stanowe udostępniają niezawodne kolekcje, które umożliwiają pisanie aplikacji w chmurze o wysokiej dostępności i skalowalności.
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 7d705f81b4ad31559886e43226febcd4cf1d345d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784380"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Wprowadzenie do niezawodnych kolekcji w usłudze Azure Service Fabric stanowe usługi

Niezawodne Kolekcje umożliwiają pisanie aplikacji w chmurze o wysokiej dostępności, skalowalności i małych opóźnieniach, tak jakby były napisane aplikacje pojedynczej aplikacji. Klasy w przestrzeni nazw **Microsoft. servicefabric. Data. Collections** zapewniają zestaw kolekcji, które automatycznie sprawiają, że stan ma wysoką dostępność. Deweloperzy muszą programować tylko do niezawodnych interfejsów API kolekcji i umożliwiają niezawodne kolekcje zarządzać stanem replikowanym i lokalnym.

Kluczowa różnica między niezawodnymi kolekcjami i innymi technologiami wysokiej dostępności (takimi jak Redis, Azure Table service i Azure usługa kolejki) polega na tym, że stan jest przechowywany lokalnie w wystąpieniu usługi, a także zapewnia wysoką dostępność. Oznacza to, że:

* Wszystkie odczyty są lokalne, co skutkuje małymi opóźnieniami i odczytami o wysokiej przepływności.
* Wszystkie zapisy mają minimalną liczbę urządzeń z systemem IOs, co skutkuje małymi opóźnieniami i zapisami o wysokiej przepływności.

![Obraz ewolucji kolekcji.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Niezawodne Kolekcje można traktować jako naturalną ewolucję klas **System. Collections** : nowy zestaw kolekcji przeznaczony do obsługi aplikacji w chmurze i wielokomputerowych bez zwiększania złożoności dla deweloperów. W związku z tym niezawodne kolekcje są następujące:

* Zreplikowane: zmiany stanu są replikowane w celu zapewnienia wysokiej dostępności.
* Asynchronicznie: interfejsy API są asynchroniczne, aby upewnić się, że wątki nie są blokowane podczas nanoszenia we/wy.
* Transakcyjne: interfejsy API wykorzystują abstrakcję transakcji, dzięki czemu można łatwo zarządzać wieloma niezawodnymi kolekcjami w ramach usługi.
* Utrwalone lub nietrwałe: dane mogą być utrwalane na dysku w celu zapewnienia trwałości przed awarią dużej skali (na przykład awaria napięcia). Niektóre niezawodne kolekcje obsługują również tryb nietrwały (z [zastrzeżeniami](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)), w którym wszystkie dane są przechowywane w pamięci, na przykład w zreplikowanej pamięci podręcznej w pamięci.

Niezawodne kolekcje oferują Gwarancje silnej spójności, aby zapewnić łatwiejsze tworzenie informacji o stanie aplikacji.
Silna spójność jest zapewniana przez zagwarantowanie, że zatwierdzenia transakcji kończą się tylko po zarejestrowaniu całej transakcji na większości kworum replik, łącznie z podstawowym.
Aby zapewnić słabą spójność, aplikacje mogą potwierdzić z powrotem do klienta/osoby żądającej przed zwróceniem asynchronicznej deklaracji.

Niezawodne interfejsy API kolekcji to ewolucja współbieżnych kolekcji interfejsów API (znajdujących się w przestrzeni nazw **System. Collections. współbieżne** ):

* Asynchroniczne: zwraca zadanie od, w przeciwieństwie do współbieżnych kolekcji, operacje są replikowane i utrwalane.
* Brak parametrów out: używa `ConditionalValue<T>` do zwracania `bool` wartości i zamiast parametrów out. `ConditionalValue<T>` przypomina `Nullable<T>` , ale nie musi być strukturą.
* Transakcje: używa obiektu transakcji, aby umożliwić użytkownikowi grupowanie akcji na wielu niezawodnych kolekcjach w transakcji.

Dzisiaj **firma Microsoft. servicefabric. Data. Collections** zawiera trzy Kolekcje:

* [Niezawodny słownik](/dotnet/api/microsoft.servicefabric.data.collections.ireliabledictionary-2#microsoft_servicefabric_data_collections_ireliabledictionary_2): reprezentuje replikowany, transakcyjny i asynchroniczny zbiór par klucz/wartość. Podobnie jak w przypadku **ConcurrentDictionary**, zarówno klucz, jak i wartość mogą być dowolnego typu.
* [Niezawodna Kolejka](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1): reprezentuje kolejkę z wbudowaną, transakcyjną i asynchroniczną, pierwszą kolejką (FIFO). Podobnie jak w przypadku **ConcurrentQueue**, wartość może być dowolnego typu.
* [Niezawodna Kolejka współbieżna](service-fabric-reliable-services-reliable-concurrent-queue.md): reprezentuje zreplikowaną, transakcyjną i asynchroniczną kolejkę kolejności, w której ma być duża przepływność. Podobnie jak w przypadku **ConcurrentQueue**, wartość może być dowolnego typu.

## <a name="next-steps"></a>Następne kroki

* [Wskazówki dotyczące niezawodnej kolekcji & zalecenia](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transakcje i blokady](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Zarządzanie danymi
  * [Wykonywanie kopii zapasowych i przywracanie](service-fabric-reliable-services-backup-restore.md)
  * [Powiadomienia](service-fabric-reliable-services-notifications.md)
  * [Serializacja elementu Reliable Collection](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serializacja i uaktualnienie](service-fabric-application-upgrade-data-serialization.md)
  * [Konfiguracja niezawodnego menedżera stanu](service-fabric-reliable-services-configuration.md)
* Inne
  * [Reliable Services — Szybki Start](service-fabric-reliable-services-quick-start.md)
  * [Dokumentacja dla deweloperów dla niezawodnych kolekcji](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
