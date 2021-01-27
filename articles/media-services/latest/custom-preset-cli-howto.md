---
title: Kodowanie przekształcenia niestandardowego przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 3 Media Services
description: W tym temacie przedstawiono sposób kodowania niestandardowego przekształcenia przy użyciu interfejsu wiersza polecenia platformy Azure przy użyciu Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 9758ed7403e11ca30732377a65bbde1944368cb2
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897194"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>Jak kodować przy użyciu niestandardowej transformacji — interfejs wiersza polecenia platformy Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Przy kodowaniu przy użyciu Azure Media Services można szybko rozpocząć pracę z jednym z zalecanych wbudowanych ustawień wstępnych, opartych na najlepszych rozwiązaniach branżowych, jak pokazano w [plikach przesyłania strumieniowego](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) — Szybki Start. Możesz również utworzyć niestandardowe ustawienie wstępne, aby określić wymagania dotyczące określonego scenariusza lub urządzenia.

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas tworzenia niestandardowych ustawień wstępnych są stosowane następujące zagadnienia:

* Wszystkie wartości wysokości i szerokości dla zawartości AVC muszą być wielokrotnością 4.
* W Azure Media Services V3 wszystkie szybkości transmisji bitów są w bitach na sekundę. Różni się to od ustawień predefiniowanych za pomocą naszych interfejsów API v2, które używały kilobitów/s jako jednostki. Na przykład, jeśli szybkość transmisji bitów w wersji 2 została określona jako 128 (kilobity/sekundę), w v3 zostanie ustawiona wartość 128000 (BITS/s).

## <a name="prerequisites"></a>Wymagania wstępne

[Utwórz konto Media Services](./create-account-howto.md).

Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta Media Services.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Definiowanie niestandardowego ustawienia wstępnego

W poniższym przykładzie zdefiniowano treść żądania nowej transformacji. Definiujemy zestaw danych wyjściowych, które mają być generowane, gdy zostanie użyte to przekształcenie.

W tym przykładzie najpierw dodamy warstwę AacAudio dla kodowania audio i dwóch warstw H264Video dla kodowania wideo. W warstwach wideo przypiszemy etykiety, aby można było ich używać w nazwach plików wyjściowych. Następnie chcemy, aby dane wyjściowe zawierały także miniatury. W poniższym przykładzie określimy obrazy w formacie PNG, Wygenerowano na 50% rozdzielczości wejściowego filmu wideo oraz trzy sygnatury czasowe — {25%, 50%, 75} długości wejściowego wideo. Na koniec określimy format plików wyjściowych — jeden do wideo i audio, a drugi dla miniatur. Ponieważ mamy wiele H264Layers, musimy używać makr, które generują unikatowe nazwy na warstwę. Możemy użyć `{Label}` makra lub, jak `{Bitrate}` pokazano w przykładzie.

Zamierzamy zapisać tę transformację w pliku. W tym przykładzie nazwamy plik `customPreset.json` .

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
            "step": "25%",
            "range": "80%",
            "layers": [
                {
                    "width": "50%",
                    "height": "50%"
                }
            ]
        }
    ],
    "formats": [
        {
            "@odata.type": "#Microsoft.Media.Mp4Format",
            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
            "outputFiles": []
        },
        {
            "@odata.type": "#Microsoft.Media.PngFormat",
            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
        }
    ]
}
```

## <a name="create-a-new-transform"></a>Utwórz nowe przekształcenie  

W tym przykładzie utworzysz **transformację** opartą na niestandardowym ustawieniu wstępnym zdefiniowanym wcześniej. Podczas tworzenia przekształcenia należy najpierw sprawdzić, czy już istnieje. Jeśli transformacja istnieje, użyj jej ponownie. Następujące `show` polecenie zwraca `customTransformName` transformację, jeśli istnieje:

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

Następujące polecenie interfejsu wiersza polecenia platformy Azure tworzy transformację na podstawie niestandardowego ustawienia wstępnego (zdefiniowanego wcześniej).

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Aby Media Services zastosować transformację do określonego wideo lub audio, należy przesłać zadanie w ramach tej transformacji. Pełny przykład pokazujący, jak przesłać zadanie w ramach przekształcenia, zobacz [Szybki Start: przesyłanie strumieniowe plików wideo — interfejs wiersza polecenia platformy Azure](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Zobacz także

[Interfejs wiersza polecenia platformy Azure](/cli/azure/ams)
