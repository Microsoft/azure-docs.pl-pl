---
title: Wysyłanie żądań do interfejs API automatycznego sugerowania Bing
titleSuffix: Azure Cognitive Services
description: Interfejs API automatycznego sugerowania Bing zwraca listę proponowanych zapytań na podstawie częściowego ciągu zapytania w polu wyszukiwania. Dowiedz się więcej o wysyłaniu żądań.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: dd845c0fb877afa76b84eb5c2d86392f763eccf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96353395"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Wysyłanie żądań do interfejs API automatycznego sugerowania Bing.

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Jeśli aplikacja wysyła zapytania do dowolnego z interfejsów API wyszukiwania Bing, możesz użyć interfejsu API automatycznego sugerowania Bing w celu ulepszenia środowiska wyszukiwania dla użytkowników. Interfejs API automatycznego sugerowania Bing zwraca listę proponowanych zapytań na podstawie częściowego ciągu zapytania w polu wyszukiwania. Gdy znaki są wprowadzane do pola wyszukiwania w aplikacji, można wyświetlić sugestie na liście rozwijanej. Skorzystaj z tego artykułu, aby dowiedzieć się więcej o wysyłaniu żądań do tego interfejsu API. 

## <a name="bing-autosuggest-api-endpoint"></a>interfejs API automatycznego sugerowania Bing punkt końcowy

**Interfejs API automatycznego sugerowania Bing** zawiera jeden punkt końcowy, który zwraca listę sugerowanych zapytań z częściowego terminu wyszukiwania.

Aby uzyskać sugerowane zapytania przy użyciu interfejsu API Bing, Wyślij `GET` żądanie do poniższego punktu końcowego. Użyj nagłówków i parametrów adresu URL, aby zdefiniować dalsze specyfikacje.

**Punkt końcowy:** Zwraca sugestie wyszukiwania jako wyniki JSON, które są istotne dla danych wejściowych użytkownika zdefiniowanych przez `?q=""` .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Aby uzyskać szczegółowe informacje o nagłówkach, parametrach, kodach rynku, obiektach odpowiedzi, błędach itp., zobacz [interfejs API automatycznego sugerowania Bing odwołanie wersji 7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) .

Interfejsy API **Bing** obsługują akcje wyszukiwania, które zwracają wyniki zgodnie z ich typem. Wszystkie punkty końcowe wyszukiwania zwracają wyniki jako obiekty odpowiedzi JSON.
Wszystkie punkty końcowe obsługują zapytania, które zwracają określony język i/lub lokalizację według długości geograficznej, szerokości geograficznej i usługi wyszukiwania.

Aby uzyskać pełne informacje na temat parametrów obsługiwanych przez poszczególne punkty końcowe, zobacz strony referencyjne dla każdego typu.
Przykłady podstawowych żądań korzystających z interfejsu API automatycznego sugerowania można znaleźć w temacie automatyczne [sugerowanie szybki start](/azure/cognitive-services/Bing-Autosuggest).

## <a name="bing-autosuggest-api-requests"></a>Żądania interfejs API automatycznego sugerowania Bing

> [!NOTE]
> * Żądania do interfejs API automatycznego sugerowania Bing muszą używać protokołu HTTPS.

Zalecamy, aby wszystkie żądania pochodziły z serwera. Dystrybuowanie klucza jako części aplikacji klienckiej zapewnia większą szansę dostępu do złośliwej strony innej firmy. Ponadto wykonywanie wywołań z serwera zapewnia jeden punkt uaktualnienia dla przyszłych aktualizacji.

