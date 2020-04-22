---
title: Uzyskaj intencję dzięki połączeniu REST w go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: d9af9f788e2309cdf687e0a13b77220c150a0e1e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733309"
---
## <a name="prerequisites"></a>Wymagania wstępne

* [Przejdź](https://golang.org/) do języka programowania
* [Visual Studio Code](https://code.visualstudio.com/)
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

Użyj Przejdź do kwerendy [punktu końcowego przewidywania](https://aka.ms/luis-apim-v3-prediction) i uzyskać wynik prognozowania.

1. Utwórz nowy plik o nazwie `predict.go`. Dodaj następujący kod:

    ```go
    package main

    /* Do dependencies */
    import (
        "fmt"
        "net/http"
        "net/url"
        "io/ioutil"
        "log"
    )
    func main() {

        // public app
        var appID = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"

        // utterance for public app
        var utterance = "turn on all lights"

        // YOUR-KEY - your **Runtime** key
        var endpointKey = "YOUR-KEY"

        // YOUR-ENDPOINT - example is your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"

        endpointPrediction(appID, endpointKey, endpoint, utterance)
    }
    func endpointPrediction(appID string, endpointKey string, endpoint string, utterance string) {

        var endpointUrl = fmt.Sprintf("https://%s/luis/prediction/v3.0/apps/%s/slots/production/predict?subscription-key=%s&verbose=true&show-all-intents=true&query=%s", endpoint, appID, endpointKey, url.QueryEscape(utterance))

        response, err := http.Get(endpointUrl)

        if err!=nil {
            // handle error
            fmt.Println("error from Get")
            log.Fatal(err)
        }

        response2, err2 := ioutil.ReadAll(response.Body)

        if err2!=nil {
            // handle error
            fmt.Println("error from ReadAll")
            log.Fatal(err2)
        }

        fmt.Println("response")
        fmt.Println(string(response2))
    }
    ```

1. Zastąp `YOUR-KEY` i `YOUR-ENDPOINT` wartości z własnym kluczem środowiska **wykonawczego** przewidywania i punktu końcowego.

    |Informacje|Przeznaczenie|
    |--|--|
    |`YOUR-KEY`|Twój klucz **32-znakowy przewidywanie środowiska uruchomieniowego.**|
    |`YOUR-ENDPOINT`| Punkt końcowy adresu URL przewidywania. Na przykład `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. W wierszu polecenia w tym samym katalogu, w którym utworzono plik, wprowadź następujące polecenie, aby skompilować plik Go:

    ```console
    go build predict.go
    ```

1. Uruchom aplikację języka Go z wiersza polecenia, wprowadzając następujący tekst w wierszu polecenia:

    ```console
    go run predict.go
    ```

    Odpowiedź w wierszu polecenia jest następująca:

    ```console
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value a7b206911f714e71a1ddae36928a61cc
    endpoint has value westus2.api.cognitive.microsoft.com
    utterance has value turn on all lights
    response
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    JSON sformatowany pod kątem czytelności:

    ```json
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
> [Dodawanie wypowiedzi i szkolenie](../get-started-get-model-rest-apis.md)