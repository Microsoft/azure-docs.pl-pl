---
title: Wskazówki dotyczące migracji pakietu i dostarczania
description: Ten artykuł zawiera wskazówki dotyczące tworzenia pakietów i dostarczania na podstawie scenariuszy, które ułatwiają migrowanie z Azure Media Services V2 do wersji v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 0a0052fa3d78a3b77094cfccbd4c011321ac5925
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279021"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>Wskazówki dotyczące migracji i tworzenia pakietów na podstawie scenariusza dostarczania

![logo przewodnika migracji](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroki migracji 2](./media/migration-guide/steps-4.svg)

Ten artykuł zawiera wskazówki dotyczące tworzenia pakietów i dostarczania w oparciu o scenariusze, które ułatwiają migrowanie z Azure Media Services V2 do wersji v3.

Najważniejsze zmiany w sposobie publikowania zawartości w interfejsie API v3. Nowy model publikowania jest uproszczony i używa mniejszej liczby jednostek do utworzenia lokalizatora przesyłania strumieniowego. Interfejs API zredukowany do zaledwie dwóch jednostek a cztery jednostki wcześniej wymagane. Zasady kluczy zawartości i lokalizatory przesyłania strumieniowego zastępują teraz potrzebę `ContentKeyAuthoriationPolicy` , `AssetDeliveyPolicy` , `ContentKey` i `AccessPolicy` .

## <a name="packaging-and-delivery-in-v3"></a>Pakowanie i dostarczanie w wersji 3

1. Utwórz [Zasady kluczy zawartości](drm-content-key-policy-concept.md).
1. Utwórz [lokalizatory przesyłania strumieniowego](stream-streaming-locators-concept.md).
1. Pobieranie [ścieżek przesyłania strumieniowego](create-streaming-locator-build-url.md) 
    1. Skonfiguruj ją dla [łącznika](encode-dynamic-packaging-concept.md#mpeg-dash-protocol) lub odtwarzacza [HLS](encode-dynamic-packaging-concept.md#hls-protocol) .

Zapoznaj się z tematami dotyczącymi publikowania, samouczkami i przewodnikami poniżej dotyczącymi określonych kroków.

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>Publikowanie koncepcji, samouczków i przewodników

### <a name="concepts"></a>Pojęcia

- [Dynamiczne pakowanie w Media Services v3](encode-dynamic-packaging-concept.md)
- [Filtry](filters-concept.md)
- [Filtrowanie manifestów przy użyciu Pakowarki dynamicznej](filters-dynamic-manifest-concept.md)
- [Punkty końcowe przesyłania strumieniowego (Źródło) w Azure Media Services](stream-streaming-endpoint-concept.md)
- [Przesyłanie strumieniowe zawartości z integracją z usługą CDN](stream-scale-streaming-cdn-concept.md)
- [Lokalizatory przesyłania strumieniowego](stream-streaming-locators-concept.md)

### <a name="how-to-guides"></a>Przewodniki z instrukcjami

- [Zarządzanie punktami końcowymi przesyłania strumieniowego z Media Services v3](stream-manage-streaming-endpoints-how-to.md)
- [Przykład interfejsu wiersza polecenia: publikowanie elementu zawartości](cli-publish-asset.md)
- [Tworzenie lokalizatora przesyłania strumieniowego i adresów URL kompilacji](create-streaming-locator-build-url.md)
- [Pobierz wyniki zadania](job-download-results-how-to.md)
- [Śledzenie sygnałów dźwiękowych](signal-descriptive-audio-howto.md)
- [Pełna konfiguracja usługi Azure Media Player](../azure-media-player/azure-media-player-full-setup.md)
- [Jak używać odtwarzacza Video.js z Azure Media Services](player-how-to-video-js-player.md)
- [Jak używać odtwarzacza Shaka z Azure Media Services](player-shaka-player-how-to.md)

## <a name="samples"></a>Samples

Możesz również [porównać kod v2 i V3 w przykładach kodu](migrate-v-2-v-3-migration-samples.md).
