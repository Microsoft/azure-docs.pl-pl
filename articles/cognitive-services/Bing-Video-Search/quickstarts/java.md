---
title: 'Szybki Start: wyszukiwanie filmów wideo przy użyciu interfejsu API REST i środowiska Java — wyszukiwanie wideo Bing'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki start, aby wysyłać żądania wyszukiwania wideo do interfejsu API REST wyszukiwania wideo Bing przy użyciu języka Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: b042c125d3a7327d3e59424809a7dc7e9339e8fc
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93094205"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-java"></a>Szybki Start: wyszukiwanie filmów wideo przy użyciu interfejsu API REST wyszukiwanie wideo Bing i środowiska Java

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Użyj tego przewodnika Szybki Start, aby wykonać pierwsze wywołanie do interfejs API wyszukiwania wideo Bing. Ta prosta aplikacja Java wysyła zapytanie wyszukiwania wideo HTTP do interfejsu API i wyświetla odpowiedź JSON. Mimo że aplikacja jest zapisywana w języku Java, interfejs API jest usługą sieci Web RESTful zgodną z większością języków programowania. 

Kod źródłowy dla tego przykładu jest dostępny w serwisie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingVideoSearchv7.java) wraz z dodatkową obsługą błędów, funkcjami i adnotacjami kodu.

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* [Biblioteka Gson](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

1. Utwórz nowy projekt Java w ulubionym środowisku IDE lub edytorze, a następnie zaimportuj następujące biblioteki:

    ```java
    import java.net.*;
    import java.util.*;
    import java.io.*;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Utwórz nową klasę o nazwie `SearchResults` służącą do przechowywania nagłówków i odpowiedź JSON z interfejsu API.

    ```java
    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

3. Utwórz nową metodę o nazwie `SearchVideos()` ze zmiennymi dla hosta i ścieżki punktu końcowego interfejsu API, klucza subskrypcji i terminu wyszukiwania. Ta metoda zwraca `SearchResults` obiekt. Dla `host` wartości można użyć globalnego punktu końcowego w poniższym kodzie lub użyć niestandardowego punktu końcowego [poddomeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) wyświetlanego w Azure Portal dla zasobu.

    ```java
    public static SearchResults SearchVideos (String searchQuery) throws Exception {
        static String subscriptionKey = "enter your key here";
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bing/v7.0/videos/search";
        static String searchTerm = "kittens";
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Tworzenie i wysyłanie żądania wyszukiwania

W `SearchVideos()` metodzie wykonaj następujące czynności:

1. Utwórz adres URL żądania, łącząc hosta interfejsu API, ścieżkę i zakodowane zapytanie wyszukiwania. Użyj `openConnection()` , aby utworzyć połączenie, a następnie Dodaj swój klucz subskrypcji do `Ocp-Apim-Subscription-Key` nagłówka.

     ```java
     URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8"));
     HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
     connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
     ```

2. Pobierz odpowiedź z interfejsu API i zapisz ciąg JSON.

     ```java
     InputStream stream = connection.getInputStream();
     String response = new Scanner(stream).useDelimiter("\\A").next();
     ```

 3. Użyj funkcji `getHeaderFields()` do wyodrębnienie nagłówków HTTP z odpowiedzi i zapisania nagłówków związanych z usługą Bing w obiekcie `results`. Następnie zamknij strumień i zwróć wynik.

     ```java
     // extract Bing-related HTTP headers
     Map<String, List<String>> headers = connection.getHeaderFields();
     for (String header : headers.keySet()) {
         if (header == null) continue;      // may have null key
         if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
             results.relevantHeaders.put(header, headers.get(header).get(0));
         }
     }
     stream.close();
     return results;
     ```

## <a name="format-the-response"></a>Formatowanie odpowiedzi

Utwórz metodę o nazwie `prettify()` w celu sformatowania odpowiedzi z interfejsu API wideo Bing. Biblioteka Gson służy `JsonParser` do konwertowania ciągu JSON na obiekt. Następnie użyj `GsonBuilder()` i, `toJson()` Aby utworzyć sformatowany ciąg.

```java
// pretty-printer for JSON; uses GSON parser to parse and re-serialize
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="send-the-request-and-print-the-response"></a>Wysyłanie żądania i wyświetlanie odpowiedzi

W metodzie głównej aplikacji wywołaj element `SearchVideos` przy użyciu terminu wyszukiwania. Następnie wydrukuj nagłówki HTTP przechowywane w odpowiedzi i ciąg JSON zwrócony przez interfejs API.

 ```java
 public static void main (String[] args) {

     SearchResults result = SearchVideos(searchTerm);
     //print the Relevant HTTP Headers
     for (String header : result.relevantHeaders.keySet())
         System.out.println(header + ": " + result.relevantHeaders.get(header));
     System.out.println(prettify(result.jsonResponse));
 }
 ```

## <a name="json-response"></a>Odpowiedź w formacie JSON

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie:

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zobacz też 

 [Co to jest interfejs API wyszukiwania wideo Bing?](../overview.md)
