---
title: Wygeneruj miniaturę Sprite z Azure Media Services | Microsoft Docs
description: W tym temacie pokazano, jak wygenerować miniaturę ikon z Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: e5d32d1bc3bd704b03e58c62251a323ed3f4662c
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "61229054"
---
# <a name="generate-a-thumbnail-sprite"></a>Generowanie sprite’u miniatury  

Za pomocą Media Encoder Standard można wygenerować miniaturkę Sprite, czyli plik JPEG, który zawiera wiele miniatur małych rozdzielczości połączonych w jeden (duży) obraz wraz z plikiem VTT. Ten plik VTT określa zakres czasu w wejściowym wideo, który reprezentuje każda miniatura, wraz z rozmiarem i współrzędnymi tej miniatury w dużym pliku JPEG. Odtwarzacze wideo wykorzystują plik VTT i Sprite Image do wyświetlania "wizualnego" elementu SeekBar, oferując podgląd z wizualną opinią podczas szybkiej kontroli i przesyłania dalej wzdłuż osi czasu wideo.

Aby można było użyć Media Encoder Standard do wygenerowania miniaturowej Sprite, ustawienie wstępne:

1. Należy użyć formatu obrazu z miniaturą JPG
2. Należy określić wartości początkowe/krok/zakres jako sygnatury czasowe lub wartości procentowe (a nie liczby ramek) 
    
    1. Nie można mieszać sygnatur czasowych i% wartości

3. Wartość SpriteColumn musi być liczbą nieujemną większą lub równą 1.

    1. Jeśli SpriteColumn jest ustawiony na M >= 1, obraz wyjściowy jest prostokątem z kolumnami M. Jeśli liczba miniatur wygenerowanych za pośrednictwem #2 nie jest dokładną wielokrotnością M, ostatni wiersz będzie niepełny i pozostawia czarne piksele.  

Oto przykład:

```json
{
    "Version": 1.0,
    "Codecs": [
    {
      "Start": "00:00:01",
      "Type": "JpgImage",
      "Step": "5%",
      "Range": "100%",
      "JpgLayers": [
        {
          "Type": "JpgLayer",
          "Width": "10%",
          "Height": "10%",
          "Quality": 90
        }
      ],
      "SpriteColumn": 10
    }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
   ]
}
```

## <a name="known-issues"></a>Znane problemy

1.  Nie jest możliwe wygenerowanie obrazu Sprite z pojedynczym wierszem obrazów (SpriteColumn = 1 spowoduje utworzenie obrazu z jedną kolumną).
2.  Fragmentowanie obrazów Sprite do obrazów JPEG o umiarkowanym rozmiarze nie jest jeszcze obsługiwane. Z tego względu należy zachować ostrożność, aby ograniczyć liczbę miniatur i ich rozmiar, tak aby wynikowa miniaturka Sprite nie przekraczała 8 M pikseli.
3.  Azure Media Player obsługuje sprites w przeglądarkach Microsoft Edge, Chrome i Firefox. Analizowanie VTT nie jest obsługiwane w IE11.

## <a name="next-steps"></a>Następne kroki

[Kodowanie zawartości](media-services-encode-asset.md)
