---
title: Agregacje zdefiniowane przez użytkownika w języku JavaScript w Azure Stream Analytics
description: W tym artykule opisano sposób wykonywania zaawansowanych zapytań Mechanics z agregacjami zdefiniowanymi przez użytkownika w języku JavaScript w Azure Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.custom: devx-track-js
ms.openlocfilehash: 8891bb5cd3b8bd8ae9e02c871d6d0ffe42078c0b
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124768"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates"></a>Azure Stream Analytics kodu JavaScript zdefiniowanych przez użytkownika
 
Azure Stream Analytics obsługuje agregacje zdefiniowane przez użytkownika (UDA) zapisaną w języku JavaScript, dzięki czemu można zaimplementować kompleksową logikę biznesową. W ramach UDA masz pełną kontrolę nad strukturą danych stanu, akumulacją stanu, kumulacją stanu i agregacją obliczeń wyniku. W tym artykule przedstawiono dwa różne interfejsy UDA języka JavaScript, kroki umożliwiające utworzenie UDA i sposób używania UDA z operacjami opartymi na oknach w Stream Analytics Query.

## <a name="javascript-user-defined-aggregates"></a>Agregacje zdefiniowane przez użytkownika w języku JavaScript

Zdefiniowana przez użytkownika wartość zagregowana jest używana na podstawie specyfikacji przedziału czasu do agregowania zdarzeń w tym oknie i tworzenia pojedynczej wartości wyniku. Istnieją dwa typy interfejsów UDA, które Stream Analytics obsługuje dzisiaj, AccumulateOnly i AccumulateDeaccumulate. Oba typy UDA mogą być używane przez wirowania, przeskoku, przesuwania i okna sesji. AccumulateDeaccumulate UDA wykonuje lepsze niż AccumulateOnly UDA używany razem z przeskoku, przesuwaniem i oknem sesji. Wybierz jeden z dwóch typów oparty na używanym algorytmie.

### <a name="accumulateonly-aggregates"></a>Agregaty AccumulateOnly

Agregacje AccumulateOnly mogą jedynie zbierać nowe zdarzenia do swojego stanu, ale algorytm nie zezwala na sumowanie wartości. Wybierz ten typ agregacji, gdy nie można zaimplementować informacji o zdarzeniu z wartości stanu. Poniżej znajduje się szablon JavaScript dla agregacji AccumulatOnly:

```JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

### <a name="accumulatedeaccumulate-aggregates"></a>Agregaty AccumulateDeaccumulate

Agregacje AccumulateDeaccumulate umożliwiają rozkumulowanie poprzedniej wartości skumulowanej ze stanu, na przykład Usuń parę klucz-wartość z listy wartości zdarzeń lub Odejmij wartość od stanu agregacji SUM. Poniżej znajduje się szablon JavaScript dla agregacji AccumulateDeaccumulate:

```JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

## <a name="uda---javascript-function-declaration"></a>UDA — deklaracja funkcji JavaScript

Każdy UDA JavaScript jest zdefiniowany przez deklarację obiektu funkcji. Poniżej przedstawiono główne elementy w definicji UDA.

### <a name="function-alias"></a>Alias funkcji

Alias funkcji jest identyfikatorem UDA. Gdy jest wywoływana w Stream Analytics Query, zawsze używaj aliasu UDA razem z "uda". .

### <a name="function-type"></a>Typ funkcji

W przypadku UDA typ funkcji powinien mieć wartość **JavaScript uda** .

### <a name="output-type"></a>Typ danych wyjściowych

Określony typ, który Stream Analytics obsługiwane zadanie, lub "any", jeśli chcesz obsłużyć typ w zapytaniu.

### <a name="function-name"></a>Nazwa funkcji

Nazwa tego obiektu funkcji. Nazwa funkcji powinna być zgodna z aliasem UDA.

### <a name="method---init"></a>Metoda-init ()

Metoda init () inicjuje stan agregacji. Ta metoda jest wywoływana, gdy okno zostanie uruchomione.

### <a name="method--accumulate"></a>Metoda — gromadzenie ()

Metoda kumulacja () oblicza stan UDA na podstawie poprzedniego stanu i bieżących wartości zdarzenia. Ta metoda jest wywoływana po przejściu zdarzenia w przedziale czasu (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW lub SESSIONWINDOW).

### <a name="method--deaccumulate"></a>Metoda — desumowanie ()

Metoda deakumulacja () ponownie oblicza stan na podstawie poprzedniego stanu i bieżących wartości zdarzenia. Ta metoda jest wywoływana, gdy zdarzenie opuszcza element SLIDINGWINDOW lub SESSIONWINDOW.

### <a name="method--deaccumulatestate"></a>Metoda — deaccumulateState ()

Metoda deaccumulateState () ponownie oblicza stan na podstawie poprzedniego stanu i stanu przeskoku. Ta metoda jest wywoływana, gdy zestaw zdarzeń opuszcza HOPPINGWINDOW.

### <a name="method--computeresult"></a>Metoda — computeResult ()

Metoda computeResult () zwraca zagregowany wynik na podstawie bieżącego stanu. Ta metoda jest wywoływana na końcu przedziału czasu (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW lub SESSIONWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>Obsługiwane typy danych wejściowych i wyjściowych języka JavaScript UDA
W przypadku typów danych języka JavaScript UDA zapoznaj się z sekcją **Stream Analytics i konwersją typu JavaScript** integracji z usługą [JavaScript UDF](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Dodawanie UDA języka JavaScript z Azure Portal

Poniżej przeprowadzimy proces tworzenia UDA z portalu. Przykładem używanym w tym miejscu jest Obliczanie średniej ważonej czasowo.

Teraz Utwórzmy kod JavaScript UDA w ramach istniejącego zadania ASA, wykonując następujące kroki.

1. Zaloguj się do Azure Portal i Znajdź istniejące Stream Analytics zadanie.
1. Następnie kliknij link funkcje w obszarze **topologia zadania** .
1. Kliknij ikonę **Dodaj** , aby dodać nową funkcję.
1. W obszarze nowy widok funkcji wybierz pozycję **JavaScript uda** jako typ funkcji, a następnie w edytorze zostanie wyświetlony domyślny szablon uda.
1. Wypełnij wartość "TWA" jako alias UDA i Zmień implementację funkcji w następujący sposób:

    ```JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ```

1. Po kliknięciu przycisku "Zapisz" UDA zostanie wyświetlona na liście funkcji.

1. Kliknij nową funkcję "TWA", aby sprawdzić definicję funkcji.

## <a name="calling-javascript-uda-in-asa-query"></a>Wywołanie JavaScript UDA w zapytaniu ASA

W Azure Portal i Otwórz zadanie, Edytuj zapytanie i wywołaj funkcję TWA () z prefiksem mandatu "uda". Przykład:

```SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
```

## <a name="testing-query-with-uda"></a>Testowanie zapytania za pomocą UDA

Utwórz lokalny plik JSON z poniższą zawartością, Przekaż plik do Stream Analytics zadania i przetestuj powyższe zapytanie.

```JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
```

## <a name="get-help"></a>Uzyskaj pomoc

Aby uzyskać dodatkową pomoc, Wypróbuj naszą [stronę pytań firmy&Microsoft dotyczącą Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Dokumentacja języka zapytań](/stream-analytics-query/stream-analytics-query-language-reference)
* [Dokumentacja interfejsu API REST zarządzania Azure Stream Analytics](/rest/api/streamanalytics/)