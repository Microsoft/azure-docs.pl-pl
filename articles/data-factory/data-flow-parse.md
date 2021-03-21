---
title: Przeanalizuj transformację danych przy mapowaniu przepływu danych
description: Analizuj osadzone dokumenty kolumnowy
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 4db9503ea84ae13148a89a03048c73399413e5cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710196"
---
# <a name="parse-transformation-in-mapping-data-flow"></a>Przeanalizuj transformację w mapowaniu przepływu danych

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj przekształcenia Analizuj, aby przeanalizować kolumny w danych, które znajdują się w formularzu dokumentu. Aktualnie obsługiwane typy osadzonych dokumentów, które mogą być analizowane, to JSON i rozdzielony tekst.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWykdO]

## <a name="configuration"></a>Konfigurowanie

W panelu konfiguracja transformacji przekształceń najpierw wybierz typ danych zawartych w kolumnach, które chcesz przeanalizować w tekście. Transformacja analizy zawiera również następujące ustawienia konfiguracji.

![Przeanalizuj ustawienia](media/data-flow/data-flow-parse-1.png "Analizuj")

### <a name="column"></a>Kolumna

Podobnie jak w przypadku kolumn pochodnych i agregacji, w tym miejscu należy zmodyfikować pustą kolumnę, wybierając ją z selektora listy rozwijanej. Możesz też wpisać tutaj nazwę nowej kolumny. Na stronie ADF będą przechowywane przeanalizowane dane źródłowe w tej kolumnie.

### <a name="expression"></a>Wyrażenie

Użyj konstruktora wyrażeń, aby ustawić źródło do analizy. Może to być proste wybranie kolumny źródłowej zawierającej dane, które chcesz przeanalizować, lub można utworzyć złożone wyrażenia do analizy.

### <a name="output-column-type"></a>Typ kolumny wyjściowej

Tutaj można skonfigurować docelowy schemat wyjściowy z analizy, która zostanie zapisywana w jednej kolumnie.

![Przykład analizy](media/data-flow/data-flow-parse-2.png "Przykład analizy")

W tym przykładzie zdefiniowano analizę pola przychodzącego "jsonString", które jest zwykłym tekstem, ale sformatowaną jako strukturę JSON. Zamierzamy przechowywać przeanalizowane wyniki jako kod JSON w nowej kolumnie o nazwie "JSON" z tym schematem:

```(trade as boolean, customers as string[])```

Zapoznaj się z kartami Inspekcja i podglądem danych, aby sprawdzić, czy dane wyjściowe zostały prawidłowo zamapowane.

## <a name="examples"></a>Przykłady

```
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    documentForm: 'documentPerLine') ~> JsonSource
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false) ~> CsvSource
JsonSource derive(jsonString = toString(goods)) ~> StringifyJson
StringifyJson parse(json = jsonString ? (trade as boolean,
        customers as string[]),
    format: 'json',
    documentForm: 'arrayOfDocuments') ~> ParseJson
CsvSource derive(csvString = 'Id|name|year\n\'1\'|\'test1\'|\'1999\'') ~> CsvString
CsvString parse(csv = csvString ? (id as integer,
        name as string,
        year as string),
    format: 'delimited',
    columnNamesAsHeader: true,
    columnDelimiter: '|',
    nullValue: '',
    documentForm: 'documentPerLine') ~> ParseCsv
ParseJson select(mapColumn(
        jsonString,
        json
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedJson
ParseCsv select(mapColumn(
        csvString,
        csv
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> KeepStringAndParsedCsv
```

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

### <a name="examples"></a>Przykłady

```
parse(json = jsonString ? (trade as boolean,
                                customers as string[]),
                format: 'json',
                documentForm: 'singleDocument') ~> ParseJson

parse(csv = csvString ? (id as integer,
                                name as string,
                                year as string),
                format: 'delimited',
                columnNamesAsHeader: true,
                columnDelimiter: '|',
                nullValue: '',
                documentForm: 'documentPerLine') ~> ParseCsv
```    

## <a name="next-steps"></a>Następne kroki

* Użyj [przekształcenia Spłaszcz](data-flow-flatten.md) , aby przestawić wiersze do kolumn.
* Użyj [przekształcenia kolumn pochodnych](data-flow-derived-column.md) do kolumn tabeli przestawnej do wierszy.