Żądanie musi określać parametr zapytania [q](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query), który zawiera częściowy termin wyszukiwany przez użytkownika. Chociaż jest to opcjonalne, żądanie powinno również określać parametr zapytania [mkt](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt), który identyfikuje rynek, z którego mają pochodzić wyniki. Aby uzyskać listę opcjonalnych parametrów zapytania, zobacz [Query Parameters (Parametry zapytania)](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Wszystkie wartości parametrów zapytania muszą być zakodowane w adresie URL.

Żądanie musi określać nagłówek [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey). Mimo że jest to opcjonalne, zachęcamy, aby określić również następujące nagłówki:

- [User-Agent](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Nagłówki adresu IP klienta i lokalizacji są ważne z punktu widzenia zwracania zawartości odpowiedniej dla danej lokalizacji.

Aby uzyskać listę wszystkich nagłówków żądań i odpowiedzi, zobacz [Nagłówki](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

> [!NOTE]
> Gdy wywołasz interfejs API automatycznego sugerowania Bing z JavaScript, wbudowane funkcje zabezpieczeń przeglądarki mogą uniemożliwiać dostęp do wartości tych nagłówków.

Aby rozwiązać ten problem, można wykonać żądanie interfejs API automatycznego sugerowania Bing za pomocą serwera proxy CORS. Odpowiedź z takiego serwera proxy ma `Access-Control-Expose-Headers` nagłówek, który filtruje nagłówki odpowiedzi i udostępnia je dla języka JavaScript.

Można łatwo zainstalować serwer proxy CORS, aby umożliwić naszej [aplikacji samouczka](../tutorials/autosuggest.md) dostęp do opcjonalnych nagłówków klienta. Najpierw [zainstaluj platformę Node.js](https://nodejs.org/en/download/), jeśli jeszcze jej nie masz. Potem wprowadź poniższe polecenie w wierszu polecenia.

```console
npm install -g cors-proxy-server
```

Następnie Zmień punkt końcowy interfejs API automatycznego sugerowania Bing w pliku HTML na:

```http
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
```

Na koniec uruchom serwer proxy CORS za pomocą następującego polecenia:

```console
cors-proxy-server
```

Podczas korzystania z aplikacji samouczka pozostaw okno polecenia otwarte, ponieważ jego zamknięcie spowoduje zatrzymanie serwera proxy. W rozwijanej sekcji nagłówków HTML poniżej wyników wyszukiwania można teraz zobaczyć nagłówek `X-MSEdge-ClientID` (pomiędzy innymi) i sprawdzić, czy jest on taki sam dla każdego żądania.

Żądania powinny obejmować wszystkie sugerowane parametry i nagłówki zapytań. 

W poniższym przykładzie przedstawiono żądanie, które zwraca sugerowane ciągi zapytania dla terminu *sail*.

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Jeśli jest to Twoje pierwsze wywoływanie dowolnego z interfejsów API Bing, nie dołączaj nagłówka identyfikatora klienta. Nagłówek identyfikatora klienta należy uwzględnić tylko wtedy, gdy interfejs API Bing został już wywołany i usługa Bing zwróciła identyfikator klienta dla kombinacji użytkownika i urządzenia.

Następująca grupa sugestii sieci Web jest odpowiedzią na powyższe żądanie. Grupa zawiera listę sugestii zapytania wyszukiwania, z każdą sugestią, w tym `displayText` `query` pole, i `url` .

Pole `displayText` zawiera sugerowane zapytanie, używane do wypełnienia listy rozwijanej w polu wyszukiwania. Należy wyświetlić wszystkie sugestie, które zawiera odpowiedź, w podanej kolejności.  

Jeśli użytkownik wybierze zapytanie z listy rozwijanej, można użyć go do wywołania jednego z [interfejsy API wyszukiwania Bing](../../bing-web-search/bing-api-comparison.md?bc=%2fen-us%2fazure%2fbread%2ftoc.json&toc=%2fen-us%2fazure%2fcognitive-services%2fbing-autosuggest%2ftoc.json) i wyświetlić wyniki samodzielnie lub wysłać użytkownika do strony wyników Bing przy użyciu zwróconego `url` pola.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>Następne kroki

- [Czym jest funkcja automatycznego sugerowania Bing?](../get-suggested-search-terms.md)
- [Bing Autosuggest API v7 reference (Dokumentacja dotycząca automatycznego sugerowania Bing w wersji 7)](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [Pobieranie sugerowanych terminów wyszukiwania z interfejs API automatycznego sugerowania Bing](get-suggestions.md)