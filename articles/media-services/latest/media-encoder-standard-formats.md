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
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.reviewer: anilmur
ms.openlocfilehash: c8c111d046bfaefac11df576e455162d20acecd0
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266565"
---
# <a name="standard-encoder-formats-and-codecs"></a>Standardowe formaty i kodeki kodera

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ten artykuł zawiera listę najpopularniejszych formatów importu i eksportu plików, których można używać z programem [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). Aby uzyskać informacje na temat tworzenia niestandardowych ustawień wstępnych przy użyciu programu **StandardEncoderPreset**, zobacz [Tworzenie przekształcenia z niestandardowym ustawieniem wstępnym](customize-encoder-presets-how-to.md).

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

### <a name="audio-formats-in-input-containers"></a>Formaty audio w kontenerach wejściowych

Koder w warstwie Standardowa obsługuje następujące formaty audio w kontenerach wejściowych:

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
| HEVC/H. 265| Profil główny|

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

## <a name="next-steps"></a>Następne kroki

[Tworzenie przekształcenia z niestandardowym ustawieniem wstępnym](customize-encoder-presets-how-to.md)
