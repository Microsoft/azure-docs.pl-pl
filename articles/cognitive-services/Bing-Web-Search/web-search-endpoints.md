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
ms.openlocfilehash: c882e3e4d0cd6ba594a700f4fd53c14103a8d1d1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94381256"
---
# <a name="web-search-endpoint"></a>wyszukiwanie w sieci Web punkt końcowy

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

**Interfejs API wyszukiwanie w sieci Web** zwraca strony sieci Web, wiadomości, obrazy, wideo i [jednostki](../bing-entities-search/overview.md). Jednostki zawierają informacje podsumowujące dotyczące osoby, miejsca lub tematu.

## <a name="endpoint"></a>Punkt końcowy

Aby uzyskać wyniki wyszukiwania w sieci Web przy użyciu interfejsu API Bing, Wyślij `GET` żądanie do poniższego punktu końcowego. Nagłówki i parametry URL definiują dalsze specyfikacje.

**Punkt końcowy**: zwraca wyniki sieci Web, które są istotne dla zapytania wyszukiwania użytkownika zdefiniowanego przez `?q=""` .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Punkt końcowy: Aby uzyskać szczegółowe informacje o nagłówkach, parametrach, kodach rynku, obiektach odpowiedzi, błędach i innych, zobacz Dokumentacja [interfejsu API sieci Web Bing wersji 7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) .

## <a name="response-json"></a>KOD JSON odpowiedzi

Odpowiedź na żądanie wyszukiwania w sieci Web obejmuje wszystkie wyniki jako obiekty JSON. Analizowanie wyniku wymaga procedur, które obsługują elementy każdego typu. Przykłady można znaleźć w [samouczku](./tutorial-bing-web-search-single-page-app.md) i [kodzie źródłowym](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) .

## <a name="next-steps"></a>Następne kroki

Interfejsy API **Bing** obsługują akcje wyszukiwania, które zwracają wyniki zgodnie z ich typem. Wszystkie punkty końcowe wyszukiwania zwracają wyniki jako obiekty odpowiedzi JSON.  Wszystkie punkty końcowe obsługują zapytania, które zwracają określony język i lokalizację według długości geograficznej, szerokości geograficznej i usługi wyszukiwania.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez poszczególne punkty końcowe, zobacz strony referencyjne dla każdego typu.
Przykłady podstawowych żądań za pomocą interfejsu API wyszukiwania w sieci Web można znaleźć [w temacie wyszukiwanie w sieci Web — szybki start](./overview.md).