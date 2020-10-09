---
title: Funkcje języka JavaScript zdefiniowane przez użytkownika w usłudze Azure Stream Analytics
description: Ten artykuł zawiera wprowadzenie do funkcji języka JavaScript zdefiniowanych przez użytkownika w Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc, devx-track-js
ms.date: 06/16/2020
ms.openlocfilehash: 7df244ee024b0d67ba678e296b882fbb08c3e16b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317722"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Funkcje języka JavaScript zdefiniowane przez użytkownika w Azure Stream Analytics
 
Usługa Azure Stream Analytics obsługuje funkcje zdefiniowane przez użytkownika, które napisano w języku JavaScript. Dzięki bogatemu zestawowi metod **String**, **RegExp**, **Math**, **Array**i **Date** , które udostępnia kod JavaScript, złożone przekształcenia danych Stream Analytics zadania stają się łatwiejsze do utworzenia.

## <a name="overview"></a>Omówienie

Funkcje języka JavaScript zdefiniowane przez użytkownika obsługują bezstanowe, tylko obliczeniowe funkcje skalarne, które nie wymagają łączności zewnętrznej. Wartość zwracana przez funkcję może być tylko wartością skalarną (pojedynczą). Po dodaniu funkcji języka JavaScript zdefiniowanej przez użytkownika do zadania możesz jej używać we wszystkich miejscach zapytania — tak jak wbudowanej funkcji skalarnej.

Poniżej przedstawiono kilka scenariuszy, w których funkcje języka JavaScript zdefiniowane przez użytkownika mogą być przydatne:
* Analizowanie ciągów zawierających funkcje do obsługi wyrażeń regularnych, na przykład **Regexp_Replace()** i **Regexp_Extract()**, oraz wykonywanie operacji na takich ciągach
* Dekodowanie i kodowanie danych, na przykład konwersja danych binarnych na szesnastkowe
* Prowadzenie obliczeń matematycznych za pomocą funkcji **matematycznych** języka JavaScript
* Wykonywanie operacji tablicowych, takich jak Sort, join, Find i Fill

Oto kilka rzeczy, których nie można wykonać za pomocą funkcji języka JavaScript zdefiniowanej przez użytkownika w Stream Analytics:
* Wywołaj zewnętrzne punkty końcowe REST, na przykład przeszukiwanie odwrotnego adresu IP lub ściąganie danych referencyjnych ze źródła zewnętrznego
* Wykonywanie niestandardowej serializacji lub deserializacji formatu zdarzeń dla danych wejściowych lub wyjściowych
* Tworzenie niestandardowych wartości zagregowanych

Chociaż funkcje takie jak **Date. GETDATE ()** lub **Math. Random ()** nie są blokowane w definicji funkcji, należy unikać używania ich. Te funkcje **nie** zwracają tego samego wyniku przy każdym wywołaniu ich, a usługa Azure Stream Analytics nie zachowuje dziennika wywołań funkcji i zwracanych wyników. Jeśli funkcja zwraca różne wyniki dla tych samych zdarzeń, powtarzalność nie jest gwarantowana, gdy zadanie zostanie ponownie uruchomione przez użytkownika lub przez usługę Stream Analytics.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>Dodawanie funkcji języka JavaScript zdefiniowanej przez użytkownika do zadania

