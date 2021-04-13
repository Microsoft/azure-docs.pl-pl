---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 03/15/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 1dd78ba3799573e05e4ebbf55887bae3d9674b7c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310085"
---
Event Hubs organizuje sekwencje zdarzeń wysyłanych do centrum zdarzeń w co najmniej jednej partycji. Po nadejściu nowszych zdarzeń są one dodawane na końcu tej sekwencji. 

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Partycja może być uważana za "Dziennik zatwierdzeń". Partycje przechowują dane zdarzeń, które zawierają treść zdarzenia, zdefiniowany przez użytkownika zbiór właściwości opisujący zdarzenie, metadane, takie jak przesunięcie w partycji, jego numer w sekwencji strumienia oraz sygnaturę czasową po stronie usługi, przy której został zaakceptowany.

![Diagram przedstawiający starszą i nowszą sekwencję zdarzeń.](./media/event-hubs-partitions/partition.png)

### <a name="advantages-of-using-partitions"></a>Zalety korzystania z partycji
Event Hubs jest zaprojektowana w celu ułatwienia przetwarzania dużych ilości zdarzeń, a partycjonowanie ułatwia to na dwa sposoby:

- Mimo że Event Hubs jest usługą PaaS, istnieje fizyczna rzeczywistość, a obsługa dziennika, który zachowuje porządek zdarzeń, wymaga, aby te zdarzenia były przechowywane razem w źródłowym magazynie i jego replikach, a także w efekcie pułap przepływności dla tego dziennika. Partycjonowanie umożliwia używanie wielu dzienników równoległych dla tego samego centrum zdarzeń, co w związku z tym mnoży dostępną pierwotną przepływność we/wy.
- Własne aplikacje muszą być w stanie kontynuować przetwarzanie ilości zdarzeń wysyłanych do centrum zdarzeń. Może to być złożone i wymaga znacznej, skalowanej pojemności przetwarzania równoległego. Pojemność pojedynczego procesu obsługującego zdarzenia jest ograniczona, więc potrzebujesz kilku procesów. Partycje to sposób, w jaki Twoje rozwiązanie przesyła strumieniowo te procesy, a jeszcze gwarantuje, że każde zdarzenie ma zrozumiały właściciel przetwarzania. 

### <a name="number-of-partitions"></a>Liczba partycji
Liczba partycji jest określana podczas tworzenia i musi zawierać się w przedziale od 1 do 32 w standardzie Event Hubs. Liczba partycji może należeć do 2000 partycji na jednostkę pojemności w Event Hubs — warstwa Dedykowana. 

Zalecamy wybranie co najmniej tylu partycji, które powinny być wymagane w przypadku trwałych [jednostek przepływności (jednostek przepływności)](../articles/event-hubs/event-hubs-faq.yml#what-are-event-hubs-throughput-units-) podczas szczytowego ładowania aplikacji dla danego centrum zdarzeń. Należy obliczyć z jedną partycją o pojemności 1 jednostek PRZEPŁYWNOŚCI (1 MB w, 2 MB na zewnątrz). Możesz skalować TUs w przestrzeni nazw lub jednostki pojemności klastra niezależnie od liczby partycji. Centrum zdarzeń z partycjami 32 lub centrum zdarzeń z 1 partycją ponosi dokładnie ten sam koszt, gdy przestrzeń nazw jest ustawiona na 1 jednostek PRZEPŁYWNOŚCI pojemność. 

Liczba partycji centrum zdarzeń w [dedykowanym klastrze Event Hubs](../articles/event-hubs/event-hubs-dedicated-overview.md) można [zwiększyć](../articles/event-hubs/dynamically-add-partitions.md) po utworzeniu centrum zdarzeń, ale Rozkład strumieni między partycjami ulegnie zmianie, gdy zostanie on zmieniony, gdy zostanie zmienione mapowanie kluczy partycji na partycje, dlatego należy spróbować trudno uniknąć takich zmian, jeśli względna kolejność zdarzeń w aplikacji.

Ustawienie liczby partycji na maksymalną dozwoloną wartość jest uciążliwe, ale zawsze należy pamiętać, że strumienie zdarzeń muszą być strukturalne, aby można było korzystać z wielu partycji. Jeśli potrzebujesz bezwzględnie zamówionej kolejności dla wszystkich zdarzeń lub tylko kilku podstrumienia, możesz nie być w stanie korzystać z wielu partycji. Ponadto wiele partycji sprawia, że przetwarzanie jest bardziej skomplikowane. 


### <a name="mapping-of-events-to-partitions"></a>Mapowanie zdarzeń na partycje
Klucz partycji służy do mapowania danych zdarzeń przychodzących na określone partycje na potrzeby organizowania danych. Klucz partycji to wartość podawana przez nadawcę przekazywana do centrum zdarzeń. Jest on przetwarzany przez statyczną funkcję tworzenia skrótu, za pomocą której tworzone jest przypisanie partycji. Jeśli nie określisz klucza partycji podczas publikowania zdarzenia, używane jest przypisanie działania okrężnego.

Wydawca zdarzeń ma informacje tylko o kluczu partycji, a nie partycji, na której publikowane są zdarzenia. To oddzielenie klucza od partycji powoduje, że nadawca nie musi wiedzieć zbyt dużo o przetwarzaniu podrzędnym. Unikatowa tożsamość urządzenia lub użytkownika stanowi dobry klucz partycji, ale inne atrybuty, takie jak lokalizacja geograficzna, mogą również zostać użyte do grupowania powiązanych zdarzeń w jedną partycję.

Określenie klucza partycji umożliwia utrzymywanie powiązanych zdarzeń w tej samej partycji i w kolejności, w jakiej zostały wysłane. Klucz partycji to jakiś ciąg pochodzący z kontekstu aplikacji i identyfikujący wzajemne relacje zdarzeń. Sekwencja zdarzeń identyfikowanych przez klucz partycji jest *strumieniem*. Partycja to multipleksowy magazyn dzienników dla wielu takich strumieni. 

> [!NOTE]
> Mimo że możesz wysyłać zdarzenia bezpośrednio do partycji, nie zalecamy tego, zwłaszcza gdy wysoka dostępność jest dla Ciebie ważna. Obniża on dostępność centrum zdarzeń do poziomu partycji. Aby uzyskać więcej informacji, zobacz [dostępność i spójność](../articles/event-hubs/event-hubs-availability-and-consistency.md).

