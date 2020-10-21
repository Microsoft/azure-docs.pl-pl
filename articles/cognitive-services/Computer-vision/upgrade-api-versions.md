---
title: Uaktualnij do wersji v 3.0 interfejs API przetwarzania obrazów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić uaktualnienie do interfejsu API odczytu przetwarzanie obrazów v 3.0 z wersji 2.0/v 2.1.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: cfc9745fc4684a7b0d8f7da7e63149a6fe50f6d2
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331842"
---
# <a name="upgrade-from-read-v2x-to-read-v3x"></a>Uaktualnij z odczytu v2. x, aby odczytać v3. x

W tym przewodniku pokazano, jak uaktualnić istniejący kontener lub kod interfejsu API chmury z odczytu v2. x, aby przeczytać artykuł v 3.0 i v 3.1 Preview.

## <a name="determine-your-api-path"></a>Określanie ścieżki interfejsu API
Skorzystaj z poniższej tabeli, aby określić **ciąg wersji** w ścieżce interfejsu API na podstawie wersji do odczytu 3. x, do której jest przeprowadzana migracja.

|Typ produktu| Wersja | Ciąg wersji w ścieżce interfejsu API 3. x |
|:-----|:----|:----|
|Usługa | Przeczytaj 3,0 lub 3,1 | odpowiednio **3.0** lub **v 3.1** |
|Usługa | Przeczytaj Podgląd 3,2 | **v 3.2 — wersja zapoznawcza 1** |
|Kontener | Przeczytaj artykuł 3,0 Preview lub przeczytaj wersję zapoznawczą 3,1 | **v 3.0** lub **v 3.1 — wersja zapoznawcza. 2** odpowiednio |


Następnie skorzystaj z poniższych sekcji, aby zawęzić operacje i zastąpić **ciąg wersji** w ścieżce interfejsu API wartością z tabeli. Na przykład w przypadku **odczytu wersja zapoznawcza** i wersje kontenerów w wersji v 3.2 należy zaktualizować ścieżkę interfejsu API do **protokołu https://{Endpoint}/Vision/v 3.2 — wersja zapoznawcza. 1/odczyt/analiza [? język]**.

## <a name="servicecontainer"></a>Usługa/kontener

### `Batch Read File`

|Przeczytaj 2. x |Przeczytaj 3. x  |
|----------|-----------|
|https://{Endpoint}/Vision/**v 2.0/Read/Core/asyncBatchAnalyze**     |ciąg https://{Endpoint}/wersja wizji/<**wersji**>/Read/analyze [? language]|
    
Dostępny jest nowy opcjonalny parametr _języka_ . Jeśli nie znasz języka dokumentu lub plik może być wielojęzyczny, nie dołączaj go. 

### `Get Read Results`

|Przeczytaj 2. x |Przeczytaj 3. x  |
|----------|-----------|
|https://{Endpoint}/Vision/**v 2.0/Read/Operations**/{operationId}     |ciąg https://{Endpoint}/ **wersja** vision/<>/Read/analyzeResults/{operationId}|

### <a name="get-read-operation-result-status-flag"></a>`Get Read Operation Result` Flaga stanu

Gdy wywołanie `Get Read Operation Result` powiedzie się, zwraca pole ciągu stanu w treści JSON.
 
|Przeczytaj 2. x |Przeczytaj 3. x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|
    
### <a name="api-response-json"></a>Odpowiedź interfejsu API (JSON) 

Zwróć uwagę na następujące zmiany w kodzie JSON:
* W wersji 2. x `Get Read Operation Result` zwróci kod JSON rozpoznawania OCR, gdy stan to `Succeeded"` . W wersji 3.0 to pole jest `succeeded` .
* Aby uzyskać pierwiastek dla tablicy stronicowej, Zmień hierarchię JSON z `recognitionResults` na `analyzeResult` / `readResults` . Hierarchia JSON dla wiersza i słów jest niezmieniona, więc nie są wymagane żadne zmiany w kodzie.
* `clockwiseOrientation`Zmieniono nazwę kąta strony na `angle` , a zakres został zmieniony z 0-360 stopni do-180 do 180 stopni. W zależności od kodu, może być konieczne wprowadzenie zmian, ponieważ większość funkcji matematycznych może obsłużyć każdy zakres.

