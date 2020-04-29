---
title: 'Pobierz model z wywołaniem REST w języku C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: bbb2ae0b10af795d71f0a78c045bec0c216ee378
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77368419"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Azure Language Understanding — tworzenie zasobu 32 klucz znaku i adres URL punktu końcowego tworzenia. Utwórz za pomocą [Azure Portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) lub [interfejsu wiersza polecenia platformy Azure](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Zaimportuj aplikację [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) z repozytorium GitHub-Services-Language-zrozumieć.
* Identyfikator aplikacji usługi LUIS dla zaimportowanej aplikacji TravelAgent. Identyfikator aplikacji jest wyświetlany na pulpicie nawigacyjnym aplikacji.
* Identyfikator wersji w aplikacji, w której odbierane są wypowiedzi. Domyślny identyfikator to „0.1”.
* Język programowania [Node.js](https://nodejs.org/)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Plik JSON z przykładowymi wypowiedziami

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="change-model-programmatically"></a>Programowo Zmień model

1. Utwórz nowy plik o nazwie `model.js`. Dodaj następujący kod:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');

    // 32 character key value
    const LUIS_authoringKey = "YOUR-KEY";

    // endpoint example: your-resource-name.api.cognitive.microsoft.com
    const LUIS_endpoint = "YOUR-ENDPOINT";
    const LUIS_appId = "YOUR-APP-ID";
    const LUIS_versionId = "0.1";
    const addUtterancesURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/examples`;
    const addTrainURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/train`;

    const utterances = [
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
          ];

    const main = async() =>{


        await addUtterance();
        await train("POST");
        await trainStatus("GET");

    }
    const addUtterance = async () => {

        const options = {
            uri: addUtterancesURI,
            method: 'POST',
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: utterances
        };

        const response = await requestpromise(options)
        console.log(response.body);
    }
    const train = async (verb) => {

        const options = {
            uri: addTrainURI,
            method: verb,
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: null // The body can be empty for a training request
        };

        const response = await requestpromise(options)
        console.log(response.body);
    }

    // MAIN
    main().then(() => console.log("done")).catch((err)=> console.log(err returned));
    ```

1. Zastąp wartości zaczynające `YOUR-` się od własnymi wartościami.

    |Informacje|Przeznaczenie|
    |--|--|
    |`YOUR-KEY`|Klucz tworzenia znaków 32.|
    |`YOUR-ENDPOINT`| Twój punkt końcowy adresu URL tworzenia. Na przykład `replace-with-your-resource-name.api.cognitive.microsoft.com`. Nazwa zasobu jest ustawiana podczas tworzenia zasobu.|
    |`YOUR-APP-ID`| Identyfikator aplikacji LUIS. |

    Przypisane klucze i zasoby są widoczne w portalu LUIS w sekcji Zarządzanie na stronie **zasoby platformy Azure** . Identyfikator aplikacji jest dostępny w tej samej sekcji zarządzania na stronie **Ustawienia aplikacji** .

1. Za pomocą wiersza polecenia w tym samym katalogu, w którym został utworzony plik, wprowadź następujące polecenie, aby uruchomić plik:

    ```console
    node model.js
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start Usuń plik z systemu plików.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dla aplikacji](../luis-concept-best-practices.md)