---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 11/09/2020
ms.openlocfilehash: fa497b69b067d5556f11effdb52505895ecc3bdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94386644"
---
W tym samouczku Szybki start opisano sposób uzyskiwania odpowiedzi z bazy wiedzy przy użyciu narzędzia Postman.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza wersja narzędzia [**Postman**](https://www.getpostman.com/).
* Musisz mieć
    * [Usługa QNA Maker](../How-To/set-up-qnamaker-service-azure.md)
    * Przeszkolone i opublikowane [bazy wiedzy z pytaniami i odpowiedziami](../Quickstarts/add-question-metadata-portal.md) utworzonymi na podstawie przewodnika Szybki Start są skonfigurowane za pomocą metadanych i Chit Chat.

> [!NOTE]
> Gdy wszystko jest gotowe do wygenerowania odpowiedzi na pytanie z bazy wiedzy, należy przeprowadzić [uczenie](../Quickstarts/create-publish-knowledge-base.md#save-and-train) i [opublikować](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) bazę wiedzy. Po opublikowaniu bazy wiedzy na stronie **Publikowanie** zostaną wyświetlone ustawienia żądania HTTP do generowania odpowiedzi. Karta **Poster** zawiera ustawienia wymagane do wygenerowania odpowiedzi.

## <a name="set-up-postman-for-requests"></a>Skonfiguruj ogłaszanie dla żądań

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Ten przewodnik Szybki Start używa tych samych ustawień **dla żądania post programu Poster** , a następnie zostanie SKONFIGUROWANY do ogłaszania w formacie JSON treści wysyłanej do usługi na podstawie tego, co próbujesz wykonać zapytanie.

Użyj tej procedury, aby skonfigurować program, a następnie zapoznaj się z każdą kolejną sekcją, aby skonfigurować plik JSON wpisu.

1. Na stronie **Ustawienia** bazy wiedzy wybierz kartę **Ogłoś** , aby wyświetlić konfigurację używaną do generowania odpowiedzi z bazy wiedzy. Skopiuj poniższe informacje do użycia w programie Poster.

    |Nazwa|Ustawienie|Cel i wartość|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Jest to metoda HTTP i trasy dla adresu URL.|
    |`Host`|`https://YOUR-RESOURCE_NAME.azurewebsites.net/qnamaker`|Jest to host adresu URL. Połącz hosta i Opublikuj wartości, aby uzyskać pełny adres URL generateAnswer.|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|Wartość nagłówka na potrzeby autoryzacji żądania na platformie Azure. |
    |`Content-type`|`application/json`|Wartość nagłówka zawartości.|
    ||`{"question":"<Your question>"}`|Treść żądania POST jako obiekt JSON. Ta wartość zostanie zmieniona w każdej z poniższych sekcji w zależności od tego, co ma być wykonywane przez zapytanie.|

1. Otwórz program Poster i Utwórz nowe podstawowe żądanie **post** z opublikowanymi ustawieniami bazy wiedzy. W poniższych sekcjach Zmień kod JSON wpisu w celu zmiany zapytania na bazę wiedzy.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Ten przewodnik Szybki Start używa tych samych ustawień **dla żądania post programu Poster** , a następnie zostanie SKONFIGUROWANY do ogłaszania w formacie JSON treści wysyłanej do usługi na podstawie tego, co próbujesz wykonać zapytanie.

Użyj tej procedury, aby skonfigurować program, a następnie zapoznaj się z każdą kolejną sekcją, aby skonfigurować plik JSON wpisu.

1. Na stronie **Ustawienia** bazy wiedzy wybierz kartę **Ogłoś** , aby wyświetlić konfigurację używaną do generowania odpowiedzi z bazy wiedzy. Skopiuj poniższe informacje do użycia w programie Poster.

    |Nazwa|Ustawienie|Cel i wartość|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|Jest to metoda HTTP i trasy dla adresu URL.|
    |`Host`|`https://YOUR-RESOURCE_NAME.cognitiveservices.azure.com/qnamaker`|Jest to host adresu URL. Połącz hosta i Opublikuj wartości, aby uzyskać pełny adres URL generateAnswer.|
    |`Ocp-Apim-Subscription-Key`|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|Wartość nagłówka na potrzeby autoryzacji żądania. |
    |`Content-type`|`application/json`|Wartość nagłówka zawartości.|
    ||`{"question":"<Your question>"}`|Treść żądania POST jako obiekt JSON. Ta wartość zostanie zmieniona w każdej z poniższych sekcji w zależności od tego, co ma być wykonywane przez zapytanie.|

1. Otwórz program Poster i Utwórz nowe podstawowe żądanie **post** z opublikowanymi ustawieniami bazy wiedzy. W poniższych sekcjach Zmień kod JSON wpisu w celu zmiany zapytania na bazę wiedzy.
---

## <a name="use-metadata-to-filter-answer"></a>Filtrowanie odpowiedzi przy użyciu metadanych

W poprzednim przewodniku szybki start metadane zostały dodane do dwóch par QnA w celu rozróżnienia między dwoma różnymi pytaniami. Dodaj metadane do zapytania, aby ograniczyć filtr do tylko odpowiedniej pary QnA.

1. W programie Poster Zmień tylko kod JSON zapytania, dodając `strictFilters` Właściwość z parą nazwa/wartość `service:qna_maker` . Treść JSON powinna być:

    ```json
    {
        'question':'size',
        'strictFilters': [
            {
                'name':'service','value':'qna_maker'
            }
        ]
    }
    ```

    Pytanie jest tylko pojedynczym słowem, `size` które może zwracać jedną z dwóch par pytań i odpowiedzi. `strictFilters`Tablica informuje odpowiedź, aby zmniejszyć do tylko `qna_maker` odpowiedzi.

1. Odpowiedź zawiera tylko odpowiedź, która spełnia kryteria filtru.

    Następująca odpowiedź została sformatowana pod kątem czytelności:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Jeśli istnieje para pytań i odpowiedzi, która nie spełnia warunków wyszukiwania, ale spełniała filtr, nie zostanie zwrócona. Zamiast tego `No good match found in KB.` jest zwracana ogólna odpowiedź.

## <a name="use-debug-query-property"></a>Użyj właściwości zapytania debugowania

> [!NOTE]
>Nie zaleca się używania właściwości Debug dla żadnej zależności. Ta właściwość została dodana, aby ułatwić zespołowi produktu Rozwiązywanie problemów.

Informacje debugowania pomagają zrozumieć, w jaki sposób została określona zwrócona odpowiedź. Chociaż jest to przydatne, nie jest to konieczne. Aby wygenerować odpowiedź z informacjami o debugowaniu, Dodaj `debug` Właściwość:

1. W programie Poster Zmień tylko treść pliku JSON przez dodanie `debug` właściwości. KOD JSON powinien:

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. Odpowiedź zawiera odpowiednie informacje dotyczące odpowiedzi. W poniższych danych wyjściowych JSON niektóre szczegóły debugowania zostały zastąpione wielokropkiem.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) how to collaborate on a knowledge base.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>Korzystanie z bazy wiedzy testowej

Jeśli chcesz uzyskać odpowiedź z bazy wiedzy testowej, użyj `isTest` Właściwości Body.

W programie Poster Zmień tylko treść pliku JSON przez dodanie `isTest` właściwości. KOD JSON powinien:

```json
{
    'question':'size',
    'isTest': true
}
```

Odpowiedź JSON używa tego samego schematu co opublikowana kwerenda bazy wiedzy.

> [!NOTE]
> Jeśli testy i opublikowane bazy wiedzy są dokładnie takie same, nadal może być niewielka odmiana, ponieważ indeks testu jest współużytkowany przez wszystkie bazy wiedzy w zasobie.

## <a name="query-for-a-chit-chat-answer"></a>Zapytanie o odpowiedź Chit-Chat

1. W programie Poster należy zmienić tylko treść JSON elementu na instrukcję kończącą konwersację od użytkownika. KOD JSON powinien:

    ```json
    {
        'question':'thank you'
    }
    ```

1. Odpowiedź zawiera wynik i odpowiedź.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    Ponieważ pytanie `Thank you` było dokładnie zgodne z pytaniem w konwersacji, usługa QnA Maker jest całkowicie pewna odpowiedzi (ocena 100%). QnA Maker zwrócić także wszystkie powiązane pytania, a także Właściwość metadanych zawierająca informacje znacznika metadanych Chit-Chat.

## <a name="use-threshold-and-default-answer"></a>Użyj wartości progowej i domyślnej odpowiedzi

Możesz poprosić o minimalny próg odpowiedzi. Jeśli próg nie jest spełniony, zostanie zwrócona odpowiedź domyślna.

1. W programie Poster należy zmienić tylko treść JSON elementu na instrukcję kończącą konwersację od użytkownika. KOD JSON powinien:

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    Baza wiedzy nie powinna znaleźć odpowiedzi, ponieważ Ocena pytania wynosi 71%, a zamiast tego zwraca domyślną odpowiedź podaną podczas tworzenia bazy wiedzy.

    Zwrócona odpowiedź JSON, w tym wynik i odpowiedź to:

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker zwrócił wynik `0` , co oznacza brak pewności. Zwraca również odpowiedź domyślną.

1. Zmień wartość progową na 60% i ponownie Zażądaj zapytania:

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
    ```

    Zwrócony kod JSON znalazł odpowiedź.

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```