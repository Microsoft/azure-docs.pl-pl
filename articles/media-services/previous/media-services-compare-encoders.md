---
title: Porównanie koderów multimediów na żądanie na platformie Azure | Microsoft Docs
description: W tym temacie porównano możliwości kodowania **Media Encoder Standard** i **Media Encoder Premium Workflow**.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: ca17ffdc2a54b25bbfd855834a366ed381a54678
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530392"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Porównanie koderów multimediów na żądanie na platformie Azure  

W tym temacie porównano możliwości kodowania **Media Encoder Standard** i **Media Encoder Premium Workflow**.

## <a name="video-and-audio-processing-capabilities"></a>Możliwości przetwarzania wideo i audio

Poniższa tabela zawiera porównanie funkcji Media Encoder Standard (MES) i Media Encoder Premium Workflow (MEPW). 

|Możliwość|Usługa Media Encoder Standard|Przepływ pracy usługi Media Encoder w warstwie Premium|
|---|---|---|
|Stosowanie logiki warunkowej podczas kodowania<br/>(na przykład jeśli dane wejściowe to HD, a następnie kodowanie 5,1 audio)|Nie|Yes|
|Podpisy kodowane|Nie|[Tak](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[&reg;Korekcja głośności Dolby Professional](https://professional.dolby.com/product/broadcast/vm600/)<br/> za pomocą analizy dialogu&trade;|Nie|Yes|
|Usuwanie przeplotu, inverse telecine|Podstawowy|Jakość emisji|
|Wykrywaj i usuwaj czarne obramowania <br/>(pillarboxes, letterboxes)|Nie|Yes|
|Generowanie miniatur|[Tak](media-services-dotnet-generate-thumbnail-with-mes.md)|[Tak](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Przycinanie/przycinanie i łączenie filmów wideo|[Tak](media-services-advanced-encoding-with-mes.md#trim_video)|Tak|
|Nakładki audio lub wideo|[Tak](media-services-advanced-encoding-with-mes.md#overlay)|[Tak](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Nakładki grafiki|Ze źródeł obrazu|Ze źródeł obrazów i tekstu|
|Wiele ścieżek języka audio|Ograniczone|[Tak](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a name="billing-meter-used-by-each-encoder"></a><a id="billing"></a>Licznik rozliczeń używany przez każdy koder
| Nazwa procesora multimediów | Odpowiednie ceny | Uwagi |
| --- | --- | --- |
| **Usługa Media Encoder Standard** |POMOCĄ |Opłaty za zadania kodowania będą naliczone na podstawie łącznego czasu trwania (w minutach) wszystkich plików multimedialnych utworzonych jako dane wyjściowe według stawki określonej w [tym miejscu][1]w kolumnie koder. |
| **Przepływ pracy usługi Media Encoder w warstwie Premium** |KODER W WARSTWIE PREMIUM |Opłaty za zadania kodowania będą naliczone na podstawie łącznego czasu trwania (w minutach) wszystkich plików multimedialnych produkowanych jako dane wyjściowe według stawki określonej w [tym miejscu][1]w kolumnie kodera Premium. |

## <a name="input-containerfile-formats"></a>Dane wejściowe w formacie kontenera/pliku
| Formaty wejściowych kontenerów/plików | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| Adobe &reg; Flash &reg; F4V |Tak |Tak |
| MXF/SMPTE 377M |Tak |Tak |
| GXF |Tak |Tak |
| Strumienie transportowe MPEG-2 |Tak |Tak |
| Strumienie programu MPEG-2 |Tak |Tak |
| MPEG-4/MP4 |Tak |Tak |
| Windows Media/ASF |Tak |Tak |
| AVI (nieskompresowany 8bit/10bit) |Tak |Tak |
| 3GPP/3GPP2 |Yes |Nie |
| Smooth Streaming format pliku (PIFF 1,3) |Yes |Nie |
| [Cyfrowe nagrywanie wideo firmy Microsoft (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Yes |Nie |
| Matroska/WebM |Yes |Nie |
| QuickTime (mov) |Yes |Nie |

## <a name="input-video-codecs"></a>Kodery-dekoder wideo
| Kodery-dekodery wejściowych danych wideo | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AVC 8-bitowy/10-bitowy, do 4:2:2, wraz z AVCIntra |8-bitowy 4:2:0 oraz 4:2:2 |Tak |
| Avid DNxHD (w pliku MXF) |Tak |Tak |
| DVCPro/DVCProHD (w pliku MXF) |Tak |Tak |
| JPEG2000 |Tak |Tak |
| MPEG-2 (do 422 profilu i wysokiego poziomu), w tym różne warianty, takie jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs &reg; i D10) |Maksymalnie profil 422 |Tak |
| MPEG-1 |Tak |Tak |
| Windows Media Video/VC-1 |Tak |Tak |
| Canopus HQ/HQX |Nie |Nie |
| MPEG-4 część 2 |Yes |Nie |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |Nie |
| Apple ProRes 422 |Yes |Nie |
| Apple ProRes 422 LT |Yes |Nie |
| Apple ProRes 422 HQ |Yes |Nie |
| Apple ProRes Proxy |Yes |Nie |
| Apple ProRes 4444 |Yes |Nie |
| Apple ProRes 4444 XQ |Yes |Nie |
| HEVC/H. 265|Profil główny|Główny i główny 10 profilu|

## <a name="input-audio-codecs"></a>Kodery-dekoder Audio Input
| Kodery-dekodery wejściowych danych audio | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AES (SMPTE 331M oraz 302M, AES3-2003) |Nie |Yes |
| Dolby &reg; E |Nie |Yes |
| Dolby &reg; Digital (AC3) |Nie |Yes |
| Dolby &reg; Digital Plus (E-AC3) |Nie |Yes |
| AAC (AAC-LC, AAC-HE oraz AAC-HEv2; maksymalnie 5.1) |Tak |Tak |
| MPEG Layer 2 |Tak |Tak |
| MP3 (MPEG-1 Audio Layer 3) |Tak |Tak |
| Windows Media Audio |Tak |Tak |
| WAV/PCM |Tak |Tak |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |Nie |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Yes |Nie |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |Nie |

## <a name="output-containerfile-formats"></a>Pliki/kontenery wyjściowe
| Pliki/kontenery wyjściowe | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| Adobe &reg; Flash &reg; F4V |Nie |Yes |
| MXF (OP1a, XDCAM i AS02) |Nie |Yes |
| DPP (łącznie z AS11) |Nie |Yes |
| GXF |Nie |Yes |
| MPEG-4/MP4 |Tak |Tak |
| MPEG-TS |Tak |Tak |
| Windows Media/ASF |Nie |Yes |
| AVI (nieskompresowany 8bit/10bit) |Nie |Yes |
| Smooth Streaming format pliku (PIFF 1,3) |Nie |Yes |

## <a name="output-video-codecs"></a>Wyjściowe kodery-dekoder wideo
| Wyjściowe kodery-dekoder wideo | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AVC (H. 264; 8-bitowy; maksymalnie wysoki profil, poziom 5,2; 4K Ultra HD; AVC — Intra) |Tylko 8 bitów 4:2:0 |Tak |
| HEVC (H. 265; 8-bitowe i 10-bitowe;)  |Nie |Yes |
| Avid DNxHD (w pliku MXF) |Nie |Yes |
| MPEG-2 (do 422 profilu i wysokiego poziomu), w tym różne warianty, takie jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs &reg; i D10) |Nie |Yes |
| MPEG-1 |Nie |Yes |
| Windows Media Video/VC-1 |Nie |Yes |
| Tworzenie miniatur JPEG |Tak |Tak |
| Tworzenie miniatur PNG |Tak |Tak |
| Tworzenie miniatur BMP |Yes |Nie |

## <a name="output-audio-codecs"></a>Wyjściowe kodery-dekoder audio
| Wyjściowe kodery-dekoder audio | Usługa Media Encoder Standard | Przepływ pracy usługi Media Encoder w warstwie Premium |
| --- | --- | --- |
| AES (SMPTE 331M oraz 302M, AES3-2003) |Nie |Yes |
| Dolby &reg; Digital (AC3) |Nie |Yes |
| Dolby &reg; Digital Plus (E-AC3) do 7,1 |Nie |Yes |
| AAC (AAC-LC, AAC-HE oraz AAC-HEv2; maksymalnie 5.1) |Tak |Tak |
| MPEG Layer 2 |Nie |Yes |
| MP3 (MPEG-1 Audio Layer 3) |Nie |Yes |
| Windows Media Audio |Nie |Yes |

>[!NOTE]
>W przypadku kodowania do programu Dolby &reg; Digital (AC3) dane wyjściowe można zapisywać tylko w pliku MP4 ISO.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
* [Wykonywanie zaawansowanych zadań kodowania przez dostosowanie Media Encoder Standard ustawień wstępnych](media-services-custom-mes-presets-with-dotnet.md)
* [Limity przydziału i ograniczenia](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
