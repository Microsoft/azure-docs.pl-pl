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
ms.date: 04/06/2021
ms.openlocfilehash: 7524644b34a6fd479c08b9ce6418c547c836add5
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554029"
---
# <a name="sentiment-analysis"></a>Analiza tonacji
W przypadku skonfigurowania analizy tonacji odpowiedź JSON LUIS obejmuje analizę tonacji. Dowiedz się więcej o analizie tonacji w dokumentacji [Analiza tekstu](../text-analytics/index.yml) .

LUIS używa analiza tekstu v2. 

Analiza tonacji jest konfigurowany podczas publikowania aplikacji. Aby uzyskać więcej informacji, zobacz [temat jak opublikować aplikację](./luis-how-to-publish-app.md) .

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
