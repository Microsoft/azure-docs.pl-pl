---
title: Pobierz zamierzenia z wywołaniem REST w programie Node. js
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 3bef3cbb321465893b3a05242f0d72d77d091d6b
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733316"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Język programowania [Node.js](https://nodejs.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Identyfikator aplikacji LUIS — Użyj publicznego identyfikatora aplikacji IoT `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. Zapytanie użytkownika używane w kodzie szybkiego startu jest specyficzne dla tej aplikacji.

## <a name="create-luis-runtime-key-for-predictions"></a>Utwórz klucz środowiska uruchomieniowego LUIS dla prognoz

1. Zaloguj się do [Azure Portal](https://portal.azure.com)
1. Kliknij przycisk [utwórz **Language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Wprowadź wszystkie wymagane ustawienia dla klucza **czasu wykonywania** :

    |Ustawienie|Wartość|
    |--|--|
    |Nazwa|Wymagana nazwa (2-64 znaków)|
    |Subskrypcja|Wybierz odpowiednią subskrypcję|
    |Lokalizacja|Wybierz dowolną lokalizację w pobliżu i dostępną|
    |Warstwa cenowa|`F0`— minimalna warstwa cenowa|
    |Grupa zasobów|Wybierz dostępną grupę zasobów|

1. Kliknij przycisk **Utwórz** i poczekaj na utworzenie zasobu. Po jego utworzeniu przejdź do strony zasobów.
1. Zbieranie skonfigurowanych `endpoint` i `key`.

## <a name="get-intent-programmatically"></a>Pobieranie intencji w sposób programistyczny

Użyj środowiska Node. js, aby wykonać zapytanie dotyczące [punktu końcowego przewidywania](https://aka.ms/luis-apim-v3-prediction) i uzyskać wynik przewidywania.

1. Skopiuj następujący fragment kodu do pliku o nazwie `predict.js`:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    var querystring = require('querystring');

    // Analyze text
    //
    getPrediction = async () => {

        // YOUR-KEY - Language Understanding runtime key
        var endpointKey = "YOUR-KEY";

        // YOUR-ENDPOINT Language Understanding endpoint URL, an example is your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT";

        // Set the LUIS_APP_ID environment variable
        // to df67dcdb-c37d-46af-88e1-8b97951ca1c2, which is the ID
        // of a public sample application.
        var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

        var utterance = "turn on all lights";

        // Create query string
        var queryParams = {
            "show-all-intents": true,
            "verbose":  true,
            "query": utterance,
            "subscription-key": endpointKey
        }

        // append query string to endpoint URL
        var URI = `https://${endpoint}/luis/prediction/v3.0/apps/${appId}/slots/production/predict?${querystring.stringify(queryParams)}`

        // HTTP Request
        const response = await requestpromise(URI);

        // HTTP Response
        console.log(response);

    }

    // Pass an utterance to the sample LUIS app
    getPrediction().then(()=>console.log("done")).catch((err)=>console.log(err));
    ```

1. Zastąp `YOUR-KEY` wartości `YOUR-ENDPOINT` i własnymi **kluczami** i punktem końcowym przewidywania.

    |Informacje|Przeznaczenie|
    |--|--|
    |`YOUR-KEY`|Klucz **środowiska uruchomieniowego** przewidywania znaków 32.|
    |`YOUR-ENDPOINT`| Punkt końcowy adresu URL przewidywania. Na przykład `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Zainstaluj zależności `request`, `request-promise`i `querystring` z tym poleceniem:

    ```console
    npm install request request-promise querystring
    ```

1. Uruchom aplikację za pomocą tego polecenia:

    ```console
    node predict.js
    ```

 1. Przejrzyj odpowiedź przewidywania zwracaną jako kod JSON:

    ```console
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
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

Po zakończeniu pracy z tym przewodnikiem Szybki Start Usuń plik z systemu plików.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie wyrażenia długości i uczenie](../get-started-get-model-rest-apis.md)