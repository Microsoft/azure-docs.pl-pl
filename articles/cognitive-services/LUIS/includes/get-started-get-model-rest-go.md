---
title: Pobierz model z wywołaniem REST w programie go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 8d180eeffdbc41db6fa0e636daf7702faad47fcc
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77368432"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Azure Language Understanding — tworzenie zasobu 32 klucz znaku i adres URL punktu końcowego tworzenia. Utwórz za pomocą [Azure Portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) lub [interfejsu wiersza polecenia platformy Azure](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Zaimportuj aplikację [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) z repozytorium GitHub-Services-Language-zrozumieć.
* Identyfikator aplikacji usługi LUIS dla zaimportowanej aplikacji TravelAgent. Identyfikator aplikacji jest wyświetlany na pulpicie nawigacyjnym aplikacji.
* Identyfikator wersji w aplikacji, w której odbierane są wypowiedzi. Domyślny identyfikator to „0.1”.
* Język programowania języka [go](https://golang.org/)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Plik JSON z przykładowymi wypowiedziami

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Programowo Zmień model

1. Utwórz nowy plik o nazwie `predict.go`. Dodaj następujący kod:

    ```go
    // dependencies
    package main
    import (
        "fmt"
        "net/http"
        "io/ioutil"
        "log"
        "strings"
    )

    // main function
    func main() {

        // NOTE: change to your app ID
        var appID = "YOUR-APP-ID"

        // NOTE: change to your authoring key
        var authoringKey = "YOUR-KEY"

        // NOTE: change to your authoring key's endpoint, for example, your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"

        var version = "0.1"

        var exampleUtterances = `
        [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ]
        `

        fmt.Println("add example utterances requested")
        addUtterance(authoringKey, appID, version, exampleUtterances, endpoint)

        fmt.Println("training selected")
        requestTraining(authoringKey, appID, version, endpoint)

        fmt.Println("training status selected")
        getTrainingStatus(authoringKey, appID, version, endpoint)
    }

    // get utterances from file and add to model
    func addUtterance(authoringKey string, appID string,  version string, labeledExampleUtterances string, endpoint string){

        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/examples", endpoint, appID, version)

        httpRequest("POST", authoringUrl, authoringKey, labeledExampleUtterances)
    }
    func requestTraining(authoringKey string, appID string,  version string, endpoint string){

        trainApp("POST", authoringKey, appID, version, endpoint)
    }
    func trainApp(httpVerb string, authoringKey string, appID string,  version string, endpoint string){

        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/train", endpoint, appID, version)

        httpRequest(httpVerb,authoringUrl, authoringKey, "")
    }
    func getTrainingStatus(authoringKey string, appID string, version string, endpoint string){

        trainApp("GET", authoringKey, appID, version, endpoint)
    }
    // generic HTTP request
    // includes setting header with authoring key
    func httpRequest(httpVerb string, url string, authoringKey string, body string){

        client := &http.Client{}

        request, err := http.NewRequest(httpVerb, url, strings.NewReader(body))
        request.Header.Add("Ocp-Apim-Subscription-Key", authoringKey)

        fmt.Println("body")
        fmt.Println(body)

        response, err := client.Do(request)
        if err != nil {
            log.Fatal(err)
        } else {
            defer response.Body.Close()
            contents, err := ioutil.ReadAll(response.Body)
            if err != nil {
                log.Fatal(err)
            }
            fmt.Println("   ", response.StatusCode)
            fmt.Println(string(contents))
        }
    }
    ```

1. Zastąp wartości zaczynające `YOUR-` się od własnymi wartościami.

    |Informacje|Przeznaczenie|
    |--|--|
    |`YOUR-KEY`|Klucz tworzenia znaków 32.|
    |`YOUR-ENDPOINT`| Twój punkt końcowy adresu URL tworzenia. Na przykład `replace-with-your-resource-name.api.cognitive.microsoft.com`. Nazwa zasobu jest ustawiana podczas tworzenia zasobu.|
    |`YOUR-APP-ID`| Identyfikator aplikacji LUIS. |

    Przypisane klucze i zasoby są widoczne w portalu LUIS w sekcji Zarządzanie na stronie **zasoby platformy Azure** . Identyfikator aplikacji jest dostępny w tej samej sekcji zarządzania na stronie **Ustawienia aplikacji** .

1. Za pomocą wiersza polecenia w tym samym katalogu, w którym został utworzony plik, wprowadź następujące polecenie, aby skompilować plik go:

    ```console
    go build model.go
    ```

1. Uruchom aplikację języka Go z wiersza polecenia, wprowadzając następujący tekst w wierszu polecenia:

    ```console
    go run model.go
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start Usuń plik z systemu plików.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dla aplikacji](../luis-concept-best-practices.md)