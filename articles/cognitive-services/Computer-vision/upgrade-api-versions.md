---
title: Uaktualnianie do wersji 3.0 interfejsu API przetwarzanie obrazów Api
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak uaktualnić interfejs API przetwarzanie obrazów w wersji 3.0 z wersji 2.0/2.1.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 7a05b04872b4f957e879d93972edc45e2932d059
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364094"
---
# <a name="upgrade-from-read-v2x-to-read-v3x"></a>Uaktualnianie z wersji 2.x odczytu do wersji 3.x

W tym przewodniku pokazano, jak uaktualnić istniejący kontener lub kod interfejsu API w chmurze z read v2.x do Read v3.x.

## <a name="determine-your-api-path"></a>Określanie ścieżki interfejsu API
Użyj poniższej tabeli, aby określić ciąg wersji w **ścieżce** interfejsu API na podstawie wersji Read 3.x, do którą jest migrowanie.

|Typ produktu| Wersja | Ciąg wersji w ścieżce interfejsu API 3.x |
|:-----|:----|:----|
|Usługa | Przeczytaj 3.0 lub 3.1 | **Odpowiednio w wersji 3.0** **lub 3.1** |
|Usługa | Przeczytaj 3.2 (wersja zapoznawcza) | **Wersja 3.2-preview.1** |
|Kontener | Przeczytaj 3.0 (wersja zapoznawcza) lub Read 3.1 preview (Odczyt 3.1) | **Odpowiednio w wersji 3.0** **lub 3.1-preview.2** |


Następnie użyj poniższych sekcji, aby zawęzić operacje i zastąpić ciąg wersji w ścieżce interfejsu API wartością z tabeli.  Na przykład w przypadku wersji zapoznawczej wersji **3.2** odczytu z chmury i kontenera zaktualizuj ścieżkę interfejsu API do **https://{endpoint}/vision/v3.2-preview.1/read/analyze[?language]**.

## <a name="servicecontainer"></a>Usługa/kontener

### `Batch Read File`

|Przeczytaj 2.x |Przeczytaj 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/core/asyncBatchAnalyze**     |https://{endpoint}/vision/<**version string**>/read/analyze[?language]|
    
Dostępny jest _nowy opcjonalny_ parametr języka. Jeśli nie znasz języka dokumentu lub może on być wielojęzyczny, nie dołączaj go. 

### `Get Read Results`

|Przeczytaj 2.x |Przeczytaj 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/operations**/{operationId}     |https://{endpoint}/vision/<**ciąg** wersji>/read/analyzeResults/{operationId}|

### <a name="get-read-operation-result-status-flag"></a>`Get Read Operation Result` flaga stanu

Po pomyślnym `Get Read Operation Result` wywołaniu metody zwraca ono pole ciągu stanu w treści JSON.
 
|Przeczytaj 2.x |Przeczytaj 3.x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|
    
### <a name="api-response-json"></a>Odpowiedź interfejsu API (JSON) 

Zwróć uwagę na następujące zmiany w danych JSON:
* W wersji 2.x `Get Read Operation Result` program zwróci kod JSON rozpoznawania OCR, gdy jego stan to `Succeeded"` . W wersji 3.0 to pole to `succeeded` .
* Aby uzyskać katalog główny dla tablicy stron, zmień hierarchię JSON z `recognitionResults` na `analyzeResult` / `readResults` . Hierarchia JSON dla wiersza strony i słów pozostaje niezmieniona, więc nie są wymagane żadne zmiany kodu.
* Nazwa kąta strony została zmieniona na i zakres został zmieniony z `clockwiseOrientation` `angle` 0–360 stopni na -180 do 180 stopni. W zależności od kodu może być lub nie trzeba wprowadzać zmian, ponieważ większość funkcji matematycznych może obsługiwać oba zakresy.

Interfejs API w wersji 3.0 wprowadza również następujące ulepszenia, które opcjonalnie można wykorzystać:
* `createdDateTime` i `lastUpdatedDateTime` są dodawane, aby można było śledzić czas trwania przetwarzania. Więcej szczegółów można znaleźć w dokumentacji. 
* `version` informuje o wersji interfejsu API używanej do generowania wyników
* Dodano wartość `confidence` dla każdego wyrazu. Ta wartość jest skalibrowana tak, aby wartość 0,95 oznaczała, że istnieje 95% prawdopodobieństwo, że rozpoznawanie jest poprawne. Za pomocą oceny ufności można wybrać tekst do wysłania do przeglądu przez człowieka. 
    
    
W wersji 2.X format danych wyjściowych jest następujący: 
    
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
    
