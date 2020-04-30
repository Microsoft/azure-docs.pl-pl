---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 9c15e4217c5331346c5a95329bae7e2a4f0e0841
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732077"
---
Użyj biblioteki klienta środowiska uruchomieniowego Language Understanding (LUIS) dla języka Node. js, aby:

* Prognozowanie według miejsca
* Prognozowanie według wersji

[Reference documentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | [Przykłady](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_prediction.js) pakietu[kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | źródłowego[(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | biblioteki dokumentacji

## <a name="prerequisites"></a>Wymagania wstępne

* Zasób środowiska uruchomieniowego Language Understanding: [Utwórz go w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)
* Identyfikator aplikacji LUIS — Użyj publicznego identyfikatora aplikacji IoT `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. Zapytanie użytkownika używane w kodzie szybkiego startu jest specyficzne dla tej aplikacji.

## <a name="setting-up"></a>Konfigurowanie

### <a name="get-your-language-understanding-luis-runtime-key"></a>Pobierz klucz środowiska uruchomieniowego Language Understanding (LUIS)

Pobierz swój [klucz środowiska uruchomieniowego](../luis-how-to-azure-subscription.md) , tworząc zasób środowiska uruchomieniowego Luis. Zachowaj klucz i punkt końcowy klucza dla kolejnego kroku.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-javascript-nodejs-file"></a>Utwórz nowy plik JavaScript (Node. js)

Utwórz nowy plik JavaScript w preferowanym edytorze lub środowisku IDE o nazwie `luis_prediction.js`.

### <a name="install-the-npm-library-for-the-luis-runtime"></a>Zainstaluj bibliotekę NPM dla środowiska uruchomieniowego LUIS

W katalogu aplikacji Zainstaluj zależności przy użyciu następującego polecenia:

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Model obiektów

Klient tworzenia Language Understanding (LUIS) to obiekt [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) , który jest uwierzytelniany na platformie Azure, który zawiera klucz tworzenia.

Po utworzeniu klienta Użyj tego klienta, aby uzyskać dostęp do funkcji, w tym:

* [Przewidywania](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) według `staging` lub `production` gniazdo
* [Prognozowanie według wersji](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Przykłady kodu

W tych fragmentach kodu przedstawiono sposób wykonywania następujących czynności Language Understanding w ramach biblioteki klienta LUIS:

* [Prognozowanie według miejsca](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Dodawanie zależności

W katalogu projektu Otwórz `luis_prediction.js` plik w preferowanym edytorze lub w środowisku IDE. Dodaj następujące zależności:

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

1. Utwórz zmienne dla własnych wymaganych informacji LUIS:

    Dodaj zmienne, aby zarządzać kluczem predykcyjnym ściąganym ze zmiennej środowiskowej `LUIS_RUNTIME_KEY`o nazwie. Jeśli zmienna środowiskowa została utworzona po uruchomieniu aplikacji, należy zamknąć i ponownie załadować Edytor, środowisko IDE lub powłokę, aby uzyskać dostęp do zmiennej. Metody zostaną utworzone później.

    Utwórz zmienną do przechowywania nazwy `LUIS_RUNTIME_ENDPOINT`zasobu.

    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Variables)]

1. Utwórz zmienną dla identyfikatora aplikacji jako zmienną środowiskową o nazwie `LUIS_APP_ID`. Ustaw zmienną środowiskową na publiczną aplikację IoT **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Utwórz zmienną, aby ustawić `production` opublikowane gniazdo.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=OtherVariables)]


1. Utwórz obiekt msRest. ApiKeyCredentials z kluczem i użyj go w punkcie końcowym, aby utworzyć [Luis. Obiekt LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) .

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Pobierz prognozowanie z środowiska uruchomieniowego

Dodaj następującą metodę, aby utworzyć żądanie do środowiska uruchomieniowego przewidywania.

Wypowiedź użytkownika jest częścią obiektu [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest) .

Metoda **[luisRuntimeClient. przewidywania. getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** wymaga kilku parametrów, takich jak identyfikator aplikacji, nazwa gniazda i obiekt żądania prognozowania w celu spełnienia żądania. Inne opcje, takie jak verbose, pokazują wszystkie intencje i dzienniki są opcjonalne.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Kod główny do prognozowania

Użyj następującej metody Main, aby powiązać zmienne i metody w celu uzyskania prognozowania.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node luis_prediction.js` polecenia z katalogu aplikacji.

```console
node luis_prediction.js
```

Wynik przewidywania zwraca obiekt JSON:

```console
{
   "query":"turn on all lights",
   "prediction":{
      "topIntent":"HomeAutomation.TurnOn",
      "intents":{
         "HomeAutomation.TurnOn":{
            "score":0.5375382
         },
         "None":{
            "score":0.08687421
         },
         "HomeAutomation.TurnOff":{
            "score":0.0207554
         }
      },
      "entities":{
         "HomeAutomation.Operation":[
            "on"
         ],
         "$instance":{
            "HomeAutomation.Operation":[
               {
                  "type":"HomeAutomation.Operation",
                  "text":"on",
                  "startIndex":5,
                  "length":2,
                  "score":0.724984169,
                  "modelTypeId":-1,
                  "modelType":"Unknown",
                  "recognitionSources":[
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

Po wykonaniu prognoz Wyczyść prace z tego przewodnika Szybki Start, usuwając plik i jego podkatalogi.
