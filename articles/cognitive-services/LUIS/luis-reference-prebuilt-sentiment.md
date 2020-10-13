---
title: Analiza tonacji — LUIS
titleSuffix: Azure Cognitive Services
description: W przypadku skonfigurowania analizy tonacji odpowiedź JSON LUIS obejmuje analizę tonacji.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: 7c3c0f603b9e7d83e40f43ab00d31f4ca63f2b4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535376"
---
# <a name="sentiment-analysis"></a>Analiza tonacji
W przypadku skonfigurowania analizy tonacji odpowiedź JSON LUIS obejmuje analizę tonacji. Dowiedz się więcej o analizie tonacji w dokumentacji [Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) .

LUIS używa analiza tekstu v2. 

## <a name="resolution-for-sentiment"></a>Rozwiązanie dla tonacji

Dane tonacji to wynik z zakresu od 1 do 0 wskazujący dodatnie (bliżej 1) lub ujemne (bliżej 0) tonacji danych.

#### <a name="english-language"></a>[Język angielski](#tab/english)

Gdy kultura jest `en-us` , odpowiedź jest:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Inne języki](#tab/other-languages)

W przypadku wszystkich innych kultur odpowiedź jest:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

