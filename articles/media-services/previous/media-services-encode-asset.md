---
title: Omówienie koderów multimedialnych na żądanie na platformie Azure | Microsoft Docs
description: Azure Media Services udostępnia wiele opcji kodowania multimediów w chmurze. Ten artykuł zawiera omówienie koderów multimediów na żądanie na platformie Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: e841d1922beb3f0276ff94cacbbdf566b23cdf6e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017288"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Omówienie koderów multimediów na żądanie na platformie Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją [Media Services wersja 3](../latest/index.yml). Zobacz też [wskazówki dotyczące migracji od wersji 2 do V3](../latest/migrate-from-v2-to-v3.md)

Azure Media Services udostępnia wiele opcji kodowania multimediów w chmurze.

Podczas rozpoczynania pracy z Media Services należy zrozumieć różnicę między koderami-dekodermi i formatami plików.
Kodery-dekoder to oprogramowanie implementujące algorytmy kompresji/dekompresji, natomiast formaty plików są kontenerami, które przechowują skompresowane wideo.

Media Services zapewnia dynamiczne pakowanie, które pozwala na dostarczanie zawartości kodowanej przy użyciu plików MP4 lub Smooth Streaming w formatach strumieniowych obsługiwanych przez Media Services (PAUZy MPEG, HLS, Smooth Streaming) bez konieczności ponownego tworzenia pakietów w tych formatach.

Po utworzeniu konta Media Services zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego w stanie **zatrzymanym** . Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. Rozliczanie punktów końcowych przesyłania strumieniowego odbywa się za każdym razem, gdy punkt końcowy jest w stanie **uruchomienia** .

Media Services obsługuje następujący koder na żądanie:

* [Usługa Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)

Ten artykuł zawiera krótkie omówienie koderów multimedialnych na żądanie oraz linki do artykułów z bardziej szczegółowymi informacjami.

Domyślnie każde konto Media Services może mieć jedno aktywne zadanie kodowania jednocześnie. Można zarezerwować jednostki kodowania, które umożliwiają współbieżne uruchamianie wielu zadań kodowania, jeden dla każdej zakupionej zarezerwowanej jednostki kodowania. Aby uzyskać więcej informacji, zobacz [skalowanie jednostek kodowania](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Usługa Media Encoder Standard

### <a name="how-to-use"></a>Sposób użycia
[Jak kodować przy użyciu Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formaty
[Formaty i kodeki](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Zestawy
Media Encoder Standard jest skonfigurowany przy użyciu jednego z ustawień predefiniowanych kodera opisanych [poniżej](./media-services-mes-presets-overview.md).

### <a name="input-and-output-metadata"></a>Metadane wejściowe i wyjściowe
Metadane wejściowe koderów zostały opisane [tutaj](media-services-input-metadata-schema.md).

Metadane wyjściowe koderów zostały opisane [tutaj](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Generowanie miniatur
Aby uzyskać więcej informacji, zobacz [jak generować miniatury przy użyciu Media Encoder Standard](media-services-advanced-encoding-with-mes.md).

### <a name="trim-videos-clipping"></a>Przycinanie filmów wideo (wycinków)
Aby uzyskać więcej informacji, zobacz [jak przyciąć wideo przy użyciu Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Utwórz nakładki
Aby uzyskać więcej informacji, zobacz [jak utworzyć nakładki przy użyciu Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Zobacz też
[Blog Media Services](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

### <a name="known-issues"></a>Znane problemy
Jeśli wejściowy film wideo nie zawiera napisów kodowanych, element zawartości wyjściowej nadal będzie zawierał pusty plik TTML.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
* [Wykonywanie zaawansowanych zadań kodowania przez dostosowanie Media Encoder Standard ustawień wstępnych](media-services-custom-mes-presets-with-dotnet.md)
* [Limity przydziału i ograniczenia](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/