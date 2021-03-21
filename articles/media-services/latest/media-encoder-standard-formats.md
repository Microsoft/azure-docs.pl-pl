---
title: Formaty koderów standardowych i kodery-dekoder na platformie Azure
description: Ten artykuł zawiera listę najpopularniejszych formatów importu i eksportu plików, których można używać z programem StandardEncoderPreset.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.reviewer: anilmur
ms.openlocfilehash: f0cf89a755090010bb80507c52f90baa2a4e804a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617628"
---
# <a name="standard-encoder-formats-and-codecs"></a>Standardowe formaty i kodeki kodera

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ten artykuł zawiera listę najpopularniejszych formatów importu i eksportu plików, których można używać z programem [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). Aby uzyskać informacje na temat tworzenia niestandardowych ustawień wstępnych przy użyciu programu **StandardEncoderPreset**, zobacz [Tworzenie przekształcenia z niestandardowym ustawieniem wstępnym](customize-encoder-presets-how-to.md).

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

### <a name="audio-formats-in-input-containers"></a>Formaty audio w kontenerach wejściowych

Koder w warstwie Standardowa obsługuje następujące formaty audio w kontenerach wejściowych:

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
| HEVC/H. 265| Profil główny|

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
| MP4 <br/><br/>(w tym kontenery MP4 o wieloszybkości transmisji bitów) |H. 264 (profile o wysokim, głównym i bazowym), HEVC (H. 265) 8-bitowy |AAC-LC, IT-AAC V1, IT-AAC v2 |
| MPEG2 — TS |H. 264 (profile o wysokim, głównym i bazowym) |AAC-LC, IT-AAC V1, IT-AAC v2 |

## <a name="next-steps"></a>Następne kroki

[Tworzenie przekształcenia z niestandardowym ustawieniem wstępnym](customize-encoder-presets-how-to.md)
