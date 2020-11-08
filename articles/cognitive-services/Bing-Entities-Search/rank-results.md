---
title: Używanie klasyfikacji do wyświetlania odpowiedzi — wyszukiwanie jednostek Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać funkcji rankingu do wyświetlania odpowiedzi zwracanych przez interfejs API wyszukiwania jednostek Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: b63c2e53f34ac86f8ddf5ad300c2465ee2d9f032
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365639"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Używanie klasyfikacji do wyświetlania wyników wyszukiwania jednostek  

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Każda odpowiedź wyszukiwania jednostki zawiera odpowiedź [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) , która określa, jak należy wyświetlić wyniki wyszukiwania zwrócone przez interfejs API wyszukiwania jednostek Bing. Wyniki klasyfikacji są uwzględniane w grupach, linii głównej i na pasku bocznym. Wynik tego punktu jest najważniejszym lub widocznym wynikiem i powinien być wyświetlany jako pierwszy. Jeśli pozostałe wyniki nie są wyświetlane w tradycyjnym formacie linii głównej i paska bocznego, należy udostępnić zawartość linii głównej wyższą widoczność niż zawartość paska bocznego. 
  
W każdej grupie tablica [Items](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items) identyfikuje kolejność, w której zawartość musi być wyświetlana. Każdy element zapewnia dwa sposoby identyfikacji wyniku w odpowiedzi.  
 

|Pole | Opis  |
|---------|---------|
|`answerType` i `resultIndex` | `answerType` Identyfikuje odpowiedź (jednostkę lub miejsce) i `resultIndex` identyfikuje wynik w odpowiedzi (na przykład jednostki). Indeks zaczyna się od 0.|
|`value`    | `value` Zawiera identyfikator, który odpowiada IDENTYFIKATORowi odpowiedzi lub wynikowi w odpowiedzi. Odpowiedź lub wyniki zawierają identyfikator, ale nie oba jednocześnie. |
  
Korzystanie z `answerType` i `resultIndex` jest procesem dwuetapowym. Najpierw użyj, `answerType` Aby zidentyfikować odpowiedź, która zawiera wyniki do wyświetlenia. Następnie użyj `resultIndex` do indeksowania wyników odpowiedzi, aby uzyskać wynik do wyświetlenia. ( `answerType` Wartość jest nazwą pola w obiekcie [SearchResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) ). Jeśli chcesz wyświetlić wszystkie wyniki odpowiedzi razem, element odpowiedzi rankingu nie zawiera `resultIndex` pola.

Użycie identyfikatora wymaga dopasowania identyfikatora klasyfikacji z IDENTYFIKATORem odpowiedzi lub jednym z jej wyników. Jeśli obiekt odpowiedzi zawiera `id` pole, Wyświetl wszystkie wyniki odpowiedzi ze sobą. Na przykład jeśli `Entities` obiekt zawiera `id` pole, Wyświetl wszystkie artykuły dotyczące jednostek razem. Jeśli `Entities` obiekt nie zawiera `id` pola, każda jednostka zawiera `id` pole, a odpowiedź rankingu miesza jednostki z wynikami.  
  
## <a name="ranking-response-example"></a>Przykład klasyfikacji odpowiedzi

Poniżej przedstawiono przykład [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

W oparciu o tę odpowiedź klasyfikacji pasek boczny będzie wyświetlał dwa wyniki jednostki związane z Jimi Hendrix.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](tutorial-bing-entities-search-single-page-app.md)