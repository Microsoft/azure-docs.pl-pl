---
title: Agregowanie transformacji w strumieniu danych mapowania
description: Dowiedz się, jak agregować dane w skali w Azure Data Factory przy użyciu transformacji zagregowanej przepływu danych mapowania.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 871f2b49e2dce9d762ef8a54923da04b0f24e4be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606530"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Agregowanie transformacji w strumieniu danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Zagregowana transformacja definiuje agregacje kolumn w strumieniach danych. Korzystając z konstruktora wyrażeń, można definiować różne typy agregacji, takie jak SUM, MIN, MAX i COUNT pogrupowane według istniejących lub obliczanych kolumn.

## <a name="group-by"></a>Grupuj według

Wybierz istniejącą kolumnę lub Utwórz nową kolumnę obliczaną, która ma być używana jako klauzula GROUP by dla agregacji. Aby użyć istniejącej kolumny, wybierz ją z listy rozwijanej. Aby utworzyć nową kolumnę obliczaną, umieść kursor nad klauzulą i kliknij **kolumnę obliczaną**. Spowoduje to otwarcie [konstruktora wyrażeń przepływu danych](concepts-data-flow-expression-builder.md). Po utworzeniu kolumny obliczanej wprowadź nazwę kolumny wyjściowej w polu **Nazwa jako** . Jeśli chcesz dodać dodatkową klauzulę Group by, umieść kursor nad istniejącą klauzulą i kliknij ikonę znaku plus.

![Agregacja grupy transformacji według ustawień](media/data-flow/agg.png "Agregacja grupy transformacji według ustawień")

Klauzula GROUP by jest opcjonalna w transformacji agregowanej.

## <a name="aggregate-column"></a>Kolumna agregująca 

Przejdź do karty **agregaty** , aby skompilować wyrażenia agregacji. Można zastąpić istniejącą kolumnę agregacją lub utworzyć nowe pole z nową nazwą. Wyrażenie agregacji jest wprowadzane w polu po prawej stronie obok selektora nazw kolumn. Aby edytować wyrażenie, kliknij pole tekstowe, aby otworzyć program Expression Builder. Aby dodać dodatkowe agregacje, umieść kursor na istniejącym wyrażeniu i kliknij ikonę Plus, aby utworzyć nową kolumnę agregacji lub [wzorzec kolumny](concepts-data-flow-column-pattern.md).

Każde wyrażenie agregacji musi zawierać co najmniej jedną funkcję agregującą.

![Zagregowane ustawienia agregacji transformacji](media/data-flow/agg2.png "Zagregowane ustawienia agregacji transformacji")


> [!NOTE]
> W trybie debugowania Konstruktor wyrażeń nie może tworzyć podglądów danych za pomocą funkcji agregujących. Aby wyświetlić podglądy danych dla przekształceń agregowanych, należy zamknąć konstruktora wyrażeń i wyświetlić dane za pomocą karty "Podgląd danych".

## <a name="reconnect-rows-and-columns"></a>Połącz ponownie wiersze i kolumny

Przekształcenia agregowane przypominają zapytania select SQL Aggregate. Kolumny, które nie znajdują się w klauzuli Group by ani funkcji agregujących, nie będą przepływać do danych wyjściowych przekształcenia agregowanego. Jeśli chcesz uwzględnić inne kolumny w zagregowanych danych wyjściowych, wykonaj jedną z następujących metod:

* Użyj funkcji agregującej, takiej jak `last()` lub, `first()` Aby dołączyć tę dodatkową kolumnę.
* Ponownie Dołącz kolumny do strumienia wyjściowego przy użyciu [wzorca samosprzężenia](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="removing-duplicate-rows"></a>Usuwanie zduplikowanych wierszy

Typowym zastosowaniem transformacji agregowanej jest usunięcie lub zidentyfikowanie zduplikowanych wpisów w danych źródłowych. Ten proces jest nazywany deduplikacją. W oparciu o zestaw kluczy Grupuj według, użyj heurystyki wyboru, aby określić, który zduplikowany wiersz ma być zachowany. Typowe heurystyke to `first()` , `last()` , `max()` , i `min()` . Użyj [wzorców kolumn](concepts-data-flow-column-pattern.md) , aby zastosować regułę do każdej kolumny z wyjątkiem grupowania według kolumn.

![Deduplikacja](media/data-flow/agg-dedupe.png "Deduplikacja")

W powyższym przykładzie kolumny `ProductID` i `Name` są używane do grupowania. Jeśli dwa wiersze mają te same wartości dla tych dwóch kolumn, są traktowane jako duplikaty. W tej transformacji agregacji wartości pierwszego dopasowanego wiersza zostaną zachowane i wszystkie pozostałe zostaną usunięte. Przy użyciu składni wzorca kolumny wszystkie kolumny, których nazwy nie są `ProductID` i `Name` są mapowane na nazwę istniejącej kolumny i mają wartość pierwszych dopasowanych wierszy. Schemat danych wyjściowych jest taki sam jak schemat wejściowy.

W przypadku scenariuszy walidacji danych `count()` Funkcja może służyć do zliczenia liczbę duplikatów.

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

Poniższy przykład pobiera przychodzące strumienie `MoviesYear` i grupuje wiersze według kolumny `year` . Transformacja tworzy kolumnę agregującą `avgrating` , która oblicza średnią z kolumny `Rating` . Ta transformacja agregacji ma nazwę `AvgComedyRatingsByYear` .

W Data Factory środowisku użytkownika Ta transformacja wygląda jak na poniższym obrazie:

![Grupuj według przykładu](media/data-flow/agg-script1.png "Grupuj według przykładu")

![Przykład agregacji](media/data-flow/agg-script2.png "Przykład agregacji")

Skrypt przepływu danych dla tego przekształcenia znajduje się w poniższym fragmencie kodu.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![Skrypt zagregowanego przepływu danych](media/data-flow/aggdfs1.png "Skrypt zagregowanego przepływu danych")

```MoviesYear```: Kolumna pochodna definiująca kolumny Year i title ```AvgComedyRatingByYear``` : przekształcenie agregowane dla średniej klasyfikacji Comedies pogrupowane według roku ```avgrating``` : Nazwa nowej kolumny tworzonej do przechowywania zagregowanej wartości

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Następne kroki

* Definiowanie agregacji opartej na oknach przy użyciu [transformacji okna](data-flow-window.md)
