---
title: 'Szybki start: wywoływanie interfejsu API analizy tekstu przy użyciu języka PHP'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start pokazano, jak używać języka PHP do uzyskiwania informacji i przykładów kodu, aby ułatwić szybkie rozpoczęcie korzystania z interfejs API analizy tekstu na platformie Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/10/2020
ms.author: aahi
ms.openlocfilehash: 09901c514a1f2b94edc3e4108ec46051a284a6db
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90527262"
---
# <a name="quickstart-using-php-to-call-the-text-analytics-cognitive-service"></a>Szybki start: wywoływanie analizy tekstu usługi Cognitive Service przy użyciu języka PHP
<a name="HOLTop"></a>

W tym artykule przedstawiono, jak [wykrywać język](#Detect), [analizować tonację](#SentimentAnalysis), [wyodrębniać kluczowe frazy](#KeyPhraseExtraction) i [identyfikować połączone jednostki](#Entities) przy użyciu [interfejsów API analizy tekstu](//go.microsoft.com/fwlink/?LinkID=759711) i języka PHP.

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="Detect"></a>

## <a name="detect-language"></a>Wykrywanie języka

Interfejs API wykrywania języka wykrywa język dokumentu tekstowego przy użyciu [metody Detect Language](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

1. Utwórz nowy projekt PHP w ulubionym środowisku IDE.
1. Dodaj kod przedstawiony poniżej.
1. Skopiuj klucz analiza tekstu i punkt końcowy do kodu.
1. Uruchomisz program.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"

$subscription_key = "<paste-your-text-analytics-key-here>";
$endpoint = "<paste-your-text-analytics-endpoint-here>";

$path = '/text/analytics/v3.0/languages';

function DetectLanguage ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'text' => 'This is a document written in English.' ),
        array ( 'id' => '2', 'text' => 'Este es un document escrito en Español.' ),
        array ( 'id' => '3', 'text' => '这是一个用中文写的文件')
    )
);

print "Please wait a moment for the results to appear.";

$result = DetectLanguage ($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
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

Interfejs API analizy tonacji wykrywa tonację zestawu rekordów tekstowych przy użyciu [metody Sentiment](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9). W poniższym przykładzie oceniane są dwa dokumenty, jeden w języku angielskim, a drugi w hiszpańskim.


1. Utwórz nowy projekt PHP w ulubionym środowisku IDE.
1. Dodaj kod przedstawiony poniżej.
1. Skopiuj klucz analiza tekstu i punkt końcowy do kodu.
1. Uruchomisz program.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"
$subscription_key = "<paste-your-text-analytics-key-here>";
$endpoint = "<paste-your-text-analytics-endpoint-here>";

$path = '/text/analytics/v3.0/sentiment';

function GetSentiment ($host, $path, $key, $data) {
    // Make sure all text is UTF-8 encoded.
    foreach ($data as &$item) {
        foreach ($item as $ignore => &$value) {
            $value['text'] = utf8_encode($value['text']);
        }
    }

    $data = json_encode ($data);

    $headers = "Content-type: text/json\r\n" .
        "Content-Length: " . strlen($data) . "\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' ),
        array ( 'id' => '2', 'language' => 'es', 'text' => 'Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico.' )
    )
);

print "Please wait a moment for the results to appear.";

$result = GetSentiment($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Odpowiedź analizy tonacji**

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

Interfejs API wyodrębniania kluczowych fraz wyodrębnia kluczowe frazy w dokumencie tekstowym przy użyciu [metody Key Phrases](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). W poniższym przykładzie wyodrębniono frazy kluczowe dla dokumentów w języku angielskim i hiszpańskim.
1. Utwórz nowy projekt PHP w ulubionym środowisku IDE.
1. Dodaj kod przedstawiony poniżej.
1. Skopiuj klucz analiza tekstu i punkt końcowy do kodu.
1. Uruchomisz program.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"
$subscription_key = "<paste-your-text-analytics-key-here>";
$endpoint = "<paste-your-text-analytics-endpoint-here>";

$path = '/text/analytics/v3.0/keyPhrases';

function GetKeyPhrases ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";

    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable.' ),
        array ( 'id' => '2', 'language' => 'es', 'text' => 'Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema.' ),
        array ( 'id' => '3', 'language' => 'en', 'text' => 'The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I\'ve ever seen.' )
    )
);

print "Please wait a moment for the results to appear.";

$result = GetKeyPhrases($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
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

## <a name="identify-entities"></a>Identyfikowanie jednostek 

Interfejs API jednostek identyfikuje dobrze znane jednostki w dokumencie tekstowym przy użyciu [metody Entities](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634). W poniższym przykładzie zidentyfikowano jednostki dla dokumentów w języku angielskim.

1. Utwórz nowy projekt PHP w ulubionym środowisku IDE.
1. Dodaj kod przedstawiony poniżej.
1. Skopiuj klucz analiza tekstu i punkt końcowy do kodu. 
1. Uruchomisz program.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll
// You might need to set the full path, for example:
// extension="C:\Program Files\Php\ext\php_openssl.dll"
$subscription_key = "<paste-your-text-analytics-key-here>";
$endpoint = "<paste-your-text-analytics-endpoint-here>";

$path = '/text/analytics/v3.0/entities/recognition/general';

function GetEntities ($host, $path, $key, $data) {

    $headers = "Content-type: text/json\r\n" .
        "Content-Length: " . strlen($data) . "\r\n" .
        "Ocp-Apim-Subscription-Key: $key\r\n";
    $data = json_encode ($data);

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // https://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'POST',
            'content' => $data
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path, false, $context);
    return $result;
}

$data = array (
    'documents' => array (
        array ( 'id' => '1', 'language' => 'en', 'text' => 'Microsoft is and It company.' ),
    )
);

print "Please wait a moment for the results to appear.";

$result = GetEntities($endpoint, $path, $subscription_key, $data);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
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
