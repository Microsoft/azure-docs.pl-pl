---
title: 'Szybki start: zapytanie o przewidywanie za pomocą przeglądarki — usługa LUIS'
description: W tym przewodniku Szybki start użyj dostępnej publicznej aplikacji usługi LUIS, aby określić intencję użytkownika na podstawie tekstu konwersacji w przeglądarce.
ms.topic: quickstart
ms.date: 04/21/2020
ms.openlocfilehash: 5ba86882ebf3cb538ad6b865382342fcbd43d27c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769982"
---
# <a name="quickstart-query-prediction-runtime-with-user-text"></a>Szybki start: środowisko uruchomieniowe prognozowania kwerend z tekstem użytkownika

Aby zrozumieć, co zwraca punkt końcowy przewidywania usługi LUIS, wyświetl wynik przewidywania w przeglądarce internetowej.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać zapytanie do aplikacji publicznej, potrzebujesz:

* Informacje o zasobach rozumienia języka (LUIS):
    * **Klucz przewidywania** — który można uzyskać z [portalu usługi LUIS](https://www.luis.ai/). Jeśli nie masz jeszcze subskrypcji na utworzenie klucza, możesz zarejestrować się na [bezpłatne konto](https://azure.microsoft.com/free/).
    * **Przewidywanie poddomeny punktu końcowego** — poddomena jest również **nazwą** zasobu usługi LUIS.
* Identyfikator aplikacji usługi LUIS — użyj publicznego identyfikatora aplikacji IoT . `df67dcdb-c37d-46af-88e1-8b97951ca1c2` Kwerenda użytkownika używana w kodzie szybkiego startu jest specyficzna dla tej aplikacji.

## <a name="use-the-browser-to-see-predictions"></a>Użyj przeglądarki, aby zobaczyć prognozy

1. Otwórz przeglądarkę internetową.
1. Użyj pełnych adresów `YOUR-KEY` URL poniżej, zastępując własnym kluczem przewidywania usługi LUIS. Żądania są żądania GET i obejmują autoryzację, z kluczem przewidywania usługi LUIS, jako parametr ciągu zapytania.

    #### <a name="v3-prediction-request"></a>[Żądanie przewidywania wersji 3](#tab/V3-1-1)


    Format adresu URL V3 dla żądania punktu końcowego **GET** (przez szczeliny) jest:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY
    `

    #### <a name="v2-prediction-request"></a>[Żądanie przewidywania wersji 2](#tab/V2-1-2)

    Format adresu URL V2 dla żądania punktu końcowego **GET** jest:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-LUIS-PREDICTION-KEY&q=turn on all lights
    `

1. Wklej adres URL w oknie przeglądarki, a następnie naciśnij klawisz Enter. W przeglądarce zostanie wyświetlony wynik w formacie JSON, który wskazuje, że usługa LUIS wykryła intencję `HomeAutomation.TurnOn` jako główną intencję oraz jednostkę `HomeAutomation.Operation` o wartości `on`.

    #### <a name="v3-prediction-response"></a>[Odpowiedź przewidywania V3](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-response"></a>[Odpowiedź przewidywania V2](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. Aby wyświetlić wszystkie intencje, dodaj odpowiedni parametr ciągu kwerendy.

    #### <a name="v3-prediction-endpoint"></a>[Punkt końcowy przewidywania V3](#tab/V3-3-1)

    Dodaj `show-all-intents=true` na końcu ciąg zapytania, aby **wyświetlić wszystkie intencje:**

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
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
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpoint"></a>[Punkt końcowy przewidywania wersji V2](#tab/V2)

    Dodaj `verbose=true` na końcu ciąg zapytania, aby **wyświetlić wszystkie intencje:**

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach:
* [Punkt końcowy przewidywania V3](luis-migration-api-v3.md)
* [Niestandardowe poddomeny](../cognitive-services-custom-subdomains.md)

> [!div class="nextstepaction"]
> [Tworzenie aplikacji w portalu usługi LUIS](get-started-portal-build-app.md)
