---
title: Filtrowanie kontekstowe za pomocą metadanych
titleSuffix: Azure Cognitive Services
description: QnA Maker filtruje pary QnA według metadanych.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8f65ca9386963824f0cb740f587de83c9dec7f78
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103022140"
---
# <a name="filter-responses-with-metadata"></a>Filtrowanie odpowiedzi przy użyciu metadanych

QnA Maker umożliwia dodawanie metadanych, w formie par klucz-wartość, do pary pytań i odpowiedzi. Korzystając z tych informacji, można filtrować wyniki zapytań użytkowników oraz przechowywać dodatkowe informacje, które mogą być używane w konwersacjach z monitami.

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Przechowywanie pytań i odpowiedzi za pomocą jednostki QnA

Ważne jest, aby zrozumieć, w jaki sposób QnA Maker przechowuje dane pytania i odpowiedzi. Na poniższej ilustracji przedstawiono jednostkę QnA:

![Ilustracja jednostki QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Każda jednostka QnA ma unikatowy i trwały identyfikator. IDENTYFIKATORA można użyć do wprowadzania aktualizacji do określonej jednostki QnA.

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Korzystanie z metadanych do filtrowania odpowiedzi według niestandardowych tagów metadanych

Dodanie metadanych umożliwia filtrowanie odpowiedzi według tych tagów metadanych. Dodaj kolumnę Metadata z menu **Opcje widoku** . Dodaj metadane do bazy wiedzy, wybierając **+** ikonę metadanych w celu dodania pary metadanych. Ta para składa się z jednego klucza i jednej wartości.

![Zrzut ekranu przedstawiający dodawanie metadanych](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrowanie wyników za pomocą strictFilters dla tagów metadanych

Rozważ pytanie użytkownika "gdy ten hotel zostanie zamknięty?", gdzie zamiar jest implikowany dla restauracji "Paradise".

Ponieważ wyniki są wymagane tylko dla restauracji "Paradise", można ustawić filtr w wywołaniu GenerateAnswer dla metadanych "nazwa restauracji". Poniższy przykład pokazuje:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>Logiczne i domyślnie

Aby połączyć kilka filtrów metadanych w zapytaniu, Dodaj dodatkowe filtry metadanych do tablicy `strictFilters` właściwości. Domyślnie wartości są łączone logicznie (i). Kombinacja logiczna wymaga, aby wszystkie filtry pasowały do par QnA w celu zwrócenia pary w odpowiedzi.

Jest to równoważne użyciu `strictFiltersCompoundOperationType` właściwości z wartością `AND` .

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>Logiczna lub przy użyciu właściwości strictFiltersCompoundOperationType

W przypadku łączenia kilku filtrów metadanych, jeśli dotyczy tylko jednego lub niektórych pasujących filtrów, użyj `strictFiltersCompoundOperationType` właściwości z wartością `OR` .

Dzięki temu baza wiedzy może zwracać odpowiedzi w przypadku dopasowania filtru, ale nie zwraca odpowiedzi bez metadanych.

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>Przykłady metadanych w przewodnikach Szybki Start

Dowiedz się więcej na temat metadanych w portalu QnA Maker — szybki start dla metadanych:
* [Tworzenie — dodawanie metadanych do pary QnA](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Prognoza zapytania — filtrowanie odpowiedzi według metadanych](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Użyj wyników pytania i odpowiedzi, aby zachować kontekst konwersacji

Odpowiedź na GenerateAnswer zawiera odpowiednie informacje o metadanych pasującej pary pytania i odpowiedzi. Możesz użyć tych informacji w aplikacji klienckiej do przechowywania kontekstu poprzedniej konwersacji do użycia w późniejszych konwersacjach.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Analizowanie bazy wiedzy](../How-to/get-analytics-knowledge-base.md)
