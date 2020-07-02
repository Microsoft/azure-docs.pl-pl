---
title: Sztuczna inteligencja
description: LUIS używa sztucznej analizy w celu zapewnienia interpretacji języka dla danych w oparciu o zdefiniowany schemat.
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: 4f145585e097a3cf6a2338dbab879f6fce07f71d
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802659"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Sztuczna inteligencja w Language Understanding (LUIS)

LUIS korzysta ze sztucznej analizy, aby zapewnić firmie interpretacji języka naturalnego (NLU) dane na podstawie zdefiniowanego schematu.

## <a name="natural-language-processing"></a>Przetwarzanie języka naturalnego

Naturalne Language Understanding (NLU) to konkretny podtemat przetwarzania języka naturalnego (NLP).

Przetwarzanie języka naturalnego to szersze koncepcje, które obsługują jakąkolwiek formę przetwarzania danych tekstowych, obejmują m.in.:

* Tokenizacji
* Część tagowania mowy (POS)
* Segmentacja
* Analiza morfologiczna
* Podobieństwo semantyczne
* Trwa odprowadzenie
* Tłumaczenie

## <a name="natural-language-processing-in-luis"></a>Przetwarzanie języka naturalnego w LUIS

Przetwarzanie języka naturalnego jest dostępne dla aplikacji LUIS w następujący sposób:
* [Interpretacja języka naturalnego](#natural-language-understanding) (Luis)
* Konfigurowalne aspekty NLP w LUIS:
    * [Tokenizacji](luis-language-support.md#tokenization)
    * Morfologia za poorednictwem znaków diakrytycznych, interpunkcji i [ustawień interfejsu API](luis-reference-application-settings.md) formularzy programu Word
* Wstępne lub końcowe przetwarzanie zapytania wypowiedź dostarczone przez inne [Cognitive Services](../Welcome.md) takie jak:
    * [Tłumaczenie](../translator/translator-info-overview.md)

## <a name="natural-language-understanding"></a>Interpretacja języka naturalnego

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