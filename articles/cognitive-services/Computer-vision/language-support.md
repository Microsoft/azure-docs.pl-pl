---
title: Obsługa języka — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera listę języków naturalnych obsługiwanych przez funkcje przetwarzanie obrazów. OCR, Rozpoznawanie tekstu i odczytywanie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 680e3647e7a5847e132a53986e2cf17150bceac3
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560177"
---
# <a name="language-support-for-computer-vision"></a>Obsługa języka dla przetwarzanie obrazów

Niektóre funkcje przetwarzanie obrazów obsługują wiele języków; wszystkie funkcje, które nie są wymienione w tym miejscu, obsługują tylko język angielski.

## <a name="text-detection-ocr"></a>Wykrywanie tekstu (OCR)

Przetwarzanie obrazów może wykryć tekst w wielu językach. Interfejsy API odczytu i OCR nie wymagają określenia kodu języka. Aby uzyskać więcej informacji na temat tej funkcji i zalet poszczególnych interfejsów API, zobacz artykuł [odczytywanie tekstu z obrazów i dokumentów](concept-recognizing-text.md) .

|Język| Kod języka | INTERFEJS API OCR | Odczytaj interfejs API |
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

|Język | Kod języka | Kategorie | Tagi | Opis | Dorosły | Marki | Kolor | Twarze | ImageType | Obiekty | Celebryci | Charakterystycznych obiektów |
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
