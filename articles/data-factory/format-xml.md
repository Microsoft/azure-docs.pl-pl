---
title: Format XML w Azure Data Factory
description: W tym temacie opisano sposób postępowania z formatem XML w Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: a4082e1e00ecb1d3b0157fcc35616704eaab0aae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386360"
---
# <a name="xml-format-in-azure-data-factory"></a>Format XML w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Postępuj zgodnie z tym artykułem, jeśli chcesz **przeanalizować pliki XML**. 

Format XML jest obsługiwany dla następujących łączników: [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [system plików](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)i [SFTP](connector-sftp.md). Jest ona obsługiwana jako źródło, ale nie jako ujścia.

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych XML.

| Właściwość         | Opis                                                  | Wymagane |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Właściwość Type zestawu danych musi być ustawiona na wartość **XML**. | Tak      |
| location         | Ustawienia lokalizacji plików. Każdy Łącznik oparty na plikach ma własny typ lokalizacji i obsługiwane właściwości w sekcji `location` . **Zobacz szczegóły w sekcji łącznik — > właściwości zestawu danych**. | Tak      |
| encodingName     | Typ kodowania używany do odczytu/zapisu plików testowych. <br>Dozwolone wartości są następujące: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| Nie       |
| nullValue | Określa reprezentację ciągu wartości null.<br/>Wartość domyślna to **pusty ciąg**. | Nie |
| kompresja | Grupa właściwości do konfigurowania kompresji plików. Skonfiguruj tę sekcję, jeśli chcesz przeprowadzić kompresję/dekompresowanie podczas wykonywania działania. | Nie |
| typ<br>(*w `compression` obszarze*) | Koder-dekoder kompresji używany do odczytu/zapisu plików XML. <br>Dozwolone wartości to **bzip2**, **gzip**, **Wklęśnięcie**, **ZipDeflate**, **TarGzip**, **tar**, **przyciągania** lub **lz4**. Wartość domyślna nie jest skompresowana.<br>Działanie **kopiowania obecnie nie** obsługuje "przyciągania" & "lz4", a przepływ danych mapowania nie obsługuje "ZipDeflate", "TarGzip" i "tar".<br>**Uwaga** w przypadku używania działania kopiowania w celu dekompresowania **ZipDeflate** / **TarGzip** / plików **tar** i zapisu w magazynie danych obiektów ujścia opartych na plikach, domyślnie pliki są wyodrębniane do folderu: `<path specified in dataset>/<folder named as source compressed file>/` , użyj `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [źródła działania Copy](#xml-as-source) , aby określić, czy należy zachować nazwę skompresowanych plików jako strukturę folderów. | Nie.  |
| poziom<br/>(*w `compression` obszarze*) | Współczynnik kompresji. <br>Dozwolone wartości to **optymalne** lub **najszybszy**.<br>- **Najszybsze:** Operacja kompresji powinna zostać ukończona tak szybko, jak to możliwe, nawet jeśli plik nie jest optymalnie kompresowany.<br>- **Optymalnie**: operacja kompresji powinna być optymalnie skompresowana, nawet jeśli operacja trwa dłużej. Aby uzyskać więcej informacji, zobacz temat [poziom kompresji](/dotnet/api/system.io.compression.compressionlevel) . | Nie       |

Poniżej znajduje się przykład zestawu danych XML na platformie Azure Blob Storage:

```json
{
    "name": "XMLDataset",
    "properties": {
        "type": "Xml",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez Źródło XML.

Dowiedz się więcej na temat sposobu mapowania danych XML i magazynu/formatu ujścia danych z [mapowania schematu](copy-activity-schema-and-type-mapping.md). Podczas przeglądania plików XML są wyświetlane dane z hierarchią JSON, a ścieżka JSON wskazuje pola.

### <a name="xml-as-source"></a>KOD XML jako źródło

W sekcji ***\* Źródło \**** działania kopiowania są obsługiwane następujące właściwości. Dowiedz się więcej z poziomu [zachowań łączników XML](#xml-connector-behavior).

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Właściwość Type źródła działania Copy musi mieć wartość **XMLSource**. | Tak      |
| formatSettings | Grupa właściwości. Zapoznaj się z tabelą **ustawień odczytu XML** poniżej. | Nie       |
| storeSettings | Grupa właściwości do odczytywania danych z magazynu danych. Każdy Łącznik oparty na plikach ma własne obsługiwane ustawienia odczytu w obszarze `storeSettings` . **Zobacz szczegóły w artykule łącznik — > właściwości działania kopiowania**. | Nie       |

Obsługiwane **Ustawienia odczytu XML** w obszarze `formatSettings` :

| Właściwość      | Opis                                                  | Wymagane |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ formatSettings musi być ustawiony na **XmlReadSettings**. | Tak      |
| Walidacja | Określa, czy ma być sprawdzana Walidacja schematu XML.<br>Dozwolone wartości to **none** (domyślne, bez sprawdzania poprawności), **XSD** (Validate using XSD), **DTD** (Walidacja przy użyciu DTD). | Nie |
| przestrzeni | Określa, czy należy włączyć przestrzeń nazw podczas analizowania plików XML. Dozwolone wartości to: **true** (wartość domyślna), **Fałsz**. | Nie |
| namespacePrefixes | Identyfikator URI przestrzeni nazw do mapowania prefiksów, który jest używany do nazwy pól podczas analizowania pliku XML.<br/>Jeśli plik XML ma przestrzeń nazw i przestrzeń nazw jest włączona, domyślnie nazwa pola jest taka sama jak w dokumencie XML.<br>Jeśli istnieje element zdefiniowany dla identyfikatora URI przestrzeni nazw w tej mapie, nazwa pola to `prefix:fieldName` . | Nie |
| detectDataType | Określa, czy mają być wykrywane typy danych Integer, Double i Boolean. Dozwolone wartości to: **true** (wartość domyślna), **Fałsz**.| Nie |
| compressionProperties | Grupa właściwości na temat sposobu dekompresowania danych dla danego kodera kompresji. | Nie       |
| preserveZipFileNameAsFolder<br>(*w obszarze `compressionProperties` -> `type` jako `ZipDeflateReadSettings`*)  | Stosuje się, gdy zestaw danych wejściowych jest skonfigurowany z kompresją **ZipDeflate** . Wskazuje, czy podczas kopiowania zachować nazwę źródłowego pliku zip jako strukturę folderów.<br>-Po ustawieniu na **wartość true (domyślnie)** Data Factory zapisuje niespakowane pliki do `<path specified in dataset>/<folder named as source zip file>/` .<br>-Po ustawieniu na **wartość false** Data Factory zapisuje niespakowane pliki bezpośrednio do `<path specified in dataset>` . Upewnij się, że nie masz zduplikowanych nazw plików w różnych źródłowych plikach ZIP, aby uniknąć wyścigów lub nieoczekiwanych zachowań.  | Nie |
| preserveCompressionFileNameAsFolder<br>(*w obszarze `compressionProperties` -> `type` jako `TarGZipReadSettings` lub `TarReadSettings`*) | Stosuje się, gdy zestaw danych wejściowych jest skonfigurowany z / kompresją **pułapki** TarGzip. Wskazuje, czy podczas kopiowania zachować źródłową nazwę pliku skompresowanego jako strukturę folderów.<br>-Po ustawieniu na **wartość true (domyślnie)** Data Factory zapisuje zdekompresować pliki do `<path specified in dataset>/<folder named as source compressed file>/` . <br>-Po ustawieniu na **wartość false** Data Factory zapisuje dekompresowane pliki bezpośrednio do `<path specified in dataset>` . Upewnij się, że nie masz zduplikowanych nazw plików w różnych plikach źródłowych, aby uniknąć wyścigów lub nieoczekiwanych zachowań. | Nie |

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

W mapowaniu przepływów danych można odczytywać i zapisywać dane w formacie XML w następujących magazynach danych: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)i [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). Możesz wskazać pliki XML przy użyciu zestawu danych XML lub z [wbudowanego zestawu danych](data-flow-source.md#inline-datasets).

### <a name="source-properties"></a>Właściwości źródła

Poniższa tabela zawiera listę właściwości obsługiwanych przez Źródło XML. Można edytować te właściwości na karcie **Opcje źródła** . Dowiedz się więcej z poziomu [zachowań łączników XML](#xml-connector-behavior). W przypadku korzystania z wbudowanego zestawu danych zostaną wyświetlone dodatkowe ustawienia plików, które są takie same jak właściwości opisane w sekcji [Właściwości zestawu danych](#dataset-properties) . 

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Ścieżki symboli wieloznacznych | Wszystkie pliki zgodne ze ścieżką wieloznaczną zostaną przetworzone. Zastępuje folder i ścieżkę pliku ustawioną w zestawie danych. | Nie | Ciąg [] | wildcardPaths |
| Ścieżka katalogu głównego partycji | W przypadku danych plików podzielonych na partycje można wprowadzić ścieżkę katalogu głównego partycji, aby odczytywać foldery partycjonowane jako kolumny | Nie | Ciąg | partitionRootPath |
| Lista plików | Czy źródło wskazuje plik tekstowy, który zawiera listę plików do przetworzenia | Nie | `true` lub `false` | fileList |
| Kolumna do przechowywania nazwy pliku | Utwórz nową kolumnę o nazwie i ścieżce pliku źródłowego | Nie | Ciąg | rowUrlColumn |
| Po zakończeniu | Usuń lub Przenieś pliki po przetworzeniu. Ścieżka pliku zaczyna się od katalogu głównego kontenera | Nie | Usuń: `true` lub `false` <br> Przenieś `['<from>', '<to>']` | purgeFiles <br>moveFiles |
| Filtruj według ostatniej modyfikacji | Wybierz filtrowanie plików na podstawie czasu ich ostatniej modyfikacji | Nie | Znacznik czasu | modifiedAfter <br>modifiedBefore |
| Tryb walidacji | Określa, czy ma być sprawdzana Walidacja schematu XML. | Nie | `None` (domyślnie bez sprawdzania poprawności)<br>`xsd` (Weryfikuj przy użyciu XSD)<br>`dtd` (Sprawdź poprawność przy użyciu DTD). | Walidacja |
| Przestrzenie nazw | Określa, czy należy włączyć przestrzeń nazw podczas analizowania plików XML. | Nie | `true` (ustawienie domyślne) lub `false` | przestrzeni |
| Pary prefiksów przestrzeni nazw | Identyfikator URI przestrzeni nazw do mapowania prefiksów, który jest używany do nazwy pól podczas analizowania pliku XML.<br/>Jeśli plik XML ma przestrzeń nazw i przestrzeń nazw jest włączona, domyślnie nazwa pola jest taka sama jak w dokumencie XML.<br>Jeśli istnieje element zdefiniowany dla identyfikatora URI przestrzeni nazw w tej mapie, nazwa pola to `prefix:fieldName` . | Nie | Tablica ze wzorcem`['URI1'->'prefix1','URI2'->'prefix2']` | namespacePrefixes |
| Nie znaleziono plików | W przypadku wartości true błąd nie jest zgłaszany, jeśli nie znaleziono plików | nie | `true` lub `false` | ignoreNoFilesFound |

### <a name="xml-source-script-example"></a>Przykład skryptu źródłowego XML

Poniższy skrypt to przykład konfiguracji źródła XML w mapowaniu przepływów danych przy użyciu trybu zestawu danych.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

Poniższy skrypt to przykład konfiguracji źródła XML przy użyciu trybu wbudowanego zestawu danych.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'xml',
    fileSystem: 'filesystem',
    folderPath: 'folder',
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

## <a name="xml-connector-behavior"></a>Zachowanie łącznika XML

Należy pamiętać o następujących kwestiach w przypadku używania kodu XML jako źródła.

- Atrybuty XML:

    - Atrybuty elementu są analizowane jako podpola elementu w hierarchii.
    - Nazwa pola atrybutu jest zgodna ze wzorcem `@attributeName` .

- Walidacja schematu XML:

    - Możesz zrezygnować z walidacji schematu lub sprawdzić poprawność schematu przy użyciu XSD lub DTD.
    - W przypadku użycia XSD lub DTD do walidacji plików XML, XSD/DTD musi być określony wewnątrz plików XML za pośrednictwem ścieżki względnej.

- Obsługa przestrzeni nazw:

    - Przestrzeń nazw można wyłączyć w przypadku korzystania z przepływu danych. w takim przypadku atrybuty definiujące przestrzeń nazw będą analizowane jako atrybuty normalne.
    - Gdy przestrzeń nazw jest włączona, nazwy elementów i atrybutów są zgodne ze wzorcem       `namespaceUri,elementName` i `namespaceUri,@attributeName` domyślnie. Można zdefiniować prefiks przestrzeni nazw dla każdego identyfikatora URI przestrzeni nazw w źródle. w takim przypadku nazwy elementu i atrybutów są zgodne ze wzorcem `definedPrefix:elementName` lub `definedPrefix:@attributeName` .

- Kolumna wartości:

    - Jeśli element XML ma zarówno prostą wartość tekstową, jak i atrybuty/elementy podrzędne, prosta wartość tekstowa jest analizowana jako wartość kolumny "value" z wbudowaną nazwą pola `_value_` . I dziedziczy przestrzeń nazw elementu, jeśli ma zastosowanie.

## <a name="next-steps"></a>Następne kroki

- [Przegląd działania kopiowania](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)