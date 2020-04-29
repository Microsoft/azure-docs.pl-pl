---
title: 'Szybki Start: analizowanie obrazu zdalnego za pomocą interfejsu API REST i środowiska Java'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start przeprowadzisz analizę obrazu za pomocą interfejsu API przetwarzania obrazów w języku C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: f68c58c16c4efd75941e00b859784f810915b575
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81404860"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-and-java"></a>Szybki Start: analizowanie obrazu zdalnego przy użyciu interfejsu API REST przetwarzanie obrazów i środowiska Java

W tym przewodniku szybki start przeanalizuje zdalnie przechowywany obraz, aby wyodrębnić funkcje wizualne przy użyciu języka Java i interfejsu API REST przetwarzanie obrazów. [Metoda Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) umożliwia wyodrębnienie elementów wizualnych na podstawie zawartości obrazu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) .

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć zainstalowaną platformę [Java&trade; i zestaw Standard Edition Development Kit 7 lub 8](https://aka.ms/azure-jdks) (JDK 7 lub 8).
- Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Możesz uzyskać bezpłatny klucz wersji próbnej z usługi [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Lub postępuj zgodnie z instrukcjami w temacie [Tworzenie konta Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) , aby subskrybować przetwarzanie obrazów i uzyskać klucz. Następnie [Utwórz zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla ciągu punktu końcowego klucza i usługi, odpowiednio `COMPUTER_VISION_SUBSCRIPTION_KEY` nazwane `COMPUTER_VISION_ENDPOINT`i.

## <a name="create-and-run-the-sample-application"></a>Tworzenie i uruchamianie przykładowej aplikacji

Aby utworzyć i uruchomić przykład, wykonaj następujące kroki:

1. Utwórz nowy projekt języka Java w ulubionym środowisku IDE lub edytorze. Jeśli ta opcja jest dostępna, utwórz projekt języka Java na podstawie szablonu aplikacji wiersza polecenia.
1. Zaimportuj poniższe biblioteki do projektu języka Java. Jeśli używasz programu Maven, współrzędne programu Maven są udostępniane w każdej bibliotece.
   - [Klient HTTP serwera Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.5)
   - [Podstawowa wersja HTTP serwera Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.9)
   - [Biblioteka JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
1. Dodaj poniższe instrukcje `import` do pliku, który zawiera publiczną klasę `Main` dla projektu.  

   ```java
   import java.net.URI;
   import org.apache.http.HttpEntity;
   import org.apache.http.HttpResponse;
   import org.apache.http.client.methods.HttpPost;
   import org.apache.http.entity.StringEntity;
   import org.apache.http.client.utils.URIBuilder;
   import org.apache.http.impl.client.CloseableHttpClient;
   import org.apache.http.impl.client.HttpClientBuilder;
   import org.apache.http.util.EntityUtils;
   import org.json.JSONObject;
   ```

1. Zastąp `Main` klasę publiczną poniższym kodem.
1. Opcjonalnie zastąp wartość `imageToAnalyze` adresem URL innego obrazu, który chcesz analizować.

```java
public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Add your Computer Vision subscription key and endpoint to your environment variables.
    // After setting, close and then re-open your command shell or project for the changes to take effect.
    String subscriptionKey = System.getenv("COMPUTER_VISION_SUBSCRIPTION_KEY");
    String endpoint = ("COMPUTER_VISION_ENDPOINT");

    private static final String uriBase = endpoint + 
            "vision/v2.1/analyze";
    private static final String imageToAnalyze =
            "https://upload.wikimedia.org/wikipedia/commons/" +
                    "1/12/Broadway_and_Times_Square_by_night.jpg";

    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();

        try {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API method.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity requestEntity =
                    new StringEntity("{\"url\":\"" + imageToAnalyze + "\"}");
            request.setEntity(requestEntity);

            // Call the REST API method and get the response entity.
            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null) {
                // Format and display the JSON response.
                String jsonString = EntityUtils.toString(entity);
                JSONObject json = new JSONObject(jsonString);
                System.out.println("REST Response:\n");
                System.out.println(json.toString(2));
            }
        } catch (Exception e) {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="compile-and-run-the-program"></a>Kompilowanie i uruchamianie programu

1. Zapisz, a następnie skompiluj projekt języka Java.
1. Jeśli używasz środowiska IDE, uruchom klasę `Main`.

Alternatywnie, jeśli używasz programu z poziomu okna wiersza polecenia, uruchom następujące polecenia. Te polecenia zakładają, że biblioteki znajdują się `libs` w folderze o nazwie znajdującym się `Main.java`w tym samym folderze co; w przeciwnym razie należy zamienić `libs` na ścieżkę do bibliotek.

1. Skompiluj plik `Main.java`.

    ```bash
    javac -cp ".;libs/*" Main.java
    ```

1. Uruchom program. Program wyśle żądanie do interfejsu API usługi QnA Maker, aby utworzyć bazę wiedzy, a następnie będzie sondować wyniki co 30 sekund. Każda odpowiedź jest drukowana do okna wiersza polecenia.

    ```bash
    java -cp ".;libs/*" Main
    ```

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON. Przykładowa aplikacja analizuje i wyświetla pomyślną odpowiedź w oknie konsoli, podobnie jak w poniższym przykładzie:

```json
REST Response:

{
  "metadata": {
    "width": 1826,
    "format": "Jpeg",
    "height": 2436
  },
  "color": {
    "dominantColorForeground": "Brown",
    "isBWImg": false,
    "accentColor": "B74314",
    "dominantColorBackground": "Brown",
    "dominantColors": ["Brown"]
  },
  "requestId": "bbffe1a1-4fa3-4a6b-a4d5-a4964c58a811",
  "description": {
    "captions": [{
      "confidence": 0.8241405091548035,
      "text": "a group of people on a city street filled with traffic at night"
    }],
    "tags": [
      "outdoor",
      "building",
      "street",
      "city",
      "busy",
      "people",
      "filled",
      "traffic",
      "many",
      "table",
      "car",
      "group",
      "walking",
      "bunch",
      "crowded",
      "large",
      "night",
      "light",
      "standing",
      "man",
      "tall",
      "umbrella",
      "riding",
      "sign",
      "crowd"
    ]
  },
  "categories": [{
    "score": 0.625,
    "name": "outdoor_street"
  }]
}
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z aplikacją w języku Java Swing, w której zastosowano interfejs API przetwarzania obrazów do optycznego rozpoznawania znaków (OCR), inteligentnego przycinania miniatur oraz wykrywania, kategoryzowania, tagowania i opisywania elementów wizualnych, w tym twarzy, na obrazie. Aby szybko zacząć eksperymentować z interfejsem API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Computer Vision API Java Tutorial (Samouczek dla języka JavaScript dotyczący interfejsu API przetwarzania obrazów)](../Tutorials/java-tutorial.md)
