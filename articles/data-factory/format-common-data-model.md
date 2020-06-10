---
title: Format Common Data Model
description: Przekształcanie danych przy użyciu systemu metadanych usługi Common Data Model
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: daperlov
ms.openlocfilehash: 1764036413d6e4f634ed156f7cfb441b4a2bb1e6
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84604965"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Format Common Data Model w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

System metadanych usługi Common Data Model (CDM) umożliwia łatwe udostępnianie danych i ich znaczenie między aplikacjami i procesami biznesowymi. Aby dowiedzieć się więcej, zobacz Omówienie usługi [Common Data Model](https://docs.microsoft.com/common-data-model/) .

W Azure Data Factory użytkownicy mogą przetwarzać do i z jednostek CDM przechowywanych w [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) przy użyciu mapowania przepływów danych.

> [!NOTE]
> Łącznik formatu usługi Common Data Model (CDM) dla przepływów danych ADF jest obecnie dostępny jako publiczna wersja zapoznawcza.

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Wspólny model danych jest dostępny jako [Wbudowany zestaw](data-flow-source.md#inline-datasets) danych w mapowaniu przepływów danych jako źródła i ujścia.

### <a name="source-properties"></a>Właściwości źródła

Poniższa tabela zawiera listę właściwości obsługiwanych przez źródło CDM. Można edytować te właściwości na karcie **Opcje źródła** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Format musi być`cdm` | tak | `cdm` | format |
| Format metadanych | Miejsce, w którym znajdują się odwołania do danych jednostki. Jeśli jest używany program CDM w wersji 1,0, wybierz pozycję Manifest. W przypadku używania wersji CDM przed 1,0 wybierz pozycję model. JSON. | Tak | `'manifest'` lub `'model'` | manifesttype |
| Lokalizacja główna: kontener | Nazwa kontenera folderu CDM | tak | String | Wymagany |
| Lokalizacja główna: ścieżka folderu | Lokalizacja folderu głównego folderu CDM | tak | String | folderPath |
| Plik manifestu: ścieżka jednostki | Ścieżka folderu jednostki w folderze głównym | nie | String | entityPath |
| Plik manifestu: Nazwa manifestu | Nazwa pliku manifestu. Wartość domyślna to "default"  | Nie | String | manifestname |
| Filtruj według ostatniej modyfikacji | Wybierz filtrowanie plików na podstawie czasu ich ostatniej modyfikacji | nie | Znacznik czasu | modifiedAfter <br> modifiedBefore | 
| Połączona usługa schematu | Połączona usługa, w której znajduje się korpus | tak, jeśli używasz manifestu | `'adlsgen2'` lub `'github'` | corpusStore | 
| Kontener odwołania do jednostki | Korpus kontenerów znajduje się w | tak, jeśli używasz manifestu i korpus w ADLS Gen2 | String | adlsgen2_fileSystem |
| Repozytorium odwołań do jednostek | Nazwa repozytorium GitHub | tak, jeśli używasz manifestu i korpus w usłudze GitHub | String | github_repository |
| Gałąź odwołania do jednostki | Gałąź repozytorium GitHub | tak, jeśli używasz manifestu i korpus w usłudze GitHub | String |  github_branch |
| Folder korpus | główna lokalizacja korpus | tak, jeśli używasz manifestu | String | corpusPath |
| Jednostka korpus | Ścieżka do odwołania do jednostki | tak | String | jednostka |
| Nie znaleziono plików | W przypadku wartości true błąd nie jest zgłaszany, jeśli nie znaleziono plików | nie | `true` lub `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>Importuj schemat

CDM jest dostępny tylko jako Wbudowany zestaw danych i domyślnie nie ma skojarzonego schematu. Aby uzyskać metadane kolumny, kliknij przycisk **Importuj schemat** na karcie **projekcja** . Pozwoli to na odwoływanie się do nazw kolumn i typów danych określonych przez korpus. Aby zaimportować schemat, [sesja debugowania przepływu danych](concepts-data-flow-debug-mode.md) musi być aktywna.

![Importuj schemat](media/format-common-data-model/import-schema-source.png)

### <a name="cdm-source-example"></a>Przykład źródła CDM

Poniższy obraz stanowi przykład konfiguracji źródła CDM w mapowaniu przepływów danych.

![Źródło CDM](media/format-common-data-model/data-flow-source.png)

Skojarzony skrypt przepływu danych:

```
source(output(
        ServingSizeId as integer,
        ServingSize as integer,
        ServingSizeUomId as string,
        ServingSizeNote as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'ServingSize.cdm.json/ServingSize',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ServingSizeManifest',
    entityPath: 'ServingSize',
    corpusPath: 'ProductAhold_Updated',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ServingSizeData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>Właściwości ujścia

Poniższa tabela zawiera listę właściwości obsługiwanych przez ujścia CDM. Te właściwości można edytować na karcie **Ustawienia** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Format musi być`cdm` | tak | `cdm` | format |
| Lokalizacja główna: kontener | Nazwa kontenera folderu CDM | tak | String | Wymagany |
| Lokalizacja główna: ścieżka folderu | Lokalizacja folderu głównego folderu CDM | tak | String | folderPath |
| Plik manifestu: ścieżka jednostki | Ścieżka folderu jednostki w folderze głównym | nie | String | entityPath |
| Plik manifestu: Nazwa manifestu | Nazwa pliku manifestu. Wartość domyślna to "default" | Nie | String | manifestname |
| Połączona usługa schematu | Połączona usługa, w której znajduje się korpus | tak | `'adlsgen2'` lub `'github'` | corpusStore | 
| Kontener odwołania do jednostki | Korpus kontenerów znajduje się w | tak, jeśli korpus w ADLS Gen2 | String | adlsgen2_fileSystem |
| Repozytorium odwołań do jednostek | Nazwa repozytorium GitHub | tak, jeśli korpus w serwisie GitHub | String | github_repository |
| Gałąź odwołania do jednostki | Gałąź repozytorium GitHub | tak, jeśli korpus w serwisie GitHub | String |  github_branch |
| Folder korpus | główna lokalizacja korpus | tak | String | corpusPath |
| Jednostka korpus | Ścieżka do odwołania do jednostki | tak | String | jednostka |
| Ścieżka partycji | Lokalizacja, w której zostanie zapisywana partycja | nie | String | partitionPath |
| Wyczyść folder | Jeśli folder docelowy został wyczyszczony przed zapisem | nie | `true` lub `false` | obciąć |
| Typ formatu | Wybierz, aby określić format Parquet | nie | `parquet`Jeśli określony | podformat |
| Ogranicznik kolumny | Jeśli piszesz do DelimitedText, jak ograniczać kolumny | tak, jeśli piszesz do DelimitedText | String | columnDelimiter |
| Pierwszy wiersz jako nagłówek | W przypadku korzystania z DelimitedText, niezależnie od tego, czy nazwy kolumn są dodawane jako nagłówek | nie | `true` lub `false` | columnNamesAsHeader |

### <a name="cdm-sink-example"></a>Przykład ujścia CDM

Poniżej przedstawiono przykład konfiguracji ujścia CDM w mapowaniu przepływów danych.

![Źródło CDM](media/format-common-data-model/data-flow-sink.png)

Skojarzony skrypt przepływu danych:

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'ServingSize.cdm.json/ServingSize',
    format: 'cdm',
    entityPath: 'ServingSize',
    manifestName: 'ServingSizeManifest',
    corpusPath: 'ProductAhold_Updated',
    partitionPath: 'adf',
    folderPath: 'ServingSizeData',
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