W wersji 3.0 został on dostosowany:
    
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
`Recognize Text`to operacja *w wersji zapoznawczej,* która *jest przestarzała we wszystkich wersjach interfejsu API przetwarzanie obrazów .* Należy przeprowadzić migrację z `Recognize Text` wersji `Read` do wersji (3.0) lub `Batch Read File` (2.0, 2.1). Wersja 3.0 programu obejmuje nowsze, lepsze modele rozpoznawania tekstu i dodatkowych funkcji, dlatego `Read` jest to zalecane. Aby uaktualnić z `Recognize Text` programu do `Read` programu :

|Rozpoznawanie tekstu 2.x |Przeczytaj 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/recognizeText[?mode]**|https://{endpoint}/vision/<**version string**>/read/analyze[?language]|
    
Parametr _mode_ nie jest obsługiwany w programie `Read` . Tekst odręczny i drukowany będą obsługiwane automatycznie.
    
Nowy opcjonalny _parametr języka_ jest dostępny w wersji 3.0. Jeśli nie znasz języka dokumentu lub może on być wielojęzyczny, nie dołączaj go. 

### `Get Recognize Text Operation Result`

|Rozpoznawanie tekstu 2.x |Przeczytaj 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/textOperations/**{operationId}|https://{endpoint}/vision/<**ciąg** wersji>/read/analyzeResults/{operationId}|

### <a name="get-recognize-text-operation-result-status-flags"></a>`Get Recognize Text Operation Result` flagi stanu
Po pomyślnym `Get Recognize Text Operation Result` wywołaniu metody zwraca ono pole ciągu stanu w treści JSON. 
 
|Rozpoznawanie tekstu 2.x |Przeczytaj 3.x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|

### <a name="api-response-json"></a>Odpowiedź interfejsu API (JSON)

Zwróć uwagę na następujące zmiany w danych JSON:    
* W wersji 2.x `Get Read Operation Result` program zwróci kod JSON rozpoznawania OCR, gdy jego stan to `Succeeded` . W wersji 3.x to pole to `succeeded` .
* Aby uzyskać katalog główny dla tablicy stron, zmień hierarchię JSON z `recognitionResult` na `analyzeResult` / `readResults` . Hierarchia JSON dla wiersza strony i słów pozostaje niezmieniona, więc nie są wymagane żadne zmiany kodu.

Interfejs API w wersji 3.0 wprowadza również następujące ulepszenia, które można opcjonalnie wykorzystać. Aby uzyskać więcej informacji, zobacz dokumentacja interfejsu API:
* `createdDateTime` i `lastUpdatedDateTime` są dodawane, aby można było śledzić czas trwania przetwarzania. Więcej szczegółów można znaleźć w dokumentacji. 
* `version` informuje o wersji interfejsu API używanej do generowania wyników
* Dodano wartość `confidence` dla każdego wyrazu. Ta wartość jest skalibrowana tak, aby wartość 0,95 oznaczała, że istnieje 95% prawdopodobieństwo, że rozpoznawanie jest poprawne. Za pomocą oceny ufności można wybrać tekst do wysłania do przeglądu przez człowieka. 
* `angle` ogólna orientacja tekstu w kierunku wskazówek zegara mierzona w stopniach od (-180, 180].
* `width` i dają wymiary dokumentu oraz zapewniają jednostkę tych wymiarów (piksele lub cale, w `"height"` `"unit"` zależności od typu dokumentu).
* `page` dokumenty wielostronicowe są obsługiwane
* `language`język wejściowy dokumentu (z opcjonalnego _parametru języka)._


W wersji 2.X format danych wyjściowych jest następujący: 
    
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
    
W wersji 3.x został on dostosowany:
    
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

|Przeczytaj 2.0 |Przeczytaj 3.x  |
|----------|-----------|
|https://{endpoint}/vision/**v2.0/read/core/Analyze**     |https://{endpoint}/vision/<**version string**>/read/syncAnalyze[?language]|