W interfejsie API programu v 3.0 wprowadzono również następujące udoskonalenia:
* `createdDateTime` i `lastUpdatedDateTime` są dodawane, aby można było śledzić czas przetwarzania. Aby uzyskać więcej informacji, zobacz dokumentację. 
* `version` informuje o wersji interfejsu API używanej do generowania wyników
* Dodano słowo dla każdego wyrazu `confidence` . Ta wartość jest skalibrowane, aby wartość 0,95 oznaczała, że występuje 95% prawdopodobieństwa, że rozpoznawanie jest poprawne. Za pomocą oceny zaufania można wybrać tekst do wysłania do recenzji przez człowieka. 
    
    
W 2. X format danych wyjściowych jest następujący: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
}
```
    
W programie v 3.0 został dostosowany:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="service-only"></a>Tylko usługa

### `Recognize Text`
`Recognize Text` jest operacją w *wersji zapoznawczej* , która jest *przestarzała we wszystkich wersjach interfejs API przetwarzania obrazów*. Należy przeprowadzić migrację z `Recognize Text` programu do `Read` wersji (v 3.0) lub `Batch Read File` (v 2.0, v 2.1). Wersja 3.0 systemu `Read` zawiera nowsze, lepsze modele na potrzeby rozpoznawania tekstu i dodatkowych funkcji, więc jest zalecana. Aby uaktualnić z `Recognize Text` programu do wersji `Read` :

|Rozpoznawanie tekstu 2. x |Przeczytaj 3. x  |
|----------|-----------|
|https://{Endpoint}/Vision/**v 2.0/recognizeText [? Mode]**|ciąg https://{Endpoint}/wersja wizji/<**wersji**>/Read/analyze [? language]|
    
Parametr _mode_ nie jest obsługiwany w programie `Read` . Tekst ręcznie i drukowany są automatycznie obsługiwane.
    
Nowy opcjonalny parametr _języka_ jest dostępny w wersji 3.0. Jeśli nie znasz języka dokumentu lub plik może być wielojęzyczny, nie dołączaj go. 

### `Get Recognize Text Operation Result`

|Rozpoznawanie tekstu 2. x |Przeczytaj 3. x  |
|----------|-----------|
|https://{Endpoint}/Vision/**v 2.0/Textoperations/**{operationId}|ciąg https://{Endpoint}/ **wersja** vision/<>/Read/analyzeResults/{operationId}|

### <a name="get-recognize-text-operation-result-status-flags"></a>`Get Recognize Text Operation Result` flagi stanu
Gdy wywołanie `Get Recognize Text Operation Result` powiedzie się, zwraca pole ciągu stanu w treści JSON. 
 
|Rozpoznawanie tekstu 2. x |Przeczytaj 3. x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|

### <a name="api-response-json"></a>Odpowiedź interfejsu API (JSON)

Zwróć uwagę na następujące zmiany w kodzie JSON:    
* W wersji 2. x `Get Read Operation Result` zwróci kod JSON rozpoznawania OCR, gdy stan to `Succeeded` . W wersji 3. x to pole jest `succeeded` .
* Aby uzyskać pierwiastek dla tablicy stronicowej, Zmień hierarchię JSON z `recognitionResult` na `analyzeResult` / `readResults` . Hierarchia JSON dla wiersza i słów jest niezmieniona, więc nie są wymagane żadne zmiany w kodzie.

W interfejsie API programu v 3.0 wprowadzono również następujące udoskonalenia, które można opcjonalnie wykorzystać. Aby uzyskać więcej informacji, zobacz informacje o interfejsie API:
* `createdDateTime` i `lastUpdatedDateTime` są dodawane, aby można było śledzić czas przetwarzania. Aby uzyskać więcej informacji, zobacz dokumentację. 
* `version` informuje o wersji interfejsu API używanej do generowania wyników
* Dodano słowo dla każdego wyrazu `confidence` . Ta wartość jest skalibrowane, aby wartość 0,95 oznaczała, że występuje 95% prawdopodobieństwa, że rozpoznawanie jest poprawne. Za pomocą oceny zaufania można wybrać tekst do wysłania do recenzji przez człowieka. 
* `angle` Ogólna Orientacja tekstu w kierunku zgodnym z ruchem, mierzona w stopniach od (-180, 180).
* `width` i `"height"` dostarczają wymiary dokumentu i `"unit"` udostępnia jednostkę dla wymiarów (piksele lub cale, w zależności od typu dokumentu).
* `page` Obsługiwane są dokumenty wielostronicowe
* `language` język wejściowy dokumentu (z opcjonalnego parametru _Language_ ).


W 2. X format danych wyjściowych jest następujący: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
```
    
W wersji 3. x został dostosowany:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="container-only"></a>Tylko kontener

### `Synchronous Read`

|Przeczytaj 2,0 |Przeczytaj 3. x  |
|----------|-----------|
|https://{Endpoint}/Vision/**v 2.0/Read/Core/Analizuj**     |ciąg https://{Endpoint}/wersja wizji/<**wersji**>/Read/syncAnalyze [? language]|
