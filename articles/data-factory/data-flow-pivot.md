---
title: Transformacja przestawna w przepływie danych mapowania
description: Dane przestawne z wierszy do kolumn przy użyciu transformacji przestawnej przepływu danych usługi Azure Data Factory mapowania
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a58444f81f60b48f9c2c76f13257a6a2431158a8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686409"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Transformacja przestawna w przepływie danych mapowania


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Transformacja przestawna służy do tworzenia wielu kolumn z unikatowych wartości wierszy pojedynczej kolumny. Pivot to transformacja agregacji, w której wybiera się grupowanie według kolumn i generujesz kolumny przestawne przy użyciu [funkcji agregujących](data-flow-expression-functions.md#aggregate-functions).

## <a name="configuration"></a>Konfigurowanie

Transformacja przestawna wymaga trzech różnych wejść: grupowanie według kolumn, klawisz przestawny i generowanie kolumn przestawnych

### <a name="group-by"></a>Grupuj według

![Grupuj według opcji](media/data-flow/pivot2.png "[Grupuj według opcji")

Wybierz kolumny, które mają agregować kolumny przestawne. Dane wyjściowe pogrupują wszystkie wiersze z tą samą grupą według wartości w jednym wierszu. Agregacja wykonana w kolumnie przestawnej będzie występować w każdej grupie.

Ta sekcja jest opcjonalna. Jeśli nie wybrano żadnej grupy według kolumn, cały strumień danych zostanie zagregowany i zostanie wysunięty tylko jeden wiersz.

### <a name="pivot-key"></a>Klawisz przestawny

![Klawisz przestawny](media/data-flow/pivot3.png "Klawisz przestawny")

Klawisz przestawny to kolumna, której wartości wierszy są przestawiane na nowe kolumny. Domyślnie transformacja przestawna utworzy nową kolumnę dla każdej unikatowej wartości wiersza.

W sekcji z etykietą **Wartość**można wprowadzić określone wartości wierszy, które mają być przestawne. Tylko wartości wierszy wprowadzone w tej sekcji zostaną przestawne. Włączenie **wartości Null** spowoduje utworzenie kolumny przestawnej dla wartości null w kolumnie.

### <a name="pivoted-columns"></a>Kolumny przestawne

![Kolumny przestawne](media/data-flow/pivot4.png "Kolumny przestawne")

Dla każdej unikatowej wartości klucza przestawnego, która staje się kolumną, wygeneruj zagregowane wartości wiersza dla każdej grupy. Można utworzyć wiele kolumn na klawisz przestawny. Każda kolumna przestawna musi zawierać co najmniej jedną [funkcję agregującą](data-flow-expression-functions.md#aggregate-functions).

**Wzór nazwy kolumny:** Wybierz sposób formatowania nazwy kolumny każdej kolumny przestawnej. Wysuwna nazwa kolumny będzie kombinacją wartości klawisza obrotu, prefiksu kolumny i opcjonalnego prefiksu, wystarczy, znaki środkowe. 

**Układ kolumny:** Jeśli generujesz więcej niż jedną kolumnę przestawną na klawisz przestawny, wybierz sposób zamawiania kolumn. 

**Prefiks kolumny:** Jeśli generujesz więcej niż jedną kolumnę przestawną na klawisz przestawny, wprowadź prefiks kolumny dla każdej kolumny. To ustawienie jest opcjonalne, jeśli masz tylko jedną kolumnę przestawną.

## <a name="help-graphic"></a>Grafika pomocy

Poniższa grafika pomocy pokazuje, jak różne składniki przestawne współdziałają ze sobą

![Grafika pomocy przestawiania](media/data-flow/pivot5.png "Grafika pomocy przestawiania")

## <a name="pivot-metadata"></a>Metadane przestawne

Jeśli w konfiguracji klucza przestawnego nie określono żadnych wartości, kolumny przestawne będą generowane dynamicznie w czasie wykonywania. Liczba kolumn przestawnych będzie równa liczbie unikatowych wartości klucza obrotu pomnożonych przez liczbę kolumn przestawnych. Ponieważ może to być zmieniająca się liczba, środowisko użytkownika nie wyświetli metadanych kolumny na karcie **Inspekcja** i nie będzie propagacji kolumn. Aby znoważyć te kolumny, należy użyć możliwości [wzorca kolumny](concepts-data-flow-column-pattern.md) mapowania przepływu danych. 

Jeśli ustawiono określone wartości klawiszy przestawnych, kolumny przestawne pojawią się w metadanych.e nazwy kolumn będą dostępne w mapowaniu Inspekcja i Ujście.

### <a name="generate-metadata-from-drifted-columns"></a>Generowanie metadanych z dryfowanych kolumn

Pivot generuje nowe nazwy kolumn dynamicznie na podstawie wartości wierszy. Możesz dodać te nowe kolumny do metadanych, do których można się odwoływać później w przepływie danych. Aby to zrobić, użyj [mapy dryfował](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) szybkie działanie w podglądzie danych. 

![Przestawianie kolumn](media/data-flow/newpivot1.png "Mapa dryfowała kolumnach przestawnych")

### <a name="sinking-pivoted-columns"></a>Zatopienie kolumn przestawnych

Chociaż kolumny przestawne są dynamiczne, nadal można je zapisywać w docelowym magazynie danych. Włącz **zezwalaj na dryft schematu** w ustawieniach ujścia. Umożliwi to pisanie kolumn, które nie są zawarte w metadanych. metadanych kolumny, ale opcja dryfu schematu pozwoli na wyładowanie danych.

### <a name="rejoin-original-fields"></a>Dołączanie do oryginalnych pól

Transformacja przestawna będzie rzutować tylko kolumny grupy według i przestawne. Jeśli chcesz, aby dane wyjściowe zawierały inne kolumny wejściowe, użyj wzorca [samoskładania.](data-flow-join.md#self-join)

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>Przykład

Ekrany wyświetlane w sekcji konfiguracji mają następujący skrypt przepływu danych:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>Następne kroki

Spróbuj [przekształcenia unpivot,](data-flow-unpivot.md) aby przekształcić wartości kolumn w wartości wierszy. 
