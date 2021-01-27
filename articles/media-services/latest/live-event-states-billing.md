---
title: Stany wydarzeń na żywo i rozliczanie w Azure Media Services
description: Ten temat zawiera omówienie Azure Media Services stanów i rozliczeń dotyczących zdarzeń na żywo.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: c9fa12e1ee3778d0865c75662064bd4067e56d89
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897819"
---
# <a name="live-event-states-and-billing"></a>Stany wydarzeń na żywo i rozliczenia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W Azure Media Services wydarzenie na żywo rozpoczyna rozliczanie od razu po zmianie stanu na **uruchomiony** lub w **stanie wstrzymania**. Opłaty są naliczane nawet wtedy, gdy nie przepływają w ramach usługi. Aby zatrzymać rozliczanie wydarzenia na żywo, należy zatrzymać wydarzenie na żywo. Transkrypcja na żywo jest rozliczana w taki sam sposób jak w przypadku zdarzenia na żywo.

Gdy **LiveEventEncodingType** w [zdarzeniu na żywo](/rest/api/media/liveevents) ma wartość standardowa lub Premium1080p, Media Services automatycznie zamyka wszystkie zdarzenia na żywo, które nadal w stanie **uruchomienia** 12 godzin po utracie danych wejściowych, i nie ma żadnych uruchomionych **danych wyjściowych na żywo**. Jednak nadal będą naliczane opłaty za czas **działania** zdarzenia na żywo.

> [!NOTE]
> Zdarzenia przekazywane na żywo nie są automatycznie wyłączane i muszą zostać jawnie zatrzymane za pomocą interfejsu API, aby uniknąć nadmiernego rozliczania.

## <a name="states"></a>Stany

Wydarzenie na żywo może być w jednym z następujących stanów.

|Stan|Opis|
|---|---|
|**Zatrzymano**| Jest to początkowy stan zdarzenia na żywo po utworzeniu (chyba że Autostart został ustawiony na wartość true). W tym stanie nie ma rozliczeń. Zdarzenie na żywo nie może odbierać danych wejściowych. |
|**Uruchamianie**| Wydarzenie na żywo ma początek i zasoby przydzielenia. W tym stanie nie ma rozliczeń.  Jeśli wystąpi błąd, zdarzenie na żywo powróci do stanu zatrzymania.|
| **Przydzielanie** | Akcja alokacji została wywołana dla zdarzenia na żywo, a zasoby są obsługiwane dla tego zdarzenia na żywo. Po pomyślnym wykonaniu tej operacji zdarzenie na żywo przejdzie do stanu wstrzymania.
|**Wstrzymywanie**| zasoby zdarzeń na żywo zostały wdrożone i są gotowe do uruchomienia. Rozliczenia odbywają się w tym stanie.  Większość właściwości można nadal aktualizować, natomiast pozyskiwanie lub przesyłanie strumieniowe jest niedozwolone w tym stanie.
|**Uruchomienie**| Zasoby zdarzeń na żywo zostały przydzieloną, Wygenerowano adresy URL pozyskiwania i podglądu oraz można odbierać strumienie na żywo. W tym momencie rozliczenia są aktywne. Należy jawnie wywołać zatrzymywanie zasobu zdarzenia na żywo, aby zatrzymać dalsze rozliczanie.|
|**Zatrzymywanie**| Zdarzenie na żywo jest przerywane, a zasoby są zwalniane. W tym stanie przejściowym nie ma rozliczeń. |
|**Usuwanie**| Trwa usuwanie zdarzenia na żywo. W tym stanie przejściowym nie ma rozliczeń. |

Możesz włączyć transkrypcje na żywo podczas tworzenia wydarzenia na żywo. Jeśli to zrobisz, za każdym razem, gdy zdarzenie na żywo będzie w stanie **uruchomienia** , zostanie naliczona stawka za dynamiczne transkrypcje. Należy pamiętać, że opłaty są naliczane nawet wtedy, gdy nie przepływają przez wydarzenie na żywo.

## <a name="next-steps"></a>Następne kroki

- [Omówienie przesyłania strumieniowego na żywo](live-streaming-overview.md)
- [Samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)
