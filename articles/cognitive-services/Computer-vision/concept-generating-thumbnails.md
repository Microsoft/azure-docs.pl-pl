---
title: Miniatury inteligentne i przycięte — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z generowaniem miniatur obrazów przy użyciu interfejs API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4874910f37b49990a659b48af0cf27921c3fcd5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "68945231"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Generowanie miniatur inteligentnych z przyciętymi przetwarzanie obrazów

Miniatura to skrócona reprezentacja obrazu. Miniatury służą do reprezentowania obrazów i innych danych w bardziej ekonomiczny, przyjazny dla układu. Interfejs API przetwarzania obrazów używa inteligentnego przycinania oraz zmiany rozmiarów obrazu w celu utworzenia intuicyjnych miniatur dla danego obrazu.

Przetwarzanie obrazów algorytm generowania miniatur działa następująco:

1. Usuń rozpraszające się elementy z obrazu i zidentyfikuj _obszar zainteresowania_ &mdash; obrazu, w którym pojawiają się obiekty główne.
1. Przytnij obraz na podstawie zidentyfikowanego _obszaru zainteresowania_.
1. Zmień współczynnik proporcji na wymiary miniatury docelowej.

## <a name="area-of-interest"></a>Obszar zainteresowania

Po przekazaniu obrazu interfejs API przetwarzania obrazów analizuje go w celu określenia *obszaru zainteresowania*. Może następnie użyć tego regionu do określenia sposobu przycinania obrazu. Operacja przycinania jest jednak zawsze zgodna z pożądanym współczynnikiem proporcji, jeśli jest określony.

Możesz również uzyskać współrzędne pola ograniczonego dla tego samego *obszaru zainteresowania* przez wywołanie interfejsu API **areaOfInterest** . Możesz następnie użyć tych informacji, aby zmodyfikować oryginalny obraz.

## <a name="examples"></a>Przykłady

Wygenerowane miniatury mogą się znacznie różnić w zależności od tego, co jest określane jako wysokość, Szerokość i inteligentne przycinanie, jak pokazano na poniższej ilustracji.

![Obraz górski obok różnych konfiguracji przycinania](./Images/thumbnail-demo.png)

W poniższej tabeli przedstawiono typowe miniatury wygenerowane przez przetwarzanie obrazów dla przykładowych obrazów. Miniatury zostały wygenerowane dla określonej wartości docelowej wysokości i szerokości 50 pikseli, z włączonym funkcją inteligentnego przycinania.

| Image (Obraz) | Miniaturk |
|-------|-----------|
|![Górski grunty na zachód słońca i Silhouette osoby](./Images/mountain_vista.png) | ![Miniatura górskiego góry o zachodzie słońca i Silhouette osoby](./Images/mountain_vista_thumbnail.png) |
|![Biały kwiat z zielonym tłem](./Images/flower.png) | ![Vision Analizuj miniaturę kwiatów](./Images/flower_thumbnail.png) |
|![Kobieta na dachu budynku apartamentu](./Images/woman_roof.png) | ![Miniatura kobieta na dachu budynku apartamentu](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [tagowania obrazów](concept-tagging-images.md) i [kategoryzacji obrazów](concept-categorizing-images.md).
