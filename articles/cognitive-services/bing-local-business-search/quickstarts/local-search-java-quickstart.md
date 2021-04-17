---
title: Szybki start — wysyłanie zapytania do interfejsu API przy użyciu języka Java — Wyszukiwanie lokalnych firm Bing
titleSuffix: Azure Cognitive Services
description: Użyj tego przewodnika Szybki start, aby rozpocząć wysyłanie żądań w języku Java do interfejsu API Wyszukiwanie lokalnych firm Bing, który jest usługą Azure Cognitive Service.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.date: 05/12/2020
ms.topic: quickstart
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.custom:
- devx-track-java
- mode-api
ms.openlocfilehash: 001fc80b30eaa736db27ba76384aaf273bdec903
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536647"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-java"></a>Szybki start: wysyłanie zapytania do interfejsu API Wyszukiwanie lokalnych firm Bing języka Java

> [!WARNING]
> Wyszukiwanie Bing API są przechodnie z Cognitive Services do Wyszukiwanie Bing Services. Od **30 października 2020** r. wszystkie nowe wystąpienia usługi Wyszukiwanie Bing należy aprowizować zgodnie z procesem [udokumentowanym tutaj.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Wyszukiwanie Bing aprowizowane przy użyciu usługi Cognitive Services będą obsługiwane przez następne trzy lata lub do Enterprise Agreement, w zależności od tego, co nastąpi najpierw.
> Aby uzyskać instrukcje dotyczące migracji, [zobacz Wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Skorzystaj z tego przewodnika Szybki start, aby dowiedzieć się, jak wysyłać żądania do interfejsu API usługi Wyszukiwanie lokalnych firm Bing, czyli usługi Azure Cognitive Service. Chociaż ta prosta aplikacja jest napisana w języku Java, interfejs API jest usługą internetową zgodną z interfejsem RESTful i dowolnym językiem programowania umożliwiającym wykonywanie żądań HTTP i analizowanie danych JSON.

Ta przykładowa aplikacja pobiera lokalne dane odpowiedzi z interfejsu API dla zapytania wyszukiwania.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Zestaw [Java Development Kit (JDK).](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Po utworzeniu subskrypcji platformy Azure utwórz zasób Wyszukiwanie Bing zasobów Wyszukiwanie Bing usłudze Azure Portal, aby uzyskać <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Po wdrożeniu kliknij pozycję **Przejdź do zasobu**.

## <a name="create-the-request"></a>Tworzenie żądania 

Poniższy kod tworzy obiekt , ustawia nagłówek klucza dostępu i dodaje ciąg zapytania `WebRequest` *dla hotel w bellevue.*  Następnie wysyła żądanie i przypisuje odpowiedź do ciągu zawierającego tekst w formacie JSON.

```java
    // construct URL of search request (endpoint + query string)
     URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + &mkt=en-us");
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
```

## <a name="run-the-complete-application"></a>Uruchamianie kompletnej aplikacji

Poniższy kod używa interfejsu API Wyszukiwanie lokalnych firm Bing do zwracania wyników wyszukiwania z wyszukiwarki Bing. Uruchom ten kod, aby wykonać następujące kroki:
1. Pobierz i zainstaluj bibliotekę gson.
2. Utwórz nowy projekt języka Java w ulubionym środowisku IDE lub edytorze.
3. Dodaj kod przedstawiony poniżej.
4. Zastąp wartość `subscriptionKey` kluczem dostępu właściwym dla Twojej subskrypcji.
5. Uruchomisz program.

```java
package localSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (localSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac localSearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar localSearch
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class LocalSearchCls {

    // ***********************************************
    // *** Update or verify the following values. ***
    // **********************************************

        // Replace the subscriptionKey string value with your valid subscription key.
        static String subscriptionKey = "YOUR-ACCESS-KEY";

        static String host = "https://api.cognitive.microsoft.com/bing";
        static String path = "/v7.0/localbusinesses/search";

        static String searchTerm = "Hotel in Bellevue";

        public static SearchResults SearchLocal (String searchQuery) throws Exception {
            // construct URL of search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&mkt=en-us");
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
            try {
                System.out.println("Searching the Web for: " + searchTerm);

                SearchResults result = SearchLocal(searchTerm);

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

## <a name="next-steps"></a>Następne kroki
- [Przewodnik Szybki start dla języka C# dla funkcji wyszukiwania lokalnych firm](local-quickstart.md)
- [Przewodnik Szybki start dla Node.js wyszukiwania lokalnych firm](local-search-node-quickstart.md)
- [Przewodnik Szybki start dla języka Python dla języka Python wyszukiwania lokalnych firm](local-search-python-quickstart.md)
