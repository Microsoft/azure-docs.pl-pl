---
title: Korzystanie z interfejsu API sprawdzania pisowni Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej na temat trybów sprawdzanie pisowni Bing, ustawień i innych informacji związanych z interfejsem API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: a02d5217cb051516e11d17730f31869618a2cfb0
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369396"
---
# <a name="using-the-bing-spell-check-api"></a>Korzystanie z interfejsu API sprawdzania pisowni Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

W tym artykule dowiesz się, jak korzystać z interfejsu API sprawdzania pisowni Bing w celu przeprowadzania kontekstowego sprawdzania pisowni i gramatyki. Podczas gdy większość narzędzi do sprawdzania pisowni polega na zestawach reguł utworzonych na podstawie słowników, narzędzie do sprawdzania pisowni Bing zapewnia dokładne i kontekstowe poprawki przy użyciu uczenia maszynowego i statystycznego tłumaczenia maszynowego. 

## <a name="spell-check-modes"></a>Tryby sprawdzania pisowni

Interfejs API obsługuje dwa tryby sprawdzania: `Proof` i `Spell`.  Wypróbuj przykłady [tutaj](https://azure.microsoft.com/services/cognitive-services/spell-check/).

### <a name="proof---for-documents"></a>Proof — dla dokumentów 

`Proof` to tryb domyślny. Tryb sprawdzania pisowni `Proof` zapewnia najbardziej kompleksowe testy, dodaje wielkie litery i podstawowe znaki interpunkcyjne oraz oferuje inne funkcje pomocne w tworzeniu dokumentu. Jest jednak dostępny tylko w wersji en-US (angielski — Stany Zjednoczone), es-ES (hiszpański) i pt-BR (portugalski). Uwaga: w przypadku języka hiszpańskiego i portugalskiego dostępna jest tylko wersja beta. W przypadku wszystkich pozostałych rynków należy ustawić wartość Spell w parametrze mode w zapytaniu. 

> [!NOTE]
> jeśli długość tekstu zapytania przekracza 4096 znaków, tekst zostanie obcięty do 4096 znaków przed przetworzeniem zapytania. 

### <a name="spell----for-web-searchesqueries"></a>Spell — dla wyszukiwań w Internecie lub zapytań

Tryb `Spell` jest bardziej agresywny i umożliwia zwrócenie lepszych wyników wyszukiwania. Tryb `Spell` odnajduje większość błędów pisowni, ale nie odnajduje części błędów gramatycznych, które wyłapuje tryb `Proof`, na przykład błędów użycia wielkiej litery i powtarzających się słów.

> [!NOTE]
> * Maksymalną obsługiwaną długość zapytania podano poniżej. Jeśli zapytanie przekracza maksymalną długość, zapytanie i jego wyniki nie zostaną zmienione.
>    * 130 znaków dla następujących kodów języka: en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, it, zh i ko. 
>    * 65 znaków dla wszystkich innych.
> * Tryb sprawdzania pisowni nie obsługuje nawiasów kwadratowych (`[` i `]`) w zapytaniach i może spowodować niespójne wyniki. W przypadku używania trybu pisowni zalecamy usunięcie ich z zapytań.

## <a name="market-setting"></a>Ustawienie rynku

[Kod rynku](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#market-codes) powinien być określony za pomocą parametru zapytania `mkt` w żądaniu. W przeciwnym razie interfejs API użyje domyślnego rynku, bazując na adresie IP żądania.


## <a name="http-post-and-get-support"></a>Obsługa żądań HTTP POST i GET

Interfejs API obsługuje żądania HTTP POST i HTTP GET. To, którego użyjesz, zależy od długości tekstu, który chcesz sprawdzić. Jeśli ciągi zawsze mają mniej niż 1500 znaków, należy użyć żądania HTTP GET. Jeśli chcesz, aby obsługiwane były ciągi dłuższe niż 10 000 znaków, należy użyć żądania HTTP POST. Ciąg tekstu może zawierać wszelkie prawidłowe znaki UTF-8.

W poniższym przykładzie przedstawiono żądanie POST sprawdzające pisownię i gramatykę w ciągu tekstowym. Przykład zawiera parametr zapytania [mode](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#mode), aby pokazać pełne zapytanie (mógł zostać pominięty, ponieważ domyślna wartość parametru `mode` to Proof). Parametr zapytania [text](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) zawiera ciąg, który ma zostać sprawdzony.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

W przypadku użycia żądania HTTP GET należy uwzględnić parametr zapytania `text` w ciągu zapytania adresu URL.
  
Poniżej przedstawiono odpowiedź na poprzednie żądanie. Odpowiedź zawiera obiekt [SpellCheck](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#spellcheck). 
  
```json
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
W polu [FlaggedTokens](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#flaggedtokens) wyświetlana jest lista błędów pisowni i gramatyki, które interfejs API odnalazł w ciągu [text](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text). Pole `token` zawiera wyraz, który ma zostać zastąpiony. Należy użyć przesunięcia zaczynającego się od zera w polu `offset`, aby znaleźć token w ciągu `text`. Następnie należy zastąpić słowo w tej lokalizacji słowem podanym w polu `suggestion`. 

Jeśli pole `type` zawiera wartość RepeatedToken, i tak należy zastąpić token słowem z pola `suggestion`, ale prawdopodobnie należy także usunąć spację końcową.

## <a name="throttling-requests"></a>Ograniczanie żądań

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Następne kroki

- [Czym jest interfejs API sprawdzania pisowni Bing?](../overview.md)
- [Dokumentacja interfejsu API sprawdzania pisowni Bing v7](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)