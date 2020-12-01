---
title: Punkt końcowy wyszukiwania niestandardowego Bing
titleSuffix: Azure Cognitive Services
description: Twórz dostosowane środowiska wyszukiwania dla interesujących Cię tematów. Użytkownicy widzą wyniki wyszukiwania dopasowane do zawartości, o której się interesują.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 35dd147574eb7820debba55b105deed001be80fb
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338490"
---
# <a name="custom-search"></a>Wyszukiwanie niestandardowe

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
Wyszukiwanie niestandardowe Bing umożliwia tworzenie dostosowanych funkcji wyszukiwania dla interesujących Cię tematów. Użytkownicy zobaczą wyniki wyszukiwania dostosowane do zawartości, która ich interesuje, zamiast przewijać kolejne strony ogólnych wyników wyszukiwania zawierające nieistotną zawartość.

## <a name="custom-search-endpoint"></a>Niestandardowy punkt końcowy wyszukiwania
Aby uzyskać wyniki przy użyciu interfejs API wyszukiwania niestandardowego Bing, Wyślij `GET` żądanie do poniższego punktu końcowego. Użyj nagłówków i parametrów adresu URL, aby zdefiniować dalsze specyfikacje.

Punkt końcowy: zwraca sugestie wyszukiwania jako wyniki JSON, które są istotne dla danych wejściowych użytkownika zdefiniowanych przez `?q=""` .
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Przykłady opisujące sposób konfigurowania niestandardowych źródeł wyszukiwania można znaleźć w [samouczku](./tutorials/custom-search-web-page.md). Aby uzyskać szczegółowe informacje o nagłówkach, parametrach, kodach rynku, obiektach odpowiedzi, błędach itp., zobacz [interfejs API wyszukiwania niestandardowego Bing odwołanie wersji 7](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference) .

## <a name="custom-search-response-json"></a>Niestandardowy kod JSON odpowiedzi wyszukiwania
Niestandardowe żądanie wyszukiwania zwraca wyniki jako obiekty JSON, zobacz [obiekty odpowiedzi](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Niestandardowe automatyczne sugerowanie
Niestandardowy interfejs API automatycznego sugerowania umożliwia wysyłanie częściowego terminu zapytania wyszukiwania do usługi Bing i powrót do listy sugerowanych zapytań, które można skonfigurować. Przy użyciu niestandardowego automatycznego sugerowania możesz dodać sugestie zwrócone przez interfejs API i opcjonalnie określić, czy mają być dołączane sugestie generowane przez usługę Bing.

## <a name="custom-autosuggest-endpoint"></a>Niestandardowy punkt końcowy automatycznego sugerowania
Aby zażądać niestandardowych sugestii dotyczących zapytań, Wyślij żądanie GET do:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Aby uzyskać informacje na temat definiowania sugestii niestandardowych, zobacz [Definiowanie niestandardowych sugestii wyszukiwania](define-custom-suggestions.md).

## <a name="custom-image-search"></a>wyszukiwanie obrazów niestandardowe
Niestandardowy interfejs API wyszukiwanie obrazów umożliwia wysłanie zapytania wyszukiwania do usługi Bing i odwracanie listy odpowiednich obrazów z wystąpienia wyszukiwania niestandardowego.

## <a name="custom-image-search-endpoint"></a>Niestandardowy punkt końcowy wyszukiwanie obrazów
Aby zażądać obrazów z niestandardowego wystąpienia wyszukiwania, Wyślij żądanie GET do następującego adresu URL:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Aby uzyskać informacje na temat konfigurowania niestandardowego wystąpienia wyszukiwania, zobacz [Konfigurowanie niestandardowego środowiska wyszukiwania](./define-your-custom-view.md).

## <a name="next-steps"></a>Następne kroki
Interfejsy API **Bing** obsługują akcje wyszukiwania, które zwracają wyniki zgodnie z ich typem. Wszystkie punkty końcowe wyszukiwania zwracają wyniki jako obiekty odpowiedzi JSON.  Wszystkie punkty końcowe obsługują zapytania, które zwracają określony język i/lub lokalizację według długości geograficznej, szerokości geograficznej i usługi wyszukiwania.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez poszczególne punkty końcowe, zobacz strony referencyjne dla każdego typu.
Przykłady podstawowych żądań przy użyciu interfejs API wyszukiwania niestandardowego można znaleźć w temacie [wyszukiwanie niestandardowe — szybki start](/azure/cognitive-services/bing-custom-search/)