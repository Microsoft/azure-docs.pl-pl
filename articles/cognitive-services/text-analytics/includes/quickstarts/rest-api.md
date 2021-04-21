---
title: 'Szybki start: używanie programu cURL do wywołania interfejsu API REST analiza tekstu API'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start pokazano, jak szybko rozpocząć korzystanie z interfejsu API analiza tekstu w Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 04/19/2021
ms.author: aahi
ms.openlocfilehash: bf18ab6cae83f3dc0908a15bf4bf53b5318613d9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765130"
---
# <a name="version-31-preview"></a>[Wersja zapoznawcza 3.1](#tab/version-3-1)

[Dokumentacja referencyjna w wersji 3.1](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-4/)

# <a name="version-30"></a>[Wersja 3.0](#tab/version-3)

[Dokumentacja referencyjna w wersji 3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0) 

---

## <a name="prerequisites"></a>Wymagania wstępne

* Bieżąca wersja programu [cURL](https://curl.haxx.se/).
* Po utworzeniu subskrypcji platformy Azure utwórz zasób usługi analiza tekstu zasób analiza tekstu w witrynie Azure Portal, aby uzyskać <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Po wdrożeniu kliknij pozycję **Przejdź do zasobu**.
    * Klucz i punkt końcowy z zasobu, który utworzysz, będą potrzebne do połączenia aplikacji z interfejsem API analiza tekstu API. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
    * Możesz użyć warstwy cenowej Bezpłatna ( ), aby wypróbować usługę, i przejść później na warstwę płatną `F0` na użytek produkcji.

> [!NOTE]
> * Poniższe przykłady powłoki BASH używają `\` znaku kontynuacji wiersza. Jeśli konsola lub terminal używa innego znaku kontynuacji wiersza, użyj tego znaku.
> * Przykłady specyficzne dla języka można znaleźć w [witrynie GitHub.](https://github.com/Azure-Samples/cognitive-services-quickstart-code)
> * Przejdź do Azure Portal i znajdź klucz i punkt końcowy dla zasobu analiza tekstu utworzonego w wymaganiach wstępnych. Będą one znajdowały się  na stronie klucza i punktu końcowego zasobu w obszarze **zarządzania zasobami**. Następnie zastąp ciągi w poniższym kodzie swoim kluczem i punktem końcowym.
Do wywołania analiza tekstu API potrzebne są następujące informacje:


|parametr  |Opis  |
|---------|---------|
|`-X POST <endpoint>`     | Określa punkt końcowy do uzyskiwania dostępu do interfejsu API.        |
|`-H Content-Type: application/json`     | Typ zawartości do wysyłania danych JSON.          |
|`-H "Ocp-Apim-Subscription-Key:<key>`    | Określa klucz do uzyskiwania dostępu do interfejsu API.        |
|`-d <documents>`     | Plik JSON zawierający dokumenty, które chcesz wysłać.         |

Następujące polecenia cURL są wykonywane z powłoki BASH. Edytuj te polecenia przy użyciu własnej nazwy zasobu, klucza zasobu i wartości JSON.

## <a name="sentiment-analysis"></a>Analiza tonacji

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-31-preview"></a>[wersja 3.1-preview](#tab/version-3-1)

> [!NOTE]
> Poniższy przykład zawiera żądanie dotyczące funkcji wyszukiwania opinii w programie analiza tonacji przy użyciu parametru , który zawiera szczegółowe informacje o ocenach (przymiotnikach) związanych z elementami docelowymi `opinionMining=true` (rzeczownikami) w tekście.

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.4/sentiment?opinionMining=true \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "The customer service here is really good."}]}'
```

### <a name="json-response"></a>Odpowiedź w formacie JSON

```json
{
   "documents":[
      {
         "id":"1",
         "sentiment":"positive",
         "confidenceScores":{
            "positive":1.0,
            "neutral":0.0,
            "negative":0.0
         },
         "sentences":[
            {
               "sentiment":"positive",
               "confidenceScores":{
                  "positive":1.0,
                  "neutral":0.0,
                  "negative":0.0
               },
               "offset":0,
               "length":41,
               "text":"The customer service here is really good.",
               "targets":[
                  {
                     "sentiment":"positive",
                     "confidenceScores":{
                        "positive":1.0,
                        "negative":0.0
                     },
                     "offset":4,
                     "length":16,
                     "text":"customer service",
                     "relations":[
                        {
                           "relationType":"assessment",
                           "ref":"#/documents/0/sentences/0/assessments/0"
                        }
                     ]
                  }
               ],
               "assessments":[
                  {
                     "sentiment":"positive",
                     "confidenceScores":{
                        "positive":1.0,
                        "negative":0.0
                     },
                     "offset":36,
                     "length":4,
                     "text":"good",
                     "isNegated":false
                  }
               ]
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-04-01"
}
``` 

#### <a name="version-30"></a>[wersja 3.0](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/sentiment/ \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "I had the best day of my life. I wish you were there with me."}]}'
```

### <a name="json-response"></a>Odpowiedź w formacie JSON

```json
{
  "documents":[
    {
      "id":"1",
      "sentiment":"positive",
      "documentScores":{
        "positive":1.0,
        "neutral":0.0,
        "negative":0.0
      },
      "sentences":[
        {
          "sentiment":"positive",
          "sentenceScores":{
            "positive":1.0,
            "neutral":0.0,
            "negative":0.0
          },
          "offset":0,
          "length":30
        }
      ]
    }
  ],
  "errors":[
  ],
  "modelVersion":"2019-10-01"
}
```

---


## <a name="language-detection"></a>Wykrywanie języka

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-31-preview"></a>[wersja 3.1-preview](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.4/languages/ \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "This is a document written in English."}]}'
```

### <a name="json-response"></a>Odpowiedź w formacie JSON

```json
{
   "documents":[
      {
         "id":"1",
         "detectedLanguage":{
            "name":"English",
            "iso6391Name":"en",
            "confidenceScore":1.0
         },
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2021-01-05"
}
```

#### <a name="version-30"></a>[wersja 3.0](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/languages/ \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "This is a document written in English."}]}'
```

### <a name="json-response"></a>Odpowiedź w formacie JSON

```json
{
   "documents":[
      {
         "id":"1",
         "detectedLanguage":{
            "name":"English",
            "iso6391Name":"en",
            "confidenceScore":0.99
         },
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-09-01"
}
```

---


## <a name="named-entity-recognition-ner"></a>rozpoznawanie jednostek nazwanych (NER)

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-31-preview"></a>[wersja 3.1-preview](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.4/entities/recognition/general \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```


### <a name="json-response"></a>Odpowiedź w formacie JSON

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "text":"Seattle",
               "category":"Location",
               "subcategory":"GPE",
               "offset":26,
               "length":7,
               "confidenceScore":0.99
            },
            {
               "text":"last week",
               "category":"DateTime",
               "subcategory":"DateRange",
               "offset":34,
               "length":9,
               "confidenceScore":0.8
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2021-01-15"
}

```

### <a name="detecting-personally-identifying-information"></a>Wykrywanie informacji identyfikujących użytkowników

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

```bash
curl -X POST https://your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.4/entities/recognition/pii \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Call our office at 312-555-1234, or send an email to support@contoso.com"}]}'
```

### <a name="json-response"></a>Odpowiedź w formacie JSON

```json
{
   "documents":[
      {
         "redactedText":"Call our office at ************, or send an email to *******************",
         "id":"1",
         "entities":[
            {
               "text":"312-555-1234",
               "category":"PhoneNumber",
               "offset":19,
               "length":12,
               "confidenceScore":0.8
            },
            {
               "text":"support@contoso.com",
               "category":"Email",
               "offset":53,
               "length":19,
               "confidenceScore":0.8
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2021-01-15"
}
```

#### <a name="version-30"></a>[wersja 3.0](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/entities/recognition/general \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```


### <a name="json-response"></a>Odpowiedź w formacie JSON

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "text":"trip",
               "category":"Event",
               "offset":18,
               "length":4,
               "confidenceScore":0.61
            },
            {
               "text":"Seattle",
               "category":"Location",
               "subcategory":"GPE",
               "offset":26,
               "length":7,
               "confidenceScore":0.82
            },
            {
               "text":"last week",
               "category":"DateTime",
               "subcategory":"DateRange",
               "offset":34,
               "length":9,
               "confidenceScore":0.8
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-04-01"
}
```

---

## <a name="entity-linking"></a>Łączenie jednostek

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-31-preview"></a>[wersja 3.1-preview](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.4/entities/linking \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975."}]}'
```

### <a name="json-response"></a>Odpowiedź w formacie JSON

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "name":"Microsoft",
               "matches":[
                  {
                     "text":"Microsoft",
                     "offset":0,
                     "length":9,
                     "confidenceScore":0.48
                  }
               ],
               "language":"en",
               "id":"Microsoft",
               "url":"https://en.wikipedia.org/wiki/Microsoft",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"0d47c987-0042-5576-15e8-97af601614fa",
               "name":"Bill Gates",
               "matches":[
                  {
                     "text":"Bill Gates",
                     "offset":25,
                     "length":10,
                     "confidenceScore":0.52
                  }
               ],
               "language":"en",
               "id":"Bill Gates",
               "url":"https://en.wikipedia.org/wiki/Bill_Gates",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"df2c4376-9923-6a54-893f-2ee5a5badbc7",
               "name":"Paul Allen",
               "matches":[
                  {
                     "text":"Paul Allen",
                     "offset":40,
                     "length":10,
                     "confidenceScore":0.54
                  }
               ],
               "language":"en",
               "id":"Paul Allen",
               "url":"https://en.wikipedia.org/wiki/Paul_Allen",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"52535f87-235e-b513-54fe-c03e4233ac6e",
               "name":"April 4",
               "matches":[
                  {
                     "text":"April 4",
                     "offset":54,
                     "length":7,
                     "confidenceScore":0.38
                  }
               ],
               "language":"en",
               "id":"April 4",
               "url":"https://en.wikipedia.org/wiki/April_4",
               "dataSource":"Wikipedia"
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-02-01"
}
```

#### <a name="version-30"></a>[wersja 3.0](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/entities/linking \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975."}]}'
```


### <a name="json-response"></a>Odpowiedź w formacie JSON

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "name":"Microsoft",
               "matches":[
                  {
                     "text":"Microsoft",
                     "offset":0,
                     "length":9,
                     "confidenceScore":0.48
                  }
               ],
               "language":"en",
               "id":"Microsoft",
               "url":"https://en.wikipedia.org/wiki/Microsoft",
               "dataSource":"Wikipedia"
            },
            {
               "name":"Bill Gates",
               "matches":[
                  {
                     "text":"Bill Gates",
                     "offset":25,
                     "length":10,
                     "confidenceScore":0.52
                  }
               ],
               "language":"en",
               "id":"Bill Gates",
               "url":"https://en.wikipedia.org/wiki/Bill_Gates",
               "dataSource":"Wikipedia"
            },
            {
               "name":"Paul Allen",
               "matches":[
                  {
                     "text":"Paul Allen",
                     "offset":40,
                     "length":10,
                     "confidenceScore":0.54
                  }
               ],
               "language":"en",
               "id":"Paul Allen",
               "url":"https://en.wikipedia.org/wiki/Paul_Allen",
               "dataSource":"Wikipedia"
            },
            {
               "name":"April 4",
               "matches":[
                  {
                     "text":"April 4",
                     "offset":54,
                     "length":7,
                     "confidenceScore":0.38
                  }
               ],
               "language":"en",
               "id":"April 4",
               "url":"https://en.wikipedia.org/wiki/April_4",
               "dataSource":"Wikipedia"
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-02-01"
}
```


---


## <a name="key-phrase-extraction"></a>Wyodrębnianie kluczowych fraz

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]


#### <a name="version-31-preview"></a>[wersja 3.1-preview](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.4/keyPhrases \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```

```json
{
   "documents":[
      {
         "id":"1",
         "keyPhrases":[
            "wonderful trip",
            "Seattle",
            "week"
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-07-01"
}
```

#### <a name="version-30"></a>[wersja 3.0](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/keyPhrases \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```

### <a name="json-response"></a>Odpowiedź w formacie JSON

```json
{
   "documents":[
      {
         "id":"1",
         "keyPhrases":[
            "wonderful trip",
            "Seattle",
            "week"
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-07-01"
}
```

---
