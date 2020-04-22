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
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732077"
---
Użyj biblioteki klienta środowiska wykonawczego language understanding (LUIS) dla node.js, aby:

* Przewidywanie według szczeliny
* Przewidywanie według wersji

[Przykłady](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | [kodu źródłowego biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [Runtime Package (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | dokumentacji[referencyjnej](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_prediction.js)

## <a name="prerequisites"></a>Wymagania wstępne

* Zasób środowiska wykonawczego opisu języka: [utwórz go w witrynie Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)
* Identyfikator aplikacji usługi LUIS — użyj publicznego identyfikatora aplikacji IoT . `df67dcdb-c37d-46af-88e1-8b97951ca1c2` Kwerenda użytkownika używana w kodzie szybkiego startu jest specyficzna dla tej aplikacji.

## <a name="setting-up"></a>Konfigurowanie

### <a name="get-your-language-understanding-luis-runtime-key"></a>Pobierz klucz środowiska uruchomieniowego language understanding (LUIS)

Pobierz [klucz środowiska wykonawczego,](../luis-how-to-azure-subscription.md) tworząc zasób środowiska uruchomieniowego usługi LUIS. Zachowaj klucz i punkt końcowy klucza dla następnego kroku.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-javascript-nodejs-file"></a>Tworzenie nowego pliku JavaScript (Node.js)

Utwórz nowy plik JavaScript w preferowanym `luis_prediction.js`edytorze lub IDE o nazwie .

### <a name="install-the-npm-library-for-the-luis-runtime"></a>Instalowanie biblioteki NPM dla środowiska wykonawczego usługi LUIS

W katalogu aplikacji zainstaluj zależności za pomocą następującego polecenia:

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Model obiektu

Klient tworzenia języka (LUIS) jest obiektem [LUISAuthoringClient,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) który uwierzytelnia się na platformie Azure, który zawiera klucz autora.

Po utworzeniu klienta użyj tego klienta, aby uzyskać dostęp do funkcji, w tym:

* [Przewidywanie](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) `staging` `production` przez lub szczeliny
* [Przewidywanie według wersji](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta przewidywania języka (LUIS):

* [Przewidywanie według szczeliny](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Dodawanie zależności

W katalogu projektu otwórz `luis_prediction.js` plik w preferowanym edytorze lub IDE. Dodaj następujące zależności:

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

1. Tworzenie zmiennych dla własnych wymaganych informacji usługi LUIS:

    Dodaj zmienne, aby zarządzać kluczem przewidywania `LUIS_RUNTIME_KEY`pobranym ze zmiennej środowiskowej o nazwie . Jeśli utworzono zmienną środowiskową po uruchomieniu aplikacji, edytor, IDE lub powłoki uruchomionej będzie musiał zostać zamknięty i ponownie załadowany, aby uzyskać dostęp do zmiennej. Metody zostaną utworzone później.

    Utwórz zmienną do `LUIS_RUNTIME_ENDPOINT`przechowywania nazwy zasobu .

    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Variables)]

1. Utwórz zmienną dla identyfikatora aplikacji `LUIS_APP_ID`jako zmienną środowiskową o nazwie . Ustaw zmienną środowiskową na publiczną **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** aplikację IoT, . Utwórz zmienną, `production` aby ustawić opublikowane gniazdo.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=OtherVariables)]


1. Utwórz obiekt msRest.ApiKeyCredentials za pomocą klucza i użyj go z punktem końcowym do utworzenia [usługi LUIS. Obiekt LUISRuntimeClient.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest)

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Pobierz przewidywanie ze środowiska wykonawczego

Dodaj następującą metodę, aby utworzyć żądanie do środowiska uruchomieniowego przewidywanie.

Wypowiedź użytkownika jest częścią [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest) obiektu.

**[LuisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** metoda wymaga kilku parametrów, takich jak identyfikator aplikacji, nazwa gniazda i obiekt żądania przewidywania do spełnienia żądania. Inne opcje, takie jak pełne, pokaż wszystkie intencje i dziennik są opcjonalne.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Główny kod do przewidywania

Użyj następującej metody głównej, aby powiązać zmienne i metody razem, aby uzyskać przewidywanie.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za `node luis_prediction.js` pomocą polecenia z katalogu aplikacji.

```console
node luis_prediction.js
```

Wynik prognozowania zwraca obiekt JSON:

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

Po zakończeniu z prognoz, oczyścić pracę z tego przewodnika Szybki start, usuwając plik i jego podkatalogów.
