---
title: 'Szybki Start: wykonywanie wyszukiwania w sieci Web za pomocą środowiska Node. js — wyszukiwanie w sieci Web Bing interfejsu API REST'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby wysyłać żądania do interfejsu API wyszukiwania w sieci Web Bing przy użyciu środowiska Node.js i otrzymywać odpowiedzi w formacie JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 54f4b38e01b51289319390779a140346befc6f0c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76168802"
---
# <a name="quickstart-search-the-web-using-the-bing-web-search-rest-api-and-nodejs"></a>Szybki Start: przeszukiwanie sieci Web przy użyciu interfejsu API REST wyszukiwanie w sieci Web Bing i środowiska Node. js

Ten przewodnik Szybki start umożliwi Ci utworzenie Twojego pierwszego wywołania interfejsu API wyszukiwania w Internecie Bing i odebranie odpowiedzi JSON. Ta aplikacja Node. js wysyła żądanie wyszukiwania do interfejsu API i wyświetla odpowiedź. Aplikacja jest napisana w języku JavaScript, natomiast interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

## <a name="prerequisites"></a>Wymagania wstępne

Oto kilka rzeczy, które są potrzebne przed rozpoczęciem tego przewodnika Szybki start:

* [Node. js 6](https://nodejs.org/en/download/) lub nowszy
* Klucz subskrypcji

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-declare-required-modules"></a>Tworzenie projektu i deklarowanie wymaganych modułów

Utwórz nowy projekt Node.js w ulubionym środowisku IDE lub edytorze.
Następnie skopiuj poniższy fragment kodu do projektu do pliku o nazwie `search.js`.

```javascript
// Use this simple app to query the Bing Web Search API and get a JSON response.
// Usage: node search.js "your query".
const https = require('https')
```

## <a name="set-the-subscription-key"></a>Ustawianie klucza subskrypcji

Ten fragment kodu używa zmiennej środowiskowej `AZURE_SUBSCRIPTION_KEY` do przechowywania klucza subskrypcji. Jest to dobre rozwiązanie pozwalające uniknąć przypadkowego ujawnienia kluczy podczas wdrażania kodu. Przejdź do [strony interfejsów API](https://azure.microsoft.com/try/cognitive-services/my-apis/?apiSlug=search-api-v7) , aby wyszukać klucz subskrypcji.

Jeśli nie wiesz, jak korzystać ze zmiennych środowiskowych, lub chcesz uruchomić tę aplikację możliwie najszybciej, możesz zastąpić `process.env['AZURE_SUBSCRIPTION_KEY']` kluczem subskrypcji ustawionym jako ciąg.

```javascript
const SUBSCRIPTION_KEY = process.env['AZURE_SUBSCRIPTION_KEY']
if (!SUBSCRIPTION_KEY) {
  throw new Error('AZURE_SUBSCRIPTION_KEY is not set.')
}
```

## <a name="create-a-function-to-make-the-request"></a>Tworzenie funkcji do wysłania żądania

Ta funkcja wyśle bezpieczne żądanie GET, zapisując zapytanie wyszukiwania jako parametr zapytania w ścieżce. `hostname`może to być globalny punkt końcowy poniżej lub niestandardowy punkt końcowy [domeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) podrzędnej wyświetlany w Azure Portal dla zasobu.  Parametr `encodeURIComponent` ułatwia uniknięcie nieprawidłowych znaków, a klucz subskrypcji jest przekazywany w nagłówku. Wywołanie zwrotne otrzymuje [odpowiedź](https://nodejs.org/dist/latest-v10.x/docs/api/http.html#http_class_http_serverresponse), która subskrybuje zdarzenia `data` w celu agregowania treści w formacie JSON, zdarzenia `error` do zapisywania wszelkich problemów w dzienniku oraz zdarzenia `end` umożliwiające określenie, kiedy należy uznać komunikat za pełny. Po zakończeniu aplikacja zacznie drukować interesujące nas nagłówki i treść komunikatów. Można poeksperymentować z kolorami i ustawić głębię w zależności od osobistych preferencji. Głębia `1` zapewnia ładne podsumowanie odpowiedzi.

```javascript
function bingWebSearch(query) {
  https.get({
    hostname: 'api.cognitive.microsoft.com',
    path:     '/bing/v7.0/search?q=' + encodeURIComponent(query),
    headers:  { 'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY },
  }, res => {
    let body = ''
    res.on('data', part => body += part)
    res.on('end', () => {
      for (var header in res.headers) {
        if (header.startsWith("bingapis-") || header.startsWith("x-msedge-")) {
          console.log(header + ": " + res.headers[header])
        }
      }
      console.log('\nJSON Response:\n')
      console.dir(JSON.parse(body), { colors: false, depth: null })
    })
    res.on('error', e => {
      console.log('Error: ' + e.message)
      throw e
    })
  })
}
```

## <a name="get-the-query"></a>Pobieranie zapytania

Przyjrzyjmy się argumentom programu, aby znaleźć zapytanie. Pierwszym argumentem jest ścieżka do węzła, drugim jest nasza nazwa pliku, a trzecim Twoje zapytanie. Jeśli nie podamy zapytania, zostanie użyte domyślne zapytanie usługi „Microsoft Cognitive Services”.

```javascript
const query = process.argv[2] || 'Microsoft Cognitive Services'
```

## <a name="make-a-request-and-print-the-response"></a>Wysyłanie żądania i wyświetlanie odpowiedzi

Teraz gdy wszystko jest zdefiniowane, wywołajmy naszą funkcję.

```javascript
bingWebSearch(query)
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

Ostatnim krokiem jest uruchomienie kodu: `node search.js "<your query>"`.

Jeśli chcesz porównać swój kod z naszym, oto pełny program:

```javascript
const https = require('https')
const SUBSCRIPTION_KEY = process.env['AZURE_SUBSCRIPTION_KEY']
if (!SUBSCRIPTION_KEY) {
  throw new Error('Missing the AZURE_SUBSCRIPTION_KEY environment variable')
}
function bingWebSearch(query) {
  https.get({
    hostname: 'api.cognitive.microsoft.com',
    path:     '/bing/v7.0/search?q=' + encodeURIComponent(query),
    headers:  { 'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY },
  }, res => {
    let body = ''
    res.on('data', part => body += part)
    res.on('end', () => {
      for (var header in res.headers) {
        if (header.startsWith("bingapis-") || header.startsWith("x-msedge-")) {
          console.log(header + ": " + res.headers[header])
        }
      }
      console.log('\nJSON Response:\n')
      console.dir(JSON.parse(body), { colors: false, depth: null })
    })
    res.on('error', e => {
      console.log('Error: ' + e.message)
      throw e
    })
  })
}
const query = process.argv[2] || 'Microsoft Cognitive Services'
bingWebSearch(query)
```

## <a name="sample-response"></a>Przykładowa odpowiedź

Odpowiedzi z interfejsu API wyszukiwania w sieci Web Bing są zwracane w formacie JSON. Ta przykładowa odpowiedź została obcięta w celu pokazania pojedynczego wyniku.

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Microsoft Cognitive Services"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q=Microsoft+cognitive+services",
    "totalEstimatedMatches": 22300000,
    "value": [
      {
        "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0",
        "name": "Microsoft Cognitive Services",
        "url": "https://www.microsoft.com/cognitive-services",
        "displayUrl": "https://www.microsoft.com/cognitive-services",
        "snippet": "Knock down barriers between you and your ideas. Enable natural and contextual interaction with tools that augment users' experiences via the power of machine-based AI. Plug them in and bring your ideas to life.",
        "deepLinks": [
          {
            "name": "Face",
            "url": "https://azure.microsoft.com/services/cognitive-services/face/",
            "snippet": "Add facial recognition to your applications to detect, identify, and verify faces using a Face service from Microsoft Azure. ... Cognitive Services; Face service;"
          },
          {
            "name": "Text Analytics",
            "url": "https://azure.microsoft.com/services/cognitive-services/text-analytics/",
            "snippet": "Cognitive Services; Text Analytics API; Text Analytics API . Detect sentiment, ... you agree that Microsoft may store it and use it to improve Microsoft services, ..."
          },
          {
            "name": "Computer Vision API",
            "url": "https://azure.microsoft.com/services/cognitive-services/computer-vision/",
            "snippet": "Extract the data you need from images using optical character recognition and image analytics with Computer Vision APIs from Microsoft Azure."
          },
          {
            "name": "Emotion",
            "url": "https://www.microsoft.com/cognitive-services/emotion-api",
            "snippet": "Cognitive Services Emotion API - microsoft.com"
          },
          {
            "name": "Bing Speech API",
            "url": "https://azure.microsoft.com/services/cognitive-services/speech/",
            "snippet": "Add speech recognition to your applications, including text to speech, with a speech API from Microsoft Azure. ... Cognitive Services; Bing Speech API;"
          },
          {
            "name": "Get Started for Free",
            "url": "https://azure.microsoft.com/services/cognitive-services/",
            "snippet": "Add vision, speech, language, and knowledge capabilities to your applications using intelligence APIs and SDKs from Cognitive Services."
          }
        ]
      }
    ]
  },
  "relatedSearches": {
    "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches",
    "value": [
      {
        "text": "microsoft bot framework",
        "displayText": "microsoft bot framework",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+bot+framework"
      },
      {
        "text": "microsoft cognitive services youtube",
        "displayText": "microsoft cognitive services youtube",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+youtube"
      },
      {
        "text": "microsoft cognitive services search api",
        "displayText": "microsoft cognitive services search api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+search+api"
      },
      {
        "text": "microsoft cognitive services news",
        "displayText": "microsoft cognitive services news",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+news"
      },
      {
        "text": "ms cognitive service",
        "displayText": "ms cognitive service",
        "webSearchUrl": "https://www.bing.com/search?q=ms+cognitive+service"
      },
      {
        "text": "microsoft cognitive services text analytics",
        "displayText": "microsoft cognitive services text analytics",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+text+analytics"
      },
      {
        "text": "microsoft cognitive services toolkit",
        "displayText": "microsoft cognitive services toolkit",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+toolkit"
      },
      {
        "text": "microsoft cognitive services api",
        "displayText": "microsoft cognitive services api",
        "webSearchUrl": "https://www.bing.com/search?q=microsoft+cognitive+services+api"
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#WebPages.0"
          }
        }
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "RelatedSearches",
          "value": {
            "id": "https://api.cognitive.microsoft.com/api/v7/#RelatedSearches"
          }
        }
      ]
    }
  }
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący jednostronicowej aplikacji wyszukiwania w sieci Web Bing](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
