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
ms.openlocfilehash: 649f1e6cf79aa17d053cc547debf5232120bb4fe
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760638"
---
# <a name="language-support-for-computer-vision"></a>Obsługa języka dla przetwarzanie obrazów

Niektóre funkcje przetwarzanie obrazów obsługują wiele języków; wszystkie funkcje, które nie są wymienione w tym miejscu, obsługują tylko język angielski.

## <a name="optical-character-recognition-ocr"></a>Optyczne rozpoznawanie znaków (OCR)

Interfejsy API rozpoznawania przetwarzanie obrazów obsługują kilka języków. Nie wymagają określenia kodu języka. Aby uzyskać więcej informacji, zobacz [optyczne rozpoznawanie znaków (OCR)](concept-recognizing-text.md) .

|Język| Kod języka | INTERFEJS API OCR | Przeczytaj v 3.0 | Przeczytaj publiczną wersję zapoznawczą wersji v 3.1 |
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
|niemiecki | `de` |✔ |✔ |✔ |
|Grecki | `el` |✔ | | |
|Węgierski | `hu` |✔ | | |
|Włoski | `it` |✔ |✔ |✔ |
|Japoński | `ja` |✔ | | |
|koreański | `ko` |✔ | | |
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

Niektóre akcje interfejsu API [analizy obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) mogą zwracać wyniki w innych językach, określone za pomocą `language` parametru zapytania. Inne akcje zwracają wyniki w języku angielskim niezależnie od tego, jaki język jest określony, a inne zgłaszają wyjątek dla nieobsługiwanych języków. Akcje są określone za pomocą `visualFeatures` `details` parametrów i. Zapoznaj się z [omówieniem](home.md) listy wszystkich akcji, które można wykonać za pomocą analizy obrazów.

|Język | Kod języka | Kategorie | Tagi | Opis | Dorosły | Marki | Kolor | Twarze | ImageType | Obiekty | Osobistości | Punkty orientacyjne |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chiński | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Angielski | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japoński | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugalski | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Hiszpański | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Następne kroki

Zacznij korzystać z funkcji przetwarzanie obrazów wymienionych w tym przewodniku.

* [Analizowanie obrazu lokalnego (REST)](./quickstarts/csharp-analyze.md)
* [Wyodrębnij drukowany tekst (REST)](./quickstarts/csharp-print-text.md)
