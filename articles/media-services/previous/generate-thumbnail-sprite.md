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
ms.date: 08/31/2020
ms.author: juliako
ms.openlocfilehash: 1d05e73bc9aa51bf20121790de94b1b6d0e26615
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89268574"
---
# <a name="generate-a-thumbnail-sprite"></a>Generowanie sprite’u miniatury

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

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
