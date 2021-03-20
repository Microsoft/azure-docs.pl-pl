---
title: Wyszukaj obrazy GIF przy użyciu interfejs API wyszukiwania obrazów Bing
titleSuffix: Azure Cognitive Services
description: Interfejs API wyszukiwania obrazów Bing pozwala również przeszukiwać w całej sieci Web odpowiednie obrazy. gif.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: d1fcac891db240def2e7bbdcb45b45caf7f49a82
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96341907"
---
# <a name="search-for-gif-images"></a>Wyszukaj obrazy GIF 

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Interfejs API wyszukiwania obrazów Bing pozwala również przeszukiwać w całej sieci Web odpowiednie obrazy. gif.  Deweloperzy mogą zintegrować atrakcyjne pliki GIF w różnych scenariuszach konwersacji. 

Następujący adres URL jest kwerendą dla animowanych obrazów GIF.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
```
Parametr [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) określa terminy wyszukiwania.  Poprzednie zapytanie określa również `animatedGif` użycie parametru filtru [ImageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) .

Aby zobaczyć przykłady wyników, użyj następującego adresu URL, aby wyszukać bing.com.
```
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif

```
## <a name="query-parameters"></a>Parametry zapytania

Aby uzyskać więcej informacji na temat parametrów zapytania i opcji, zobacz [Informacje o interfejsie API wyszukiwanie obrazów](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query-parameters). Przykład poniżej znajduje się w sekcji [przykładowe wyszukiwanie animowanych plików GIF przy użyciu języka Java](#gifExample).

## <a name="tips-and-suggestions"></a>Porady i sugestie

- Można określić parametry [MaxFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) i [minFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) . Zalecamy ustawienie maxFileSize = 2000000, ponieważ większość plików GIF w naszym indeksie jest większa niż 2 MB.  Pozwala to również kontrolować rozmiar danych w przypadku, gdy przepustowość jest istotna, np. w scenariuszach mobilnych komórkowych.
- Aby pomóc w ulepszaniu postrzeganej wydajności, najpierw Załaduj miniaturę przed załadowaniem źródłowego adresu URL.  
- W przypadku pierwszego uruchomienia lub strony docelowej, w której nie masz jeszcze zapytania użytkownika, spróbuj użyć naszych trendów wyszukiwania GIF, aby uzyskać pomoc dotyczącą [interfejsu API obrazów trendów](trending-images.md).
- Istnieją trzy ustawienia parametru [bezpieczne wyszukiwanie](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) .  `strict`Opcja blokuje zawartość dla dorosłych.
- Zobacz [MKT](./language-support.md) , aby uzyskać pełną listę obsługiwanych języków i lokalizacji.
- *AnimatedGifHttps* zwraca tylko animowane obrazy GIF, które pochodzą z adresu https. W przypadku zabezpieczeń wiele aplikacji wymaga połączenia z zewnętrznymi łączami internetowymi za pośrednictwem protokołu HTTPS. Na przykład Sklep Apple App Store wymaga połączenia z usługami sieci Web za pośrednictwem protokołu HTTPS, który szyfruje dane użytkownika w trakcie przesyłania.

<a name="gifExample"></a>

## <a name="example-search-for-animated-gif-using-java"></a>Przykładowe wyszukiwanie animowanych plików GIF przy użyciu języka Java

Następujący adres URL wyszukuje animowane obrazy GIF: `q=interesting`
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

```
Jak pokazano w poniższym przykładzie, zapytanie URL wymaga, aby nagłówek [OCP-APIM-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#headers) .

Poniższy przykład Java kompiluje i wysyła żądanie.

```
package gifSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac GIFsearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GIFsearch
 */


public class GIFsearch {

    // Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "YOUR-ACCESS-KEY";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";

    static String searchTerm = "interesting";

    public static SearchResults SearchImages (String searchQuery) throws Exception {
        // construct URL of search request (endpoint + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&imageType=AnimatedGif&mkt=en-us");
        HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

        // receive JSON body
        InputStream stream = connection.getInputStream();
        String response = new Scanner(stream).useDelimiter("\\A").next();

        // construct result object for return
        SearchResults results = new SearchResults(new HashMap<String, String>(), response);

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
    }

    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        if (subscriptionKey.length() != 32) {
            System.out.println("Invalid Bing Search API subscription key!");
            System.out.println("Please paste yours into the source code.");
            System.exit(1);
        }

        try {
            System.out.println("Searching the Web for: " + searchTerm);

            SearchResults result = SearchImages(searchTerm);

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

}

//Container class for search results encapsulates relevant headers and JSON data
class SearchResults{
 HashMap<String, String> relevantHeaders;
 String jsonResponse;
 SearchResults(HashMap<String, String> headers, String json) {
     relevantHeaders = headers;
     jsonResponse = json;
 }
}

```

## <a name="results"></a>Wyniki
Kod pobiera następujące wyniki jako obiekty JSON:

```json
    {
      "webSearchUrl": "https://www.bing.com/images/search?view\u003ddetai...",
      "name": "Very Interesting GIF - Thats Very Interesting - ...",
      "thumbnailUrl": "https://tse1.mm.bing.net/th?id\u003dOIP.yJX6Vz345JPK...",
      "datePublished": "2017-03-12T01:35:00.0000000Z",
      "contentUrl": "https://media.contoso.co/images/c895fa573df8e493ca8d0dec7d93b/raw",
      "hostPageUrl": "https://www.contoso.co/view/thats-very-interesting-christi...",
      "contentSize": "1295633 B",
      "encodingFormat": "animatedgif",
      "hostPageDisplayUrl": "https://www.contoso.co/view/thats-very-christian...",
      "width": 440,
      "height": 186,
      "thumbnail": {
        "width": 474,
        "height": 200
      },
      "imageInsightsToken": "ccid_yJX6Vz34*mid_9FF0FFA42AADA1357F042443D2103B40EA...",
      "insightsMetadata": {
        "recipeSourcesCount": 0,
        "bestRepresentativeQuery": {
          "text": "That\u0027s Very Interesting",
          "displayText": "That\u0027s Very Interesting",
          "webSearchUrl": "https://www.bing.com/images/search?q\u003dThat..."
        },
        "pagesIncludingCount": 19,
        "availableSizesCount": 2
      },
      "imageId": "9FF0FFA42AADA1357F042443D21030EAAA225F",
      "accentColor": "62452D"
    },

```

## <a name="next-steps"></a>Następne kroki
- [Przewodnik Szybki start dla języka C#](quickstarts/csharp.md)
- [Samouczek wyszukiwanie obrazów aplikacji jednostronicowej](tutorial-bing-image-search-single-page-app.md)