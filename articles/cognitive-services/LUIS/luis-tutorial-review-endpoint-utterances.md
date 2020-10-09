---
title: 'Samouczek: przeglądanie punktu końcowego wyrażenia długości — LUIS'
description: W tym samouczku należy poprawić przewidywania aplikacji przez sprawdzenie lub poprawienie wyrażenia długości odebranych za pośrednictwem punktu końcowego LUIS HTTP, który LUIS nie ma pewności. Niektóre wypowiedzi mogą wymagać weryfikacji pod kątem intencji, a inne — pod kątem jednostki.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/02/2020
ms.openlocfilehash: b8f8fa2cd3c9c22187bb95c55d9de2abb2e8caec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324641"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Samouczek: usuwanie prognoz przez przeglądanie punktu końcowego wyrażenia długości
W tym samouczku poprawisz przewidywania aplikacji przez sprawdzenie lub poprawienie wyrażenia długości, odebranych za pośrednictwem punktu końcowego HTTPS LUIS, który LUIS nie ma pewności. Należy zapoznać się z punktem końcowym wyrażenia długości jako regularną częścią konserwacji zaplanowanej LUIS.

Ten proces przeglądu pozwala LUIS na naznanie Twojej domeny aplikacji. LUIS wybiera wyrażenia długości, które pojawiają się na liście przegląd. Ta lista ma następujące cechy:

* Jest specyficzna dla aplikacji.
* Ma za zadanie zwiększyć dokładność przewidywania aplikacji.
* Powinna być regularnie przeglądana.

Przeglądając wypowiedzi punktu końcowego, weryfikujesz lub poprawiasz przewidzianą intencję wypowiedzi.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importowanie aplikacji przykładowej
> * Przeglądanie wypowiedzi punktu końcowego
> * Uczenie i publikowanie aplikacji
> * Wysyłanie zapytania do punktu końcowego aplikacji w celu wyświetlenia odpowiedzi JSON usługi LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="download-json-file-for-app"></a>Pobierz plik JSON dla aplikacji

Pobierz i zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true).

## <a name="import-json-file-for-app"></a>Importuj plik JSON dla aplikacji


