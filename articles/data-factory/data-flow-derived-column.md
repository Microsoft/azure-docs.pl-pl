---
title: Transformacja kolumn pochodnych w mapowaniu przepływu danych
description: Dowiedz się, jak przekształcać dane w skali w Azure Data Factory przy użyciu transformacji kolumn pochodnych przepływu danych mapowania.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 38ec2d4619f47bf9fc4d1815cb6e9990cef72dcf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606499"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformacja kolumn pochodnych w mapowaniu przepływu danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj przekształcenia kolumn pochodnych, aby wygenerować nowe kolumny w przepływie danych lub zmodyfikować istniejące pola.

## <a name="derived-column-settings"></a>Ustawienia kolumn pochodnych

Aby zastąpić istniejącą kolumnę, wybierz ją za pomocą listy rozwijanej kolumny. W przeciwnym razie użyj pola wybór kolumny jako pola tekstowego i wpisz nazwę nowej kolumny. Aby utworzyć wyrażenie kolumny pochodnej, kliknij pole "wprowadź wyrażenie", aby otworzyć [konstruktora wyrażeń przepływu danych](concepts-data-flow-expression-builder.md).

![Ustawienia kolumn pochodnych](media/data-flow/dc1.png "Ustawienia kolumn pochodnych")

Aby dodać dodatkowe kolumny pochodne, umieść kursor na istniejącej kolumnie pochodnej i kliknij ikonę znaku plus. Wybierz opcję **Dodaj kolumnę** lub **Dodaj wzorzec kolumny**. Wzorce kolumn mogą być przydatne, jeśli nazwy kolumn są zmienne ze źródeł. Aby uzyskać więcej informacji, zobacz [wzorce kolumn](concepts-data-flow-column-pattern.md).

![Nowy wybór kolumny pochodnej](media/data-flow/columnpattern.png "Nowy wybór kolumny pochodnej")

## <a name="build-schemas-in-output-schema-pane"></a>Kompiluj schematy w okienku schematu danych wyjściowych

Kolumny modyfikowane i dodawane do schematu są wyświetlane w okienku schematu danych wyjściowych. W tym miejscu możesz interaktywnie kompilować proste i złożone struktury danych. Aby dodać dodatkowe pola, wybierz pozycję **Dodaj kolumnę**. Aby utworzyć hierarchie, wybierz pozycję **Dodaj podkolumnę**.

![Dodaj podkolumnę](media/data-flow/addsubcolumn.png "Dodaj podkolumnę")

Aby uzyskać więcej informacji na temat obsługi typów złożonych w przepływie danych, zobacz [Obsługa JSON w mapowaniu przepływu danych](format-json.md#mapping-data-flow-properties).

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

Poniższy przykład jest kolumną pochodną o nazwie `CleanData` , która przyjmuje strumień przychodzący `MoviesYear` i tworzy dwie kolumny pochodne. Pierwsza kolumna pochodna zastępuje kolumnę `Rating` z wartością klasyfikacji jako typ liczby całkowitej. Druga kolumna pochodna to wzorzec pasujący do każdej kolumny, której nazwa rozpoczyna się od "filmów". Dla każdej dopasowanej kolumny tworzy kolumnę `movie` , która jest równa wartości dopasowanej kolumny poprzedzonej prefiksem "movie_". 

W Data Factory środowisku użytkownika Ta transformacja wygląda jak na poniższym obrazie:

![Utwórz przykład](media/data-flow/derive-script1.png "Utwórz przykład")

Skrypt przepływu danych dla tego przekształcenia znajduje się w poniższym fragmencie kodu:

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

- Dowiedz się więcej o [języku wyrażeń przepływu danych mapowania](data-flow-expression-functions.md).
