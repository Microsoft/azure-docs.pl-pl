---
title: 'Szybki Start: uzyskiwanie modelu za pomocą interfejsów API REST — LUIS'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start interfejsu API REST Użyj Zwinięciea, aby dodać wyrażenia długości i szkolić model.
services: cognitive-services
author: nitinme
manager: diberry
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 06/19/2020
ms.author: nitinme
ms.openlocfilehash: e5cf3160e6592a48e3a81480480ad8f01cc6af3a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "86206252"
---
# <a name="quickstart-use-curl-and-rest-to-change-model"></a>Szybki Start: używanie Zwinięciea i REST do zmiany modelu

W tym przewodniku szybki start pokazano, jak dodać przykład wyrażenia długości do aplikacji Pizza i szkolić aplikację. Przykładowe wypowiedzi to tekst z rozmowami użytkownika mapowany na intencje. Poprzez zapewnienie przykładowych wypowiedzi dla intencji możesz nauczyć aplikację LUIS, jakim intencjom odpowiada rodzaj tekstu podanego przez użytkownika.

Postępując zgodnie z tym przewodnikiem Szybki Start, będziesz wykonywał trzy wywołania REST w sekwencji.

- Najpierw przekażesz partię przykładowej wyrażenia długości, która będzie używana do uczenia modelu aplikacji Pizza, przy użyciu wywołania [Dodaj etykiety](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09) w ramach usługi Batch.
- Następnie rozpocznie się sesja szkoleniowa dla aplikacji Pizza przy użyciu wywołania [wersji aplikacji szkolenia REST](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) .
- Na koniec zostanie wyświetlony stan sesji szkoleniowej dla aplikacji Pizza przy użyciu połączenia [stanu usługi REST Get Version](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c46) .

[Dokumentacja referencyjna](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)

## <a name="prerequisites"></a>Wymagania wstępne

