---
title: Jak przyciąć pliki wideo za pomocą Media Services — .NET | Microsoft Docs
description: Przycinanie jest procesem wyboru prostokątnego okna wewnątrz ramki wideo i kodowania tylko pikseli w tym oknie. W tym temacie przedstawiono sposób przycinania plików wideo za pomocą Media Services przy użyciu platformy .NET.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 43d0e1e3b8c0eaa37a3b09557b333c3abafe8b63
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572438"
---
# <a name="how-to-crop-video-files-with-media-services---net"></a>Jak przyciąć pliki wideo za pomocą Media Services — .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Za pomocą Media Services można przyciąć wejściowe wideo. Przycinanie jest procesem wyboru prostokątnego okna wewnątrz ramki wideo i kodowania tylko pikseli w tym oknie. Poniższy diagram ułatwia zilustrowanie tego procesu.

## <a name="pre-processing-stage"></a>Etap przetwarzania wstępnego

Przycinanie jest etapem przetwarzania wstępnego, dlatego *Parametry przycinania* w ustawieniach wstępnych kodowania mają zastosowanie do *wejściowego* wideo. Kodowanie jest kolejnym etapem, a ustawienia szerokość/wysokość mają zastosowanie do *wstępnie przetworzonego* wideo, a nie do oryginalnego wideo. Podczas projektowania ustawień wstępnych wykonaj następujące czynności:

1. Wybierz parametry przycinania na podstawie oryginalnego wejściowego wideo
1. Wybierz ustawienia kodowania na podstawie przyciętego wideo.

> [!WARNING]
> Jeśli ustawienia kodowania nie są zgodne z przyciętym wideo, dane wyjściowe nie będą oczekiwane.

Na przykład wejściowy film wideo ma rozdzielczość 1920 x 1080 pikseli (współczynnik proporcji 16:9), ale ma czarne słupki (pola filaru) po lewej stronie i po prawej stronie, dzięki czemu tylko okna 4:3 lub 1440x1080 pikseli zawierają aktywne wideo. Można przyciąć czarne słupki i zakodować obszar 1440x1080.

## <a name="transform-code"></a>Przekształć kod

Poniższy fragment kodu ilustruje sposób pisania transformacji w programie .NET do przycinania wideo.  W kodzie założono, że masz plik lokalny do pracy z programem.

- Po lewej stronie znajduje się lewa lokalizacja kadrowania.
- Góra jest największą górną lokalizacją przycinania.
- Szerokość to końcowa szerokość przycinania.
- Height to końcowa wysokość przycinania.

```dotnet
var preset = new StandardEncoderPreset

    {

        Filters = new Filters

        {                   

            Crop = new Rectangle

            {

                Left = "200",

                Top = "200",

                Width = "1280",

                Height = "720"

            }

        },

        Codecs =

        {

            new AacAudio(),

            new H264Video()

            {

                Layers =

                {                           

                    new H264Layer

                    {

                        Bitrate = 1000000,

                        Width = "1280",

                        Height = "720"

                    }

                }

            }

        },

        Formats =

        {

            new Mp4Format

            {

                FilenamePattern = "{Basename}_{Bitrate}{Extension}"

            }

        }

    }

```
