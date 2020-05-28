---
title: Zapobiegaj translacji zawartości — translator
titleSuffix: Azure Cognitive Services
description: Zapobiegaj translacji zawartości za pomocą usługi Translator. Translator umożliwia oznaczanie zawartości w taki sposób, aby nie został przetłumaczony.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 37d2e4c0131569ab50ebf49ff73b6adf7a420713
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996180"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Jak zapobiec translacji zawartości z tłumaczeniem

Translator umożliwia oznaczanie zawartości w taki sposób, aby nie został przetłumaczony. Na przykład możesz chcieć zakodować kod, nazwę marki lub wyraz/frazę, która nie ma sensu, gdy jest zlokalizowany.

## <a name="methods-for-preventing-translation"></a>Metody uniemożliwiające tłumaczenie

1. Oznacz swoją zawartość za pomocą `notranslate` . Jest to projekt, który działa tylko wtedy, gdy typ danych wejściowych jest ustawiony jako HTML

   Przykład:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Użyj [słownika dynamicznego](dynamic-dictionary.md) , aby określić tłumaczenie.

3. Nie przekazuj ciągu do translatora w celu tłumaczenia.

4. Translator niestandardowy: Użyj [słownika w translatoru niestandardowym](custom-translator/what-is-dictionary.md) , aby określić tłumaczenie frazy z prawdopodobieństwem 100%.


## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Unikaj tłumaczenia w wywołaniu translatora](reference/v3-0-translate.md)
