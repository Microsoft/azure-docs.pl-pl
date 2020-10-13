---
title: Format Delta w Azure Data Factory
description: Przekształcanie i przenoszenie danych z programu Delta Lake przy użyciu formatu Delta
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: daperlov
ms.openlocfilehash: 3e1c5f3b360960779dd58c8c05b25885df81d2e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91276512"
---
# <a name="delta-format-in-azure-data-factory"></a>Format Delta w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób kopiowania danych do i z usługi Delta Lake przechowywanej w [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) lub [Azure Blob Storage](connector-azure-blob-storage.md) przy użyciu formatu delty. Ten łącznik jest dostępny jako [Wbudowany zestaw](data-flow-source.md#inline-datasets) danych w mapowaniu przepływów danych jako źródła i ujścia.

> [!NOTE]
> Łącznik formatu Delta na potrzeby mapowania przepływów danych jest obecnie dostępny jako publiczna wersja zapoznawcza.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4ALTs]

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Ten łącznik jest dostępny jako [Wbudowany zestaw](data-flow-source.md#inline-datasets) danych w mapowaniu przepływów danych jako źródła i ujścia.

### <a name="source-properties"></a>Właściwości źródła

Poniższa tabela zawiera listę właściwości obsługiwanych przez źródło różnicowe. Można edytować te właściwości na karcie **Opcje źródła** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Format musi być `delta` | tak | `delta` | format |
| System plików | Kontener/system plików delty Lake | tak | Ciąg | Wymagany |
| Ścieżka folderu | Bezpośrednia różnica w usłudze Data Lake | tak | Ciąg | folderPath |
| Typ kompresji | Typ kompresji tabeli różnicowej | nie | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | CompressionType |
| Poziom kompresji | Zdecyduj, czy kompresja kończy się tak szybko, jak to możliwe, czy plik powinien być optymalnie kompresowany. | wymagane, jeśli `compressedType` jest określony. | `Optimal` lub `Fastest` | compressionLevel |
| Czas podróży | Zdecyduj, czy chcesz utworzyć zapytanie do starszej migawki tabeli różnicowej | nie | Zapytanie według sygnatury czasowej: sygnatura czasowa <br> Zapytanie według wersji: liczba całkowita | timestampAsOf <br> versionAsOf |
| Nie znaleziono plików | W przypadku wartości true błąd nie jest zgłaszany, jeśli nie znaleziono plików | nie | `true` lub `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>Importuj schemat

Delta jest dostępna tylko jako Wbudowany zestaw danych i domyślnie nie ma skojarzonego schematu. Aby uzyskać metadane kolumny, kliknij przycisk **Importuj schemat** na karcie **projekcja** . Pozwoli to na odwoływanie się do nazw kolumn i typów danych określonych przez korpus. Aby zaimportować schemat, [sesja debugowania przepływu danych](concepts-data-flow-debug-mode.md) musi być aktywna i trzeba mieć istniejący plik definicji jednostki CDM.
 

### <a name="delta-source-script-example"></a>Przykład skryptu źródła różnicowego

```
source(output(movieId as integer,
            title as string,
            releaseDate as date,
            rated as boolean,
            screenedOn as timestamp,
            ticketPrice as decimal(10,2)
            ),
    store: 'local',
    format: 'delta',
    versionAsOf: 0,
    allowSchemaDrift: false,
    folderPath: $tempPath + '/delta'
  ) ~> movies
```

### <a name="sink-properties"></a>Właściwości ujścia

Poniższa tabela zawiera listę właściwości obsługiwanych przez ujścia różnic. Te właściwości można edytować na karcie **Ustawienia** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Format musi być `delta` | tak | `delta` | format |
| System plików | Kontener/system plików delty Lake | tak | Ciąg | Wymagany |
| Ścieżka folderu | Bezpośrednia różnica w usłudze Data Lake | tak | Ciąg | folderPath |
| Typ kompresji | Typ kompresji tabeli różnicowej | nie | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | CompressionType |
| Poziom kompresji | Zdecyduj, czy kompresja kończy się tak szybko, jak to możliwe, czy plik powinien być optymalnie kompresowany. | wymagane, jeśli `compressedType` jest określony. | `Optimal` lub `Fastest` | compressionLevel |
| Vacuum | Określ próg przechowywania w godzinach dla starszych wersji tabeli. Wartość 0 lub mniej wartością domyślną jest 30 dni | tak | Liczba całkowita | ciśnienie |
| Update — Metoda | Określ, które operacje aktualizacji są dozwolone w ramach delty Lake. W przypadku metod, które nie są wstawiane, do oznaczania wierszy wymagane jest przekształcenie poprzedzające zmianę wiersza. | tak | `true` lub `false` | usuwaln <br> wstawialny <br> aktualizowalne <br> upsertable |

### <a name="delta-sink-script-example"></a>Przykład skryptu ujścia różnicowego

Skojarzony skrypt przepływu danych:

```
moviesAltered sink(
          input(movieId as integer,
                title as string
            ),
           mapColumn(
                movieId,
                title
            ),
           insertable: true,
           updateable: true,
           deletable: true,
           upsertable: false,
           keys: ['movieId'],
            store: 'local',
           format: 'delta',
           vacuum: 180,
           folderPath: $tempPath + '/delta'
           ) ~> movieDB
```

### <a name="known-limitations"></a>Znane ograniczenia

Podczas zapisywania do ujścia różnicowego występuje znane ograniczenie, w którym liczby zapisywanych wierszy nie będą zwracane w danych wyjściowych monitorowania.

## <a name="next-steps"></a>Następne kroki

* Utwórz [transformację źródłową](data-flow-source.md) w mapowaniu przepływu danych.
* Utwórz [transformację ujścia](data-flow-sink.md) w mapowaniu przepływu danych.
* Utwórz [przekształcenia ALTER Row](data-flow-alter-row.md) , aby oznaczyć wiersze jako INSERT, Update, upsert lub DELETE.
