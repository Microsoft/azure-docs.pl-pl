---
title: 'Samouczek: Publikowanie ustawień — LUIS'
description: W tym samouczku Zmień ustawienia publikowania, aby uzyskać lepsze przewidywania.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: d976d06fbf20ea0e64fa01bd94658a27d680bc86
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588795"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>Samouczek: Dodawanie analizy tonacji jako ustawienia publikowania

W tym samouczku zmodyfikuj ustawienia publikowania w celu wyodrębnienia analizy tonacji, a następnie Zbadaj punkt końcowy LUIS, aby zobaczyć zwrócone tonacji wypowiedź użytkownika.

**Z tego samouczka dowiesz się, jak wykonywać następujące czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Dodawanie analizy tonacji jako ustawienia publikowania
> * Pobierz tonacji wypowiedź z opublikowanego punktu końcowego

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Analiza tonacji to ustawienie publikowania

Następujące wypowiedzi przedstawiają przykłady tonacji:

|Opinia|Wynik|Wypowiedź|
|:--|:--|:--|
|negative|0,01 |Pizza Awful.|
|pozytywna|0,97 |Pizza sera była wspaniałe.|

Analiza tonacji to ustawienie publikowania, które ma zastosowanie do każdej wypowiedzi. Po ustawieniu aplikacja zwraca tonacji wypowiedź, bez konieczności etykietowania danych.

Ponieważ jest to ustawienie publikowania, nie widzisz go na stronach intencji lub jednostek. Jest ono widoczne w okienku [interactive test](luis-interactive-test.md#view-sentiment-results) (Test interaktywny) lub podczas testowania pod adresem URL punktu końcowego.

## <a name="download-json-file-for-app"></a>Pobierz plik JSON dla aplikacji

Pobierz i Zapisz [plik JSON aplikacji](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

## <a name="import-json-file-for-app"></a>Importuj plik JSON dla aplikacji

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>Uczenie aplikacji

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurowanie aplikacji do uwzględnienia analizy tonacji

1. Wybierz pozycję **Publikuj** z górnego menu. Tonacji Analysis jest ustawieniem publikowania.

1. Wybierz pozycję **gniazdo produkcyjne** i wybierz pozycję **Zmień ustawienia**.
1. Dla ustawienia analiza tonacji ustaw wartość **włączone**.

    ![Włączanie analizy tonacji jako ustawienia publikowania](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Uzyskiwanie tonacji wypowiedzi z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Przejdź na koniec adresu URL na pasku adresu i Zastąp _YOUR_QUERY_HERE_ :

    `Deliver 2 of the best cheese pizzas ever!!!`

    Ta wypowiedź jest inna niż wszystkie pozostałe oznaczone wypowiedzi, dlatego jest dobra do testowania i powinna zwrócić intencję `OrderPizza` z wyodrębnioną analizą tonacji.

    ```json
    {
        "query": "Deliver 2 of the best cheese pizzas ever!!!",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.4659952
                },
                "None": {
                    "score": 0.16901511
                },
                "Confirm": {
                    "score": 0.00421415
                },
                "Greeting": {
                    "score": 0.00109914376
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            },
            "sentiment": {
                "label": "positive",
                "score": 0.861665964
            }
        }
    }
    ```

    Analiza tonacji jest dodatnia z wynikiem 86%.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informacje pokrewne

* Analiza tonacji jest udostępniana za pośrednictwem funkcji [analizy tekstu](../Text-Analytics/index.yml) usługi Cognitive Service. Ta funkcja jest ograniczona do [obsługiwanych języków](luis-language-support.md#languages-supported) przez analizę tekstu.
* [Jak trenować](luis-how-to-train.md)
* [Jak opublikować](luis-how-to-publish-app.md)
* [Jak przeprowadzać testy w portalu usługi LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Następne kroki
W tym samouczku dodaliśmy analizę tonacji jako ustawienie publikowania, aby wyodrębnić wartości tonacji z wypowiedzi jako całości.

> [!div class="nextstepaction"]
> [Przejrzyj wypowiedzi punktu końcowego w aplikacji HR](luis-tutorial-review-endpoint-utterances.md)

