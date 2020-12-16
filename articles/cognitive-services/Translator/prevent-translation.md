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
ms.openlocfilehash: bf8923c1090669caa46ef51a26418933b1cda023
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563436"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Jak zapobiec translacji zawartości z tłumaczeniem

Translator umożliwia oznaczanie zawartości w taki sposób, aby nie został przetłumaczony. Na przykład można oznaczyć kod, nazwę marki lub wyraz/frazę, która po zlokalizowaniu nie ma sensu.

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

2. Oznacz swoją zawartość za pomocą `translate="no"` . Działa to tylko wtedy, gdy wejściowy typ texttype jest ustawiony jako HTML

   Przykład:

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>
   ```
   
3. Użyj [słownika dynamicznego](dynamic-dictionary.md) , aby określić tłumaczenie.

4. Nie przekazuj ciągu do translatora w celu tłumaczenia.

5. Translator niestandardowy: Użyj [słownika w translatoru niestandardowym](custom-translator/what-is-dictionary.md) , aby określić tłumaczenie frazy z prawdopodobieństwem 100%.


## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Używanie operacji tłumaczenia do tłumaczenia tekstu](reference/v3-0-translate.md)
