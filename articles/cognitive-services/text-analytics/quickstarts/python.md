---
title: 'Szybki start: wywoływanie interfejsu API analizy tekstu przy użyciu języka Python'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start pokazano, jak za pomocą języka Python uzyskać informacje i przykłady kodu, aby szybko rozpocząć korzystanie z interfejs API analizy tekstu na platformie Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 0ef870b6b2d3b88b13c16c8c2acbfcee7ed551c1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90527245"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Szybki Start: korzystanie z interfejsu API REST języka Python w celu wywołania usługi analiza tekstu poznawczej 
<a name="HOLTop"></a>

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć analizowanie języka za pomocą interfejsu API REST analiza tekstu i środowiska Python. W tym artykule pokazano, jak [wykryć język](#Detect), [analizować tonacji](#SentimentAnalysis), [wyodrębniać kluczowe frazy](#KeyPhraseExtraction)i [identyfikować połączone jednostki](#Entities).

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 3.x](https://python.org)

* Biblioteka żądań języka Python
    
    Bibliotekę można zainstalować za pomocą tego polecenia:

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nową aplikację w języku Python w ulubionym edytorze lub w środowisku IDE. Dodaj następujące Importy do pliku.

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
```

Utwórz zmienne dla punktu końcowego platformy Azure i klucza subskrypcji zasobu.
    
```python
import os

subscription_key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```

W poniższych sekcjach opisano sposób wywoływania poszczególnych funkcji interfejsu API.

<a name="Detect"></a>

## <a name="detect-languages"></a>Wykrywanie języków

Dołącz `/text/analytics/v3.0/languages` do podstawowego punktu końcowego analiza tekstu, aby utworzyć adres URL wykrywania języka. Na przykład: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`
    
```python
language_api_url = endpoint + "/text/analytics/v3.0/languages"
```

Ładunek do interfejsu API składa się z listy `documents` , która jest krotką zawierającą `id` i `text` atrybut. Ten `text` atrybut przechowuje tekst do przeanalizowania, a `id` może być dowolną wartością. 

```python
documents = {"documents": [
    {"id": "1", "text": "This is a document written in English."},
    {"id": "2", "text": "Este es un document escrito en Español."},
    {"id": "3", "text": "这是一个用中文写的文件"}
]}
```

Użyj biblioteki Requests, aby wysłać dokumenty do interfejsu API. Dodaj swój klucz subskrypcji do `Ocp-Apim-Subscription-Key` nagłówka i Wyślij żądanie przy użyciu `requests.post()` . 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>Dane wyjściowe

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Analiza tonacji

Aby wykryć tonacji (zakres wartości dodatnich lub ujemnych) zestawu dokumentów, Dołącz `/text/analytics/v3.0/sentiment` do podstawowego punktu końcowego analiza tekstu, aby utworzyć adres URL wykrywania języka. Na przykład: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`
    
```python
sentiment_url = endpoint + "/text/analytics/v3.0/sentiment"
```

Podobnie jak w przypadku wykrywania języka, Utwórz słownik z kluczem, `documents` który składa się z listy dokumentów. Każdy dokument jest spójną kolekcją składająca się z elementów `id` i `text` do przeanalizowania oraz elementem `language` tekstu. 

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."}
]}
```

Użyj biblioteki Requests, aby wysłać dokumenty do interfejsu API. Dodaj swój klucz subskrypcji do `Ocp-Apim-Subscription-Key` nagłówka i Wyślij żądanie przy użyciu `requests.post()` . 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>Dane wyjściowe

Wynik tonacji dla dokumentu ma wartość z zakresu od 0,0 do 1,0, o wyższym wyniku, wskazujący bardziej pozytywną tonacji.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 102,
                    "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                }
            ],
            "warnings": []
        },
        {
            "id": "2",
            "sentiment": "negative",
            "confidenceScores": {
                "positive": 0.02,
                "neutral": 0.05,
                "negative": 0.93
            },
            "sentences": [
                {
                    "sentiment": "negative",
                    "confidenceScores": {
                        "positive": 0.02,
                        "neutral": 0.05,
                        "negative": 0.93
                    },
                    "offset": 0,
                    "length": 92,
                    "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Wyodrębnianie kluczowych fraz
 
Aby wyodrębnić kluczowe frazy z zestawu dokumentów, Dołącz `/text/analytics/v3.0/keyPhrases` do podstawowego punktu końcowego analiza tekstu, aby utworzyć adres URL wykrywania języka. Na przykład: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`
    
```python
keyphrase_url = endpoint + "/text/analytics/v3.0/keyphrases"
```

Ta kolekcja dokumentów jest taka sama, jak w przypadku przykładu analizy tonacji.

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
    {"id": "3", "language": "en",
        "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."}
]}
```

Użyj biblioteki Requests, aby wysłać dokumenty do interfejsu API. Dodaj swój klucz subskrypcji do `Ocp-Apim-Subscription-Key` nagłówka i Wyślij żądanie przy użyciu `requests.post()` . 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>Dane wyjściowe

```json
{
    "documents": [
        {
            "id": "1",
            "keyPhrases": [
                "HDR resolution",
                "new XBox",
                "clean look"
            ],
            "warnings": []
        },
        {
            "id": "2",
            "keyPhrases": [
                "Carlos",
                "notificacion",
                "algun problema",
                "telefono movil"
            ],
            "warnings": []
        },
        {
            "id": "3",
            "keyPhrases": [
                "new hotel",
                "Grand Hotel",
                "review",
                "center of Seattle",
                "classiest decor",
                "stars"
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Identyfikowanie jednostek

Aby zidentyfikować dobrze znane jednostki (osoby, miejsca i rzeczy) w dokumentach tekstowych, Dołącz `/text/analytics/v3.0/entities/recognition/general` do podstawowego punktu końcowego analiza tekstu, aby utworzyć adres URL wykrywania języka. Na przykład: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`
    
```python
entities_url = endpoint + "/text/analytics/v3.0/entities/recognition/general"
```

Utwórz kolekcję dokumentów, jak w poprzednich przykładach. 

```python
documents = {"documents": [
    {"id": "1", "text": "Microsoft is an It company."}
]}
```

Użyj biblioteki Requests, aby wysłać dokumenty do interfejsu API. Dodaj swój klucz subskrypcji do `Ocp-Apim-Subscription-Key` nagłówka i Wyślij żądanie przy użyciu `requests.post()` .

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
pprint(entities)
```

### <a name="output"></a>Dane wyjściowe

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "text": "Microsoft",
                    "category": "Organization",
                    "offset": 0,
                    "length": 9,
                    "confidenceScore": 0.86
                },
                {
                    "text": "IT",
                    "category": "Skill",
                    "offset": 16,
                    "length": 2,
                    "confidenceScore": 0.8
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [analiza tekstu z Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Zobacz też 

 [Przegląd analizy tekstu](../overview.md)  
 [Często zadawane pytania](../text-analytics-resource-faq.md)
