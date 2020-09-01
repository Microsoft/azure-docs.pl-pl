---
title: Formaty Media Encoder Standard i kodeki — Azure
description: Ten artykuł zawiera omówienie formatów Media Encoder Standard i koderów-dekoder.
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
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 78236a334b6c75f823819c70c0cdbb75bb30191d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89257436"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Formaty i kodeki narzędzia Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Wybierz używaną wersję Media Services:"]
> * [Wersja 2](media-services-media-encoder-standard-formats.md)
> * [Wersja 3](../latest/media-encoder-standard-formats.md)

Ten dokument zawiera listę najpopularniejszych formatów importu i eksportu plików, których można używać z Media Encoder Standard.

## <a name="input-containerfile-formats"></a>Dane wejściowe w formacie kontenera/pliku
| Formaty plików (rozszerzenia nazw plików) | Obsługiwane |
| --- | --- |
| FLV (z koderami-dekoderami H.264 i AAC) (flv) |Yes |
| MXF (mxf) |Yes |
| GXF (gxf) |Yes |
| MPEG2-PS, MPEG2-TS, 3GP (ts, ps, 3gp, 3gpp, mpg) |Yes |
| Windows Media Video (WMV)/ASF (wmv, asf) |Yes |
| AVI (nieskompresowany 8-bitowy/10-bitowy) (avi) |Yes |
| MP4 (mp4, m4a, m4v)/ISMV (isma, ismv) |Yes |
| [Microsoft Digital Video Recording (DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) (dvr-ms) |Yes |
| Matroska/WebM (mkv) |Yes |
| WAVE/WAV (wav) |Yes |
| QuickTime (mov) |Yes |

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
| Avid DNxHD (w pliku MXF) |Yes |
| DVCPro/DVCProHD (w pliku MXF) |Yes |
| Digital video (DV) (w plikach AVI) |Yes |
| JPEG 2000 |Yes |
| MPEG-2 (maksymalny profil 422 i wysoki poziom; wraz z wariantami, takimi jak XDCAM, XDCAM HD, XDCAM IMX, CableLabs® oraz D10) |Maksymalnie profil 422 |
| MPEG-1 |Yes |
| VC-1/WMV9 |Yes |
| Canopus HQ/HQX |Nie |
| MPEG-4 część 2 |Yes |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |
| YUV420 bez kompresji lub mezzanine |Yes |
| Apple ProRes 422 |Yes |
| Apple ProRes 422 LT |Yes |
| Apple ProRes 422 HQ |Yes |
| Apple ProRes Proxy |Yes |
| Apple ProRes 4444 |Yes |
| Apple ProRes 4444 XQ |Yes |
| HEVC/H. 265| Główne i główne 10 (&#42;) profile<br/>Główna obsługa 10 profilów jest przeznaczona dla zawartości 8bit 4:2:0. |

## <a name="input-audio-codecs"></a>Kodery-dekoder Audio Input
| Kodery-dekodery wejściowych danych audio | Obsługiwane |
| --- | --- |
| AAC (AAC-LC, AAC-HE oraz AAC-HEv2; maksymalnie 5.1) |Yes |
| MPEG Layer 2 |Yes |
| MP3 (MPEG-1 Audio Layer 3) |Yes |
| Windows Media Audio |Yes |
| WAV/PCM |Yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Yes |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |
| AMR (Adaptive Multi-Rate) |Yes |
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
