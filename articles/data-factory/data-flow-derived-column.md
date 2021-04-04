---
title: Transformacja kolumn pochodnych w mapowaniu przepływu danych
description: Dowiedz się, jak przekształcać dane w skali w Azure Data Factory przy użyciu transformacji kolumn pochodnych przepływu danych mapowania.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 2e90a8779322cf8967ca9a194c6cc760f7c8b8f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90532026"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformacja kolumn pochodnych w mapowaniu przepływu danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj przekształcenia kolumn pochodnych, aby wygenerować nowe kolumny w przepływie danych lub zmodyfikować istniejące pola.

## <a name="create-and-update-columns"></a>Tworzenie i aktualizowanie kolumn

Podczas tworzenia kolumny pochodnej można wygenerować nową kolumnę lub zaktualizować istniejącą. W **kolumnie** pole tekstowe wpisz w tworzonej kolumnie. Aby zastąpić istniejącą kolumnę w schemacie, można użyć listy rozwijanej kolumny. Aby utworzyć wyrażenie kolumny pochodnej, kliknij pole tekstowe **wprowadź wyrażenie** . Możesz rozpocząć wpisywanie wyrażenia lub otworzyć Konstruktora wyrażeń, aby utworzyć logikę.

![Ustawienia kolumn pochodnych](media/data-flow/create-derive-column.png "Ustawienia kolumn pochodnych")

Aby dodać więcej kolumn pochodnych, kliknij pozycję **Dodaj** powyżej listy kolumn lub ikonę znaku plus obok istniejącej kolumny pochodnej. Wybierz opcję **Dodaj kolumnę** lub **Dodaj wzorzec kolumny**.

![Nowy wybór kolumny pochodnej](media/data-flow/add-derived-column.png "Nowy wybór kolumny pochodnej")

### <a name="column-patterns"></a>Wzorce kolumn

W przypadkach, gdy schemat nie jest jawnie zdefiniowany lub chcesz zaktualizować zestaw kolumn zbiorczo, należy utworzyć kolumnę patters. Wzorce kolumn umożliwiają dopasowywanie kolumn przy użyciu reguł opartych na metadanych kolumn i tworzenie kolumn pochodnych dla każdej dopasowanej kolumny. Aby uzyskać więcej informacji, zapoznaj się [z informacjami na temat tworzenia wzorców kolumn](concepts-data-flow-column-pattern.md#column-patterns-in-derived-column-and-aggregate) w transformację kolumn pochodnych.

![Wzorce kolumn](media/data-flow/column-pattern-derive.png "Wzorce kolumn")

## <a name="building-schemas-using-the-expression-builder"></a>Kompilowanie schematów przy użyciu Konstruktora wyrażeń

Podczas korzystania z [konstruktora wyrażeń](concepts-data-flow-expression-builder.md)przepływu danych mapowania można tworzyć, edytować i zarządzać kolumnami pochodnymi w sekcji **kolumny pochodne** . Zostanie wyświetlona lista wszystkich kolumn utworzonych lub zmienionych w transformacji. Interaktywnie wybierz kolumnę lub wzorzec, który edytujesz, klikając nazwę kolumny. Aby dodać dodatkową kolumnę, wybierz pozycję **Utwórz nową** , a następnie wybierz, czy chcesz dodać pojedynczą kolumnę, czy wzorzec.

![Utwórz nową kolumnę](media/data-flow/derive-add-column.png "Utwórz nową kolumnę")

Podczas pracy z złożonymi kolumnami można tworzyć podkolumny. Aby to zrobić, kliknij ikonę plus obok dowolnej kolumny i wybierz pozycję **Dodaj podkolumnę**. Aby uzyskać więcej informacji na temat obsługi typów złożonych w przepływie danych, zobacz [Obsługa JSON w mapowaniu przepływu danych](format-json.md#mapping-data-flow-properties).

![Dodaj podkolumnę](media/data-flow/derive-add-subcolumn.png "Dodaj podkolumnę")

Aby uzyskać więcej informacji na temat obsługi typów złożonych w przepływie danych, zobacz [Obsługa JSON w mapowaniu przepływu danych](format-json.md#mapping-data-flow-properties).

![Dodawanie złożonej kolumny](media/data-flow/derive-complex-column.png "Dodawanie kolumn")

### <a name="locals"></a>Zmienne lokalne

Jeśli udostępniasz logikę w wielu kolumnach lub chcesz compartmentalize logikę, możesz utworzyć lokalną w ramach przekształcenia kolumn pochodnych. Local to zestaw logiki, który nie jest propagowany do następującej transformacji. Elementy lokalne można tworzyć w ramach konstruktora wyrażeń, przechodząc do **elementów Expression** i wybierając pozycję **locale**. Utwórz nowy element, wybierając pozycję **Utwórz nowy**.

![Utwórz lokalne](media/data-flow/create-local.png "Utwórz lokalne")

Elementy lokalne mogą odwoływać się do dowolnego elementu wyrażenia w kolumnie pochodnej, w tym funkcji, schematu wejściowego, parametrów i innych zmiennych lokalnych. W przypadku odwoływania się do innych elementów lokalnych kolejność jest określana jako "powyżej" jako "powyżej".

![Utwórz lokalne 2](media/data-flow/create-local-2.png "Utwórz lokalne 2")

Aby odwołać się do lokalnego w kolumnie pochodnej, kliknij element lokalny w widoku **elementów wyrażenia** lub odwołuje się do niego dwukropek przed jego nazwą. Na przykład w przypadku lokalnego o nazwie local1 będzie przywoływany `:local1` . Aby edytować definicję lokalną, umieść kursor nad nim w widoku elementy wyrażenia i kliknij ikonę ołówka.

![Używanie zmiennych lokalnych](media/data-flow/using-locals.png "Używanie zmiennych lokalnych")

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

![Utwórz przykład](media/data-flow/derive-script.png "Utwórz przykład")

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
