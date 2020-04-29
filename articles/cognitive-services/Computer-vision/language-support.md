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
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220139"
---
# <a name="language-support-for-computer-vision"></a>Obsługa języka dla przetwarzanie obrazów

Niektóre funkcje przetwarzanie obrazów obsługują wiele języków; wszystkie funkcje, które nie są wymienione w tym miejscu, obsługują tylko język angielski.

## <a name="text-recognition"></a>Rozpoznawanie tekstu

Przetwarzanie obrazów może rozpoznawać tekst w wielu językach. Interfejs API [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) obsługuje wiele języków, podczas gdy interfejs API [odczytu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) i interfejs API [rozpoznawanie tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) obsługują tylko język angielski. Aby uzyskać więcej informacji na temat tej funkcji oraz zalety poszczególnych interfejsów API, zobacz [rozpoznawanie wydrukowanych i pisanych tekstu](concept-recognizing-text.md) .

OCR automatycznie wykrywa język materiału wejściowego, dlatego nie trzeba określać kodu języka w wywołaniu interfejsu API. Jednak kody języków są zawsze zwracane jako wartość `"language"` węzła w odpowiedzi JSON.

|Język| Kod języka | INTERFEJS API OCR |
|:-----|:----:|:-----:|
|Arabski | `ar`|✔ |
|Chiński uproszczony | `zh-Hans`|✔ |
|Chiński (tradycyjny) | `zh-Hant`|✔ |
|Czeski | `cs` |✔ |
|duński | `da` |✔ |
|Niderlandzki | `nl` |✔ |
|Angielski | `en` |✔ |
|fiński | `fi` |✔ |
|Francuski | `fr` |✔ |
|niemiecki | `de` |✔ |
|grecki | `el` |✔ |
|węgierski | `hu` |✔ |
|Włoski | `it` |✔ |
|japoński | `ja` |✔ |
|koreański | `ko` |✔ |
|Norweski | `nb` |✔ |
|Polski | `pl` |✔ |
|Portugalski | `pt` |✔ |
|Rumuński | `ro` |✔ |
|Rosyjski | `ru` |✔ |
|Serbski (cyrylica) | `sr-Cyrl` |✔ |
|Serbski (łaciński) | `sr-Latn` |✔ |
|Słowacki | `sk` |✔ |
|Hiszpański | `es` |✔ |
|szwedzki | `sw` |✔ |
|Turecki | `tr` |✔ |

## <a name="image-analysis"></a>Analiza obrazu

Niektóre akcje interfejsu API [analizy obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) mogą zwracać wyniki w innych językach, określone za pomocą parametru `language` zapytania. Inne akcje zwracają wyniki w języku angielskim niezależnie od tego, jaki język jest określony, a inne zgłaszają wyjątek dla nieobsługiwanych języków. Akcje są określone za pomocą `visualFeatures` parametrów `details` i. Zapoznaj się z [omówieniem](home.md) , aby zapoznać się z listą wszystkich akcji, które można wykonać za pomocą analizy obrazu.

|Język | Kod języka | Kategorie | Tagi | Opis | Dorosły | Marki | Kolor | Twarze | ImageType | Obiekty | Celebryci | Charakterystycznych obiektów |
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