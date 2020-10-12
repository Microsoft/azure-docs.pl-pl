---
title: Punkt końcowy wyszukiwania w sieci Web
titleSuffix: Azure Cognitive Services
description: Aby uzyskać wyniki wyszukiwania w sieci Web, Wyślij `GET` żądanie do poniższego punktu końcowego. Nagłówki i parametry URL definiują dalsze specyfikacje.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: 2edc6a4cf4d4fb174c1b2067d8b3fb3b6f31859f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74111337"
---
# <a name="web-search-endpoint"></a>wyszukiwanie w sieci Web punkt końcowy

**Interfejs API wyszukiwanie w sieci Web** zwraca strony sieci Web, wiadomości, obrazy, wideo i [jednostki](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Jednostki zawierają informacje podsumowujące dotyczące osoby, miejsca lub tematu.

## <a name="endpoint"></a>Punkt końcowy

Aby uzyskać wyniki wyszukiwania w sieci Web przy użyciu interfejsu API Bing, Wyślij `GET` żądanie do poniższego punktu końcowego. Nagłówki i parametry URL definiują dalsze specyfikacje.

**Punkt końcowy**: zwraca wyniki sieci Web, które są istotne dla zapytania wyszukiwania użytkownika zdefiniowanego przez `?q=""` .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Punkt końcowy: Aby uzyskać szczegółowe informacje o nagłówkach, parametrach, kodach rynku, obiektach odpowiedzi, błędach i innych, zobacz Dokumentacja [interfejsu API sieci Web Bing wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) .

## <a name="response-json"></a>KOD JSON odpowiedzi

Odpowiedź na żądanie wyszukiwania w sieci Web obejmuje wszystkie wyniki jako obiekty JSON. Analizowanie wyniku wymaga procedur, które obsługują elementy każdego typu. Przykłady można znaleźć w [samouczku](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/tutorial-bing-web-search-single-page-app) i [kodzie źródłowym](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) .

## <a name="next-steps"></a>Następne kroki

Interfejsy API **Bing** obsługują akcje wyszukiwania, które zwracają wyniki zgodnie z ich typem.Wszystkie punkty końcowe wyszukiwania zwracają wyniki jako obiekty odpowiedzi JSON. Wszystkie punkty końcowe obsługują zapytania, które zwracają określony język i lokalizację według długości geograficznej, szerokości geograficznej i usługi wyszukiwania.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez poszczególne punkty końcowe, zobacz strony referencyjne dla każdego typu.
Przykłady podstawowych żądań za pomocą interfejsu API wyszukiwania w sieci Web można znaleźć [w temacie wyszukiwanie w sieci Web — szybki start](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/search-the-web).
