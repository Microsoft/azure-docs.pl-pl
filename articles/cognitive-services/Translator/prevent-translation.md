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
ms.openlocfilehash: ec39b3692a90f22409e85b5502d3ea874e3282d6
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91742064"
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

3. Use the [dynamic dictionary](dynamic-dictionary.md) to prescribe a specific translation.

4. Don't pass the string to the Translator for translation.

5. Custom Translator: Use a [dictionary in Custom Translator](custom-translator/what-is-dictionary.md) to prescribe the translation of a phrase with 100% probability.


## Next steps
> [!div class="nextstepaction"]
> [Use the Translate operation to translate text](reference/v3-0-translate.md)
