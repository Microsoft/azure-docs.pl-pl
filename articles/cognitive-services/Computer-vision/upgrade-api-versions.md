---
title: Uaktualnij do wersji 3.0 interfejs API przetwarzania obrazów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić uaktualnienie z wersji 2.0 i 1.0 do wersji 3.0 interfejs API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 16add0dce88d0f809dc291d3c9de33e1a853f257
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136503"
---
# <a name="upgrade-to-v30-of-computer-vision-api-from-v20-and-v21"></a>Uaktualnienie do wersji 3.0 programu interfejs API przetwarzania obrazów z wersji 2.0 i v 2.1

W tym przewodniku pokazano, jak zmodyfikować istniejący kod w celu migrowania z programu v 2.0 lub v 2.1 interfejs API przetwarzania obrazów do wersji 3.0 dla użytkowników interfejsu API REST. 

## <a name="upgrade-batch-read-file-to-read"></a>Uaktualnij `Batch Read File` do programu`Read`


1. Zmień ścieżkę interfejsu API dla `Batch Read File` 2. x w następujący sposób:


    |Przeczytaj 2. x |Przeczytaj 3,0  |
    |----------|-----------|
    |https://{Endpoint}/Vision/**v 2.0/Read/Core/asyncBatchAnalyze**     |https://{Endpoint}/Vision/**v 3.0/Read/Analizuj**[? language]|
    
    Dostępny jest nowy opcjonalny parametr _języka_ . Jeśli nie znasz języka dokumentu lub plik może być wielojęzyczny, nie dołączaj go. 

2. Zmień ścieżkę interfejsu API dla `Get Read Results` programu w 2. x w następujący sposób:

    |Przeczytaj 2. x |Przeczytaj 3,0  |
    |----------|-----------|
    |https://{Endpoint}/Vision/**v 2.0**/Read/**Operations**/{operationId}     |https://{Endpoint}/Vision/**v 3.0**/Read/**analyzeResults**/{operationId}|

3. Zmień kod służący do sprawdzania wyników w formacie JSON z elementu `Get Read Operation Result` . Gdy wywołanie `Get Read Operation Result` powiedzie się, zwraca pole ciągu stanu w treści JSON. Następujące wartości z wersji 2.0 zostały zmienione w celu lepszego dopasowania z innymi interfejsami API REST usługi poznawczej. 
 
    |Przeczytaj 2. x |Przeczytaj 3,0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|
    
4. Zmień kod, aby interpretować końcowy wynik kodu JSON z `Get Read Operation Result` . 

    Zwróć uwagę na następujące zmiany w kodzie JSON:
    
    - W wersji 2. x `"Get Read Operation Result"` zwróci kod JSON rozpoznawania OCR, gdy stan to `"Succeeded"` . W wersji 3.0 to pole jest `"succeeded"` .
    - Aby uzyskać pierwiastek dla tablicy stronicowej, Zmień hierarchię JSON z `"recognitionResults"` na `"analyzeResult"` / `"readResults"` . Hierarchia JSON dla wiersza i słów jest niezmieniona, więc nie są wymagane żadne zmiany w kodzie.
    -   `"clockwiseOrientation"`Zmieniono nazwę kąta strony na `"angle"` , a zakres został zmieniony z 0-360 stopni do-180 do 180 stopni. W zależności od kodu, może być konieczne wprowadzenie zmian, ponieważ większość funkcji matematycznych może obsłużyć każdy zakres.
    -   W interfejsie API programu v 3.0 wprowadzono również następujące udoskonalenia, które można opcjonalnie wykorzystać:- `"createdDateTime"` i `"lastUpdatedDateTime"` są dodawane, aby można było śledzić czas przetwarzania. Aby uzyskać więcej informacji, zobacz dokumentację. 
        - `"version"`informuje o wersji interfejsu API używanej do generowania wyników
        - Dodano słowo dla każdego wyrazu `"confidence"` . Ta wartość jest skalibrowane, aby wartość 0,95 oznaczała, że występuje 95% prawdopodobieństwa, że rozpoznawanie jest poprawne. Za pomocą oceny zaufania można wybrać tekst do wysłania do recenzji przez człowieka. 
    
    
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

