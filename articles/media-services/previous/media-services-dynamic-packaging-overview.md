---
title: Azure Media Services omówienie tworzenia pakietów dynamicznych | Microsoft Docs
description: W tym artykule omówiono Microsoft Azure Media Services tworzenia pakietów dynamicznych.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d2394cba157a7049e3b63b09fda9e018f0a0b32f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013622"
---
# <a name="dynamic-packaging"></a>Dynamiczne tworzenie pakietów

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Wybierz używaną wersję Media Services:"]
> * [Wersja 3](../latest/dynamic-packaging-overview.md)
> * [Wersja 2](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-v-2-v-3-migration-introduction.md)

Microsoft Azure Media Services może służyć do dostarczania wielu formatów plików źródłowych multimediów, formatów przesyłania strumieniowego multimediów i formatów ochrony zawartości do różnych technologii klienta (na przykład iOS, XBOX, Silverlight i Windows 8). Ci klienci znają różne protokoły, na przykład system iOS wymaga formatu HTTP Live Streaming (HLS) v4, a technologia Silverlight i konsola Xbox wymagają Smooth Streaming. Jeśli masz zestaw plików MP4 z adaptacyjną szybkością transmisji bitów (z nośnikami ISO 14496-12) lub zestaw z adaptacyjną szybkością transmisji bitów Smooth Streaming plików, które mają być używane dla klientów, którzy wiedzą, że HLS lub Smooth Streaming, należy skorzystać z Media Services dynamicznego tworzenia pakietów.

W przypadku dynamicznego tworzenia pakietów wystarczy utworzyć element zawartości zawierający zestaw plików MP4 z adaptacyjną szybkością transmisji bitów lub z adaptacyjną szybkością transmisji bitów Smooth Streaming plików. Następnie na podstawie określonego formatu w żądaniu manifestu lub fragmentu serwer przesyłania strumieniowego na żądanie zapewni otrzymanie strumienia w wybranym protokole. Dzięki temu wystarczy przechowywać i opłacać pliki w jednym formacie magazynu, a usługa Media Services utworzy oraz udostępni właściwą odpowiedź na podstawie żądań klienta.

Na poniższym diagramie przedstawiono tradycyjne kodowanie i statyczny przepływ pracy tworzenia pakietów.

![Kodowanie statyczne](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Na poniższym diagramie przedstawiono przepływ pracy tworzenia pakietów dynamicznych.

![Kodowanie dynamiczne](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Typowy scenariusz

1. Przekaż plik wejściowy (nazywany plikem Mezzanine). Na przykład H. 264, MP4 lub WMV (dla listy obsługiwanych formatów zobacz [formaty obsługiwane przez Media Encoder Standard](media-services-media-encoder-standard-formats.md).
2. Koduj plik w formacie Mezzanine do H. 264 zbiorów z adaptacyjną szybkością transmisji bitów.
3. Opublikuj element zawartości zawierający zestaw plików MP4 z adaptacyjną szybkością transmisji bitów, tworząc lokalizator na żądanie.
4. Utwórz adresy URL przesyłania strumieniowego w celu uzyskania dostępu do zawartości i przesyłania strumieniowego.

## <a name="preparing-assets-for-dynamic-streaming"></a>Przygotowywanie zasobów do dynamicznego przesyłania strumieniowego

Aby przygotować zasób do dynamicznego przesyłania strumieniowego, dostępne są następujące opcje:

- [Przekaż plik główny](media-services-dotnet-upload-files.md).
- [Użyj kodera Media Encoder Standard, aby utworzyć zestaw H. 264 plików MP4 z adaptacyjną szybkością transmisji bitów](media-services-dotnet-encode-with-media-encoder-standard.md).
- [Strumieniowe przesyłanie zawartości](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Kodery-dekoder audio obsługiwane przez pakowanie dynamiczne

Dynamiczne pakowanie obsługuje pliki MP4, które zawierają kodowanie audio z [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, IT-AAC V1, AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 lub E-AC3), Dolby Atmos lub [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD). Przesyłanie strumieniowe zawartości Dolby Atmos jest obsługiwane w przypadku standardów, takich jak protokół PAUZy MPEG ze wspólnym formatem przesyłania strumieniowego (CSF) lub typowym formatem aplikacji Media Format (CMAF), a za pośrednictwem HTTP Live Streaming (HLS) z CMAF.

> [!NOTE]
> Pakowanie dynamiczne nie obsługuje plików zawierających audio [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) (jest to starszy koder-dekoder).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
