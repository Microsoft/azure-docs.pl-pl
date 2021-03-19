---
title: Przekształcenie tabeli przestawnej w mapowaniu przepływu danych
description: Przestawianie danych z wierszy do kolumn przy użyciu funkcji mapowania Azure Data Factory przekształcenie przestawnego przepływu danych
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: e098182c000cbe05df533434a41c55b797ef876f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87086663"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Przekształcenie tabeli przestawnej w mapowaniu przepływu danych


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj transformacji przestawnej, aby utworzyć wiele kolumn z unikatowych wartości wierszy pojedynczej kolumny. Pivot to transformacja agregacji, w której wybiera się pozycję Grupuj według kolumn i generuje kolumny tabeli przestawnej przy użyciu [funkcji agregujących](data-flow-expression-functions.md#aggregate-functions).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4C4YN]

## <a name="configuration"></a>Konfigurowanie

Przekształcenie przestawne wymaga trzech różnych danych wejściowych: Grupuj według kolumn, klucz przestawny i sposób generowania kolumn przestawnych

### <a name="group-by"></a>Grupuj według

![Grupuj według opcji](media/data-flow/pivot2.png "Grupuj według opcji")

Wybierz kolumny, dla których mają zostać zagregowane przestawiane kolumny. Dane wyjściowe będą grupować wszystkie wiersze z tą samą grupą według wartości w jednym wierszu. Agregacja wykonana w kolumnie przestawnej będzie miała miejsce dla każdej grupy.

Ta sekcja jest opcjonalna. Jeśli nie wybrano żadnej kolumny Grupuj według, cały strumień danych zostanie zagregowany i tylko jeden wiersz zostanie wyszukany.

### <a name="pivot-key"></a>Klucz przestawny

![Klucz przestawny](media/data-flow/pivot3.png "Klucz przestawny")

Klucz przestawny to kolumna, której wartości wierszy są przestawiane na nowe kolumny. Domyślnie transformacja przestawna spowoduje utworzenie nowej kolumny dla każdej unikatowej wartości wiersza.

W sekcji z etykietą **wartość** możesz wprowadzić określone wartości wierszy do przestawienia. Tylko wartości wierszy wprowadzonych w tej sekcji zostaną przestawiane. Włączenie **wartości null** spowoduje utworzenie kolumny przestawnej dla wartości null w kolumnie.

### <a name="pivoted-columns"></a>Kolumny przestawne

![Kolumny przestawne](media/data-flow/pivot4.png "Kolumny przestawne")

Dla każdej unikatowej wartości klucza przestawnego, która jest kolumną, wygeneruj zagregowaną wartość wiersza dla każdej grupy. Można utworzyć wiele kolumn dla każdego klucza tabeli przestawnej. Każda kolumna przestawna musi zawierać co najmniej jedną [funkcję agregującą](data-flow-expression-functions.md#aggregate-functions).

**Wzorzec nazwy kolumny:** Wybierz sposób formatowania nazwy kolumny dla każdej kolumny tabeli przestawnej. Nazwa kolumny, która zostanie wywstawiana, będzie kombinacją wartości klucza przestawnego, prefiksu kolumny i opcjonalnego prefiksu, wystarczającej liczby znaków środkowych. 

**Układ kolumny:** W przypadku generowania więcej niż jednej kolumny tabeli przestawnej dla każdego klucza tabeli przestawnej wybierz, jak mają być uporządkowane kolumny. 

**Prefiks kolumny:** W przypadku generowania więcej niż jednej kolumny tabeli przestawnej dla każdego klucza tabeli przestawnej wprowadź prefiks kolumny dla każdej kolumny. To ustawienie jest opcjonalne, jeśli istnieje tylko jedna kolumna przestawna.

## <a name="help-graphic"></a>Grafika pomocy

Poniższa ilustracja pomocy pokazuje, jak różne składniki Pivot współdziałają ze sobą

![Grafika dotycząca pomocy](media/data-flow/pivot5.png "Grafika dotycząca pomocy Pivot")

## <a name="pivot-metadata"></a>Metadane tabeli przestawnej

Jeśli w konfiguracji klucza przestawnego nie określono żadnych wartości, kolumny przestawne będą dynamicznie generowane w czasie wykonywania. Liczba przestawianych kolumn będzie równa liczbie unikatowych wartości klucza przestawnego pomnożonej przez liczbę kolumn tabeli przestawnej. Ponieważ może to być zmiana numeru, środowisko użytkownika nie będzie wyświetlało metadanych kolumn na karcie **Inspekcja** i nie będzie można propagować kolumn. Aby przekształcać te kolumny, użyj funkcji [wzorca kolumny](concepts-data-flow-column-pattern.md) w celu mapowania przepływu danych. 

W przypadku ustawienia określonych wartości klucza przestawnego w metadanych zostanie wyświetlona kolumna przestawna. Nazwy kolumn będą dostępne dla Ciebie w mapowaniu inspekcji i ujścia.

### <a name="generate-metadata-from-drifted-columns"></a>Generuj metadane na podstawie kolumn z przedryfem

Pivot automatycznie generuje nowe nazwy kolumn na podstawie wartości wierszy. Te nowe kolumny można dodać do metadanych, do których można przystąpić później w przepływie danych. Aby to zrobić, użyj szybkiej akcji [Mapuj](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) w podglądzie danych. 

![Przestawianie kolumn](media/data-flow/newpivot1.png "Mapuj kolumny tabeli przestawnej z przełożeń")

### <a name="sinking-pivoted-columns"></a>Wystawianie kolumn na wystawienie

Mimo że przestawiane kolumny są dynamiczne, nadal mogą być zapisywane w docelowym magazynie danych. Włącz opcję **Zezwalaj na dryf schematu** w ustawieniach ujścia. Pozwoli to na pisanie kolumn, które nie są uwzględnione w metadanych. Nowe nazwy dynamiczne nie będą widoczne w metadanych kolumny, ale opcja dryfowania schematu umożliwi wygruntowanie danych.

### <a name="rejoin-original-fields"></a>Odłączanie oryginalnych pól

Przekształcenie przestawne będzie dotyczyć tylko kolumn Grupuj według i przestawnych. Jeśli chcesz, aby dane wyjściowe zawierały inne kolumny wejściowe, użyj wzorca [samosprzężenia](data-flow-join.md#self-join) .

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

Wypróbuj [transformację UNPIVOT](data-flow-unpivot.md) , aby przekształcić wartości kolumn w wartości wierszy. 
