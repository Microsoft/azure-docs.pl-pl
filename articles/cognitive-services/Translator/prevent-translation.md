---
title: Zapobiegaj translacji zawartości — interfejs API tłumaczenia tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Zapobiegaj translacji zawartości przy użyciu interfejs API tłumaczenia tekstu w usłudze Translator. Interfejs API tłumaczenia tekstu w usłudze Translator pozwala oznaczyć zawartość tak, aby nie została przetłumaczona.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: c7be4a0ea1a9d24a8b262132632a0bbb63ae1b96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80052483"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Jak zapobiec translacji zawartości przy użyciu interfejs API tłumaczenia tekstu w usłudze Translator

Interfejs API tłumaczenia tekstu w usłudze Translator pozwala oznaczyć zawartość tak, aby nie została przetłumaczona. Na przykład możesz chcieć zakodować kod, nazwę marki lub wyraz/frazę, która nie ma sensu, gdy jest zlokalizowany.

## <a name="methods-for-preventing-translation"></a>Metody uniemożliwiające tłumaczenie

1. Oznacz swoją zawartość za `notranslate`pomocą. Jest to projekt, który działa tylko wtedy, gdy typ danych wejściowych jest ustawiony jako HTML

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

3. Nie przekazuj ciągu do interfejs API tłumaczenia tekstu w usłudze Translator na potrzeby tłumaczenia.

4. Translator niestandardowy: Użyj [słownika w translatoru niestandardowym](custom-translator/what-is-dictionary.md) , aby określić tłumaczenie frazy z prawdopodobieństwem 100%.


## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Unikaj tłumaczenia w wywołaniu interfejsu API usługi translator](reference/v3-0-translate.md)
