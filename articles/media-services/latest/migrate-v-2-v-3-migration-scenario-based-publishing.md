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
ms.openlocfilehash: 3a09e3f2bf29c09066e9414f9aa02a7879375425
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563535"
---
# <a name="packaging-and-delivery-scenario-based-migration-guidance"></a>Wskazówki dotyczące migracji i tworzenia pakietów na podstawie scenariusza dostarczania

![logo przewodnika migracji](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroki migracji 2](./media/migration-guide/steps-4.svg)

Ten artykuł zawiera wskazówki dotyczące tworzenia pakietów i dostarczania w oparciu o scenariusze, które ułatwiają migrowanie z Azure Media Services V2 do wersji v3.

Najważniejsze zmiany w sposobie publikowania zawartości w interfejsie API v3. Nowy model publikowania jest uproszczony i używa mniejszej liczby jednostek do utworzenia lokalizatora przesyłania strumieniowego. Interfejs API zredukowany do zaledwie dwóch jednostek a cztery jednostki wcześniej wymagane. Zasady kluczy zawartości i lokalizatory przesyłania strumieniowego zastępują teraz potrzebę `ContentKeyAuthoriationPolicy` , `AssetDeliveyPolicy` , `ContentKey` i `AccessPolicy` .

## <a name="packaging-and-delivery-in-v3"></a>Pakowanie i dostarczanie w wersji 3

1. Utwórz [Zasady kluczy zawartości](content-key-policy-concept.md).
1. Utwórz [lokalizatory przesyłania strumieniowego](streaming-locators-concept.md).
1. Pobieranie [ścieżek przesyłania strumieniowego](create-streaming-locator-build-url.md) 
    1. Skonfiguruj ją dla [łącznika](dynamic-packaging-overview.md#mpeg-dash-protocol) lub odtwarzacza [HLS](dynamic-packaging-overview.md#hls-protocol) .

Zapoznaj się z tematami dotyczącymi publikowania, samouczkami i przewodnikami poniżej dotyczącymi określonych kroków.

## <a name="publishing-concepts-tutorials-and-how-to-guides"></a>Publikowanie koncepcji, samouczków i przewodników

### <a name="concepts"></a>Pojęcia

- [Dynamiczne pakowanie w Media Services v3](dynamic-packaging-overview.md)
- [Filtry](filters-concept.md)
- [Filtrowanie manifestów przy użyciu Pakowarki dynamicznej](filters-dynamic-manifest-overview.md)
- [Punkty końcowe przesyłania strumieniowego (Źródło) w Azure Media Services](streaming-endpoint-concept.md)
- [Przesyłanie strumieniowe zawartości z integracją z usługą CDN](scale-streaming-cdn.md)
- [Lokalizatory przesyłania strumieniowego](streaming-locators-concept.md)

### <a name="how-to-guides"></a>Przewodniki z instrukcjami

- [Zarządzanie punktami końcowymi przesyłania strumieniowego z Media Services v3](manage-streaming-endpoints-howto.md)
- [Przykład interfejsu wiersza polecenia: publikowanie elementu zawartości](cli-publish-asset.md)
- [Tworzenie lokalizatora przesyłania strumieniowego i adresów URL kompilacji](create-streaming-locator-build-url.md)
- [Pobierz wyniki zadania](download-results-howto.md)
- [Śledzenie sygnałów dźwiękowych](signal-descriptive-audio-howto.md)
- [Pełna konfiguracja usługi Azure Media Player](../azure-media-player/azure-media-player-full-setup.md)
- [Jak używać odtwarzacza Video.js z Azure Media Services](how-to-video-js-player.md)
- [Jak używać odtwarzacza Shaka z Azure Media Services](how-to-shaka-player.md)

## <a name="samples"></a>Samples

Możesz również [porównać kod v2 i V3 w przykładach kodu](migrate-v-2-v-3-migration-samples.md).
