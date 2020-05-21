---
title: Pobieranie modelu z wywołaniem REST w języku Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.author: diberry
ms.openlocfilehash: 7d1915025c31a0c201da87b1d4bde9b52659e5e3
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655481"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Azure Language Understanding — tworzenie zasobu 32 klucz znaku i adres URL punktu końcowego tworzenia. Utwórz za pomocą [Azure Portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) lub [interfejsu wiersza polecenia platformy Azure](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Zaimportuj aplikację [Pizza](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json) z `Azure-Samples/cognitive-services-sample-data-files` repozytorium GitHub.
* Identyfikator aplikacji LUIS dla zaimportowanej aplikacji Pizza. Identyfikator aplikacji jest wyświetlany na pulpicie nawigacyjnym aplikacji.
* Identyfikator wersji w aplikacji, w której odbierane są wypowiedzi.
* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/) lub ULUBIONEGO środowiska IDE

## <a name="example-utterances-json-file"></a>Plik JSON z przykładowymi wypowiedziami

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Programowo Zmień model

1. Utwórz nowy folder do przechowywania projektu Java, np `java-model-with-rest` ..

1. Utwórz podkatalog o nazwie `lib` i skopiuj w następującym libs języka Java do `lib` podkatalogu:

    * [commons-logging-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. Utwórz nowy plik o nazwie `Model.java`. Dodaj następujący kod:

```java
import java.io.*;
import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

// To compile, execute this command at the console:
//      Windows: javac -cp ";lib/*" Model.java
//      macOs: javac -cp ":lib/*" Model.java
//      Linux: javac -cp ":lib/*" Model.java

// To run, execute this command at the console:
//      Windows: java -cp ";lib/*" Model
//      macOs: java -cp ":lib/*" Model
//      Linux: java -cp ":lib/*" Model

public class Model
{
    public static void main(String[] args)
    {
        try
        {
            //////////
            // Values to modify.

            // YOUR-APP-ID: The App ID GUID found on the www.luis.ai Application Settings page.
            String AppId = "YOUR-APP-ID";

            // YOUR-AUTHORING-KEY: Your LUIS authoring key, 32 character value.
            String Key = "YOUR-AUTHORING-KEY";

            // YOUR-AUTHORING-ENDPOINT: Replace this with your authoring key endpoint.
            // For example, "https://your-resource-name.api.cognitive.microsoft.com/"
            String Endpoint = "YOUR-AUTHORING-ENDPOINT";

            // NOTE: Replace this your version number. The Pizza app uses a version number of "0.1".
            String Version = "0.1";
            //////////

            // The list of utterances to add, in JSON format.
            String Utterances = "[{'text': 'order a pizza', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 6, 'endCharIndex': 12}]}, {'text': 'order a large pepperoni pizza', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 6, 'endCharIndex': 28}, {'entityName': 'FullPizzaWithModifiers', 'startCharIndex': 6, 'endCharIndex': 28}, {'entityName': 'PizzaType', 'startCharIndex': 14, 'endCharIndex': 28}, {'entityName': 'Size', 'startCharIndex': 8, 'endCharIndex': 12}]}, {'text': 'I want two large pepperoni pizzas on thin crust', 'intentName': 'ModifyOrder', 'entityLabels': [{'entityName': 'Order', 'startCharIndex': 7, 'endCharIndex': 46}, {'entityName': 'FullPizzaWithModifiers', 'startCharIndex': 7, 'endCharIndex': 46}, {'entityName': 'PizzaType', 'startCharIndex': 17, 'endCharIndex': 32}, {'entityName': 'Size', 'startCharIndex': 11, 'endCharIndex': 15}, {'entityName': 'Quantity', 'startCharIndex': 7, 'endCharIndex': 9}, {'entityName': 'Crust', 'startCharIndex': 37, 'endCharIndex': 46}]}]";

            // Create the URLs for uploading example utterances and for training.
            URIBuilder addUtteranceURL = new URIBuilder(Endpoint + "luis/authoring/v3.0-preview/apps/" + AppId + "/versions/" + Version + "/examples");
            URIBuilder trainURL = new URIBuilder(Endpoint + "luis/authoring/v3.0-preview/apps/" + AppId + "/versions/" + Version + "/train");
            URI addUtterancesURI = addUtteranceURL.build();
            URI trainURI = trainURL.build();


            // Add the utterances.

            // Create the request.
            HttpClient addUtterancesClient = HttpClients.createDefault();
            HttpPost addUtterancesRequest = new HttpPost(addUtterancesURI);

            // Add the headers.
            addUtterancesRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
            addUtterancesRequest.setHeader("Content-type","application/json");

            // Add the body.
            StringEntity stringEntity = new StringEntity(Utterances);
            addUtterancesRequest.setEntity(stringEntity);

            // Execute the request and obtain the response.
            HttpResponse addUtterancesResponse = addUtterancesClient.execute(addUtterancesRequest);
            HttpEntity addUtterancesEntity = addUtterancesResponse.getEntity();

            // Print the response on the console.
            if (addUtterancesEntity != null)
            {
                System.out.println(EntityUtils.toString(addUtterancesEntity));
            }


            // Train the model.

            // Create the request.
            HttpClient trainClient = HttpClients.createDefault();
            HttpPost trainRequest = new HttpPost(trainURI);

            // Add the headers.
            trainRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
            trainRequest.setHeader("Content-type","application/json");

            // Execute the request and obtain the response.
            HttpResponse trainResponse = trainClient.execute(trainRequest);
            HttpEntity trainEntity = trainResponse.getEntity();

            // Print the response on the console.
            if (trainEntity != null)
            {
                System.out.println(EntityUtils.toString(trainEntity));
            }


            // Get the training status.


            // Create the request.
            HttpClient trainStatusClient = HttpClients.createDefault();
            HttpGet trainStatusRequest = new HttpGet(trainURI);

            // Add the headers.
            trainStatusRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
            trainStatusRequest.setHeader("Content-type","application/json");

            // Execute the request and obtain the response.
            HttpResponse trainStatusResponse = trainStatusClient.execute(trainStatusRequest);
            HttpEntity trainStatusEntity = trainStatusResponse.getEntity();

            // Print the response on the console.
            if (trainStatusEntity != null)
            {
                System.out.println(EntityUtils.toString(trainStatusEntity));
            }
        }

        // Display errors if they occur.
        catch (Exception e)
        {
            System.out.println(e.getMessage());
        }
    }
}
```

1. Zastąp wartości zaczynające się od `YOUR-` własnymi wartościami.

    |Informacje|Przeznaczenie|
    |--|--|
    |`YOUR-APP-ID`| Identyfikator aplikacji LUIS. |
    |`YOUR-AUTHORING-KEY`|Klucz tworzenia znaków 32.|
    |`YOUR-AUTHORING-ENDPOINT`| Twój punkt końcowy adresu URL tworzenia. Na przykład `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`. Nazwa zasobu jest ustawiana podczas tworzenia zasobu.|

    Przypisane klucze i zasoby są widoczne w portalu LUIS w sekcji Zarządzanie na stronie **zasoby platformy Azure** . Identyfikator aplikacji jest dostępny w tej samej sekcji zarządzania na stronie **Ustawienia aplikacji** .

1. Za pomocą wiersza polecenia w tym samym katalogu, w którym został utworzony `Model.java` plik, wprowadź następujące polecenie, aby skompilować plik Java:

    ::: zone pivot="client-operating-system-linux"

    ```console
    javac -cp ":lib/*" Model.java
    ```

    ::: zone-end

    ::: zone pivot="client-operating-system-macos"

    ```console
    javac -cp ":lib/*" Model.java
    ```

    ::: zone-end

    ::: zone pivot="client-operating-system-windows"

    ```console
    javac -cp ";lib/*" Model.java
    ```

    ::: zone-end

1. Uruchom aplikację Java z wiersza polecenia, wprowadzając następujący tekst w wierszu polecenia:

    ::: zone pivot="client-operating-system-linux"

    ```console
    java -cp ":lib/*" Model
    ```

    ::: zone-end

    ::: zone pivot="client-operating-system-macos"

    ```console
    java -cp ":lib/*" Model
    ```

    ::: zone-end

    ::: zone pivot="client-operating-system-windows"

    ```console
    java -cp ";lib/*" Model
    ```

    ::: zone-end

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start Usuń folder projektu z systemu plików.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dla aplikacji](../luis-concept-best-practices.md)