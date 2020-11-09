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
ms.openlocfilehash: 5bd5ff0e49b125277255ec8e5c216583d75043a0
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381137"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Automatyczne sugerowanie terminów wyszukiwania Bing w aplikacji

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Jeśli udostępniasz pole wyszukiwania, w którym użytkownik wprowadza wyszukiwany termin, użyj [interfejsu API automatycznego sugerowania Bing](../bing-autosuggest/get-suggested-search-terms.md) w celu ulepszenia jego działania. Interfejs API zwraca sugerowane ciągi zapytań na podstawie częściowych wyszukiwanych terminów w miarę wpisywania ich przez użytkownika.

Gdy użytkownik wprowadzi termin wyszukiwania, musi być zakodowany w adresie URL przed ustawieniem parametru zapytania [q](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) . Na przykład jeśli użytkownik wprowadzi frazę *sailing dinghies* , ustaw parametr `q` na wartość `sailing+dinghies` lub `sailing%20dinghies`.

Jeśli termin zapytania zawiera błąd pisowni, odpowiedź wyszukiwania zawiera obiekt [kontekst zapytania](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) . Obiekt ten zawiera pierwotną pisownię oraz poprawioną pisownię użytą przez usługę Bing podczas wyszukiwania.

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

* [Informacje interfejs API wyszukiwania w sieci Web Bing](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)