1. W [portalu Luis](https://www.luis.ai)na stronie **Moje aplikacje** wybierz pozycję **+ Nowa aplikacja do konwersacji**, a następnie **zaimportuj jako plik JSON**. Znajdź zapisany plik JSON z poprzedniego kroku. Nie musisz zmieniać nazwy aplikacji. Wybierz pozycję **gotowe**

1. Wybierz **Build** opcję Kompiluj **, a następnie zapoznaj się z** intencjami, głównymi blokami konstrukcyjnymi aplikacji Luis.

    :::image type="content" source="media/luis-tutorial-review-endpoint-utterances/initial-intents-in-app.png" alt-text="Przejdź do strony wersje na stronie intencje.":::

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Uczenie aplikacji w celu zastosowania zmian jednostek w aplikacji

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Opublikuj aplikację, aby uzyskać do niej dostęp z punktu końcowego HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Dodawanie wyrażenia długości w punkcie końcowym

W tej aplikacji masz intencje i jednostki, ale nie masz żadnego użycia punktów końcowych. To użycie punktu końcowego jest wymagane w celu usprawnienia aplikacji za pomocą przeglądu wypowiedź punktu końcowego.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Przejdź na koniec adresu URL na pasku adresu i Zastąp _YOUR_QUERY_HERE_ wyrażenia długości w poniższej tabeli. Dla każdego wypowiedź Prześlij wypowiedź i Pobierz wynik. Następnie zastąp wypowiedź na końcu następnym wypowiedź.

    |Wypowiedź punktu końcowego|Wyrównany cel|
    |--|--|
    |`I'm looking for a job with Natural Language Processing`|`GetJobInformation`|
    |`I want to cancel on March 3`|`Utilities.Cancel`|
    |`When were HRF-123456 and hrf-234567 published in the last year?`|`FindForm`|
    |`shift 123-45-6789 from Z-1242 to T-54672`|`MoveEmployee`|
    |`Please relocation jill-jones@mycompany.com from x-2345 to g-23456`|`MoveEmployee`|
    |`Here is my c.v. for the programmer job`|`ApplyForJob`|
    |`This is the lead welder paperwork.`|`ApplyForJob`|
    |`does form hrf-123456 cover the new dental benefits and medical plan`|`FindForm`|
    |`Jill Jones work with the media team on the public portal was amazing`|`EmployeeFeedback`|

    Jest tylko jedna pula wypowiedzi do przejrzenia, niezależnie od aktywnie edytowanej wersji i od wersji aplikacji opublikowanej w punkcie końcowym.

## <a name="review-endpoint-utterances"></a>Przeglądanie wypowiedzi punktu końcowego

Przejrzyj wyrażenia długości punktu końcowego pod kątem prawidłowo dopasowanego zamiaru. Chociaż istnieje jedna pula wyrażenia długości do przejrzenia we wszystkich wersjach, proces prawidłowo wyrównany do zamiaru dodaje przykład wypowiedź do bieżącego _aktywnego modelu_ .

1. W sekcji **kompilacja** portalu wybierz pozycję **Przegląd punktu końcowego wyrażenia długości** w lewym okienku nawigacji. Lista jest przefiltrowana pod kątem intencji **ApplyForJob**.

    :::image type="content" source="./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png" alt-text="Przejdź do strony wersje na stronie intencje.":::

    Ten wypowiedź, `I'm looking for a job with Natural Language Processing` , nie jest w poprawnym zamierzeniu, _GetJobInformation_. Został on źle przewidziany jako _ApplyForJob_ ze względu na podobieństwo nazw zadań i zleceń w dwóch intencjach.

1.  Aby wyrównać ten wypowiedź, wybierz odpowiedni **wyrównany przeznaczenie** `GetJobInformation` . Dodaj zmieniony wypowiedź do aplikacji, zaznaczając znacznik wyboru.

    Zapoznaj się z pozostałymi wyrażenia długości w tym zamiarze, poprawiając odpowiednio wyrównany cel. Użyj początkowej tabeli wypowiedź w tym samouczku, aby wyświetlić wyrównany cel.

    Lista **wyrażenia długości punktów końcowych recenzji** nie powinna już być poprawioną wyrażenia długości. Jeśli pojawią się więcej wyrażenia długości, Kontynuuj pracę z listą, poprawiając odpowiednie zamiary do momentu, gdy lista jest pusta.

    Dowolna korekta jednostek jest wykonywana po wyrównaniu zamiaru na stronie Szczegóły intencji.

1. Przeszkol i ponownie opublikuj aplikację.

## <a name="get-intent-prediction-from-endpoint"></a>Pobierz prognozowanie intencji z punktu końcowego

Aby sprawdzić poprawność wyrównanego przykładu wyrażenia długości do prognozowania aplikacji, wypróbuj wypowiedź blisko poprawionej wypowiedź.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Przejdź na koniec adresu URL na pasku adresu i Zastąp _YOUR_QUERY_HERE_ `Are there any natural language processing jobs in my department right now?` .

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.901367366
                },
                "ApplyForJob": {
                    "score": 0.0307973567
                },
                "EmployeeFeedback": {
                    "score": 0.0296942145
                },
                "MoveEmployee": {
                    "score": 0.00739785144
                },
                "FindForm": {
                    "score": 0.00449316856
                },
                "Utilities.Stop": {
                    "score": 0.00417657848
                },
                "Utilities.StartOver": {
                    "score": 0.00407167152
                },
                "Utilities.Help": {
                    "score": 0.003662492
                },
                "None": {
                    "score": 0.00335733569
                },
                "Utilities.Cancel": {
                    "score": 0.002225436
                },
                "Utilities.Confirm": {
                    "score": 0.00107437756
                }
            },
            "entities": {
                "keyPhrase": [
                    "natural language processing jobs",
                    "department"
                ],
                "datetimeV2": [
                    {
                        "type": "datetime",
                        "values": [
                            {
                                "timex": "PRESENT_REF",
                                "resolution": [
                                    {
                                        "value": "2020-07-02 21:45:50"
                                    }
                                ]
                            }
                        ]
                    }
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "natural language processing jobs",
                            "startIndex": 14,
                            "length": 32,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "builtin.keyPhrase",
                            "text": "department",
                            "startIndex": 53,
                            "length": 10,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "datetimeV2": [
                        {
                            "type": "builtin.datetimeV2.datetime",
                            "text": "right now",
                            "startIndex": 64,
                            "length": 9,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
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

   Teraz, gdy nie ma pewności, że wyrażenia długości są prawidłowo wyrównane, poprawna Metoda została przewidywalna o **wysokiej**wartości.

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>Czy zamiast przeglądania można dodać więcej wypowiedzi?
Możesz się zastanawiać, dlaczego nie dodać więcej przykładowych wypowiedzi. Jaki jest cel przeglądania wypowiedzi punktu końcowego? W rzeczywistej aplikacji usługi LUIS wypowiedzi punktu końcowego pochodzą od użytkowników, a ich dobór i kolejność słów różnią się od użytych. Gdyby użyto tych samych słów w tej samej kolejności, pierwotne przewidywanie miałoby wyższą wartość procentową.

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Dlaczego intencja o najwyższej ocenie znajduje się na liście wypowiedzi?
Niektóre wypowiedzi punktu końcowego będą miały wysoki współczynnik przewidywania na liście do przeglądu. Mimo to należy przejrzeć i zweryfikować te wypowiedzi. Znajdują się one na liście, ponieważ różnica między intencją o najwyższej ocenie i intencją drugą w kolejności jest zbyt mała. Różnica między dwiema najważniejszymi intencjami powinna wynosić około 15%.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przejrzano wypowiedzi przesłane w punkcie końcowym, które usługa LUIS uznała za niepewne. Zweryfikowanie i przeniesienie tych wypowiedzi do poprawnych intencji jako przykładowych wypowiedzi spowoduje, że dokładność przewidywania usługi LUIS zwiększy się.

> [!div class="nextstepaction"]
> [Dowiedz się, jak używać wzorców](luis-tutorial-pattern.md)
