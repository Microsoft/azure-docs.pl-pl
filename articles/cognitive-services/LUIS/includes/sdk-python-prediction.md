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
ms.openlocfilehash: eabec50f57785bde6760db053eb3b12f6f5b6452
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732072"
---
Użyj biblioteki klienta przewidywania zrozumienia języka (LUIS) dla języka Python, aby:

* Uzyskaj przewidywanie według gniazda
* Pobierz przewidywanie według wersji

[Dokumentacja](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [referencyjna Przykłady pakietu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | uruchamiania przewidywania kodu źródłowego biblioteki[ biblioteki](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS) [(PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/)  | 

## <a name="prerequisites"></a>Wymagania wstępne

* Konto portalu language understanding (LUIS) — [utwórz je bezpłatnie](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)
* Identyfikator aplikacji usługi LUIS — użyj publicznego identyfikatora aplikacji IoT . `df67dcdb-c37d-46af-88e1-8b97951ca1c2` Kwerenda użytkownika używana w kodzie szybkiego startu jest specyficzna dla tej aplikacji.

## <a name="setting-up"></a>Konfigurowanie

### <a name="get-your-language-understanding-luis-runtime-key"></a>Pobierz klucz środowiska uruchomieniowego language understanding (LUIS)

Pobierz [klucz środowiska wykonawczego,](../luis-how-to-azure-subscription.md) tworząc zasób środowiska uruchomieniowego usługi LUIS. Zachowaj klucz i punkt końcowy klucza dla następnego kroku.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-python-file"></a>Tworzenie nowego pliku języka Python

Utwórz nowy plik języka Python w `prediction_quickstart.py`preferowanym edytorze lub IDE o nazwie .

### <a name="install-the-sdk"></a>Instalacja zestawu SDK

W katalogu aplikacji zainstaluj bibliotekę klienta środowiska uruchomieniowego środowiska wykonawczego (Language Understanding) (LUIS) dla języka Python za pomocą następującego polecenia:

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Model obiektu

Klient środowiska uruchomieniowego przewidywania języka (LUIS) jest obiektem [LUISRuntimeClient,](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python) który uwierzytelnia się na platformie Azure, który zawiera klucz zasobu.

Po utworzeniu klienta użyj tego klienta, aby uzyskać dostęp do funkcji, w tym:

* Przewidywanie przez [miejsce przemieszczania lub produkcji](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Przewidywanie według [wersji](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta przewidywania języka (LUIS) dla języka Python:

* [Przewidywanie według szczeliny](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Dodawanie zależności

W katalogu projektu otwórz `prediction_quickstart.py` plik w preferowanym edytorze lub IDE. Dodaj następujące zależności:

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>Uwierzytelnij klienta

1. Tworzenie zmiennych dla własnych wymaganych informacji usługi LUIS:

    Dodaj zmienne, aby zarządzać kluczem przewidywania `LUIS_RUNTIME_KEY`pobranym ze zmiennej środowiskowej o nazwie . Jeśli utworzono zmienną środowiskową po uruchomieniu aplikacji, edytor, IDE lub powłoki uruchomionej będzie musiał zostać zamknięty i ponownie załadowany, aby uzyskać dostęp do zmiennej. Metody zostaną utworzone później.

    Utwórz zmienną do `LUIS_RUNTIME_ENDPOINT`przechowywania nazwy zasobu .

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=AuthorizationVariables)]

1. Utwórz zmienną dla identyfikatora aplikacji `LUIS_APP_ID`jako zmienną środowiskową o nazwie . Ustaw zmienną środowiskową na publiczną **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** aplikację IoT, . Utwórz zmienną, `production` aby ustawić opublikowane gniazdo.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=OtherVariables)]


1. Utwórz obiekt poświadczeń za pomocą klucza i użyj go z punktemhttps://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python() końcowym, aby utworzyć obiekt [LUISRuntimeClientConfiguration].

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>Pobierz przewidywanie ze środowiska wykonawczego

Dodaj następującą metodę, aby utworzyć żądanie do środowiska uruchomieniowego przewidywanie.

Wypowiedź użytkownika jest częścią [obiektu prediction_request.](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python)

Metoda **[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** wymaga kilku parametrów, takich jak identyfikator aplikacji, nazwa gniazda i obiekt żądania przewidywania, aby spełnić żądanie. Inne opcje, takie jak pełne, pokaż wszystkie intencje i dziennik są opcjonalne. Żądanie zwraca [PredictionResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python) obiektu.

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>Główny kod do przewidywania

Użyj następującej metody głównej, aby powiązać zmienne i metody razem, aby uzyskać przewidywanie.

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za `python prediction_quickstart.py` pomocą polecenia z katalogu aplikacji.

```console
python prediction_quickstart.py
```

Konsola szybkiego startu wyświetla dane wyjściowe:

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu z prognoz, oczyścić pracę z tego przewodnika Szybki start, usuwając plik i jego podkatalogów.
