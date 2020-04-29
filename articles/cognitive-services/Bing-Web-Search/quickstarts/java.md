---
title: 'Szybki Start: używanie języka Java do wywoływania interfejsu API REST wyszukiwanie w sieci Web Bing'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby wysyłać żądania do interfejsu API wyszukiwania w sieci Web Bing przy użyciu języka Java i otrzymywać odpowiedzi w formacie JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.custom: seodec2018, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 1fdeffb5ee5b1e2d66fbf5586d307cd8d8b78858
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76166729"
---
# <a name="quickstart-use-java-to-search-the-web-with-the-bing-web-search-rest-api-an-azure-cognitive-service"></a>Szybki Start: używanie języka Java do przeszukiwania sieci Web za pomocą interfejsu API REST wyszukiwanie w sieci Web Bing, usługi poznawczej platformy Azure

W tym przewodniku szybki start użyjesz aplikacji Java do nadania pierwszego wywołania do interfejs API wyszukiwania w sieci Web Bing i otrzymania odpowiedzi JSON. Ta aplikacja Java wysyła żądanie wyszukiwania do interfejsu API i wyświetla odpowiedź. Chociaż ta aplikacja jest napisana w języku Java, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

## <a name="prerequisites"></a>Wymagania wstępne

Oto kilka rzeczy, które są potrzebne przed rozpoczęciem tego przewodnika Szybki start:

* [Zestaw JDK 7 lub 8](https://aka.ms/azure-jdks)
* [Biblioteka Gson](https://github.com/google/gson)
* Klucz subskrypcji

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-import-dependencies"></a>Tworzenie projektu i importowanie zależności

Utwórz nowy projekt w języku Java w ulubionym środowisku IDE lub edytorze i zaimportuj następujące biblioteki. Biblioteka Gson jest wymagana do konwertowania obiektów Java na format JSON.

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

### <a name="declare-gson-in-the-maven-pom-file"></a>Deklarowanie biblioteki Gson w pliku POM narzędzia Maven

Jeśli używasz narzędzia Maven, zadeklaruj bibliotekę Gson w pliku `POM.xml`. Jeśli bibliotekę Gson zainstalowano lokalnie, pomiń ten krok.

```xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.8.5</version>
</dependency>
```

## <a name="declare-the-bingwebsearch-class"></a>Deklarowanie klasy BingWebSearch

Zadeklaruj klasę `BingWebSearch`. Będzie ona zawierać większość kodu omawianego w tym przewodniku Szybki start, w tym metodę `main`.  

```java
public class BingWebSearch {

// The code in the following sections goes here.

}
```

## <a name="define-variables"></a>Definiowanie zmiennych

Ten kod ustawia wartości `subscriptionKey`, `host`, `path` i `searchTerm`. `host`może to być globalny punkt końcowy poniżej lub niestandardowy punkt końcowy [domeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) podrzędnej wyświetlany w Azure Portal dla zasobu. Zamień wartość `subscriptionKey` na odpowiedni klucz subskrypcji ze swojego konta platformy Azure. Możesz dostosować zapytanie wyszukiwania, zamieniając wartość `searchTerm`. Pamiętaj, aby dodać ten kod do `BingWebSearch` klasy, jak wspomniano powyżej.

```java
// Enter a valid subscription key.
static String subscriptionKey = "enter key here";

/*
 * If you encounter unexpected authorization errors, double-check these values
 * against the endpoint for your Bing Web search instance in your Azure
 * dashboard.
 */
static String host = "https://api.cognitive.microsoft.com";
static String path = "/bing/v7.0/search";
static String searchTerm = "Microsoft Cognitive Services";
```

## <a name="construct-a-request"></a>Konstruowanie żądania

Ta metoda, znajdująca się w klasie `BingWebSearch`, konstruuje wartość `url`, odbiera i analizuje odpowiedź, a także wyodrębnia nagłówki HTTP związane z usługą Bing.  

```java
public static SearchResults SearchWeb (String searchQuery) throws Exception {
    // Construct the URL.
    URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8"));

    // Open the connection.
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // Receive the JSON response body.
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // Construct the result object.
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);

    // Extract Bing-related HTTP headers.
    Map<String, List<String>> headers = connection.getHeaderFields();
    for (String header : headers.keySet()) {
        if (header == null) continue;      // may have null key
        if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")){
            results.relevantHeaders.put(header, headers.get(header).get(0));
        }
    }
    stream.close();
    return results;
}
```

## <a name="handle-the-response"></a>Obsługa odpowiedzi

Za pomocą biblioteki Gson przeanalizuj i ponownie zserializuj odpowiedź.

```java
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="declare-the-main-method"></a>Deklarowanie metody main

Ta metoda jest wymagana i jest pierwszą wywoływaną metodą po uruchomieniu programu. W tej aplikacji zawiera ona kod weryfikujący wartość `subscriptionKey`, wysyłający żądanie i wyświetlający odpowiedź JSON.

```java
public static void main (String[] args) {
    // Confirm the subscriptionKey is valid.
    if (subscriptionKey.length() != 32) {
        System.out.println("Invalid Bing Search API subscription key!");
        System.out.println("Please paste yours into the source code.");
        System.exit(1);
    }

    // Call the SearchWeb method and print the response.
    try {
        System.out.println("Searching the Web for: " + searchTerm);
        SearchResults result = SearchWeb(searchTerm);
        System.out.println("\nRelevant HTTP Headers:\n");
        for (String header : result.relevantHeaders.keySet())
        System.out.println(header + ": " + result.relevantHeaders.get(header));
        System.out.println("\nJSON Response:\n");
        System.out.println(prettify(result.jsonResponse));
    }
    catch (Exception e) {
        e.printStackTrace(System.out);
        System.exit(1);
    }
}
```

## <a name="create-a-container-class-for-search-results"></a>Tworzenie klasy kontenera dla wyników wyszukiwania

Klasa kontenera `BingWebSearch` znajduje się poza klasą `SearchResults`. Obejmuje ona odpowiednie nagłówki i dane JSON na potrzeby odpowiedzi.

```java
class SearchResults{
    HashMap<String, String> relevantHeaders;
    String jsonResponse;
    SearchResults(HashMap<String, String> headers, String json) {
        relevantHeaders = headers;
        jsonResponse = json;
    }
}
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

Ostatnim krokiem jest skompilowanie kodu i uruchomienie go. Oto odpowiednie polecenia:

```powershell
javac BingWebSearch.java -classpath ./gson-2.8.5.jar -encoding UTF-8
java -cp ./gson-2.8.5.jar BingWebSearch
```

Jeśli chcesz porównać swój kod z naszym, [kod przykładowy jest dostępny w witrynie GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingWebSearchv7.java).

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
            "url": "https://www.microsoft.com/cognitive-services/en-us/emotion-api",
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
