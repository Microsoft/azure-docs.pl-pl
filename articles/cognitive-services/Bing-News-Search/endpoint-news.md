---
title: Punkty końcowe funkcji wyszukiwania wiadomości Bing
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera podsumowanie punktów końcowych interfejsu API wyszukiwania wiadomości. wiadomości, najpopularniejsze wiadomości i trendy.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 9c095a46b3a0526b23645c9cbb5e99eb8eda9067
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94366404"
---
# <a name="bing-news-search-api-endpoints"></a>interfejs API wyszukiwania wiadomości Bing punkty końcowe

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

**Interfejs API wyszukiwanie wiadomości** zwraca artykuły z wiadomościami, strony sieci Web, obrazy, filmy wideo i [jednostki](../bing-entities-search/overview.md). Jednostki zawierają informacje podsumowujące dotyczące osoby, miejsca lub tematu.

## <a name="endpoints"></a>Punkty końcowe

Aby uzyskać wyniki wyszukiwania wiadomości za pomocą interfejs API wyszukiwania wiadomości Bing, Wyślij `GET` żądanie do jednego z następujących punktów końcowych. Nagłówki i parametry URL definiują dalsze specyfikacje.

### <a name="news-items-by-search-query"></a>Elementy wiadomości według zapytania wyszukiwania

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Zwraca elementy wiadomości na podstawie zapytania wyszukiwania. Jeśli zapytanie wyszukiwania jest puste, interfejs API zwróci najpopularniejsze artykuły z różnych kategorii. Wyślij zapytanie według adresu URL, aby zakodować termin wyszukiwania i dołączyć je do `q=""` parametru. Aby uzyskać dostęp, zobacz temat [Obsługiwane kraje/regiony i rynki](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Najważniejsze elementy wiadomości według kategorii

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Zwraca najważniejsze elementy wiadomości według kategorii. Możesz zażądać najważniejszych artykułów firmy, sportu lub rozrywki, korzystając z `category=business` , `category=sports` lub `category=entertainment` .  `category`Parametru można używać tylko z `/news` adresem URL. Istnieją pewne formalne wymagania dotyczące określania kategorii; Zapoznaj się z `category` dokumentacją dotyczącą [parametrów zapytania](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters) . Wyślij zapytanie według adresu URL, aby zakodować termin wyszukiwania i dołączyć je do `q=""` parametru. Aby uzyskać dostęp, zobacz temat [Obsługiwane kraje/regiony i rynki](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Informacje o trendach tematów 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Zwraca tematy dotyczące nowości w sieciach społecznościowych. Gdy ta `/trendingtopics` opcja jest uwzględniona, wyszukiwanie w usłudze Bing ignoruje kilka innych parametrów, takich jak `freshness` i `?q=""` . Aby uzyskać dostęp, zobacz temat [Obsługiwane kraje/regiony i rynki](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje o nagłówkach, parametrach, kodach rynku, obiektach odpowiedzi, błędach itp., zobacz informacje o [interfejsie API wyszukiwania wiadomości Bing wersji 7](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) .

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez poszczególne punkty końcowe, zobacz strony referencyjne dla każdego typu.
Przykłady podstawowych żądań za pomocą interfejsu API wyszukiwania wiadomości można znaleźć w temacie [Wyszukiwanie wiadomości Bing Quick-Start](/azure/cognitive-services/bing-news-search).