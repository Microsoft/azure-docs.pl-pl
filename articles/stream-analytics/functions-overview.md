---
title: Funkcje zdefiniowane przez użytkownika w Azure Stream Analytics
description: Ten artykuł zawiera omówienie funkcji zdefiniowanych przez użytkownika w programie Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: c671d3989fa46fa7546ba042b9132e19d80265a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020506"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Funkcje zdefiniowane przez użytkownika w Azure Stream Analytics

Język zapytań przypominający SQL w Azure Stream Analytics ułatwia implementowanie logiki analizy w czasie rzeczywistym na danych przesyłanych strumieniowo. Stream Analytics zapewnia dodatkową elastyczność za poorednictwem funkcji niestandardowych, które są wywoływane w zapytaniu. Poniższy przykład kodu jest atrybutem UDF o nazwie `sampleFunction` , który akceptuje jeden parametr, każdy rekord danych wejściowych odbiera zadanie, a wynik jest zapisywany w danych wyjściowych jako `sampleResult` .

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Typy funkcji

Azure Stream Analytics obsługuje następujące cztery typy funkcji: 

* Funkcje języka JavaScript zdefiniowane przez użytkownika 
* Agregacje zdefiniowane przez użytkownika w języku JavaScript 
* Funkcje zdefiniowane przez użytkownika w języku C# (przy użyciu programu Visual Studio) 
* Azure Machine Learning 

Tych funkcji można używać do scenariuszy, takich jak ocenianie w czasie rzeczywistym przy użyciu modeli uczenia maszynowego, manipulowania ciągami, złożone obliczenia matematyczne, kodowanie i dekodowanie danych. 

## <a name="limitations"></a>Ograniczenia

Funkcje zdefiniowane przez użytkownika są bezstanowe, a zwracana wartość może być tylko wartością skalarną. Nie można wywoływać zewnętrznych punktów końcowych REST z tych funkcji zdefiniowanych przez użytkownika, ponieważ prawdopodobnie wpłynie to na wydajność zadania. 

Azure Stream Analytics nie zachowuje rekordu wszystkich wywołań funkcji i zwracanych wyników. W celu zagwarantowania powtarzalności — na przykład ponowne uruchomienie zadania ze starszej sygnatury czasowej daje te same wyniki ponownie — nie należy używać takich funkcji jak `Date.GetData()` lub `Math.random()` , ponieważ te funkcje nie zwracają tego samego wyniku dla każdego wywołania.  

## <a name="resource-logs"></a>Dzienniki zasobów

Wszystkie błędy środowiska uruchomieniowego są uznawane za krytyczne i są rozłączone za poorednictwem dzienników aktywności i zasobów. Zaleca się, aby funkcja obsługiwała wszystkie wyjątki i błędy i zwracała prawidłowy wynik do zapytania. Uniemożliwi to przechodzenie zadania do [stanu zakończonego niepowodzeniem](job-states.md).  

## <a name="exception-handling"></a>Obsługa wyjątków

Każdy wyjątek podczas przetwarzania danych jest uznawany za krytyczny błąd podczas używania danych w Azure Stream Analytics. Funkcje zdefiniowane przez użytkownika mają wyższy potencjał do zgłaszania wyjątków i spowodowania zatrzymania przetwarzania. Aby uniknąć tego problemu, należy użyć bloku *try-catch* w języku JavaScript lub C# do przechwytywania wyjątków podczas wykonywania kodu. Przechwycone wyjątki mogą być rejestrowane i traktowane bez powodowania awarii systemu. Zaleca się, aby zawsze otoczyć niestandardowy kod w bloku *try-catch* , aby uniknąć zgłaszania nieoczekiwanych wyjątków do aparatu przetwarzania.

## <a name="next-steps"></a>Następne kroki

* [Funkcje języka JavaScript zdefiniowane przez użytkownika w Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics kodu JavaScript zdefiniowanych przez użytkownika](stream-analytics-javascript-user-defined-aggregates.md)
* [Opracowywanie .NET Standard funkcji zdefiniowanych przez użytkownika dla zadań Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)
* [Integracja Azure Stream Analytics z usługą Azure Machine Learning](machine-learning-udf.md)
