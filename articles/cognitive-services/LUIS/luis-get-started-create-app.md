---
title: 'Szybki Start: Tworzenie aplikacji — LUIS'
description: W tym przewodniku szybki start pokazano, jak utworzyć aplikację LUIS, która używa wbudowanej domeny `HomeAutomation` do włączania i wyłączania świateł i urządzeń. Ta wstępnie utworzona domena zapewnia intencje, jednostki i przykładowe wypowiedzi. Po zakończeniu samouczka punkt końcowy usługi LUIS będzie działał w chmurze.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/29/2021
ms.openlocfilehash: e0972998fad647c1c70be814cb7c13f14a6e1141
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277032"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Szybki start: używanie wstępnie utworzonej aplikacji Home Automation

W tym przewodniku Szybki start utworzysz aplikację usługi LUIS, która używa wstępnie utworzonej domeny `HomeAutomation` na potrzeby włączania i wyłączania świateł i urządzeń. Ta wstępnie utworzona domena zapewnia intencje, jednostki i przykładowe wypowiedzi. Po zakończeniu samouczka punkt końcowy usługi LUIS będzie działał w chmurze.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji
Tworzenie aplikacji i zarządzanie nimi jest możliwe w obszarze **My Apps** (Moje aplikacje).

### <a name="create-an-application"></a>Tworzenie aplikacji

Aby utworzyć aplikację, kliknij pozycję  **+ Nowa aplikacja**. 

W wyświetlonym oknie wprowadź następujące informacje:

|Nazwa  |Opis  |
|---------|---------|
|AName     | Nazwa aplikacji. Na przykład "Automatyzacja domu".        |
|Kultura     | Język, który aplikacja rozpoznaje i mówi.   |
|Opis | Opis aplikacji.
|Zasób predykcyjny | Zasób predykcyjny, który będzie odbierać zapytania. |

Kliknij **Gotowe**.

>[!NOTE]
>Kultury nie można zmienić po utworzeniu aplikacji.

## <a name="add-prebuilt-domain"></a>Dodawanie wstępnie utworzonej domeny

1. W lewym okienku nawigacji wybierz opcję **prekompilowane domeny**.
1. Wyszukaj **HomeAutomation**.
1. Wybierz pozycję **Dodaj domenę** na karcie HomeAutomation.

    > [!div class="mx-imgBorder"]
    > ![Wybierz pozycję "prekompilowane domeny", a następnie wyszukaj ciąg "HomeAutomation". Wybierz pozycję "Dodaj domenę" na karcie HomeAutomation.](media/luis-quickstart-new-app/home-automation.png)

    Po pomyślnym dodaniu domeny pole wstępnie utworzonej domeny wyświetla przycisk **Remove domain** (Usuń domenę).

## <a name="intents-and-entities"></a>Intencje i jednostki

1. Wybierz pozycję **intencje** w menu nawigacji po lewej stronie, aby zobaczyć intencje domeny HomeAutomation. Ma przykład wyrażenia długości, takie jak `HomeAutomation.QueryState` i     `HomeAutomation.SetDevice` .

    > [!NOTE]
    > **None** (Brak) to intencja zapewniana przez wszystkie aplikacje usługi LUIS. Używa się jej do obsługi wypowiedzi, które nie odnoszą się do funkcji zapewnianej przez aplikację.

1. Wybierz intencję **HomeAutomation.TurnOff**. Zamiar zawiera listę przykładowych wyrażenia długości, które są oznaczone obiektami.

    > [!div class="mx-imgBorder"]
    > [![Zrzut ekranu przedstawiający intencję HomeAutomation.TurnOff](media/luis-quickstart-new-app/home-automation-turnoff.png "Zrzut ekranu przedstawiający intencję HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnoff.png)

1. Jeśli chcesz wyświetlić jednostki dla aplikacji, wybierz pozycję **jednostki**. Po kliknięciu jednej z jednostek, takiej jak **HomeAutomation. DeviceName** , zostanie wyświetlona lista skojarzonych z nią wartości. 
 
    :::image type="content" source="media/luis-quickstart-new-app/entities-page.png" alt-text="Tekst alternatywny obrazu" lightbox="media/luis-quickstart-new-app/entities-page.png":::

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Testowanie aplikacji
Po przeprowadzeniu uczenia aplikacji możesz ją przetestować.

1. Wybierz pozycję **Testuj** w prawym górnym rogu.

1. Wpisz wypowiedź testu podobne `Turn off the lights` do interaktywnego okienka testów, a następnie naciśnij klawisz ENTER. Na przykład Wyłącz *światła*.

    W tym przykładzie `Turn off the lights` jest poprawnie zidentyfikowany jako najpopularniejszy cel oceniania **HomeAutomation.**.

    ![Zrzut ekranu przedstawiający panel testowania z wyróżnioną wypowiedzią](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Wybierz pozycję **Sprawdź** , aby wyświetlić więcej informacji na temat przewidywania.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający Panel testowy z informacjami o inspekcji](media/luis-quickstart-new-app/test.png)

1. Zamknij okienko testowania.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikowanie aplikacji w celu uzyskania adresu URL punktu końcowego

Aby otrzymywać prognozę LUIS w programie Chat bot lub innej aplikacji klienckiej, należy opublikować aplikację w punkcie końcowym przewidywania.

1. Wybierz pozycję **Publikuj** w prawym górnym rogu okna.

1. Wybierz miejsce **produkcyjne** , a następnie wybierz pozycję **gotowe**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający LUIS publikowanie w punkcie końcowym](media/howto-publish/publish-app-popup.png)

1. Wybierz link **dostęp do adresów URL punktu końcowego** w obszarze powiadomień, aby przejść do strony **zasobów platformy Azure** . Adresy URL punktów końcowych są wyświetlane jako **przykładowe zapytanie**.

<!-- [!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)] -->

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Zbadaj punkt końcowy przewidywania interfejsu API v3

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. Na pasku adresu przeglądarki dla ciągu zapytania upewnij się, że w adresie URL znajdują się następujące nazwy i słupki wartości. Jeśli nie znajdują się w ciągu zapytania, Dodaj je:

    |Para nazwa/wartość|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. Na pasku adresu przeglądarki przejdź na koniec adresu URL i wprowadź `turn off the living room light` wartość _zapytania_ , a następnie naciśnij klawisz ENTER.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.902181149,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
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

    Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Iteracyjne opracowywanie aplikacji dla LUIS](./luis-concept-app-iteration.md)