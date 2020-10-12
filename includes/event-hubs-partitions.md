---
title: plik dołączany
description: plik dołączany
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: dc7c86ff1df48f9ce96769098f7aab76d33c8822
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "68481534"
---
Usługa Event Hubs udostępnia funkcję transmisji strumieniowej komunikatów za pośrednictwem partycjonowanego wzorca odbiorców, w ramach którego każdy odbiorca odczytuje tylko konkretny podzbiór (partycję) strumienia komunikatów. Ten wzorzec umożliwia skalowanie w poziomie przetwarzania zdarzeń oraz udostępnia inne funkcje dotyczące strumienia, które są niedostępne w przypadku kolejek i tematów.

Partycja to uporządkowana sekwencja zdarzeń przechowywana w centrum zdarzeń. Po nadejściu nowszych zdarzeń są one dodawane na końcu sekwencji. Partycję można traktować jako „dziennik zatwierdzania”.

![Event Hubs](./media/event-hubs-partitions/partition.png)

Event Hubs zachowuje dane przez skonfigurowany czas przechowywania, który jest stosowany dla wszystkich partycji w centrum zdarzeń. Zdarzenia wygasają czasowo — nie można ich jawnie usunąć. Ponieważ partycje są niezależne i zawierają własne sekwencje danych, często rosną z różną szybkością.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Liczba partycji jest określana podczas tworzenia i musi należeć do zakresu od 2 do 32. Liczby partycji nie można zmieniać, dlatego ustawiając liczbę partycji, trzeba planować długoterminowo. Partycje stanowią mechanizm organizacji danych powiązany z równoległością podrzędną wymaganą w aplikacjach korzystających z tych danych. Liczba partycji w centrum zdarzeń jest bezpośrednio związana z oczekiwaną liczbą jednoczesnych czytników. Możesz zwiększyć liczbę partycji ponad 32, kontaktując się z zespołem ds. usługi Event Hubs.

Może być konieczne ustawienie najwyższej możliwej wartości, która jest 32 w momencie tworzenia. Należy pamiętać, że z więcej niż jedną partycją będzie można wysyłać zdarzenia do wielu partycji bez zachowywania kolejności, chyba że skonfigurowano nadawców tylko do jednej partycji z 32, pozostawiając pozostałe 31 partycji. W poprzednim przypadku trzeba będzie odczytywać zdarzenia ze wszystkich partycji 32. W tym drugim przypadku nie ma żadnych oczywistych dodatkowych kosztów poza dodatkową konfiguracją, którą trzeba wykonać na hoście procesora zdarzeń.

Podczas gdy partycje są identyfikowane i mogą być wysyłane bezpośrednio do partycji, nie jest to zalecane. Zamiast tego można użyć konstrukcji wyższego poziomu wprowadzonych w sekcji [wydawcy zdarzeń](../articles/event-hubs/event-hubs-features.md#event-publishers) . 

Partycje są wypełnione sekwencją danych zdarzenia, które obejmują treść zdarzenia, zdefiniowany przez użytkownika zbiór właściwości oraz metadane, takie jak przesunięcie w partycji i jego numer w sekwencji strumienia.

Firma Microsoft zaleca, aby zapewnić optymalne skalowanie jednostek przepływności i partycji 1:1. Pojedyncza partycja ma gwarantowane dane wejściowe i wyjściowe do jednej jednostki przepływności. Chociaż możliwe jest osiągnięcie większej przepływności na partycji, wydajność nie jest gwarantowana. Dlatego zdecydowanie zaleca się, aby liczba partycji w centrum zdarzeń była większa lub równa liczbie jednostek przepływności.

Mając na względzie łączną przepływność, z której korzystasz, musisz znać żądaną liczbę jednostek przepływności i minimalną liczbę partycji, ale liczbę partycji należy wykonać? Wybierz liczbę partycji na podstawie równoległości podrzędnej, która ma zostać osiągnięta, a także do przyszłych potrzeb dotyczących przepływności. Nie jest naliczana opłata za liczbę partycji znajdujących się w centrum zdarzeń.

Aby uzyskać więcej informacji na temat partycji i równowagi między dostępnością i niezawodnością, zobacz [Przewodnik dotyczący programowania w usłudze Event Hubs](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) i artykuł [Availability and consistency in Event Hubs](../articles/event-hubs/event-hubs-availability-and-consistency.md) (Dostępność i spójność w usłudze Event Hubs).
