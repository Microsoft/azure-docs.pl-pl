---
title: Kodowanie przekształcenia niestandardowego
description: W tym temacie pokazano, jak używać Azure Media Services v3 do kodowania niestandardowej transformacji przy użyciu interfejsu REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 157a3063df384ca8be55c28e3502ff59d0593d68
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960575"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Jak kodować przy użyciu niestandardowej transformacji

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Przy kodowaniu przy użyciu Azure Media Services można szybko rozpocząć pracę z jednym z zalecanych wbudowanych ustawień wstępnych, opartych na najlepszych rozwiązaniach branżowych, jak pokazano w samouczku [pliki przesyłania strumieniowego](stream-files-tutorial-with-rest.md#create-a-transform) . Możesz również utworzyć niestandardowe ustawienie wstępne, aby określić wymagania dotyczące określonego scenariusza lub urządzenia.

## <a name="considerations"></a>Zagadnienia do rozważenia

Podczas tworzenia niestandardowych ustawień wstępnych są stosowane następujące zagadnienia:

* Wszystkie wartości wysokości i szerokości dla zawartości AVC muszą być wielokrotnością 4.
* W Azure Media Services V3 wszystkie szybkości transmisji bitów są w bitach na sekundę. Różni się to od ustawień predefiniowanych za pomocą naszych interfejsów API v2, które używały kilobitów/s jako jednostki. Na przykład, jeśli szybkość transmisji bitów w wersji 2 została określona jako 128 (kilobity/sekundę), w v3 zostanie ustawiona wartość 128000 (BITS/s).

## <a name="prerequisites"></a>Wymagania wstępne 

- [Utwórz konto Media Services](./account-create-how-to.md). <br/>Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta Media Services. 
- [Skonfiguruj funkcję publikowania dla Azure Media Services wywołań interfejsu API REST](media-rest-apis-with-postman.md).<br/>Pamiętaj, aby postępować zgodnie z ostatnim krokiem w temacie [pobieranie tokenu usługi Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Definiowanie niestandardowego ustawienia wstępnego

W poniższym przykładzie zdefiniowano treść żądania nowej transformacji. Definiujemy zestaw danych wyjściowych, które mają być generowane, gdy zostanie użyte to przekształcenie. 

W tym przykładzie najpierw dodamy warstwę AacAudio dla kodowania audio i dwóch warstw H264Video dla kodowania wideo. W warstwach wideo przypiszemy etykiety, aby można było ich używać w nazwach plików wyjściowych. Następnie chcemy, aby dane wyjściowe zawierały także miniatury. W poniższym przykładzie określimy obrazy w formacie PNG, Wygenerowano na 50% rozdzielczości wejściowego filmu wideo oraz trzy sygnatury czasowe — {25%, 50%, 75} długości wejściowego wideo. Na koniec określimy format plików wyjściowych — jeden do wideo i audio, a drugi dla miniatur. Ponieważ mamy wiele H264Layers, musimy używać makr, które generują unikatowe nazwy na warstwę. Możemy użyć `{Label}` makra lub, jak `{Bitrate}` pokazano w przykładzie.

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
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
            }
        ]
    }
}

```

## <a name="create-a-new-transform"></a>Utwórz nowe przekształcenie  

W tym przykładzie utworzysz **transformację** opartą na niestandardowym ustawieniu wstępnym zdefiniowanym wcześniej. Podczas tworzenia transformacji należy najpierw użyć opcji [Get](/rest/api/media/transforms/get) do sprawdzenia, czy już istnieje. Jeśli transformacja istnieje, użyj jej ponownie. 

W pobranej kolekcji programu Poster wybierz pozycję **transformacje i zadania** -> **Utwórz lub zaktualizuj transformację**.

Metoda **Put** żądania HTTP jest podobna do:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Wybierz kartę **treść** i Zamień treść na [wcześniej zdefiniowany](#define-a-custom-preset)kod JSON. Aby Media Services zastosować transformację do określonego wideo lub audio, należy przesłać zadanie w ramach tej transformacji.

Wybierz pozycję **Wyślij**. 

Aby Media Services zastosować transformację do określonego wideo lub audio, należy przesłać zadanie w ramach tej transformacji. Aby zapoznać się z kompletnym przykładem, który pokazuje, jak przesłać zadanie w ramach przekształcenia, zobacz [Samouczek: przesyłanie strumieniowe plików wideo — REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Następne kroki

Zobacz [inne operacje REST](/rest/api/media/)
