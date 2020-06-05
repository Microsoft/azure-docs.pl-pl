---
title: Format Common Data Model
description: Przekształcanie danych przy użyciu systemu metadanych usługi Common Data Model
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 2faa14ef7724bb9cccfe425569539f5ef0621a28
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84435394"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Format Common Data Model w Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

System metadanych usługi Common Data Model (CDM) umożliwia łatwe udostępnianie danych i ich znaczenie między aplikacjami i procesami biznesowymi. Aby dowiedzieć się więcej, zobacz Omówienie usługi [Common Data Model](https://docs.microsoft.com/common-data-model/) .

W Azure Data Factory użytkownicy mogą przetwarzać do i z jednostek CDM przechowywanych w [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) przy użyciu mapowania przepływów danych.

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Wspólny model danych jest dostępny jako [Wbudowany zestaw](data-flow-source.md#inline-datasets) danych w mapowaniu przepływów danych jako źródła i ujścia.

### <a name="source-properties"></a>Właściwości źródła

Poniższa tabela zawiera listę właściwości obsługiwanych przez źródło CDM.

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Jest zawsze`cdm` | tak | `cdm` | format |
| Format metadanych | Miejsce, w którym znajdują się odwołania do danych jednostki. Jeśli jest używany program CDM w wersji 1,0, wybierz pozycję Manifest. W przypadku używania wersji CDM przed 1,0 wybierz pozycję model. JSON. | Tak | `'manifest'` lub `'model'` | manifesttype |
| Lokalizacja główna: kontener | Nazwa kontenera folderu CDM | tak | Ciąg | Wymagany |
| Lokalizacja główna: ścieżka folderu | Lokalizacja folderu głównego folderu CDM | tak | Ciąg | folderPath |
| Plik manifestu: ścieżka jednostki | Ścieżka folderu jednostki w folderze głównym | nie | Ciąg | entityPath |
| Plik manifestu: Nazwa manifestu | Nazwa pliku manifestu  | tak, jeśli używasz manifestu | Ciąg | manifestname |
| Filtruj według ostatniej modyfikacji | Wybierz filtrowanie plików na podstawie czasu ich ostatniej modyfikacji | nie | Znacznik czasu | modifiedAfter <br> modifiedBefore | 
| Połączona usługa schematu | Połączona usługa, w której znajduje się korpus | tak, jeśli używasz manifestu | `'adlsgen2'` lub `'github'` | corpusStore | 
| Kontener odwołania do jednostki | Korpus kontenerów znajduje się w | tak, jeśli używasz manifestu i korpus w ADLS Gen2 | Ciąg | adlsgen2_fileSystem |
| Repozytorium odwołań do jednostek | Nazwa repozytorium GitHub | tak, jeśli używasz manifestu i korpus w usłudze GitHub | Ciąg | github_repository |
| Gałąź odwołania do jednostki | Gałąź repozytorium GitHub | tak, jeśli używasz manifestu i korpus w usłudze GitHub | Ciąg |  github_branch |
| Folder korpus | główna lokalizacja korpus | tak, jeśli używasz manifestu | Ciąg | corpusPath |
| Jednostka korpus | Ścieżka do odwołania do jednostki | tak | Ciąg | jednostka |
| Nie znaleziono plików | W przypadku wartości true błąd nie jest zgłaszany, jeśli nie znaleziono plików | nie | `true` lub `false` | ignoreNoFilesFound |

![Źródło CDM](media/format-common-data-model/data-flow-source.png)

### <a name="sink-properties"></a>Właściwości ujścia

Poniższa tabela zawiera listę właściwości obsługiwanych przez ujścia CDM.

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Format | Jest zawsze`cdm` | tak | `cdm` | format |
| Lokalizacja główna: kontener | Nazwa kontenera folderu CDM | tak | Ciąg | Wymagany |
| Lokalizacja główna: ścieżka folderu | Lokalizacja folderu głównego folderu CDM | tak | Ciąg | folderPath |
| Plik manifestu: ścieżka jednostki | Ścieżka folderu jednostki w folderze głównym | nie | Ciąg | entityPath |
| Plik manifestu: Nazwa manifestu | Nazwa pliku manifestu  | tak | Ciąg | manifestname |
| Połączona usługa schematu | Połączona usługa, w której znajduje się korpus | tak | `'adlsgen2'` lub `'github'` | corpusStore | 
| Kontener odwołania do jednostki | Korpus kontenerów znajduje się w | tak, jeśli korpus w ADLS Gen2 | Ciąg | adlsgen2_fileSystem |
| Repozytorium odwołań do jednostek | Nazwa repozytorium GitHub | tak, jeśli korpus w serwisie GitHub | Ciąg | github_repository |
| Gałąź odwołania do jednostki | Gałąź repozytorium GitHub | tak, jeśli korpus w serwisie GitHub | Ciąg |  github_branch |
| Folder korpus | główna lokalizacja korpus | tak | Ciąg | corpusPath |
| Jednostka korpus | Ścieżka do odwołania do jednostki | tak | Ciąg | jednostka |
| Ścieżka partycji | Lokalizacja, w której zostanie zapisywana partycja | nie | Ciąg | partitionPath |
| Wyczyść folder | Jeśli folder docelowy został wyczyszczony przed zapisem | nie | `true` lub `false` | obciąć |
| Typ formatu | Wybierz, aby określić format Parquet | nie | `parquet`Jeśli określony | podformat |
| Ogranicznik kolumny | Jeśli piszesz do DelimitedText, jak ograniczać kolumny | tak, jeśli piszesz do DelimitedText | Ciąg | columnDelimiter |
| Pierwszy wiersz jako nagłówek | W przypadku korzystania z DelimitedText, niezależnie od tego, czy nazwy kolumn są dodawane jako nagłówek | nie | `true` lub `false` | columnNamesAsHeader |

![Źródło CDM](media/format-common-data-model/data-flow-sink.png)

## <a name="next-steps"></a>Następne kroki

Utwórz [transformację źródłową](data-flow-source.md) w mapowaniu przepływu danych.