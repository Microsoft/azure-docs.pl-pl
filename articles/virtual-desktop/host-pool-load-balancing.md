---
title: Równoważenie obciążenia puli hostów usług pulpitu wirtualnego systemu Windows — Azure
description: Metody równoważenia obciążenia puli hostów dla środowiska pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 15d50033a1316601dd8c36bd5748c659f4397d66
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612048"
---
# <a name="host-pool-load-balancing-methods"></a>Metody równoważenia obciążenia puli hostów

>[!IMPORTANT]
>Ta zawartość ma zastosowanie do aktualizacji wiosennej 2020 z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows, wykorzystaj wersję 2019 bez obiektów Azure Resource Manager, zobacz [ten artykuł](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md).
>
> Aktualizacja systemu Windows Virtual Desktop wiosna 2020 jest obecnie dostępna w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie zalecamy jej używania w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. 
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pulpit wirtualny systemu Windows obsługuje dwie metody równoważenia obciążenia. Każda metoda określa, który Host sesji będzie hostować sesję użytkownika, gdy łączą się z zasobem w puli hostów.

Następujące metody równoważenia obciążenia są dostępne w programie Virtual Desktop systemu Windows:

- Równoważenie obciążenia w pierwszej kolejności umożliwia równomierne dystrybuowanie sesji użytkowników między hostami sesji w puli hostów.
- Głębokość — pierwsze Równoważenie obciążenia pozwala na nasycenie hosta sesji z sesjami użytkowników w puli hostów. Gdy pierwsza sesja osiągnie próg limitu sesji, moduł równoważenia obciążenia kieruje wszystkie nowe połączenia użytkowników do następnego hosta sesji w puli hostów do momentu osiągnięcia limitu i tak dalej.

Dla każdej puli hostów można skonfigurować tylko jeden typ równoważenia obciążenia. Jednak obie metody równoważenia obciążenia współdzielą następujące zachowania niezależnie od tego, z których puli hostów znajdują się:

- Jeśli użytkownik ma już sesję w puli hostów i ponownie nawiązuje połączenie z tą sesją, moduł równoważenia obciążenia pomyślnie przekieruje je do hosta sesji z istniejącą sesją. To zachowanie ma zastosowanie, nawet jeśli właściwość AllowNewConnections hosta sesji ma wartość false.
- Jeśli użytkownik nie ma jeszcze sesji w puli hostów, moduł równoważenia obciążenia nie będzie uwzględniać hostów sesji, których właściwość AllowNewConnections jest ustawiona na wartość false podczas równoważenia obciążenia.

## <a name="breadth-first-load-balancing-method"></a>Metoda równoważenia obciążenia po pierwszej szerokości

Metoda równoważenia obciążenia pierwszej szerokości umożliwia dystrybucję połączeń użytkowników w celu optymalizacji w tym scenariuszu. Ta metoda jest idealnym rozwiązaniem dla organizacji, które chcą zapewnić najlepszym użytkownikom łączenie się ze środowiskiem pulpitu wirtualnego w puli.

Pierwsza metoda pierwsza bada hosty sesji, które zezwalają na nowe połączenia. Następnie Metoda wybiera hosta sesji z najmniejszą liczbą sesji. Jeśli istnieje powiązanie, Metoda wybiera pierwszego hosta sesji w zapytaniu.

## <a name="depth-first-load-balancing-method"></a>Głębokość — pierwsza metoda równoważenia obciążenia

Metoda równoważenia obciążenia po pierwszej drodze pozwala na optymalizację jednego hosta sesji w czasie w celu optymalizacji w tym scenariuszu. Ta metoda jest idealnym rozwiązaniem w przypadku organizacji branżowych, które chcą mieć dokładniejszą kontrolę nad liczbą maszyn wirtualnych przyznanych dla puli hostów.

Pierwsza metoda najpierw bada hosty sesji, które zezwalają na nowe połączenia i nie przekroczyły limitu maksymalnej liczby sesji. Następnie Metoda wybiera hosta sesji z największą liczbą sesji. Jeśli istnieje powiązanie, Metoda wybiera pierwszego hosta sesji w zapytaniu.