---
title: Funkcje zdefiniowane przez użytkownika w Azure Stream Analytics
description: Ten artykuł zawiera omówienie funkcji zdefiniowanych przez użytkownika w programie Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 45e766c624ee96f7faa06fb07d00349e620a4c0a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133482"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Funkcje zdefiniowane przez użytkownika w Azure Stream Analytics

Język zapytań przypominający SQL w Azure Stream Analytics ułatwia implementowanie logiki analizy w czasie rzeczywistym na danych przesyłanych strumieniowo. Stream Analytics zapewnia dodatkową elastyczność za poorednictwem funkcji niestandardowych, które są wywoływane w zapytaniu. Poniższy przykład kodu jest atrybutem UDF o `sampleFunction` nazwie, który akceptuje jeden parametr, każdy rekord danych wejściowych odbiera zadanie, a wynik jest zapisywany w danych `sampleResult`wyjściowych jako.

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

Azure Stream Analytics nie zachowuje rekordu wszystkich wywołań funkcji i zwracanych wyników. W celu zagwarantowania powtarzalności — na przykład ponowne uruchomienie zadania ze starszej sygnatury czasowej daje te same wyniki ponownie — nie należy używać takich `Date.GetData()` funkcji `Math.random()`jak lub, ponieważ te funkcje nie zwracają tego samego wyniku dla każdego wywołania.  

## <a name="resource-logs"></a>Dzienniki zasobów

Wszystkie błędy środowiska uruchomieniowego są uznawane za krytyczne i są rozłączone za poorednictwem dzienników aktywności i zasobów. Zaleca się, aby funkcja obsługiwała wszystkie wyjątki i błędy i zwracała prawidłowy wynik do zapytania. Uniemożliwi to przechodzenie zadania do [stanu zakończonego niepowodzeniem](job-states.md).  


## <a name="next-steps"></a>Następne kroki

* [Funkcje języka JavaScript zdefiniowane przez użytkownika w Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics kodu JavaScript zdefiniowanych przez użytkownika](stream-analytics-javascript-user-defined-aggregates.md)
* [Opracowywanie .NET Standard funkcji zdefiniowanych przez użytkownika dla zadań Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)
* [Integracja Azure Stream Analytics z usługą Azure Machine Learning](machine-learning-udf.md)

