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
ms.openlocfilehash: 81d0183b6dde1a5fe6bd9cd36910fd3dfc1cc5b1
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86205287"
---
# <a name="language-support-for-computer-vision"></a>Obsługa języka dla przetwarzanie obrazów

Niektóre funkcje przetwarzanie obrazów obsługują wiele języków; wszystkie funkcje, które nie są wymienione w tym miejscu, obsługują tylko język angielski.

## <a name="optical-character-recognition-ocr"></a>Optyczne rozpoznawanie znaków (OCR)

Interfejsy API rozpoznawania przetwarzanie obrazów obsługują kilka języków. Nie wymagają określenia kodu języka. Aby uzyskać więcej informacji, zobacz [optyczne rozpoznawanie znaków (OCR)](concept-recognizing-text.md) .

|Language| Kod języka | INTERFEJS API OCR | Odczytaj interfejs API |
|:-----|:----:|:-----:|:---:|
|Arabski | `ar`|✔ | |
|Chiński (uproszczony) | `zh-Hans`|✔ | |
|Chiński (tradycyjny) | `zh-Hant`|✔ | |
|Czeski | `cs` |✔ | |
|Duński | `da` |✔ | |
|Niderlandzki | `nl` |✔ |✔ |
|Angielski | `en` |✔ |✔ |
|Fiński | `fi` |✔ | |
|Francuski | `fr` |✔ |✔ |
|Niemiecki | `de` |✔ |✔ |
|Grecki | `el` |✔ | |
|Węgierski | `hu` |✔ | |
|Włoski | `it` |✔ |✔ |
|Japoński | `ja` |✔ | |
|Koreański | `ko` |✔ | |
|Norweski | `nb` |✔ | |
|Polski | `pl` |✔ | |
|Portugalski | `pt` |✔ |✔ |
|Rumuński | `ro` |✔ | |
|Rosyjski | `ru` |✔ | |
|Serbski (cyrylica) | `sr-Cyrl` |✔ | |
|Serbski (łaciński) | `sr-Latn` |✔ | |
|Słowacki | `sk` |✔ | |
|Hiszpański | `es` |✔ |✔ |
|Szwedzki | `sw` |✔ | |
|Turecki | `tr` |✔ | |

## <a name="image-analysis"></a>Analiza obrazu

Niektóre akcje interfejsu API [analizy obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) mogą zwracać wyniki w innych językach, określone za pomocą `language` parametru zapytania. Inne akcje zwracają wyniki w języku angielskim niezależnie od tego, jaki język jest określony, a inne zgłaszają wyjątek dla nieobsługiwanych języków. Akcje są określone za pomocą `visualFeatures` `details` parametrów i. Zapoznaj się z [omówieniem](home.md) listy wszystkich akcji, które można wykonać za pomocą analizy obrazów.

|Language | Kod języka | Kategorie | Tagi | Opis | Dorosły | Marki | Kolor | Twarze | ImageType | Obiekty | Osobistości | Punkty orientacyjne |
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
