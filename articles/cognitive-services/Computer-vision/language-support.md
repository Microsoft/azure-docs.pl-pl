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
ms.openlocfilehash: b065b36103b69f0601daa1388b45865856543d2b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540522"
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

|Język | Kod języka | Kategorie | Tagi | Opis | Dorosły | Marki | Color (Kolor) | Twarze | ImageType | Obiekty | Osobistości | Punkty orientacyjne |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chiński | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Angielski | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|japoński | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugalski | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Hiszpański | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Następne kroki

Zacznij korzystać z funkcji przetwarzanie obrazów wymienionych w tym przewodniku.

* [Analizowanie obrazu lokalnego (REST)](./quickstarts/csharp-analyze.md)
* [Wyodrębnij drukowany tekst (REST)](./quickstarts/csharp-print-text.md)
