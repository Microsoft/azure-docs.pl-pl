---
title: Sztuczna inteligencja
description: LUIS korzysta z sztucznej analizy (AI) w celu zapewnienia interpretacji języka dla danych w oparciu o zdefiniowany schemat.
ms.topic: conceptual
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 39f73dd002091451ae832516d525499eae98564d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "95021375"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Sztuczna inteligencja w Language Understanding (LUIS)

LUIS używa sztucznej analizy (AI) do zapewniania informacji o języku naturalnym (NLU) w oparciu o zdefiniowany schemat.

## <a name="natural-language-processing-nlp"></a>Przetwarzanie języka naturalnego (NLP)

Naturalne Language Understanding (NLU) to konkretny podtemat przetwarzania języka naturalnego (NLP).

Przetwarzanie języka naturalnego to szersze koncepcje, które obsługują jakąkolwiek formę przetwarzania danych tekstowych, obejmują m.in.:

* Tokenizacji
* Część tagowania mowy (POS)
* Segmentacja
* Analiza morfologiczna
* Podobieństwo semantyczne
* Rozmowa
* Tłumaczenie

## <a name="natural-language-processing-in-luis"></a>Przetwarzanie języka naturalnego w LUIS

Przetwarzanie języka naturalnego jest dostępne dla aplikacji LUIS w następujący sposób:
* [Interpretacja języka naturalnego](#natural-language-processing-nlp) (Luis)
* Konfigurowalne aspekty NLP w LUIS:
    * [Tokenizacji](luis-language-support.md#tokenization)
    * Morfologia za poorednictwem znaków diakrytycznych, interpunkcji i [ustawień interfejsu API](luis-reference-application-settings.md) formularzy programu Word
* Wstępne lub końcowe przetwarzanie zapytania wypowiedź dostarczone przez inne [Cognitive Services](../what-are-cognitive-services.md) takie jak:
    * [Tłumaczenie](../translator/translator-info-overview.md)

## <a name="natural-language-understanding-nlu"></a>Interpretacja języka naturalnego (NLU)

NLU to możliwość _przekształcenia_ języka w reprezentację, która umożliwia w naturalny sposób zrozumienie Twoich użytkowników. Interpretacja języka naturalnego pozostaje bardzo trudne i jest definiowana jako problem z _systemem AI_ .

LUIS jest przeznaczony do skoncentrowania się na zamiarach i wyodrębnianiu, co obejmuje możliwość zidentyfikowania:
* Jak użytkownik chce
* Co mówią.

LUIS ma niewielki lub nie znają szerszego aspektów _NLP_ , takich jak podobieństwo semantyczne, bez jawnej identyfikacji w przykładach. Na przykład następujące tokeny (słowa) są trzy różne rzeczy, dopóki nie są używane w podobnych kontekstach w podanych przykładach:

* kupna
* Kupuj
* skupiony

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [cyklu rozwoju](luis-concept-app-iteration.md) aplikacji Luis