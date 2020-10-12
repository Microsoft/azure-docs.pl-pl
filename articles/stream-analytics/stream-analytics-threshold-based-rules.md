---
title: Konfigurowalne reguły oparte na progach w Azure Stream Analytics
description: W tym artykule opisano sposób korzystania z danych referencyjnych w celu uzyskania rozwiązania do obsługi alertów, które ma konfigurowalne reguły oparte na progach w Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/30/2018
ms.openlocfilehash: 215835bf7f1e6676adba6541da70dcb86fc3500c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86039045"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Przetwarzaj konfigurowalne reguły oparte na progach w Azure Stream Analytics
W tym artykule opisano sposób korzystania z danych referencyjnych w celu uzyskania rozwiązania do obsługi alertów, które używa konfigurowalnych reguł opartych na progach w Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Scenariusz: generowanie alertów na podstawie regulowanych progów reguł
Może być konieczne wygenerowanie alertu jako danych wyjściowych, gdy przychodzące zdarzenia strumieniowe osiągną pewną wartość lub gdy zagregowana wartość oparta na przychodzących zdarzeniach przesyłanych strumieniowo przekracza określony próg. Można to łatwo skonfigurować Stream Analytics kwerendę, która porówna wartość ze statycznym progiem, który jest stały i wstępnie określony. Stała wartość progowa może być sztywno zakodowana w składni zapytania przesyłania strumieniowego przy użyciu prostych porównań liczbowych (większe niż, mniejsze niż i równość).

W niektórych przypadkach wartości progowe muszą być łatwiejsze do skonfigurowania bez konieczności edytowania składni zapytania przy każdej zmianie wartości progowej. W innych przypadkach może być konieczne posiadanie wielu urządzeń lub użytkowników przetwarzanych przez to samo zapytanie, które mają różne wartości progowe na poszczególnych typach urządzeń. 

Ten wzorzec może służyć do dynamicznego konfigurowania progów, selektywnego wybierania rodzaju urządzenia stosowanego przez filtrowanie danych wejściowych i selektywnego wybierania pól do uwzględnienia w danych wyjściowych.

## <a name="recommended-design-pattern"></a>Zalecany Wzorzec projektowy
Użyj danych referencyjnych do zadania Stream Analytics jako wyszukiwania progów alertu:
- Przechowuj wartości progowe w danych referencyjnych, jedną wartość na klucz.
- Dołącz zdarzenia wejściowe danych przesyłania strumieniowego do danych referencyjnych w kolumnie klucz.
- Użyj wartości ustalonej z danych referencyjnych jako wartości progowej.

## <a name="example-data-and-query"></a>Przykładowe dane i zapytanie
W tym przykładzie alerty są generowane, gdy agregowanie danych przesyłanych strumieniowo z urządzeń w długim okresie minuty jest zgodne z określonymi wartościami w regule dostarczonej jako dane referencyjne.

W zapytaniu dla każdego elementu deviceId i każdej metryki w obszarze deviceId można skonfigurować od 0 do 5 wymiarów w grupie według. Grupowane są tylko zdarzenia z odpowiednimi wartościami filtru. Po zgrupowaniu okna zagregowane wartości min, Max i AVG są obliczane w 60-sekundowym oknie wirowania. Filtry wartości zagregowanych są następnie obliczane zgodnie ze skonfigurowanym progiem w odwołaniu w celu wygenerowania zdarzenia wyjściowego alertu.

Załóżmy na przykład, że istnieje zadanie Stream Analytics, które ma referencyjne dane wejściowe danych o nazwie **Rules**, i dane wejściowe przesyłania strumieniowego o nazwie **Metrics**. 

## <a name="reference-data"></a>Dane referencyjne
Te przykładowe dane referencyjne pokazują, jak można przedstawić regułę opartą na progach. Plik JSON zawiera dane referencyjne i jest zapisywany w usłudze Azure Blob Storage, a kontener magazynu obiektów BLOB jest używany jako dane wejściowe danych referencyjnych o nazwie **Rules**. Można zastąpić ten plik JSON i zamienić konfigurację reguły na czas, bez zatrzymywania lub uruchamiania zadania przesyłania strumieniowego.

- Przykładowa reguła jest używana do reprezentowania regulowanego alertu, gdy procesor CPU przekracza (średnia jest większa lub równa) wartości `90` procentowej. `value`Pole można skonfigurować zgodnie z wymaganiami.
- Zwróć uwagę, że reguła ma pole **operatora** , które jest dynamicznie interpretowane w składni zapytania w dalszej części `AVGGREATEROREQUAL` . 
- Reguła filtruje dane w określonym kluczu wymiaru `2` o wartości `C1` . Inne pola są pustym ciągiem, co oznacza, że nie można filtrować strumienia wejściowego według tych pól zdarzeń. Można skonfigurować dodatkowe reguły procesora, aby odfiltrować inne dopasowane pola zgodnie z wymaganiami.
- Nie wszystkie kolumny są uwzględniane w wyjściowym zdarzeniu alertu. W takim przypadku `includedDim` numer klucza `2` jest włączony, `TRUE` aby reprezentować, że pole numer 2 danych zdarzenia w strumieniu zostanie uwzględnione w kwalifikujących się zdarzeniach wyjściowych. Pozostałe pola nie są uwzględniane w danych wyjściowych alertu, ale można dostosować listę pól.


```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>Przykładowe zapytanie przesyłania strumieniowego
Ten przykład Stream Analytics zapytanie sprzęga dane referencyjne **reguł** z powyższego przykładu do strumienia wejściowego danych o nazwie **Metrics**.

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>Przykładowe dane wejściowe zdarzenia przesyłania strumieniowego
Te przykładowe dane JSON reprezentują dane wejściowe **metryk** , które są używane w powyższym zapytaniu przesyłania strumieniowego. 

- Trzy przykładowe zdarzenia są wymienione w 1-minutowym przedziale czasu, wartość `T14:50` . 
- Wszystkie trzy mają tę samą `deviceId` wartość `978648` .
- Wartości metryk procesora CPU różnią się w zależności od zdarzenia, `98` `95` `80` odpowiednio. Tylko pierwsze dwa Przykładowe zdarzenia przekraczają regułę alertu procesora CPU ustanowioną w regule.
- Pole includeDim w regule alertu było kluczem nr 2. Odpowiednie pole klucz 2 w zdarzeniach przykładowych ma nazwę `NodeName` . Trzy przykładowe zdarzenia mają odpowiednio wartości `N024` , `N024` , i `N014` . W danych wyjściowych zobaczysz tylko węzeł, `N024` który jest jedynymi danymi, które pasują do kryteriów alertów dla wysokiego procesora. `N014` nie spełnia wysokiego progu procesora CPU.
- Reguła alertu ma skonfigurowany `filter` tylko dla klucza o numerze 2, który odnosi się do `cluster` pola w przykładowych zdarzeniach. Trzy przykładowe zdarzenia wszystkie mają wartość `C1` i pasują do kryteriów filtrowania.

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>Przykładowe dane wyjściowe
Te przykładowe dane wyjściowe JSON pokazują pojedyncze zdarzenie alertu na podstawie reguły progu procesora zdefiniowanej w danych referencyjnych. Zdarzenie wyjściowe zawiera nazwę alertu, a także zagregowaną (średnią, minimalną, maksymalną) pól branych pod uwagę. Dane wyjściowe zdarzenia zawierają wartość klucza pola numer 2 `NodeName` `N024` ze względu na konfigurację reguły. (Kod JSON został zmieniony, aby pokazać podziały wierszy w celu zapewnienia czytelności).

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```
