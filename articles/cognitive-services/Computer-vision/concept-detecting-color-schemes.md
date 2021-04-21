---
title: Wykrywanie schematu kolorów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z wykrywaniem schematu kolorów na obrazach przy użyciu interfejsu API przetwarzanie obrazów API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2b113c424851065e244c3943bf4a4816bae9bba8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778917"
---
# <a name="detect-color-schemes-in-images"></a>Wykrywanie schematów kolorów na obrazach

przetwarzanie obrazów analizuje kolory na obrazie, aby zapewnić trzy różne atrybuty: dominujący kolor pierwszego planu, dominujący kolor tła i zestaw dominujących kolorów obrazu jako całości. Zwrócone kolory należą do zestawu: czarny, niebieski, brązowy, szary, zielony, pomarańczowy, fioletowy, purpurowy, czerwony, tealowy, biały i żółty. 

przetwarzanie obrazów wyodrębnia również kolor akcentu, który reprezentuje najbardziej nabłędniejszy kolor na obrazie, na podstawie kombinacji dominujących kolorów i nasycenia. Kolor akcentu jest zwracany jako szesnastkowym kodem koloru HTML. 

przetwarzanie obrazów zwraca również wartość logiczną wskazującą, czy obraz jest czarno-biały.

## <a name="color-scheme-detection-examples"></a>Przykłady wykrywania schematu kolorów

Poniższy przykład ilustruje odpowiedź JSON zwróconą przez przetwarzanie obrazów podczas wykrywania schematu kolorów przykładowego obrazu. W tym przypadku przykładowy obraz nie jest czarno-białym obrazem, ale dominującymi kolorami pierwszego planu i tła są czarne, a dominującymi kolorami obrazu jako całości są czarne i białe.

![Góry na zewnątrz o wyechurze, z cytą osoby](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Przykłady kolorów dominujących

W poniższej tabeli przedstawiono zwracane kolory pierwszego planu, tła i obrazu dla każdego przykładowego obrazu.

| Obraz | Kolory dominujące |
|-------|-----------------|
|![Biały kwiat z zielonym tłem](./Images/flower.png)| Pierwszy plan: czarny<br/>Tło: biały<br/>Kolory: czarny, biały, zielony|
![Pociąg przejechyny przez stację](./Images/train_station.png) | Pierwszy plan: czarny<br/>Tło: czarny<br/>Kolory: Czarny |

### <a name="accent-color-examples"></a>Przykłady kolorów akcentu

 W poniższej tabeli przedstawiono zwracany kolor akcentu jako szesnastkową wartość koloru HTML dla każdego przykładowego obrazu.

| Obraz | Kolor akcentu |
|-------|--------------|
|![Osoba stojąca na skałach górskich o wycopszanych skałach](./Images/mountain_vista.png) | #BB6D10 |
|![Biały kwiat z zielonym tłem](./Images/flower.png) | #C6A205 |
|![Pociąg uruchomiony przez stację](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Przykłady wykrywania & czarnej białej

W poniższej tabeli przetwarzanie obrazów oceny czarno-białe na przykładowych obrazach.

| Obraz | Czarny & biały? |
|-------|----------------|
|![Czarno-biały obraz budynków w Centrum](./Images/bw_buildings.png) | true |
|![Niebieski dom i front](./Images/house_yard.png) | fałsz |

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja wykrywania schematu kolorów jest częścią interfejsu API [analizowanie obrazów](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. Ten interfejs API można wywołać za pomocą natywnego zestawu SDK lub wywołań REST. Uwzględnij `Color` w **parametrze zapytania visualFeatures.** Następnie, gdy otrzymasz pełną odpowiedź JSON, po prostu prze analizuj ciąg pod celu uzyskania zawartości `"color"` sekcji.

* [Szybki start: przetwarzanie obrazów API REST lub bibliotek klienckich](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