## <a name="upgrade-from-recognize-text-to-read"></a>Uaktualnij z `Recognize Text` programu do wersji`Read`
`Recognize Text`jest operacją w *wersji zapoznawczej* , która jest *przestarzała we wszystkich wersjach interfejs API przetwarzania obrazów*. Należy przeprowadzić migrację z `Recognize Text` programu do `Read` wersji (v 3.0) lub `Batch Read File` (v 2.0, v 2.1). Wersja 3.0 systemu `Read` zawiera nowsze, lepsze modele na potrzeby rozpoznawania tekstu i dodatkowych funkcji, więc jest zalecana. Aby uaktualnić z `Recognize Text` programu do wersji `Read` :

1. Zmień ścieżkę interfejsu API dla `Recognize Text` wersji 2. x w następujący sposób:


    |Rozpoznawanie tekstu 2. x |Przeczytaj 3,0  |
    |----------|-----------|
    |https://{Endpoint}/Vision/**v 2.0/recognizeText [? Mode]**|https://{Endpoint}/Vision/**v 3.0/Read/Analizuj**[? language]|
    
    Parametr _mode_ nie jest obsługiwany w programie `Read` . Tekst ręcznie i drukowany są automatycznie obsługiwane.
    
    Nowy opcjonalny parametr _języka_ jest dostępny w wersji 3.0. Jeśli nie znasz języka dokumentu lub plik może być wielojęzyczny, nie dołączaj go. 

2. Zmień ścieżkę interfejsu API dla `Get Recognize Text Operation Result` wersji 2. x w następujący sposób:

    |Rozpoznawanie tekstu 2. x |Przeczytaj 3,0  |
    |----------|-----------|
    |https://{Endpoint}/Vision/**v 2.0/Textoperations/**{operationId}|https://{Endpoint}/Vision/**v 3.0/Read/analyzeResults**/{operationId}|

3. Zmień kod służący do sprawdzania wyników w formacie JSON z elementu `Get Recognize Text Operation Result` . Gdy wywołanie `Get Read Operation Result` powiedzie się, zwraca pole ciągu stanu w treści JSON. 
 
    |Rozpoznawanie tekstu 2. x |Przeczytaj 3,0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|


4. Zmień kod, aby interpretował dane JSON końcowego wyniku rozpoznawania z `Get Recognize Text Operation Result` do obsługi `Get Read Operation Result` .

    Zwróć uwagę na następujące zmiany w kodzie JSON:    

    - W wersji 2. x `"Get Read Operation Result"` zwróci kod JSON rozpoznawania OCR, gdy stan to `"Succeeded"` . W wersji 3.0 to pole jest `"succeeded"` .
    - Aby uzyskać pierwiastek dla tablicy stronicowej, Zmień hierarchię JSON z `"recognitionResult"` na `"analyzeResult"` / `"readResults"` . Hierarchia JSON dla wiersza i słów jest niezmieniona, więc nie są wymagane żadne zmiany w kodzie.
    -   W interfejsie API programu v 3.0 wprowadzono również następujące udoskonalenia, które można opcjonalnie wykorzystać. Aby uzyskać więcej informacji, zobacz informacje o interfejsie API:- `"createdDateTime"` i `"lastUpdatedDateTime"` są dodawane, aby można było śledzić czas przetwarzania. Aby uzyskać więcej informacji, zobacz dokumentację. 
        - `"version"`informuje o wersji interfejsu API używanej do generowania wyników
        - Dodano słowo dla każdego wyrazu `"confidence"` . Ta wartość jest skalibrowane, aby wartość 0,95 oznaczała, że występuje 95% prawdopodobieństwa, że rozpoznawanie jest poprawne. Za pomocą oceny zaufania można wybrać tekst do wysłania do recenzji przez człowieka. 
        - `"angle"`Ogólna Orientacja tekstu w kierunku zgodnym z ruchem, mierzona w stopniach od (-180, 180).
        -  `"width"`i `"height"` dostarczają wymiary dokumentu i `"unit"` udostępnia jednostkę dla wymiarów (piksele lub cale, w zależności od typu dokumentu).
        - `"page"`Obsługiwane są dokumenty wielostronicowe
        - `"language"`język wejściowy dokumentu (z opcjonalnego parametru _Language_ ).


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
    
## <a name="all-other-operations"></a>Wszystkie inne operacje

Nie istnieją żadne inne istotne zmiany między wersjami 2. X i v 3.0 dla interfejs API przetwarzania obrazów. Możesz po prostu zmodyfikować ścieżkę interfejsu API, aby zastąpić ją `v2.0` `v3.0` .