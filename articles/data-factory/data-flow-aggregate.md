---
title: Agregowanie transformacji w przepływie danych mapowania
description: Dowiedz się, jak agregować dane na dużą skalę w usłudze Azure Data Factory za pomocą transformacji zbiorczej przepływu danych mapowania.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 871f2b49e2dce9d762ef8a54923da04b0f24e4be
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606530"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Agregowanie transformacji w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Transformacja agregująca definiuje agregacje kolumn w strumieniach danych. Za pomocą Konstruktora wyrażeń można zdefiniować różne typy agregacji, takie jak SUMA, MIN, MAX i COUNT pogrupowane według istniejących lub obliczonych kolumn.

## <a name="group-by"></a>Grupuj według

Wybierz istniejącą kolumnę lub utwórz nową kolumnę obliczeniową, która będzie używana jako klauzula grupy według agregacji. Aby użyć istniejącej kolumny, zaznacz ją z listy rozwijanej. Aby utworzyć nową kolumnę obliczeń, umieść wskaźnik myszy na klauzuli i kliknij pozycję **Obliczona kolumna**. Spowoduje to otwarcie [konstruktora wyrażeń przepływu danych](concepts-data-flow-expression-builder.md). Po utworzeniu kolumny obliczanej wprowadź nazwę kolumny wyjściowej w polu **Nazwa jako.** Jeśli chcesz dodać dodatkową grupę według klauzuli, umieść wskaźnik myszy na istniejącej klauzuli i kliknij ikonę plus.

![Agreguj grupę transformacji według ustawień](media/data-flow/agg.png "Agreguj grupę transformacji według ustawień")

Klauzula grupy według jest opcjonalna w transformacji agregacji.

## <a name="aggregate-column"></a>Kolumna agregująca 

Przejdź do karty **Agreguj,** aby utworzyć wyrażenia agregacji. Można zastąpić istniejącą kolumnę agregacją lub utworzyć nowe pole o nowej nazwie. Wyrażenie agregacji jest wprowadzane w prawym polu obok selektora nazw kolumn. Aby edytować wyrażenie, kliknij pole tekstowe, aby otworzyć konstruktora wyrażeń. Aby dodać dodatkowe agregacje, umieść wskaźnik myszy na istniejącym wyrażeniu i kliknij ikonę plus, aby utworzyć nową kolumnę agregacji lub [wzorzec kolumny](concepts-data-flow-column-pattern.md).

Każde wyrażenie agregacji musi zawierać co najmniej jedną funkcję agregacji.

![Agreguj ustawienia agregacji transformacji](media/data-flow/agg2.png "Agreguj ustawienia agregacji transformacji")


> [!NOTE]
> W trybie debugowania konstruktor wyrażeń nie może tworzyć podglądów danych z funkcjami agregucyjnymi. Aby wyświetlić podglądy danych dla zagregowanych przekształceń, zamknij konstruktora wyrażeń i wyświetl dane za pomocą karty "Podgląd danych".

## <a name="reconnect-rows-and-columns"></a>Łączenie wierszy i kolumn

Transformacje agregowane są podobne do zapytań wyboru agregacji SQL. Kolumny, które nie są uwzględnione w grupie przez klauzuli lub funkcji agregacji nie będzie przepływać do danych wyjściowych transformacji agregacji. Jeśli chcesz dołączyć inne kolumny do zagregowanych danych wyjściowych, wykonaj jedną z następujących metod:

* Użyj funkcji agregującej, takiej jak `last()` lub, `first()` aby dołączyć tę dodatkową kolumnę.
* Dołącz ponownie do kolumn do strumienia wyjściowego przy użyciu [wzorca samosiewu](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="removing-duplicate-rows"></a>Usuwanie zduplikowanych wierszy

Typowym zastosowaniem transformacji agregacji jest usuwanie lub identyfikowanie zduplikowanych wpisów w danych źródłowych. Proces ten jest znany jako deduplikacji. Na podstawie zestawu grup według kluczy użyj heurystyki wybranej do określenia, który zduplikowany wiersz zachować. Wspólne heurystyki `first()` `last()`są `max()`, `min()`, i . Użyj [wzorców kolumn,](concepts-data-flow-column-pattern.md) aby zastosować regułę do każdej kolumny z wyjątkiem grupy według kolumn.

![Deduplikacja](media/data-flow/agg-dedupe.png "Deduplikacja")

W powyższym przykładzie `ProductID` `Name` kolumny i są używane do grupowania. Jeśli dwa wiersze mają te same wartości dla tych dwóch kolumn, są one uważane za duplikaty. W tej transformacji agregacji wartości pierwszego wiersza dopasowane zostaną zachowane i wszystkie inne zostaną usunięte. Przy użyciu składni wzorca kolumny wszystkie kolumny, których nazwy nie `ProductID` są i `Name` są mapowane na istniejącą nazwę kolumny i otrzymują wartość pierwszych dopasowanych wierszy. Schemat danych wyjściowych jest taki sam jak schemat wejściowy.

W scenariuszach sprawdzania `count()` poprawności danych funkcja może służyć do zliczania liczby duplikatów.

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>Przykład

Poniższy przykład przyjmuje `MoviesYear` strumień przychodzący i `year`grupuje wiersze według kolumny . Transformacja tworzy kolumnę `avgrating` agregującą, `Rating`która ocenia średnią kolumny . Ta transformacja `AvgComedyRatingsByYear`agregująca nosi nazwę .

W ux fabryki danych ta transformacja wygląda jak poniższy obraz:

![Grupowanie według przykładów](media/data-flow/agg-script1.png "Grupowanie według przykładów")

![Przykład agregacji](media/data-flow/agg-script2.png "Przykład agregacji")

Skrypt przepływu danych dla tej transformacji znajduje się w urywek poniżej.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![Zagreguj skrypt przepływu danych](media/data-flow/aggdfs1.png "Zagreguj skrypt przepływu danych")

```MoviesYear```: Kolumna pochodna definiująca rok i tytuł ```AvgComedyRatingByYear```kolumn: Zagregowane ```avgrating```przekształcenie dla średniej klasyfikacji komedii pogrupowanych według roku : Nazwa nowej kolumny tworzonej w celu przechowywania wartości zagregowanej

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Następne kroki

* Definiowanie agregacji opartej na oknach przy użyciu [transformacji okna](data-flow-window.md)
