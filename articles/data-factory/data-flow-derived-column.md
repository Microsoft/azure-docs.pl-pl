---
title: Transformacja kolumn pochodnych w przepływie danych mapowania
description: Dowiedz się, jak przekształcać dane na dużą skalę w usłudze Azure Data Factory dzięki transformacji kolumn pochodnych przepływu danych mapowania.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 06cb868071612ae2825e86cac32734dcd279c99f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413843"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformacja kolumn pochodnych w przepływie danych mapowania

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Transformacja kolumn pochodnych służy do generowania nowych kolumn w przepływie danych lub modyfikowania istniejących pól.

## <a name="derived-column-settings"></a>Ustawienia kolumny pochodnej

Aby zastąpić istniejącą kolumnę, zaznacz ją za pomocą listy rozwijanej kolumny. W przeciwnym razie użyj pola wyboru kolumny jako pola tekstowego i wpisz nazwę nowej kolumny. Aby utworzyć wyrażenie kolumny pochodnej, kliknij pole "Wprowadź wyrażenie", aby otworzyć [konstruktora wyrażeń przepływu danych](concepts-data-flow-expression-builder.md).

![Ustawienia kolumny pochodnej](media/data-flow/dc1.png "Ustawienia kolumny pochodnej")

Aby dodać dodatkowe kolumny pochodne, umieść wskaźnik myszy na istniejącej kolumnie pochodnej i kliknij ikonę plus. Wybierz pozycję **Dodaj kolumnę** lub **Dodaj wzór kolumny**. Wzorce kolumn mogą się przydać, jeśli nazwy kolumn są zmienne ze źródeł. Aby uzyskać więcej informacji, zobacz [Wzorce kolumn](concepts-data-flow-column-pattern.md).

![Nowy wybór kolumny pochodnej](media/data-flow/columnpattern.png "Nowy wybór kolumny pochodnej")

## <a name="build-schemas-in-output-schema-pane"></a>Tworzenie schematów w okienku Schemat wyjściowy

Kolumny, które modyfikujesz i dodajesz do schematu, są wymienione w okienku Schemat wyjściowy. W tym miejscu można interaktywnie tworzyć proste i złożone struktury danych. Aby dodać dodatkowe pola, wybierz pozycję **Dodaj kolumnę**. Aby utworzyć hierarchie, wybierz **pozycję Dodaj podkolumnę**.

![Dodaj podkolumny](media/data-flow/addsubcolumn.png "Dodaj podkolumnę")

Aby uzyskać więcej informacji na temat obsługi złożonych typów w przepływie danych, zobacz [Obsługa JSON w przepływie danych mapowania](format-json.md#mapping-data-flow-properties).

![Dodawanie złożonej kolumny](media/data-flow/complexcolumn.png "Dodawanie kolumn")

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Przykład

Poniższy przykład jest kolumna `CleanData` pochodna o `MoviesYear` nazwie, która ma strumień przychodzący i tworzy dwie kolumny pochodne. Pierwsza kolumna pochodna `Rating` zastępuje kolumnę wartością klasyfikacji jako typem liczby całkowitej. Druga kolumna pochodna to wzorzec, który pasuje do każdej kolumny, której nazwa zaczyna się od "filmów". Dla każdej dopasowanej kolumny tworzy `movie` kolumnę, która jest równa wartości dopasowanej kolumny poprzedzone "movie_". 

W ux fabryki danych ta transformacja wygląda jak poniższy obraz:

![Przykład wyprowadzenia](media/data-flow/derive-script1.png "Przykład wyprowadzenia")

Skrypt przepływu danych dla tej transformacji znajduje się we urywce poniżej:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [języku wyrażeń Mapowanie przepływu danych](data-flow-expression-functions.md).
