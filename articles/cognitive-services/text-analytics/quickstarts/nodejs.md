---
title: 'Szybki Start: używanie Node.js do wywoływania interfejsu API REST analiza tekstu'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start pokazano, jak używać Node.js, aby uzyskać informacje i przykłady kodu, aby szybko rozpocząć korzystanie z interfejs API analizy tekstu na platformie Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: aahi
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 912434f20d00d276bde23803f4560ebd528872d9
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365129"
---
# <a name="quickstart-use-nodejs-to-call-the-text-analytics-cognitive-service"></a>Szybki Start: używanie Node.js do wywoływania usługi analiza tekstu poznawczej  
<a name="HOLTop"></a>

W tym artykule przedstawiono, jak [wykrywać język](#Detect), [analizować tonację](#SentimentAnalysis), [wyodrębniać kluczowe frazy](#KeyPhraseExtraction) i [identyfikować połączone jednostki](#Entities) przy użyciu [interfejsów API analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759711) i środowiska Node.js.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="Detect"></a>

## <a name="detect-language"></a>Wykrywanie języka

Interfejs API wykrywania języka wykrywa język dokumentu tekstowego przy użyciu [metody Detect Language](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

1. Utwórz nowy projekt Node.JS w ulubionym środowisku IDE lub folderze na pulpicie.
1. Dodaj kod podany poniżej do nowego `.js` pliku.
1. Skopiuj klucz i punkt końcowy do kodu. 
1. Uruchom program z poziomu środowiska IDE lub wiersza polecenia, na przykład `npm start` lub `node detect.js` .

```javascript
'use strict';

let https = require ('https');
subscription_key = "<paste-your-text-analytics-key-here>";
endpoint = "<paste-your-text-analytics-endpoint-here>";

let path = '/text/analytics/v3.0/languages';

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let get_language = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'text': 'This is a document written in English.' },
        { 'id': '2', 'text': 'Este es un document escrito en Español.' },
        { 'id': '3', 'text': '这是一个用中文写的文件' }
    ]
};

get_language(documents);
```

**Odpowiedź wykrywania języka**

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie: 

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

Interfejs API analizy tonacji wykrywa tonację zestawu rekordów tekstowych przy użyciu [metody Sentiment](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9). Za pomocą analizy tonacji można dowiedzieć się, co klienci uważają za swoją markę lub temat, analizując nieprzetworzony tekst w celu uzyskania wskazówek dotyczących pozytywnych lub negatywnych tonacji. Poniższy przykład przedstawia wyniki dla dwóch dokumentów, jeden w języku angielskim i drugi w hiszpańskim.

1. Utwórz nowy projekt Node.JS w ulubionym środowisku IDE lub folderze na pulpicie.
1. Dodaj kod podany poniżej do nowego `.js` pliku.
1. Skopiuj klucz analiza tekstu i punkt końcowy do kodu. 
1. Uruchom program z poziomu środowiska IDE lub wiersza polecenia, na przykład `npm start` lub `node sentiment.js` .

```javascript
'use strict';

let https = require ('https');

subscription_key = "<paste-your-text-analytics-key-here>";
endpoint = "<paste-your-text-analytics-endpoint-here>";

let path = '/text/analytics/v3.0/sentiment';

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let get_sentiments = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'language': 'en', 'text': 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
        { 'id': '2', 'language': 'es', 'text': 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' },
    ]
};

get_sentiments(documents);
```

**Odpowiedź analizy tonacji**

Wynik jest mierzony jako dodatni, jeśli ocenia się bliżej 1,0 i wartość ujemna, jeśli jest to wynik zbliżony do 0,0.
Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie:

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

Interfejs API wyodrębniania kluczowych fraz wyodrębnia kluczowe frazy w dokumencie tekstowym przy użyciu [metody Key Phrases](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). Wyodrębnianie kluczowych fraz służy do szybkiego identyfikowania głównych punktów dokumentu lub tekstu. W poniższym przykładzie wyodrębniono frazy kluczowe dla dokumentów w języku angielskim i hiszpańskim.

1. Utwórz nowy projekt Node.JS w ulubionym środowisku IDE lub folderze na pulpicie.
1. Dodaj kod podany poniżej do nowego `.js` pliku.
1. Skopiuj klucz analiza tekstu i punkt końcowy do kodu. 
1. Uruchom program z poziomu środowiska IDE lub wiersza polecenia, na przykład `npm start` lub `node key-phrases.js` .

```javascript
'use strict';

let https = require ('https');

subscription_key = "<paste-your-text-analytics-key-here>";
endpoint = "<paste-your-text-analytics-endpoint-here>";

let path = '/text/analytics/v3.0/keyPhrases';

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let get_key_phrases = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'language': 'en', 'text': 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' },
        { 'id': '2', 'language': 'es', 'text': 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' },
        { 'id': '3', 'language': 'en', 'text': 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' }
    ]
};

get_key_phrases(documents);
```

**Odpowiedź wyodrębniania kluczowych fraz**

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie: 

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

## <a name="identify-linked-entities"></a>Identyfikowanie połączonych jednostek

Interfejs API jednostek identyfikuje dobrze znane jednostki w dokumencie tekstowym przy użyciu [metody Entities](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634). [Jednostki](../how-tos/text-analytics-how-to-entity-linking.md) wyodrębniają wyrazy z tekstu, takie jak "Stany Zjednoczone", a następnie zawierają link do typu i/lub Wikipedia dla tych wyrazów. Typ dla "Stany Zjednoczone" to `location` , podczas gdy link do witryny Wikipedia ma wartość `https://en.wikipedia.org/wiki/United_States` .  W poniższym przykładzie zidentyfikowano jednostki dla dokumentów w języku angielskim.

1. Utwórz nowy projekt Node.JS w ulubionym środowisku IDE lub folderze na pulpicie.
1. Dodaj kod podany poniżej do nowego `.js` pliku.
1. Skopiuj klucz analizy tekstu i punkt końcowy do kodu
1. Uruchom program z poziomu środowiska IDE lub wiersza polecenia, na przykład `npm start` lub `node entities.js` .

```javascript
'use strict';

let https = require ('https');

subscription_key = "<paste-your-text-analytics-key-here>";
endpoint = "<paste-your-text-analytics-endpoint-here>";

let path = '/text/analytics/v3.0/entities/recognition/general';

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let get_entities = function (documents) {
    let body = JSON.stringify(documents);

    let request_params = {
        method: 'POST',
        hostname: (new URL(endpoint)).hostname,
        path: path,
        headers: {
            'Ocp-Apim-Subscription-Key': subscription_key,
        }
    };

    let req = https.request(request_params, response_handler);
    req.write(body);
    req.end();
}

let documents = {
    'documents': [
        { 'id': '1', 'language': 'en', 'text': 'Microsoft is an It company.' }
    ]
};

get_entities(documents);
```

**Odpowiedź wyodrębniania jednostek**

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie:

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