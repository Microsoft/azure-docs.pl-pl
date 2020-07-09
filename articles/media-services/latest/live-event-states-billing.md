---
title: LiveEvent Stany i rozliczenia w Azure Media Services | Microsoft Docs
description: Ten temat zawiera Azure Media Services Omówienie LiveEvent stanów i rozliczeń.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 10/24/2019
ms.author: juliako
ms.openlocfilehash: e4eee3b9f3f97bf7cd7a7b61425ec5c9a3a198ef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76543079"
---
# <a name="live-event-states-and-billing"></a>Stany wydarzeń na żywo i rozliczenia

W Azure Media Services wydarzenie na żywo rozpoczyna rozliczanie od razu po zmianie stanu na **uruchomiony**. Opłaty są naliczane nawet wtedy, gdy nie przepływają w ramach usługi. Aby zatrzymać rozliczanie wydarzenia na żywo, należy zatrzymać wydarzenie na żywo. Transkrypcja na żywo jest rozliczana w taki sam sposób jak w przypadku zdarzenia na żywo.

Gdy **LiveEventEncodingType** w [zdarzeniu na żywo](https://docs.microsoft.com/rest/api/media/liveevents) ma wartość standardowa lub Premium1080p, Media Services automatycznie zamyka wszystkie zdarzenia na żywo, które nadal w stanie **uruchomienia** 12 godzin po utracie danych wejściowych, i nie ma żadnych uruchomionych **danych wyjściowych na żywo**. Jednak nadal będą naliczane opłaty za czas **działania** zdarzenia na żywo.

> [!NOTE]
> Zdarzenia przekazywane na żywo nie są automatycznie wyłączane i muszą zostać jawnie zatrzymane za pomocą interfejsu API, aby uniknąć nadmiernego rozliczania. 

## <a name="states"></a>Stany

Wydarzenie na żywo może być w jednym z następujących stanów.

|Stan|Opis|
|---|---|
|**Zatrzymano**| Jest to początkowy stan zdarzenia na żywo po utworzeniu (chyba że Autostart został ustawiony na wartość true). W tym stanie nie ma rozliczeń. W tym stanie właściwości zdarzenia na żywo można zaktualizować, ale przesyłanie strumieniowe jest niedozwolone.|
|**Uruchamianie**| Trwa Rozpoczynanie zdarzenia na żywo i alokowanie zasobów. W tym stanie nie ma rozliczeń. Aktualizacje lub przesyłanie strumieniowe są niedozwolone w tym stanie. Jeśli wystąpi błąd, zdarzenie na żywo powróci do stanu zatrzymania.|
|**Uruchomiono**| Zasoby zdarzeń na żywo zostały przydzieloną, Wygenerowano adresy URL pozyskiwania i podglądu oraz można odbierać strumienie na żywo. W tym momencie rozliczenia są aktywne. Należy jawnie wywołać operację zatrzymywania w zasobie wydarzenia na żywo, aby zatrzymać dalsze rozliczenia.|
|**Zatrzymywanie**| Zdarzenie na żywo jest przerywane, a zasoby są zwalniane. W tym stanie przejściowym nie ma rozliczeń. Aktualizacje lub przesyłanie strumieniowe są niedozwolone w tym stanie.|
|**Usuwanie**| Trwa usuwanie zdarzenia na żywo. W tym stanie przejściowym nie ma rozliczeń. Aktualizacje lub przesyłanie strumieniowe są niedozwolone w tym stanie.|

Możesz włączyć transkrypcje na żywo podczas tworzenia wydarzenia na żywo. Jeśli to zrobisz, za każdym razem, gdy zdarzenie na żywo będzie w stanie **uruchomienia** , zostanie naliczona stawka za dynamiczne transkrypcje. Należy pamiętać, że opłaty są naliczane nawet wtedy, gdy nie przepływają przez wydarzenie na żywo.

## <a name="next-steps"></a>Następne kroki

- [Omówienie przesyłania strumieniowego na żywo](live-streaming-overview.md)
- [Samouczek przesyłania strumieniowego na żywo](stream-live-tutorial-with-api.md)
