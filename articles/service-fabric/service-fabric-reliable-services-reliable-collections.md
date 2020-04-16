---
title: Wprowadzenie do niezawodnych kolekcji
description: Usługi stanowe sieci szkieletowej usług zapewniają niezawodne kolekcje, które umożliwiają pisanie aplikacji w chmurze o wysokiej dostępności, skalowalnych i o małych opóźnieniach.
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 78ecc57a4da43bf416839226253e6d0e2f4c1651
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398428"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Wprowadzenie do niezawodnych kolekcji w usługach stanowych usługi usługi Azure Service Fabric

Niezawodne kolekcje umożliwiają pisanie aplikacji w chmurze o wysokiej dostępności, skalowalnych i o małych opóźnieniach, tak jak podczas pisania aplikacji jednokomputerowych. Klasy w obszarze nazw **Microsoft.ServiceFabric.Data.Collections** zapewniają zestaw kolekcji, które automatycznie sprawiają, że stan jest wysoce dostępny. Deweloperzy muszą programować tylko do interfejsów API niezawodnej kolekcji i niech niezawodne kolekcje zarządzać zreplikowanym i lokalnym stanie.

Kluczową różnicą między niezawodne kolekcje i innych technologii wysokiej dostępności (takich jak Redis, usługa Azure Table service i usługi Azure Queue) jest, że stan jest przechowywany lokalnie w wystąpieniu usługi, a jednocześnie są udostępniane jako wysoce dostępne. Oznacza to, że:

* Wszystkie odczyty są lokalne, co powoduje niskie opóźnienia i odczyty o wysokiej przepływności.
* Wszystkie zapisy ponoszą minimalną liczbę we/wy sieci, co powoduje niskie opóźnienia i zapisy o wysokiej przepływności.

![Obraz ewolucji kolekcji.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Niezawodne kolekcje można traktować jako naturalną ewolucję klasy **System.Collections:** nowy zestaw kolekcji, które są przeznaczone dla aplikacji w chmurze i wielu komputerach bez zwiększania złożoności dla dewelopera. W związku z tym niezawodne kolekcje są:

* Replikowane: zmiany stanu są replikowane w celu zapewnienia wysokiej dostępności.
* Asynchroniczne: interfejsy API są asynchroniczne, aby upewnić się, że wątki nie są blokowane podczas ponoszenia we/wy.
* Transakcyjne: interfejsy API wykorzystują abstrakcję transakcji, dzięki czemu można łatwo zarządzać wieloma niezawodnymi kolekcjami w ramach usługi.
* Utrwalone lub nietrwałe: Dane mogą być utrwalone na dysku w celu trwałości przed awariami na dużą skalę (na przykład awaria zasilania centrum danych). Niektóre niezawodne kolekcje obsługują również tryb nietrwały (z [zastrzeżeniami),](service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)w którym wszystkie dane są przechowywane w pamięci, takie jak replikowana pamięć podręczna w pamięci.

Niezawodne kolekcje zapewniają silne gwarancje spójności po wyjęciu z pudełka, aby ułatwić rozumowanie dotyczące stanu aplikacji.
Silna spójność jest osiągana przez zapewnienie zatwierdzenia transakcji zakończyć tylko po całej transakcji został zarejestrowany na kworum większości replik, w tym podstawowego.
Aby osiągnąć słabszą spójność, aplikacje mogą potwierdzić powrót do klienta/żądacza przed zwraca zatwierdzenia asynchroniiowego.

Interfejsy API niezawodnych kolekcji są ewolucją równoczesnych interfejsów API kolekcji (znalezionych w obszarze nazw **System.Collections.Concurrent):**

* Asynchroniczne.
* Brak parametrów `ConditionalValue<T>` out: Służy `bool` do zwracania wartości i wartości zamiast out parametrów. `ConditionalValue<T>`jest `Nullable<T>` podobny, ale nie wymaga T być strużką.
* Transakcje: używa obiektu transakcji, aby umożliwić użytkownikowi grupowanie akcji na wielu niezawodnych kolekcjach w transakcji.

Obecnie **microsoft.ServiceFabric.Data.Collections** zawiera trzy kolekcje:

* [Niezawodny słownik:](https://msdn.microsoft.com/library/azure/dn971511.aspx)Reprezentuje replikowaną, transakcyjną i asynchroniczne kolekcję par klucz/wartość. Podobnie **jak ConcurrentDictionary**, klucz i wartość może być dowolnego typu.
* [Niezawodna kolejka:](https://msdn.microsoft.com/library/azure/dn971527.aspx)Reprezentuje replikowaną, transakcyjną i asynchroniczne ścisłe pierwsze w, pierwszy na zewnątrz (FIFO) kolejki. Podobnie jak **ConcurrentQueue**, wartość może być dowolnego typu.
* [Niezawodna kolejka współbieżna:](service-fabric-reliable-services-reliable-concurrent-queue.md)reprezentuje replikowaną, transakcyjną i asynchroniczne kolejki zamawiania najlepszych wysiłków na potrzeby wysokiej przepływności. Podobnie **jak ConcurrentQueue**, wartość może być dowolnego typu.

## <a name="next-steps"></a>Następne kroki

* [Wytyczne dotyczące niezawodnej zbiórki & zalecenia](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transakcje i blokady](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Zarządzanie danymi
  * [Tworzenie kopii zapasowych i przywracanie](service-fabric-reliable-services-backup-restore.md)
  * [Powiadomienia](service-fabric-reliable-services-notifications.md)
  * [Serializacja elementu Reliable Collection](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serializacja i uaktualnianie](service-fabric-application-upgrade-data-serialization.md)
  * [Niezawodna konfiguracja menedżera stanu](service-fabric-reliable-services-configuration.md)
* Inne
  * [Szybki start niezawodnych usług](service-fabric-reliable-services-quick-start.md)
  * [Informacje dla dewelopera dotyczące niezawodnych kolekcji](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
