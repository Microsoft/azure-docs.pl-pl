---
title: Analizuj wideo na żywo przy użyciu wybranego obrazu systemu Azure
description: W tym artykule dowiesz się, jak utworzyć moduł IoT Edge, który można zintegrować z usługą analiza filmów wideo na żywo na IoT Edge w celu przeanalizowania wideo na żywo przy użyciu wybranego modelu przetwarzania obrazów.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 0ac2af280eefd5ce293a8be422551d5ee6f6d3f3
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261261"
---
# <a name="analyze-live-video-with-ai-of-your-choice"></a>Analizuj wideo na żywo przy użyciu systemu AI

W tym artykule dowiesz się, jak utworzyć moduł IoT Edge, który można zintegrować z usługą analiza filmów wideo na żywo na IoT Edge w celu przeanalizowania wideo na żywo przy użyciu wybranego modelu przetwarzania obrazów. 

## <a name="pre-reading"></a>Wstępne odczytywanie

[Koncepcje grafu multimediów](media-graph-concept.md)

## <a name="media-graph-extension"></a>Rozszerzenie grafu multimediów

Funkcja analizy filmów wideo na żywo na IoT Edge definiuje model rozszerzalności, który umożliwia rozszerzanie możliwości przetwarzania wykresów multimedialnych na własne składniki analizy multimediów za pomocą rozszerzenia grafu. Składnik analizy może korzystać z tradycyjnych technik przetwarzania obrazów lub modeli AI. Rozszerzenia programu Graph są włączane przez dołączenie węzła [procesora rozszerzenia http](media-graph-concept.md#http-extension-processor) na wykresie multimedialnym. Procesor rozszerzeń HTTP może przekazywać ramki wideo do punktu końcowego HTTP określonego przez użytkownika i pełni rolę interfejsu do składnika za pośrednictwem tego. Połączenie może być nawiązywane w lokalnym lub zdalnym punkcie końcowym i może być zabezpieczone przez uwierzytelnianie i szyfrowanie TLS, jeśli jest to wymagane. Dodatkowo procesor umożliwia opcjonalne skalowanie i kodowanie klatek wideo przed ich przekazaniem do przodu.

Można wybrać, aby uruchomić wybrany model wnioskowania na dowolnym dostępnym środowisku uruchomieniowym wnioskowania, takim jak ONNX, TensorFlow, PyTorch lub innych w odniesieniu do własnego kontenera Docker. Możesz również użyć wybranego języka i bibliotek, aby uwidocznić możliwości inferencing obrazów, chociaż serwer HTTP we własnym kontenerze. Kontener inferencing należy wdrożyć razem z modułem usługi analiz wideo na żywo w celu uzyskania najlepszej wydajności, a następnie będzie wywoływany za pośrednictwem procesora rozszerzenia HTTP w topologii grafu.
Ponadto częstotliwość wywołań do modułu niestandardowego można ograniczyć, opcjonalnie dodając [procesor detektora ruchu](media-graph-concept.md#motion-detection-processor) i przechodzący [procesor filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) do procesora rozszerzeń http.

Na poniższym diagramie przedstawiono przepływ danych wysokiego poziomu:

![Urządzenie brzegowe](./media/analyze-live-video-with-ai-your-choice-how-to/edge-device.png)

Dzięki temu można analizować wideo przy użyciu wybranych przez Ciebie modeli AI do zaspokajania unikatowych potrzeb firmy. Modele AI mogą pochodzić z społeczności typu "open source" lub od własnych analityków danych lub z wyspecjalizowanego dostawcy AI.

## <a name="media-graph-http-extension-contract-definitions"></a>Definicje kontraktu rozszerzenia HTTP programu Media Graph

Ta sekcja definiuje kontrakt HTTP definiujący przepływ danych.

### <a name="http-extensibility-protocol"></a>Protokół rozszerzalności HTTP  

Kontrakt HTTP został zdefiniowany w następujący sposób:

* Moduł działa jako serwer HTTP.
* Analiza filmów wideo na żywo na IoT Edge module działa jako klient HTTP.

### <a name="request"></a>Żądanie

Żądania z modułu analizy wideo na żywo do modułu będą następujące:

|||
|---|---|
|POST| `https://hostname/optional-path?optional-query`|
|Zaakceptuj|Application/JSON,*/*|
|Autoryzacja| Basic, Digest, Bearer (za pomocą obsługi niestandardowego nagłówka)|
|Content-Type|image/jpeg<br/>image/png<br/>image/bmp<br/>obraz/x — RAW|
|Długość zawartości|Długość treści w bajtach|
|User-Agent|Azure Media Services|
|Treść|Bajty obrazów, binarne zakodowane w jednym z obsługiwanych typów zawartości.|

#### <a name="example"></a>Przykład

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>Odpowiedź

Odpowiedzi z modułu do modułu analizy wideo na żywo powinny być następujące:

|||
|---|---|
|Kody stanu|200 OK — znaleziono wyniki wnioskowania<br/>204 Brak zawartości — nie znaleziono zawartości przez AI<br/>400 Nieprawidłowe żądanie — nieoczekiwane<br/>Błąd wewnętrzny serwera 500 — nieoczekiwany<br/>503 serwer zajęty — AMS zostanie wycofana w oparciu o nagłówek "Ponów próbę po" lub na podstawie domyślnej wielkości czasu w nagłówku przypadku, który nie jest wstępnie ustawiony.|
|Content-Type|application/json|
|Długość zawartości|    Długość treści w bajtach|
|Treść|Obiekt JSON z pojedynczą właściwością wnioskowania.|

#### <a name="example"></a>Przykład

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

Zdecydowanie zaleca się, aby odpowiedzi są zwracane przy użyciu prawidłowych dokumentów JSON, zgodnie ze wstępnie ustalonym schematem zdefiniowanym poniżej. Pozwoli to lepiej zapewnić współdziałanie z innymi składnikami i możliwymi przyszłymi możliwościami dodanymi do modułu analizy wideo na żywo.

Jeśli moduł zwróci odpowiedź, gdy typem zawartości nie jest "Application/JSON", analiza wideo na żywo będzie kodować komunikat jako podstawową zawartość 64 i serializować go jako nieprzezroczysty ładunek JSON.

Jeśli moduł zwróci odpowiedź z typem zawartości jako "Application/JSON", ale schemat JSON nie postępuje zgodnie ze [schematem metadanych wnioskowania](#inference-metadata-schema)opisanym poniżej, ładunek wiadomości zostanie przekazany przez potok, ale współdziałanie zostanie zredukowane.

> [!NOTE]
> Jeśli moduł nie wygenerował żadnego wyniku, powinien zwrócić kod stanu HTTP 204 (brak zawartości) z pustą treścią odpowiedzi. Analiza filmów wideo na żywo będzie zrozumieć ten element jako pusty wynik i nie spowoduje przekazanie zdarzenia w toku.

### <a name="inference-metadata-schema"></a>Schemat metadanych wnioskowania

Każdy obiekt wnioskowania jest zgodny z tym nadzbiorowym schematem:

```
{
  "type": "[classification|motion|entity|text|other]",
  "subtype": "",              // Free form subtype id

  // Object has *at most one of* the following
  "classification":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ]
  },

  "motion":
  {
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
    // "regionId": ""
  },

  "entity":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ],
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
  },

  "text":
  {
    "value": "",              // Text value
    "language": "",           // Optional BCP47 Language Code (https://tools.ietf.org/html/bcp47)
    "startTimestamp": 0,      // Optional start timestamp
    "endTimestamp": 0         // Optional end timestamp
  },

  // Plus every object can have zero or more extensions
  "extensions":
  {
    "name1": "value1",
    "name2": "value2"
    // ...
  }
}
```
 
### <a name="data-contracts---class-hierarchy"></a>Kontrakty danych — hierarchia klas

![Kontrakty danych — hierarchia klas](./media/analyze-live-video-with-ai-your-choice-how-to/data-contracts.png)

### <a name="examples"></a>Przykłady  

Poniższy przykład zawiera jedno zdarzenie ze wszystkimi obsługiwanymi typami wnioskowania:

```
{
  "inferences": [
    // Light detection
    {
      "type": "classification",
      "subtype": "lightDetection",
      "classification": {
        "tag": { "value": "daylight", "confidence": 0.86 },
        "attributes": [
            { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 }
        ]
      }
    },

    // Motion detection
    {
      "type": "motion",
      "subtype": "motionDetection",
      "motion":
      {
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Object detection
    {
      "type": "entity",
      "subtype": "objectDetection",    
      "entity":
      {
        "tag": { "value": "dog", "confidence": 0.97 },
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Vehicle identification
    {
      "type": "entity",
      "subtype": "vehicleIdentification",    
      "entity":
      {
        "tag": { "value": "007-SPY", "confidence": 0.82 },
        "attributes":[  
          { "name": "color", "value": "black", "confidence": 0.90 },
          { "name": "body", "value": "coupe", "confidence": 0.87 },
          { "name": "make", "value": "Aston Martin", "confidence": 0.35 },
          { "name": "model", "value": "DBS V12", "confidence": 0.33 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // People identification
    {
      "type": "entity",
      "subtype": "peopleIdentification",    
      "entity":
      {
        "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 },
        "attributes":[  
          { "name": "age", "value": "73", "confidence": 0.87 },
          { "name": "glasses", "value": "yes", "confidence": 0.94 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      },
    }
  ]
}
```
 
## <a name="sample-http-extension-modules"></a>Przykładowe moduły rozszerzenia HTTP

Kilka przykładowych modułów rozszerzeń HTTP można znaleźć w [repozytorium GitHub usługi wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis). W jednym z tych [przykładów analizy wideo](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) pokazano, jak używać modelu [Yolov3](https://pjreddie.com/darknet/yolo/) [ONNX](http://onnx.ai/) do kompilowania modułu IoT Edge na potrzeby wykrywania obiektów. Możesz użyć tego samego podejścia, aby utworzyć własny moduł z wybranym przez Ciebie modelem AI.

## <a name="next-steps"></a>Następne kroki

[Rozwiązywanie problemów](troubleshoot-how-to.md)
