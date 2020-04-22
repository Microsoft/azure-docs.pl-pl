---
title: Uzyskaj intencję dzięki wywołaniu REST w pythonie
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 2307ef6ea8940a3b3ddfb8c7539f4f809dc4c52c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733277"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko [Python 3.6](https://www.python.org/downloads/) lub nowsze.
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

## <a name="get-intent-from-the-prediction-endpoint"></a>Uzyskaj intencję z punktu końcowego przewidywania

Użyj Języka Python, aby zbadać [punkt końcowy prognozowania](https://aka.ms/luis-apim-v3-prediction) i uzyskać wynik prognozowania.

1. Skopiuj ten fragment kodu `predict.py`do pliku o nazwie:

    ```python
    ########### Python 3.6 #############
    import requests

    try:

        key = 'YOUR-KEY' # your Runtime key
        endpoint = 'YOUR-ENDPOINT' # such as 'your-resource-name.api.cognitive.microsoft.com'
        appId = 'df67dcdb-c37d-46af-88e1-8b97951ca1c2'
        utterance = 'turn on all lights'

        headers = {
        }

        params ={
            'query': utterance,
            'timezoneOffset': '0',
            'verbose': 'true',
            'show-all-intents': 'true',
            'spellCheck': 'false',
            'staging': 'false',
            'subscription-key': key
        }

        r = requests.get(f'https://{endpoint}/luis/prediction/v3.0/apps/{appId}/slots/production/predict',headers=headers, params=params)
        print(r.json())

    except Exception as e:
        print(f'{e}')
    ```

1. Zastąp `YOUR-KEY` i `YOUR-ENDPOINT` wartości z własnym kluczem środowiska **wykonawczego** przewidywania i punktu końcowego.

    |Informacje|Przeznaczenie|
    |--|--|
    |`YOUR-KEY`|Twój klucz **32-znakowy przewidywanie środowiska uruchomieniowego.**|
    |`YOUR-ENDPOINT`| Punkt końcowy adresu URL przewidywania. Na przykład `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Zainstaluj `requests` zależność. Służy do wysyłania żądań HTTP:

    ```console
    pip install requests
    ```

1. Uruchom skrypt za pomocą tego polecenia konsoli:

    ```console
    python predict.py
    ```

1. Przejrzyj odpowiedź przewidywania, która jest zwracana jako JSON:

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    Oto odpowiedź JSON sformatowany dla czytelności:

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
> [Dodawanie wypowiedzi i szkolenie](../get-started-get-model-rest-apis.md)