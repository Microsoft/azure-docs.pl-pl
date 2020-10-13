---
title: Równoważenie obciążenia puli hostów usług pulpitu wirtualnego systemu Windows — Azure
description: Informacje o metodach równoważenia obciążenia puli hostów dla środowiska pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd8f9e4a3ef63cd97f96af3d4f96a2bb65c3cd09
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951863"
---
# <a name="host-pool-load-balancing-methods"></a>Metody równoważenia obciążenia puli hostów

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md).

Pulpit wirtualny systemu Windows obsługuje dwie metody równoważenia obciążenia. Każda metoda określa, który Host sesji będzie hostować sesję użytkownika, gdy łączą się z zasobem w puli hostów.

Następujące metody równoważenia obciążenia są dostępne w programie Virtual Desktop systemu Windows:

- Równoważenie obciążenia w pierwszej kolejności umożliwia równomierne dystrybuowanie sesji użytkowników między hostami sesji w puli hostów.
- Głębokość — pierwsze Równoważenie obciążenia pozwala na nasycenie hosta sesji z sesjami użytkowników w puli hostów. Gdy pierwsza sesja osiągnie próg limitu sesji, moduł równoważenia obciążenia kieruje wszystkie nowe połączenia użytkowników do następnego hosta sesji w puli hostów do momentu osiągnięcia limitu i tak dalej.

Dla każdej puli hostów można skonfigurować tylko jeden typ równoważenia obciążenia. Jednak obie metody równoważenia obciążenia współdzielą następujące zachowania niezależnie od tego, z których puli hostów znajdują się:

- Jeśli użytkownik ma już sesję w puli hostów i ponownie nawiązuje połączenie z tą sesją, moduł równoważenia obciążenia pomyślnie przekieruje je do hosta sesji z istniejącą sesją. To zachowanie ma zastosowanie, nawet jeśli właściwość AllowNewConnections hosta sesji ma wartość false.
- Jeśli użytkownik nie ma jeszcze sesji w puli hostów, moduł równoważenia obciążenia nie będzie uwzględniać hostów sesji, których właściwość AllowNewConnections jest ustawiona na wartość false podczas równoważenia obciążenia.

## <a name="breadth-first-load-balancing-method"></a>Metoda równoważenia obciążenia po pierwszej szerokości

Metoda równoważenia obciążenia pierwszej szerokości umożliwia dystrybucję połączeń użytkowników w celu optymalizacji w tym scenariuszu. Ta metoda jest idealnym rozwiązaniem dla organizacji, które chcą zapewnić najlepszym użytkownikom łączenie się ze środowiskiem pulpitu wirtualnego w puli.

Pierwsza metoda pierwsza bada hosty sesji, które zezwalają na nowe połączenia. Następnie Metoda wybiera hosta sesji losowo z połowy zestawu hostów sesji o najmniejszej liczbie sesji. Na przykład jeśli istnieją dziewięć maszyn z 11, 12, 13, 14, 15, 16, 17, 18 i 19 sesji, nowo utworzona sesja nie będzie automatycznie przechodzić do pierwszej maszyny. Zamiast tego może przejść do dowolnego z pięciu pierwszych maszyn z najmniejszą liczbą sesji (11, 12, 13, 14, 15).

## <a name="depth-first-load-balancing-method"></a>Głębokość — pierwsza metoda równoważenia obciążenia

Metoda równoważenia obciążenia po pierwszej drodze pozwala na optymalizację jednego hosta sesji w czasie w celu optymalizacji w tym scenariuszu. Ta metoda jest idealnym rozwiązaniem w przypadku organizacji branżowych, które chcą mieć dokładniejszą kontrolę nad liczbą maszyn wirtualnych przyznanych dla puli hostów.

Pierwsza metoda najpierw bada hosty sesji, które zezwalają na nowe połączenia i nie przekroczyły limitu maksymalnej liczby sesji. Następnie Metoda wybiera hosta sesji z największą liczbą sesji. Jeśli istnieje powiązanie, Metoda wybiera pierwszego hosta sesji w zapytaniu.

>[!IMPORTANT]
>Algorytm równoważenia obciążenia pierwszego (głębokość) dystrybuuje sesje do hostów sesji na podstawie maksymalnego limitu hosta sesji. Ten parametr jest wymagany w przypadku użycia algorytmu równoważenia obciążenia pierwszego numeru. W celu uzyskania najlepszego możliwego środowiska użytkownika należy zmienić parametr limitu maksymalnej liczby hostów sesji na numer, który najlepiej odpowiada Twojemu środowisku.