* Bezpłatne konto [Luis](https://www.luis.ai) .
* Edytor tekstu, taki jak [Visual Studio Code](https://code.visualstudio.com/).
* Zwinięcie programu wiersza polecenia. Program zwinięcie jest już zainstalowany w macOS, większości dystrybucji systemu Linux i w systemie Windows 10 Build 1803 i nowszych.

    Jeśli zachodzi potrzeba zainstalowania zawieszania, można pobrać zwinięcie ze [strony pobierania plików](https://curl.haxx.se/download.html).

## <a name="create-pizza-app"></a>Tworzenie aplikacji Pizza

[!INCLUDE [Create pizza app](includes/get-started-get-model-create-pizza-app.md)]

## <a name="create-a-json-file-to-train-the-pizza-app"></a>Tworzenie pliku JSON do uczenia aplikacji Pizza

Aby utworzyć plik JSON z trzema przykładowymi wyrażenia długości, Zapisz następujące dane JSON do pliku o nazwie `ExampleUtterances.JSON` :

```JSON
[
  {
    "text": "order a pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "order a large pepperoni pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 14,
        "endCharIndex": 28
      },
      {
        "entityName": "Size",
        "startCharIndex": 8,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "I want two large pepperoni pizzas on thin crust",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 17,
        "endCharIndex": 32
      },
      {
        "entityName": "Size",
        "startCharIndex": 11,
        "endCharIndex": 15
      },
      {
        "entityName": "Quantity",
        "startCharIndex": 7,
        "endCharIndex": 9
      },
      {
        "entityName": "Crust",
        "startCharIndex": 37,
        "endCharIndex": 46
      }
    ]
  }
]`
```

Przykładowy kod JSON wyrażenia długości jest zgodny z określonym formatem.

Pole `text` zawiera tekst przykładowej wypowiedzi. Pole `intentName` musi odpowiadać nazwie istniejącej wypowiedzi w aplikacji LUIS. Pole `entityLabels` jest wymagane. Jeśli nie chcesz dodawać etykiet do żadnych jednostek, podaj pustą tablicę.

Jeśli tablica entityLabels nie jest pusta, elementy `startCharIndex` i `endCharIndex` muszą oznaczać jednostkę określoną w polu `entityName`. Jest to indeks zaczynający się od zera. Jeśli rozpoczniesz lub zakończysz etykietę w miejscu tekstu, wywołanie interfejsu API w celu dodania wyrażenia długości zakończy się niepowodzeniem.

## <a name="add-example-utterances"></a>Dodawanie przykładowych wypowiedzi

1. Aby przekazać partię przykładowej wyrażenia długości, skopiuj to polecenie do edytora tekstu:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" ^
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" \
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" \
          --data "@ExampleUtterances.JSON"
    ```

    ***

1. Zastąp wartości zaczynające się od `***YOUR-` własnymi wartościami.

    |Informacje|Przeznaczenie|
    |--|--|
    |`***YOUR-AUTHORING-ENDPOINT***`| Twój punkt końcowy adresu URL tworzenia. Na przykład „https://REPLACE-WITH-YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/”. Nazwa zasobu jest ustawiana podczas tworzenia zasobu.|
    |`***YOUR-APP-ID***`| Identyfikator aplikacji LUIS. |
    |`***YOUR-APP-VERSION***`| Wersja aplikacji LUIS. W przypadku aplikacji Pizza numer wersji to "0,1" bez cudzysłowów.|
    |`***YOUR-AUTHORING-KEY***`|Klucz tworzenia znaków 32.|

    Przypisane klucze i zasoby są widoczne w portalu LUIS w sekcji Zarządzanie na stronie **zasoby platformy Azure** . Identyfikator aplikacji jest dostępny w tej samej sekcji zarządzania na stronie **Ustawienia aplikacji** .

1. Uruchom wiersz polecenia (Windows) lub terminal (macOS i Linux), a następnie zmień katalogi na ten sam katalog, w którym zapisano `ExampleUtterances.JSON` plik.

1. Skopiuj polecenie zwinięcie z edytora i wklej je do wiersza polecenia (Windows) lub terminalu (macOS i Linux). Naciśnij klawisz ENTER, aby wykonać polecenie.

    Powinna zostać wyświetlona następująca odpowiedź:

    ```json
    [{"value":{"ExampleId":1255129706,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1255129707,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1255129708,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    ```

    Oto dane wyjściowe sformatowane pod kątem czytelności:

    ```json
    [
      {
        "value": {
          "ExampleId": 1255129706,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129707,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129708,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    ```

## <a name="train-the-pizza-app-model"></a>Uczenie modelu aplikacji Pizza

1. Aby rozpocząć sesję szkoleniową dla aplikacji Pizza, skopiuj to polecenie do edytora tekstu:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" ^
          --data "" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    ***

1. Tak jak wcześniej, Zastąp wartości, zaczynając od `***YOUR-` własnych wartości.

1. Skopiuj polecenie zwinięcie z edytora i wklej je do wiersza polecenia (Windows) lub terminalu (macOS i Linux). Naciśnij klawisz ENTER, aby wykonać polecenie.

    Powinna zostać wyświetlona następująca odpowiedź:

    ```json
    {"statusId":2,"status":"UpToDate"}
    ```

    Oto dane wyjściowe sformatowane pod kątem czytelności:

    ```json
    {
      "statusId": 2,
      "status": "UpToDate"
    }
    ```

## <a name="get-the-status-of-training"></a>Pobierz stan szkolenia

1. Aby uzyskać stan szkolenia dla sesji szkoleniowej, skopiuj to polecenie do edytora tekstu:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" ^
            --request GET ^
            --header "Content-Type:application/json" ^
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    ***

1. Tak jak wcześniej, Zastąp wartości, zaczynając od `***YOUR-` własnych wartości.

1. Skopiuj polecenie zwinięcie z edytora i wklej je do wiersza polecenia (Windows) lub terminalu (macOS i Linux). Naciśnij klawisz ENTER, aby wykonać polecenie.

    Powinna zostać wyświetlona następująca odpowiedź:

    ```json
    [{"modelId":"8eb7ad8f-5db5-4c28-819b-ca3905fffd80","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6f53bc92-ae54-44ce-bc4e-010d1f8cfda0","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6cb17888-ad6e-464c-82c0-d37fd1f2c4f8","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"a16fc4fd-1949-4e77-9de3-30369f16c3a5","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6bacdb75-1889-4f93-8971-8c8995ff8960","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"be963f4c-4898-48d7-9eba-3c6af0867b9d","details":{"statusId":2,"status":"UpToDate","exampleCount":171}}]
    ```

    Oto dane wyjściowe sformatowane pod kątem czytelności:

    ```json
    [
      {
        "modelId": "8eb7ad8f-5db5-4c28-819b-ca3905fffd80",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6f53bc92-ae54-44ce-bc4e-010d1f8cfda0",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6cb17888-ad6e-464c-82c0-d37fd1f2c4f8",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "a16fc4fd-1949-4e77-9de3-30369f16c3a5",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6bacdb75-1889-4f93-8971-8c8995ff8960",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "be963f4c-4898-48d7-9eba-3c6af0867b9d",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      }
    ]
    ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start Usuń `ExampleUtterances.JSON` plik z systemu plików.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dla aplikacji](luis-concept-best-practices.md)
