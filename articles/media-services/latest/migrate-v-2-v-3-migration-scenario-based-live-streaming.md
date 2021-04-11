---
title: Wskazówki dotyczące migracji na żywo w ramach usługi Media Services Streaming
description: Ten artykuł zawiera wskazówki dotyczące scenariusza przesyłania strumieniowego na żywo, które ułatwiają minimalną migrację z Azure Media Services V2 do wersji v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 273a1b5986a72acc1d6725fbb3101c9c1df17e45
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122054"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>Wskazówki dotyczące migracji opartej na scenariuszu przesyłania strumieniowego na żywo

![logo przewodnika migracji](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroki migracji 2](./media/migration-guide/steps-4.svg)

Azure Portal obsługuje teraz na żywo i zarządzanie nimi.  Zaleca się wypróbowanie jej podczas testowania migracji z wersji 2 do v3.

## <a name="test-the-v3-live-event-workflow"></a>Testowanie przepływu pracy zdarzenia na żywo v3

> [!NOTE]
> Kanały i programy utworzone przy użyciu wersji 2 (które są mapowane na zdarzenia na żywo i wyjście na żywo) mogą być zarządzane za pomocą interfejsu API v3. Wskazówki polegają na przełączeniu się do V3 zdarzeń na żywo i danych wyjściowych na żywo w dogodnym czasie, gdy można zatrzymać istniejący kanał v2. Obecnie nie jest obsługiwane bezproblemowe Migrowanie ciągle działającego kanału 24x7 na żywo do nowych wydarzeń na żywo. W związku z tym przestoje związane z konserwacją muszą być skoordynowane z najlepszą wygodą dla odbiorców i firm.

Przetestuj nowy sposób dostarczania zdarzeń na żywo z Media Services przed przeniesieniem zawartości z wersji 2 do v3. Poniżej przedstawiono funkcje wersji v3 do współpracy z usługą i należy rozważyć migrację.

- Utwórz nowe wydarzenie v3 na [żywo](live-event-outputs-concept.md#live-events) na potrzeby kodowania. Można włączyć [Ustawienia zakodowania 1080p i 720](live-event-types-comparison-reference.md#system-presets).
- Użyj jednostki [wyjściowej na żywo](live-event-outputs-concept.md#live-outputs) zamiast programów
- Utwórz [lokalizatory przesyłania strumieniowego](streaming-locators-concept.md).
- Weź pod uwagę potrzebę [HLS i przerywanie](encode-dynamic-packaging-concept.md) transmisji strumieniowej na żywo.
- W przypadku konieczności szybkiego uruchamiania zdarzeń na żywo Eksploruj nowe funkcje [trybu wstrzymania](live-event-outputs-concept.md#standby-mode) .
- Jeśli chcesz transkrypcja wydarzenie na żywo w czasie, Poznaj nową funkcję [transkrypcji na żywo](live-event-live-transcription-how-to.md) .
- Utwórz 24x7x365 zdarzenia na żywo w wersji 3, jeśli potrzebujesz dłuższego czasu trwania przesyłania strumieniowego.
- Użyj [Event Grid](monitoring/monitor-events-portal-how-to.md) do monitorowania wydarzeń na żywo.

Zapoznaj się z pojęciami dotyczącymi wydarzeń na żywo, samouczkami i przewodnikami poniżej, aby poznać określone kroki.

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>Pojęcia dotyczące wydarzeń na żywo, samouczki i instrukcje dotyczące przewodników

### <a name="concepts"></a>Pojęcia

- [Przesyłanie strumieniowe na żywo z Azure Media Services v3](stream-live-streaming-concept.md)
- [Zdarzenia na żywo i wyjście na żywo w Media Services](live-event-outputs-concept.md)
- [Zweryfikowane lokalne kodery przesyłania strumieniowego na żywo](recommended-on-premises-live-encoders.md)
- [Korzystaj z przesunięć czasowych i danych wyjściowych, aby utworzyć odtwarzanie wideo na żądanie](live-event-cloud-dvr-time-how-to.md)
- [Live-Event-Live-transkrypcje-How-to (wersja zapoznawcza)](live-event-live-transcription-how-to.md)
- [Porównanie typów zdarzeń na żywo](live-event-types-comparison-reference.md)
- [Stany wydarzeń na żywo i rozliczenia](live-event-states-billing-concept.md)
- [Ustawienia niskiego opóźnienia zdarzenia na żywo](live-event-latency-reference.md)
- [Kody błędów zdarzeń na żywo Media Services](live-event-error-codes-reference.md)

### <a name="tutorials-and-quickstarts"></a>Samouczki i Przewodniki Szybki Start

- [Samouczek: przesyłanie strumieniowe na żywo za pomocą Media Services](stream-live-tutorial-with-api.md)
- [Tworzenie strumienia Azure Media Services na żywo za pomocą OBS](live-event-obs-quickstart.md)
- [Szybki Start: przekazywanie, kodowanie i przesyłanie strumieniowe zawartości za pomocą portalu](asset-create-asset-upload-portal-quickstart.md)
- [Szybki Start: Tworzenie Azure Media Services strumienia na żywo za pomocą Wirecast](live-event-wirecast-quickstart.md)

## <a name="samples"></a>Samples

Możesz również [porównać kod v2 i V3 w przykładach kodu](migrate-v-2-v-3-migration-samples.md).
