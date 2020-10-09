---
title: Automatyczne sugerowanie terminów wyszukiwania — interfejs API wyszukiwania w sieci Web Bing
titleSuffix: Azure Cognitive Services
description: Parowanie interfejs API wyszukiwania w sieci Web Bing z interfejs API automatycznego sugerowania Bing, aby zapewnić użytkownikom udoskonalone środowisko wyszukiwania.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 0fb62966c78eb19c1daf9294efba786a267ae200
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "66384859"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Automatyczne sugerowanie terminów wyszukiwania Bing w aplikacji

Jeśli udostępniasz pole wyszukiwania, w którym użytkownik wprowadza wyszukiwany termin, użyj [interfejsu API automatycznego sugerowania Bing](../bing-autosuggest/get-suggested-search-terms.md) w celu ulepszenia jego działania. Interfejs API zwraca sugerowane ciągi zapytań na podstawie częściowych wyszukiwanych terminów w miarę wpisywania ich przez użytkownika.

Gdy użytkownik wprowadzi termin wyszukiwania, musi być zakodowany w adresie URL przed ustawieniem parametru zapytania [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) . Na przykład jeśli użytkownik wprowadzi frazę *sailing dinghies*, ustaw parametr `q` na wartość `sailing+dinghies` lub `sailing%20dinghies`.

Jeśli termin zapytania zawiera błąd pisowni, odpowiedź wyszukiwania zawiera obiekt [kontekst zapytania](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) . Obiekt ten zawiera pierwotną pisownię oraz poprawioną pisownię użytą przez usługę Bing podczas wyszukiwania.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Tych informacji można użyć do poinformowania użytkownika o modyfikacji ciągu zapytania podczas wyświetlania wyników wyszukiwania.

![Przykład środowiska użytkownika kontekstu zapytania](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Następne kroki  

* Przejrzyj przykładowe [odpowiedzi interfejs API wyszukiwania w sieci Web Bing](search-responses.md).  

## <a name="see-also"></a>Zobacz też  

* [Informacje interfejs API wyszukiwania w sieci Web Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
