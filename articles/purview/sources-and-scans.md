---
title: Obsługiwane źródła danych i typy plików
description: Ten artykuł zawiera szczegółowe informacje koncepcyjne o obsługiwanych źródłach danych i typach plików w programie kontrolą.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 3b19fab33d0c8f53025605fd14fe65f08e660392
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677915"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Obsługiwane źródła danych i typy plików w usłudze Azure kontrolą

W tym artykule omówiono obsługiwane źródła danych, typy plików i koncepcje skanowania w programie kontrolą.

## <a name="supported-data-sources"></a>Obsługiwane źródła danych

Usługa Azure kontrolą obsługuje następujące źródła:

| Typ magazynu | Obsługiwany typ uwierzytelniania | Konfigurowanie skanowania za pomocą środowiska użytkownika/programu PowerShell |
| ---------- | ------------------- | ------------------------------ |
| SQL Server lokalne                   | Uwierzytelnianie SQL                        | UX                                |
| Azure Synapse Analytics (dawniej SQL DW)            | Uwierzytelnianie SQL, nazwa główna usługi, plik MSI               | UX                             |
| Azure SQL Database (DB)                  | Uwierzytelnianie SQL, nazwa główna usługi, plik MSI               | UX |
| Wystąpienie zarządzane usługi Azure SQL Database      | Uwierzytelnianie SQL, nazwa główna usługi, plik MSI               | UX    |
| Azure Blob Storage                       | Klucz konta, nazwa główna usługi, plik MSI | UX            |
| Azure Data Explorer                      | Jednostka usługi                              | UX            |
| Azure Data Lake Storage Gen1 (ADLS Gen1) | Nazwa główna usługi, plik MSI                              | UX            |
| Azure Data Lake Storage Gen2 (ADLS Gen2) | Klucz konta, nazwa główna usługi, plik MSI            | UX            |
| Azure Cosmos DB                          | Klucz konta                                    | UX            |


> [!Note]
> Usługa Azure Data Lake Storage Gen2 jest teraz ogólnie dostępna. Zalecamy, aby zacząć z niej korzystać już dziś. Aby uzyskać więcej informacji, zobacz [stronę produktu](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="file-types-supported-for-scanning"></a>Typy plików obsługiwane do skanowania

Następujące typy plików są obsługiwane na potrzeby skanowania w celu wyodrębnienia schematu i klasyfikacji tam, gdzie ma to zastosowanie:

- Strukturalne formaty plików obsługiwane przez rozszerzenie: AVRO, ORC, PARQUET, CSV, JSON, PSV, SSV, TSV, TXT, XML, GZIP
- Formaty plików dokumentów obsługiwane przez rozszerzenie: DOC, DOCM, DOCX, DOT, ODP, ODS, ODT, PDF, POT, PPS, PPSX, PPT, PPTM, PPTX, XLC, XLS, XLSB, XLSM, XLSX, XLT
- Kontrolą obsługuje również niestandardowe rozszerzenia plików i analizatory niestandardowe.
 
> [!Note]
> Każdy plik GZIP musi być zamapowany na pojedynczy plik CSV w ramach programu. Pliki GZIP podlegają systemowi i regułom klasyfikacji niestandardowych. Obecnie nie obsługujemy skanowania pliku gzip zamapowanego do wielu plików w programie lub dowolnego typu pliku innego niż CSV. 

## <a name="sampling-within-a-file"></a>Próbkowanie w pliku

W terminologii kontrolą
- Skanowanie L1: wyodrębnia podstawowe informacje oraz metadane, takie jak nazwa pliku, rozmiar i w pełni kwalifikowana nazwa
- Skanowanie L2: wyodrębnia schemat dla typów plików strukturalnych i tabel bazy danych
- Skanowanie P3: wyodrębnia schemat, gdzie ma to zastosowanie, i omawia plik próbkowany do systemu i niestandardowych reguł klasyfikacji

We wszystkich formatach plików ze strukturą kontrolą pliki z skanerów w następujący sposób:

- W przypadku typów plików ze strukturą jest to wiersz 128 wierszy w każdej kolumnie lub 1 MB, w zależności od tego, która z nich jest niższa.
- W przypadku formatów plików dokumentów pobieranych jest 20 MB każdego pliku.
    - Jeśli plik dokumentu jest większy niż 20 MB, to nie podlega głębokiemu skanowaniu (zgodnie z klasyfikacją). W takim przypadku kontrolą przechwytuje tylko podstawowe metadane, takie jak nazwa pliku i w pełni kwalifikowana nazwa.

## <a name="resource-set-file-sampling"></a>Próbkowanie pliku zestawu zasobów

Folder lub grupa plików partycji jest wykrywana jako *zestaw zasobów* w kontrolą, jeśli jest zgodny z zasadami zestawu zasobów systemowych lub zasadami zestawu zasobów zdefiniowanymi przez klienta. Jeśli zostanie wykryty zestaw zasobów, kontrolą będzie próbkować każdy folder, który zawiera. Dowiedz się więcej o zestawach zasobów w [tym miejscu](concept-resource-sets.md).

Próbkowanie plików dla zestawów zasobów według typów plików:

- **Pliki rozdzielane (CSV, PSV, SSV, TSV)** -1 w 100 plikach są próbkowane (skanowanie P3) w obrębie folderu lub grupy plików partycji, które są uznawane za zestaw zasobów.
- **Typy plików Data Lake (Parquet, Avro, Orc)** -1 w 18446744073709551615 są (Long Max) są próbkowane (skanowanie P3) w obrębie folderu lub grupy plików partycji, które są uznawane za *zestaw zasobów*
- **Inne strukturalne typy plików (JSON, XML, txt)** — 1 w 100 plikach są próbkowane (skanowanie P3) w obrębie folderu lub grupy plików partycji, które są uznawane za zestaw zasobów.
- **Obiekty SQL i jednostki CosmosDB** — każdy plik ma skanowanie P3.
- **Typy plików dokumentów** — każdy plik jest skanowany w P3. Wzorce zestawu zasobów nie mają zastosowania do tych typów plików.

## <a name="scan-regions"></a>Regiony skanowania
Poniżej znajduje się lista wszystkich regionów źródła danych platformy Azure, w których działa skaner kontrolą. Jeśli źródło danych platformy Azure znajduje się w regionie poza tą listą, skaner zostanie uruchomiony w regionie wystąpienia usługi kontrolą.
 
### <a name="purview-scanner-regions"></a>Regiony skanera kontrolą

- EastUs
- EastUs2 
- SouthCentralUS
- WestUs
- WestUs2
- SoutheastAsia
- WestEurope
- NorthEurope
- UkSouth
- AustraliaEast
- CanadaCentral
- BrazilSouth
- CentralIndia
- JapanEast
- SouthAfricaNorth
- FranceCentral

## <a name="classification"></a>Klasyfikacja

Wszystkie reguły klasyfikacji systemu 105 dotyczą formatów plików ze strukturą. Tylko reguły klasyfikacji systemu MCE mają zastosowanie do typów plików dokumentów (a nie natywnych wzorców wyrażenia regularnego skanowania danych, wbudowanego wykrywania filtrów). Aby uzyskać więcej informacji na temat obsługiwanych klasyfikacji, zobacz [obsługiwane klasyfikacje w usłudze Azure kontrolą](supported-classifications.md).

## <a name="next-steps"></a>Następne kroki

- [Samouczek: uruchamianie zestawu startowego i Skanuj dane](tutorial-scan-data.md)
- [Zarządzanie źródłami danych w usłudze Azure kontrolą (wersja zapoznawcza)](manage-data-sources.md)