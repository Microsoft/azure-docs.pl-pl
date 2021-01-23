---
title: Obsługa języka — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera listę języków naturalnych obsługiwanych przez funkcje przetwarzanie obrazów. OCR, analiza obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1abb857c9f03be502db02099383c6fe0b5110461
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736822"
---
# <a name="language-support-for-computer-vision"></a>Obsługa języka dla przetwarzanie obrazów

Niektóre funkcje przetwarzanie obrazów obsługują wiele języków; wszystkie funkcje, które nie są wymienione w tym miejscu, obsługują tylko język angielski.

## <a name="optical-character-recognition-ocr"></a>Optyczne rozpoznawanie znaków (OCR)

Interfejsy API rozpoznawania przetwarzanie obrazów obsługują kilka języków. Nie wymagają określenia kodu języka. Aby uzyskać więcej informacji, zobacz [optyczne rozpoznawanie znaków (OCR)](concept-recognizing-text.md) .

|Język| Kod języka | INTERFEJS API OCR | Przeczytaj 3,0 i 3,1 | Przeczytaj v 3.2 — wersja zapoznawcza 1 |
|:-----|:----:|:-----:|:---:|:---:|
|Arabski | `ar`|✔ | | |
|Chiński (uproszczony) | `zh-Hans`|✔ | |✔ |
|Chiński (tradycyjny) | `zh-Hant`|✔ | | |
|Czeski | `cs` |✔ | | |
|Duński | `da` |✔ | | |
|Niderlandzki | `nl` |✔ |✔ |✔ |
|Angielski | `en` |✔ |✔ |✔ |
|Fiński | `fi` |✔ | | |
|Francuski | `fr` |✔ |✔ |✔ |
|Niemiecki | `de` |✔ |✔ |✔ |
|Grecki | `el` |✔ | | |
|Węgierski | `hu` |✔ | | |
|Włoski | `it` |✔ |✔ |✔ |
|japoński | `ja` |✔ | |✔ |
|Koreański | `ko` |✔ | | |
|Norweski | `nb` |✔ | | |
|Polski | `pl` |✔ | | |
|Portugalski | `pt` |✔ |✔ |✔ |
|Rumuński | `ro` |✔ | | |
|Rosyjski | `ru` |✔ | | |
|Serbski (cyrylica) | `sr-Cyrl` |✔ | | |
|Serbski (łaciński) | `sr-Latn` |✔ | | |
|Słowacki | `sk` |✔ | | |
|Hiszpański | `es` |✔ |✔ |✔ |
|Szwedzki | `sw` |✔ | | |
|Turecki | `tr` |✔ | | |

## <a name="image-analysis"></a>Analiza obrazu

Niektóre akcje interfejsu API [analizy obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) mogą zwracać wyniki w innych językach, określone za pomocą `language` parametru zapytania. Inne akcje zwracają wyniki w języku angielskim niezależnie od tego, jaki język jest określony, a inne zgłaszają wyjątek dla nieobsługiwanych języków. Akcje są określone za pomocą `visualFeatures` `details` parametrów i. Zapoznaj się z [omówieniem](overview.md) listy wszystkich akcji, które można wykonać za pomocą analizy obrazów.

|Język | Kod języka | Kategorie | Tagi | Opis | Dorosły | Marki | Kolor | Twarze | ImageType | Obiekty | Osobistości | Punkty orientacyjne |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chiński | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Angielski | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|japoński | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugalski | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Hiszpański | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Następne kroki

Zacznij korzystać z funkcji przetwarzanie obrazów wymienionych w tym przewodniku.

* [Analizowanie obrazu lokalnego (REST)](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-analyze.md)
* [Wyodrębnij drukowany tekst (REST)](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-print-text.md)