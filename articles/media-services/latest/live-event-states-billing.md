---
title: LiveEvent Stany i rozliczenia w Azure Media Services | Microsoft Docs
description: Ten temat zawiera Azure Media Services Omówienie LiveEvent stanów i rozliczeń.
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
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 1b058eefe22238b60c3482c55b5ae340f4e597f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89296740"
---
# <a name="live-event-states-and-billing"></a>Stany wydarzeń na żywo i rozliczenia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W Azure Media Services wydarzenie na żywo rozpoczyna rozliczanie od razu po zmianie stanu na **uruchomiony**. Opłaty są naliczane nawet wtedy, gdy nie przepływają w ramach usługi. Aby zatrzymać rozliczanie wydarzenia na żywo, należy zatrzymać wydarzenie na żywo. Transkrypcja na żywo jest rozliczana w taki sam sposób jak w przypadku zdarzenia na żywo.

Gdy **LiveEventEncodingType** w [zdarzeniu na żywo](/rest/api/media/liveevents) ma wartość standardowa lub Premium1080p, Media Services automatycznie zamyka wszystkie zdarzenia na żywo, które nadal w stanie **uruchomienia** 12 godzin po utracie danych wejściowych, i nie ma żadnych uruchomionych **danych wyjściowych na żywo**. Jednak nadal będą naliczane opłaty za czas **działania** zdarzenia na żywo.

> [!NOTE]
> Zdarzenia przekazywane na żywo nie są automatycznie wyłączane i muszą zostać jawnie zatrzymane za pomocą interfejsu API, aby uniknąć nadmiernego rozliczania. 

## <a name="states"></a>Stany

Wydarzenie na żywo może być w jednym z następujących stanów.

|Stan|Opis|
|---|---|
|**Zatrzymano**| Jest to początkowy stan zdarzenia na żywo po utworzeniu (chyba że Autostart został ustawiony na wartość true). W tym stanie nie ma rozliczeń. W tym stanie właściwości zdarzenia na żywo można zaktualizować, ale przesyłanie strumieniowe jest niedozwolone.|
|**Uruchamianie**| Trwa Rozpoczynanie zdarzenia na żywo i alokowanie zasobów. W tym stanie nie ma rozliczeń. Aktualizacje lub przesyłanie strumieniowe są niedozwolone w tym stanie. Jeśli wystąpi błąd, zdarzenie na żywo powróci do stanu zatrzymania.|
|**Uruchomienie**| Zasoby zdarzeń na żywo zostały przydzieloną, Wygenerowano adresy URL pozyskiwania i podglądu oraz można odbierać strumienie na żywo. W tym momencie rozliczenia są aktywne. Należy jawnie wywołać operację zatrzymywania w zasobie wydarzenia na żywo, aby zatrzymać dalsze rozliczenia.|
|**Zatrzymywanie**| Zdarzenie na żywo jest przerywane, a zasoby są zwalniane. W tym stanie przejściowym nie ma rozliczeń. Aktualizacje lub przesyłanie strumieniowe są niedozwolone w tym stanie.|
|**Usuwanie**| Trwa usuwanie zdarzenia na żywo. W tym stanie przejściowym nie ma rozliczeń. Aktualizacje lub przesyłanie strumieniowe są niedozwolone w tym stanie.|

Możesz włączyć transkrypcje na żywo podczas tworzenia wydarzenia na żywo. Jeśli to zrobisz, za każdym razem, gdy zdarzenie na żywo będzie w stanie **uruchomienia** , zostanie naliczona stawka za dynamiczne transkrypcje. Należy pamiętać, że opłaty są naliczane nawet wtedy, gdy nie przepływają przez wydarzenie na żywo.

## <a name="next-steps"></a>Następne kroki

- [Omówienie przesyłania strumieniowego na żywo](live-streaming-overview.md)
- [Samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)
