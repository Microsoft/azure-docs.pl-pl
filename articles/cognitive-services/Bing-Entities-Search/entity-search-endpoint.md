---
title: Punkt końcowy interfejs API wyszukiwania jednostek Bing
titleSuffix: Azure Cognitive Services
description: Interfejs API wyszukiwania jednostek Bing ma jeden punkt końcowy, który zwraca jednostki z sieci Web na podstawie zapytania. Te wyniki wyszukiwania są zwracane w formacie JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: e7f5af42d0bb0079746cc9e64b621adfebd565d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74072667"
---
# <a name="bing-entity-search-api-endpoint"></a>interfejs API wyszukiwania jednostek Bing punkt końcowy


Interfejs API wyszukiwania jednostek Bing ma jeden punkt końcowy, który zwraca jednostki z sieci Web na podstawie zapytania. Te wyniki wyszukiwania są zwracane w formacie JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Pobierz wyniki jednostki z punktu końcowego

Aby uzyskać wyniki jednostki przy użyciu **interfejsu API Bing**, Wyślij `GET` żądanie do poniższego punktu końcowego. Użyj [nagłówków](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) i [parametrów zapytania](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) , aby dostosować żądanie wyszukiwania. Żądania wyszukiwania można wysyłać przy użyciu `?q=` parametru.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Czym jest interfejs API wyszukiwania jednostek Bing?](overview.md)

## <a name="see-also"></a>Zobacz też 

Aby uzyskać więcej informacji na temat nagłówków, parametrów, kodów rynkowych, obiektów odpowiedzi, błędów i innych, zobacz artykuł dotyczący [interfejs API wyszukiwania jednostek Bing wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) .
