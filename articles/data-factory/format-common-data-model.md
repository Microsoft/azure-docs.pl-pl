---
title: Format Common Data Model
description: Przekształcanie danych przy użyciu systemu metadanych usługi Common Data Model
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: makromer
ms.openlocfilehash: a08457ba041fa39fda367976498a4a89930c56e3
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585164"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Format Common Data Model w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

System metadanych usługi Common Data Model (CDM) umożliwia łatwe udostępnianie danych i ich znaczenie między aplikacjami i procesami biznesowymi. Aby dowiedzieć się więcej, zobacz Omówienie usługi [Common Data Model](/common-data-model/) .

W Azure Data Factory użytkownicy mogą przekształcać dane z jednostek CDM zarówno w model.js, jak i w postaci manifestu przechowywanego w [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) przy użyciu mapowania przepływów danych. Możesz również ujścia danych w formacie CDM przy użyciu odwołań do jednostek CDM, które będą wyładowywane dane w formacie CSV lub Parquet w folderach partycjonowanych. 

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Wspólny model danych jest dostępny jako [Wbudowany zestaw](data-flow-source.md#inline-datasets) danych w mapowaniu przepływów danych jako źródła i ujścia.

> [!NOTE]
> Podczas pisania jednostek CDM należy mieć istniejącą definicję jednostki CDM (schemat metadanych), która ma być już zdefiniowana jako odwołanie. Obiekt sink przepływu danych ADF odczyta ten plik jednostki CDM i zaimportuje schemat do ujścia w celu mapowania pól.

### <a name="source-properties"></a>Właściwości źródła

Poniższa tabela zawiera listę właściwości obsługiwanych przez źródło CDM. Można edytować te właściwości na karcie **Opcje źródła** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Format musi być `cdm` | tak | `cdm` | format |
| Format metadanych | Miejsce, w którym znajdują się odwołania do danych jednostki. Jeśli jest używany program CDM w wersji 1,0, wybierz pozycję Manifest. W przypadku używania wersji CDM przed 1,0 wybierz pozycję model.json. | Tak | `'manifest'` lub `'model'` | manifesttype |
| Lokalizacja główna: kontener | Nazwa kontenera folderu CDM | tak | Ciąg | Wymagany |
| Lokalizacja główna: ścieżka folderu | Lokalizacja folderu głównego folderu CDM | tak | Ciąg | folderPath |
| Plik manifestu: ścieżka jednostki | Ścieżka folderu jednostki w folderze głównym | nie | Ciąg | entityPath |
| Plik manifestu: Nazwa manifestu | Nazwa pliku manifestu. Wartość domyślna to "default"  | Nie | Ciąg | manifestname |
| Filtruj według ostatniej modyfikacji | Wybierz filtrowanie plików na podstawie czasu ich ostatniej modyfikacji | nie | Znacznik czasu | modifiedAfter <br> modifiedBefore | 
| Połączona usługa schematu | Połączona usługa, w której znajduje się korpus | tak, jeśli używasz manifestu | `'adlsgen2'` lub `'github'` | corpusStore | 
| Kontener odwołania do jednostki | Korpus kontenerów znajduje się w | tak, jeśli używasz manifestu i korpus w ADLS Gen2 | Ciąg | adlsgen2_fileSystem |
| Repozytorium odwołań do jednostek | Nazwa repozytorium GitHub | tak, jeśli używasz manifestu i korpus w usłudze GitHub | Ciąg | github_repository |
| Gałąź odwołania do jednostki | Gałąź repozytorium GitHub | tak, jeśli używasz manifestu i korpus w usłudze GitHub | Ciąg |  github_branch |
| Folder korpus | główna lokalizacja korpus | tak, jeśli używasz manifestu | Ciąg | corpusPath |
| Jednostka korpus | Ścieżka do odwołania do jednostki | tak | Ciąg | jednostka |
| Nie znaleziono plików | W przypadku wartości true błąd nie jest zgłaszany, jeśli nie znaleziono plików | nie | `true` lub `false` | ignoreNoFilesFound |

W przypadku wybrania opcji "odwołanie do jednostki" w transformacje źródła i ujścia można wybrać jedną z tych trzech opcji lokalizacji odwołania do jednostki:

* Lokalna używa jednostki zdefiniowanej w pliku manifestu, który jest już używany przez ADF
* Niestandardowi zostanie poproszony o wskazanie pliku manifestu jednostki, który jest inny niż plik manifestu ADF używa
* Standard użyje odwołania do jednostki z standardowej biblioteki jednostek CDM przechowywanych w ```Github``` .

### <a name="sink-settings"></a>Ustawienia ujścia

* Wskaż plik odwołania do jednostki CDM, który zawiera definicję jednostki, którą chcesz napisać.

![Ustawienia jednostki](media/data-flow/common-data-model-111.png "Odwołanie do jednostki")

* Zdefiniuj ścieżkę partycji i format plików wyjściowych, które mają być używane przez funkcję ADF do pisania obiektów.

![Format jednostki](media/data-flow/common-data-model-222.png "Format jednostki")

* Ustaw lokalizację pliku wyjściowego oraz lokalizację i nazwę pliku manifestu.

![Lokalizacja CDM](media/data-flow/common-data-model-333.png "Lokalizacja CDM")


#### <a name="import-schema"></a>Importuj schemat

CDM jest dostępny tylko jako Wbudowany zestaw danych i domyślnie nie ma skojarzonego schematu. Aby uzyskać metadane kolumny, kliknij przycisk **Importuj schemat** na karcie **projekcja** . Pozwoli to na odwoływanie się do nazw kolumn i typów danych określonych przez korpus. Aby zaimportować schemat, [sesja debugowania przepływu danych](concepts-data-flow-debug-mode.md) musi być aktywna i trzeba mieć istniejący plik definicji jednostki CDM.

Podczas mapowania kolumn przepływu danych na właściwości jednostki w transformację ujścia kliknij kartę "mapowanie" i wybierz pozycję "Importuj schemat". ADF odczytaje odwołanie do jednostki, które zostało wskazane w opcjach ujścia, umożliwiając zamapowanie na docelowy schemat CDM.

![Ustawienia ujścia CDM](media/data-flow/common-data-model-444.png "Mapowanie CDM")

> [!NOTE]
>  W przypadku używania model.jsw typie źródłowym, który pochodzi z przepływów danych Power BI lub platformy, może wystąpić błąd "ścieżka korpus jest pusta lub równa null" z transformacji źródłowej. Jest to prawdopodobnie spowodowane problemami z formatowaniem ścieżki lokalizacji partycji w model.jspliku. Aby rozwiązać ten problem, wykonaj następujące kroki: 

1. Otwórz model.jsw pliku w edytorze tekstu
2. Znajdź partycje. Location — właściwość 
3. Zmień wartość "blob.core.windows.net" na "dfs.core.windows.net"
4. Popraw dowolne kodowanie "% 2F" w adresie URL do "/"
5. Jeśli używasz przepływów danych ADF, znaki specjalne w ścieżce pliku partycji muszą zostać zastąpione wartościami alfanumerycznymi lub przełączać się do Synapse przepływów danych

### <a name="cdm-source-data-flow-script-example"></a>Przykład skryptu przepływu danych źródła CDM

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>Właściwości ujścia

Poniższa tabela zawiera listę właściwości obsługiwanych przez ujścia CDM. Te właściwości można edytować na karcie **Ustawienia** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Format musi być `cdm` | tak | `cdm` | format |
| Lokalizacja główna: kontener | Nazwa kontenera folderu CDM | tak | Ciąg | Wymagany |
| Lokalizacja główna: ścieżka folderu | Lokalizacja folderu głównego folderu CDM | tak | Ciąg | folderPath |
| Plik manifestu: ścieżka jednostki | Ścieżka folderu jednostki w folderze głównym | nie | Ciąg | entityPath |
| Plik manifestu: Nazwa manifestu | Nazwa pliku manifestu. Wartość domyślna to "default" | Nie | Ciąg | manifestname |
| Połączona usługa schematu | Połączona usługa, w której znajduje się korpus | tak | `'adlsgen2'` lub `'github'` | corpusStore | 
| Kontener odwołania do jednostki | Korpus kontenerów znajduje się w | tak, jeśli korpus w ADLS Gen2 | Ciąg | adlsgen2_fileSystem |
| Repozytorium odwołań do jednostek | Nazwa repozytorium GitHub | tak, jeśli korpus w serwisie GitHub | Ciąg | github_repository |
| Gałąź odwołania do jednostki | Gałąź repozytorium GitHub | tak, jeśli korpus w serwisie GitHub | Ciąg |  github_branch |
| Folder korpus | główna lokalizacja korpus | tak | Ciąg | corpusPath |
| Jednostka korpus | Ścieżka do odwołania do jednostki | tak | Ciąg | jednostka |
| Ścieżka partycji | Lokalizacja, w której zostanie zapisywana partycja | nie | Ciąg | partitionPath |
| Wyczyść folder | Jeśli folder docelowy został wyczyszczony przed zapisem | nie | `true` lub `false` | obciąć |
| Typ formatu | Wybierz, aby określić format Parquet | nie | `parquet` Jeśli określony | podformat |
| Ogranicznik kolumny | Jeśli piszesz do DelimitedText, jak ograniczać kolumny | tak, jeśli piszesz do DelimitedText | Ciąg | columnDelimiter |
| Pierwszy wiersz jako nagłówek | W przypadku korzystania z DelimitedText, niezależnie od tego, czy nazwy kolumn są dodawane jako nagłówek | nie | `true` lub `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>Przykład skryptu przepływu danych ujścia CDM

Skojarzony skrypt przepływu danych:

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>Następne kroki

Utwórz [transformację źródłową](data-flow-source.md) w mapowaniu przepływu danych.
