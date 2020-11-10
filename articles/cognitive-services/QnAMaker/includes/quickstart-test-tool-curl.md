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
ms.openlocfilehash: cbabfc1ae0c54c4ef20d3c689506e486bf4b6a66
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94386479"
---
Ten przewodnik Szybki Start przeprowadzi Cię przez proces uzyskiwania odpowiedzi z bazy wiedzy.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsze [**zwinięcie**](https://curl.haxx.se/).
* Musisz mieć
    * [Usługa QNA Maker](../How-To/set-up-qnamaker-service-azure.md)
    * Przeszkolony i opublikowany baza wiedzy z pytaniami i odpowiedziami, z poprzedniego [przewodnika Szybki Start](../Quickstarts/add-question-metadata-portal.md), skonfigurowanych za pomocą metadanych i Chit Chat.

> [!NOTE]
> Gdy wszystko jest gotowe do wygenerowania odpowiedzi na pytanie z bazy wiedzy, należy przeprowadzić [uczenie](../Quickstarts/create-publish-knowledge-base.md#save-and-train) i [opublikować](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) bazę wiedzy. Po opublikowaniu bazy wiedzy na stronie **Publikowanie** zostaną wyświetlone ustawienia żądania HTTP do generowania odpowiedzi. Na karcie **zwinięcie** są wyświetlane ustawienia wymagane do wygenerowania odpowiedzi z narzędzia wiersza polecenia.

## <a name="use-metadata-to-filter-answer"></a>Filtrowanie odpowiedzi przy użyciu metadanych

Skorzystaj z bazy wiedzy z poprzedniej szybkiej kwerendy, aby uzyskać odpowiedzi na podstawie metadanych.

1. Na stronie **Ustawienia** bazy wiedzy wybierz kartę **zwinięcie** , aby zobaczyć przykładowe polecenie zwinięcie użyte do wygenerowania odpowiedzi z bazy wiedzy.
1. Skopiuj polecenie do środowiska do edycji (takiego jak plik tekstowy), aby można było edytować polecenie. Edytuj wartość pytania w następujący sposób, aby metadane `service:qna_maker` są używane jako filtr par QNA.

   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)
    
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H   "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```
    ---

    Pytanie jest tylko pojedynczym słowem, `size` które może zwracać jedną z dwóch par QNA. `strictFilters`Tablica informuje odpowiedź, aby zmniejszyć do tylko `qna_maker` odpowiedzi.

1. Odpowiedź zawiera tylko odpowiedź, która spełnia kryteria filtru. Następująca odpowiedź programu zwinięcie została sformatowana w celu zapewnienia czytelności:

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

```json
Debug: {Enable:true}
```

1. Edytuj zwinięcie polecenia, aby dołączyć Właściwość Debug, aby wyświetlić więcej informacji.

   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)
    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'Debug':{'Enable':true}}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'Debug':{'Enable':true}}"
    ```
    ---

1. Odpowiedź zawiera odpowiednie informacje dotyczące odpowiedzi. W poniższych danych wyjściowych JSON niektóre szczegóły debugowania zostały zastąpione wielokropkiem dla zwięzłości.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base) to learn how to collaborate on a knowledge base.",
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

Właściwość jest wartością logiczną.

```json
isTest:true
```

Zwinięcie polecenia wygląda następująco:
# <a name="qna-maker-ga"></a>[QnA Maker GA](#tab/v1)
```bash
curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'IsTest':true}"
```
# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)
```bash
curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'IsTest':true}"
```

---
Odpowiedź JSON używa tego samego schematu co opublikowana kwerenda bazy wiedzy.

> [!NOTE]
> Jeśli testy i opublikowane bazy wiedzy są dokładnie takie same, nadal może być niewielka odmiana, ponieważ indeks testu jest współużytkowany przez wszystkie bazy wiedzy w zasobie.

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Użyj Zwinięciea, aby wykonać zapytanie o odpowiedź Chit-Chat

1. W terminalu z włączoną funkcją wypełniania należy użyć instrukcji bot-kończącej konwersację od użytkownika, na przykład w `Thank you` przypadku pytania. Nie ma żadnych innych właściwości do ustawienia.
    
   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'thank you'}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'thank you'}"
    ```
    ---

1. Uruchom polecenie zwinięcie i odbierz odpowiedź JSON, łącznie z oceną i odpowiedzią.

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

1. Dodaj `threshold` Właściwość w celu uzyskania odpowiedzi na odpowiedź `size` z progiem równym 80% lub lepszym. Baza wiedzy nie powinna znaleźć tej odpowiedzi, ponieważ Ocena pytania wynosi 71%. Wynik zwraca domyślną odpowiedź podaną podczas tworzenia bazy wiedzy.

   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)
    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':80.00}"
    ```
    ---

1. Uruchom polecenie zwinięcie i odbierz odpowiedź JSON.

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
    
   # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)
    ```bash
    curl -X POST https://replace-with-your-resource-name.azurewebsites.net/qnamaker/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey replace-with-your-endpoint-key" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':60.00}"
    ```
    # <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)
    ```bash
    curl -X POST https://replace-with-your-resource-name.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer -H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" -H "Content-type: application/json" -d "{'question':'size', 'scoreThreshold':60.00}"
    ```
    ---

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