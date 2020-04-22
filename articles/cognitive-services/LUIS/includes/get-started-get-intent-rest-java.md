---
title: Uzyskaj intencję z połączeniem REST w języku Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: d59b7ebd1376d0bee10482cfe5faac1c53d1bde0
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733250"
---
## <a name="prerequisites"></a>Wymagania wstępne

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/) lub twój ulubiony IDE
* Identyfikator aplikacji usługi LUIS — użyj publicznego identyfikatora aplikacji IoT . `df67dcdb-c37d-46af-88e1-8b97951ca1c2` Kwerenda użytkownika używana w kodzie szybkiego startu jest specyficzna dla tej aplikacji.

## <a name="create-luis-runtime-key-for-predictions"></a>Tworzenie klucza środowiska uruchomieniowego usługi LUIS dla prognoz

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)
1. Kliknij [pozycję Utwórz **opis języka** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Wprowadź wszystkie wymagane ustawienia dla **klucza środowiska wykonawczego:**

    |Ustawienie|Wartość|
    |--|--|
    |Nazwa|Żądana nazwa (2-64 znaki)|
    |Subskrypcja|Wybierz odpowiednią subskrypcję|
    |Lokalizacja|Wybierz dowolną lokalizację w pobliżu i do dyspozycji|
    |Warstwa cenowa|`F0`- minimalny poziom cenowy|
    |Grupa zasobów|Wybieranie dostępnej grupy zasobów|

1. Kliknij **przycisk Utwórz** i poczekaj na utworzenie zasobu. Po jego utworzeniu przejdź do strony zasobu.
1. Zbieraj `endpoint` skonfigurowane `key`i .

## <a name="get-intent-programmatically"></a>Pobieranie intencji w sposób programistyczny

Użyj języka Java, aby zbadać [punkt końcowy prognozowania](https://aka.ms/luis-apim-v3-prediction) i uzyskać wynik przewidywania.

1. Zrób podkatalog `lib` o nazwie i skopiuj w następujących libs java:

    * [commons-logging-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. Skopiuj następujący kod, aby utworzyć klasę w pliku o nazwie `Predict.java`:

    ```java
    import java.io.*;
    import java.net.URI;
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;

    public class Predict {

        public static void main(String[] args)
        {
            HttpClient httpclient = HttpClients.createDefault();

            try
            {

                // The ID of a public sample LUIS app that recognizes intents for turning on and off lights
                String AppId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

                // Add your prediction Runtime key
                String Key = "YOUR-KEY";

                // Add your endpoint, example is your-resource-name.api.cognitive.microsoft.com
                String Endpoint = "YOUR-ENDPOINT";

                String Utterance = "turn on all lights";

                // Begin endpoint URL string building
                URIBuilder endpointURLbuilder = new URIBuilder("https://" + Endpoint + "/luis/prediction/v3.0/apps/" + AppId + "/slots/production/predict?");

                // query string params
                endpointURLbuilder.setParameter("query", Utterance);
                endpointURLbuilder.setParameter("subscription-key", Key);
                endpointURLbuilder.setParameter("show-all-intents", "true");
                endpointURLbuilder.setParameter("verbose", "true");

                // create URL from string
                URI endpointURL = endpointURLbuilder.build();

                // create HTTP object from URL
                HttpGet request = new HttpGet(endpointURL);

                // access LUIS endpoint - analyze text
                HttpResponse response = httpclient.execute(request);

                // get response
                HttpEntity entity = response.getEntity();


                if (entity != null)
                {
                    System.out.println(EntityUtils.toString(entity));
                }
            }

            catch (Exception e)
            {
                System.out.println(e.getMessage());
            }
        }
    }
    ```

1. Zastąp `YOUR-KEY` i `YOUR-ENDPOINT` wartości z własnym kluczem środowiska **wykonawczego** przewidywania i punktu końcowego.

    |Informacje|Przeznaczenie|
    |--|--|
    |`YOUR-KEY`|Twój klucz **32-znakowy przewidywanie środowiska uruchomieniowego.**|
    |`YOUR-ENDPOINT`| Punkt końcowy adresu URL przewidywania. Na przykład `replace-with-your-resource-name.api.cognitive.microsoft.com`.|


1. Skompiluj program java z wiersza polecenia:

    ```console
    javac -cp ":lib/*" Predict.java
    ```

1. Uruchom program java z wiersza polecenia:

    ```console
    java -cp ":lib/*" Predict
    ```

1. Przejrzyj odpowiedź przewidywania, która jest zwracana jako JSON:

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    Odpowiedź JSON sformatowana pod kątem czytelności:

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu tego przewodnika Szybki start usuń plik z systemu plików.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie wypowiedzi i trenowanie za pomocą języka Java](../get-started-get-model-rest-apis.md)