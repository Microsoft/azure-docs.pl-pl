---
title: Formaty Media Encoder Standard i kodeki — Azure
description: Ten artykuł zawiera omówienie formatów Media Encoder Standard i koderów-dekoder.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: anilmur
ms.openlocfilehash: 9055c4c1af14c9ecc8fbdeb621c30ac6ebe5afc4
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108809"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Formaty i kodeki narzędzia Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Wybierz używaną wersję Media Services:"]
> * [Wersja 2](media-services-media-encoder-standard-formats.md)
> * [Wersja 3](../latest/encode-media-encoder-standard-formats-reference.md)

Ten dokument zawiera listę najpopularniejszych formatów importu i eksportu plików, których można używać z Media Encoder Standard.

## <a name="input-containerfile-formats"></a>Dane wejściowe w formacie kontenera/pliku
| Formaty plików (rozszerzenia nazw plików) | Obsługiwane |
| --- | --- |
| FLV (z koderami-dekoderami H.264 i AAC) (flv) |Tak |
| MXF (mxf) |Tak |
| GXF (gxf) |Tak |
| MPEG2-PS, MPEG2-TS, 3GP (ts, ps, 3gp, 3gpp, mpg) |Tak |
| Windows Media Video (WMV)/ASF (wmv, asf) |Tak |
| AVI (nieskompresowany 8-bitowy/10-bitowy) (avi) |Tak |
| MP4 (mp4, m4a, m4v)/ISMV (isma, ismv) |Tak |
| [Microsoft Digital Video Recording (DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) (dvr-ms) |Tak |
| Matroska/WebM (mkv) |Tak |
| WAVE/WAV (wav) |Tak |
| QuickTime (mov) |Tak |

> [!NOTE]
> Powyżej znajduje się lista najczęściej spotykanych rozszerzeń nazw plików. Media Encoder Standard obsługuje wiele innych (na przykład:. M2TS,. mpeg2video,. QT). Jeśli próbujesz zakodować plik i otrzymasz komunikat o błędzie o nieobsługiwanym formacie, podaj swoją opinię [tutaj](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).
> 
> 

### <a name="audio-formats-in-input-containers"></a>Formaty audio w kontenerach wejściowych
Media Encoder Standard obsługuje przeprowadzenie następujących formatów audio w kontenerach wejściowych:

* Pliki MXF, GXF i QuickTime, które mają ścieżki audio z przeplotem stereo lub 5,1 próbek

lub

* Pliki MXF, GXF i QuickTime, w których dane audio są przenoszone jako osobne ścieżki PCM, ale mapowanie kanałów (na stereo lub 5.1) może zostać ustalone na podstawie metadanych pliku

## <a name="input-video-codecs"></a>Kodery-dekoder wideo
| Kodery-dekoder wideo | Obsługiwane |
| --- | --- |
| AVC 8-bitowy/10-bitowy, do 4:2:2, wraz z AVCIntra |8-bitowy 4:2:0 oraz 4:2:2 |
| Avid DNxHD (w pliku MXF) |Tak |
| DVCPro/DVCProHD (w pliku MXF) |Tak |
| Digital video (DV) (w plikach AVI) |Tak |
| JPEG 2000 |Tak |
| MPEG-2 (maksymalny profil 422 i wysoki poziom; wraz z wariantami, takimi jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® oraz D10) |Maksymalnie profil 422 |
| MPEG-1 |Tak |
| VC-1/WMV9 |Tak |
| Canopus HQ/HQX |Nie |
| MPEG-4 część 2 |Tak |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Tak |
| YUV420 bez kompresji lub mezzanine |Tak |
| Apple ProRes 422 |Tak |
| Apple ProRes 422 LT |Tak |
| Apple ProRes 422 HQ |Tak |
| Apple ProRes Proxy |Tak |
| Apple ProRes 4444 |Tak |
| Apple ProRes 4444 XQ |Tak |
| HEVC/H. 265| Główne i główne 10 (&#42;) profile<br/>Główna obsługa 10 profilów jest przeznaczona dla zawartości 8bit 4:2:0. |

## <a name="input-audio-codecs"></a>Kodery-dekoder Audio Input
| Kodery-dekodery wejściowych danych audio | Obsługiwane |
| --- | --- |
| AAC (AAC-LC, AAC-HE oraz AAC-HEv2; maksymalnie 5.1) |Tak |
| MPEG Layer 2 |Tak |
| MP3 (MPEG-1 Audio Layer 3) |Tak |
| Windows Media Audio |Tak |
| WAV/PCM |Tak |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Tak |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Tak |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Tak |
| AMR (Adaptive Multi-Rate) |Tak |
| AES (SMPTE 331M oraz 302M, AES3-2003) |Nie |
| Dolby® E |Nie |
| Dolby® Digital (AC3) |Nie |
| Dolby® Digital Plus (E-AC3) |Nie |

## <a name="output-formats-and-codecs"></a>Formaty i kodeki wyjściowe
W poniższej tabeli wymieniono kodery-dekoder i formaty plików, które są obsługiwane do eksportowania.

| Format pliku | Koder-dekoder wideo | Koder-dekoder audio |
| --- | --- | --- |
| MP4 <br/><br/>(w tym kontenery MP4 o wieloszybkości transmisji bitów) |H. 264 (profile o wysokim, głównym i bazowym) |AAC-LC, IT-AAC V1, IT-AAC v2 |
| MPEG2 — TS |H. 264 (profile o wysokim, głównym i bazowym) |AAC-LC, IT-AAC V1, IT-AAC v2 |

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zobacz też
[Kodowanie zawartości na żądanie za pomocą Azure Media Services](media-services-encode-asset.md)

[Jak kodować przy użyciu Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)