> [!NOTE]
> Te kroki działają na Stream Analytics zadaniach skonfigurowanych do uruchamiania w chmurze. Jeśli zadanie Stream Analytics jest skonfigurowane do uruchamiania na Azure IoT Edge, zamiast tego użyj programu Visual Studio i [Napisz funkcję zdefiniowaną przez użytkownika za pomocą języka C#](stream-analytics-edge-csharp-udf.md).

Aby utworzyć funkcję języka JavaScript zdefiniowaną przez użytkownika w zadaniu Stream Analytics, wybierz pozycję **funkcje** w obszarze **topologia zadania**. Następnie wybierz pozycję **Java UDF** z menu rozwijanego **+ Dodaj** . 

![Dodaj JavaScript UDF](./media/javascript/stream-analytics-jsudf-add.png)

Następnie należy podać następujące właściwości i wybrać pozycję **Zapisz**.

|Właściwość|Opis|
|--------|-----------|
|Alias funkcji|Wprowadź nazwę, aby wywołać funkcję w zapytaniu.|
|Typ danych wyjściowych|Typ, który będzie zwracany przez funkcję języka JavaScript zdefiniowaną przez użytkownika do zapytania Stream Analytics.|
|Definicja funkcji|Implementacja funkcji języka JavaScript, która zostanie wykonana przy każdym wywołaniu elementu UDF z zapytania.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>Testowanie i rozwiązywanie problemów z UDF JavaScript 

Możesz testować i debugować logikę UDF języka JavaScript w dowolnej przeglądarce. Debugowanie i testowanie logiki tych funkcji zdefiniowanych przez użytkownika nie jest obecnie obsługiwane w portalu Stream Analytics. Gdy funkcja działa zgodnie z oczekiwaniami, można ją dodać do zadania Stream Analytics, jak wspomniano powyżej, a następnie wywołać ją bezpośrednio z zapytania. Logikę zapytań można testować za pomocą formatu UDF języka JavaScript przy użyciu [narzędzi Stream Analytics Tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install).

Błędy w czasie wykonywania kodu JavaScript są traktowane jako błędy krytyczne i uwidaczniane w dzienniku aktywności. Aby pobrać dziennik, w witrynie Azure Portal przejdź do zadania i wybierz pozycję **Dziennik aktywności**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Wywoływanie funkcji w języku JavaScript zdefiniowanej przez użytkownika w zapytaniu

Możesz łatwo wywołać funkcję JavaScript w zapytaniu przy użyciu aliasu funkcji poprzedzonej prefiksem **UDF**. Oto przykład formatu UDF języka JavaScript, który konwertuje wartości szesnastkowe na liczbę całkowitą wywoływaną w zapytaniu Stream Analytics.

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>Obsługiwane obiekty języka JavaScript

Funkcje języka JavaScript zdefiniowane przez użytkownika w usłudze Azure Stream Analytics obsługują standardowe wbudowane obiekty języka JavaScript. Aby uzyskać listę tych obiektów, zobacz artykuł [Global Objects (Obiekty globalne)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Konwersja typów usługi Stream Analytics i języka JavaScript

Między typami obsługiwanymi przez język zapytań usługi Stream Analytics i język JavaScript występują różnice. Poniższa tabela zawiera mapowania konwersji między typami:

Stream Analytics | JavaScript
--- | ---
bigint | Number (maksymalna liczba całkowita, która może być reprezentowana przez język JavaScript, to 2^53)
DateTime | Date (język JavaScript obsługuje tylko milisekundy)
double | Liczba
nvarchar(MAX) | Ciąg
Rekord | Obiekt
Tablica | Tablica
NULL | Zero

W tym miejscu przedstawiono konwersje typów języka JavaScript na typy usługi Stream Analytics:

JavaScript | Stream Analytics
--- | ---
Liczba | Bigint (jeśli liczba jest zaokrąglona i należy do zakresu long.MinValue-long.MaxValue; w przeciwnym razie to double)
Data | DateTime
Ciąg | nvarchar(MAX)
Obiekt | Rekord
Tablica | Tablica
Null, Undefined | NULL
Każdy inny typ (na przykład function lub error) | Nieobsługiwane (powoduje wystąpienie błędu w czasie wykonywania)

W języku JavaScript rozróżniana jest wielkość liter i wielkość liter w polach obiektów w kodzie JavaScript musi być zgodna z wielkością liter pól w danych przychodzących. Zadania o poziomie zgodności 1,0 spowodują przekonwertowanie pól z instrukcji SELECT języka SQL na małe litery. W obszarze poziom zgodności 1,1 i wyższych pola z instrukcji SELECT będą mieć taką samą wielkość liter jak określona w zapytaniu SQL.

## <a name="other-javascript-user-defined-function-patterns"></a>Inne wzorce funkcji języka JavaScript zdefiniowanej przez użytkownika

### <a name="write-nested-json-to-output"></a>Zapisywanie zagnieżdżonego kodu JSON do wyjścia

Jeśli stosujesz kolejny krok przetwarzania, który używa wyjścia zadania usługi Stream Analytics jako wejścia i wymaga formatu JSON, możesz zapisać ciąg JSON do wyjścia. W następnym przykładzie jest wywoływana funkcja **JSON.stringify()** w celu spakowania wszystkich par nazwa-wartość w danych wejściowych, a następnie zapisania ich w postaci pojedynczego ciągu do wyjścia.

**Definicja funkcji języka JavaScript zdefiniowanej przez użytkownika:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Przykładowe zapytanie:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

### <a name="cast-string-to-json-object-to-process"></a>Rzutowanie ciągu na obiekt JSON w celu przetworzenia

Jeśli masz pole ciągu, które jest w formacie JSON i chcesz przekonwertować je do obiektu JSON w celu przetworzenia w kodzie UDF języka JavaScript, możesz użyć funkcji **JSON. Parse ()** , aby utworzyć obiekt JSON, którego można następnie użyć.

**Definicja funkcji języka JavaScript zdefiniowanej przez użytkownika:**

```javascript
function main(x) {
var person = JSON.parse(x);  
return person.name;
}
```

**Przykładowe zapytanie:**
```SQL
SELECT
    UDF.getName(input) AS Name
INTO
    output
FROM
    input
```

### <a name="use-trycatch-for-error-handling"></a>Użyj try/catch do obsługi błędów

Bloki try/catch mogą pomóc w identyfikowaniu problemów z nieprawidłowymi danymi wejściowymi, które są przesyłane do formatu UDF języka JavaScript.

**Definicja funkcji języka JavaScript zdefiniowanej przez użytkownika:**

```javascript
function main(input, x) {
    var obj = null;

    try{
        obj = JSON.parse(x);
    }catch(error){
        throw input;
    }
    
    return obj.Value;
}
```

**Przykładowe zapytanie: Przekaż cały rekord jako pierwszy parametr, aby można było go zwrócić, jeśli wystąpił błąd.**
```SQL
SELECT
    A.context.company AS Company,
    udf.getValue(A, A.context.value) as Value
INTO
    output
FROM
    input A
```

## <a name="next-steps"></a>Następne kroki

* [Machine Learning UDF](https://docs.microsoft.com/azure/stream-analytics/machine-learning-udf)
* [Funkcja zdefiniowana przez użytkownika w języku C#](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf-methods)
