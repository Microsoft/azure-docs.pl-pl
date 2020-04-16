---
title: Kopiowanie danych z/do systemu plików przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak kopiować dane z systemu plików do obsługiwanych magazynów danych ujścia (lub) z obsługiwanych magazynów danych źródłowych do systemu plików przy użyciu usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 21329e3fada62036bc5b8d2bf6cdf6b3be5adad3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417406"
---
# <a name="copy-data-to-or-from-a-file-system-by-using-azure-data-factory"></a>Kopiowanie danych do lub z systemu plików przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-onprem-file-system-connector.md)
> * [Aktualna wersja](connector-file-system.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób kopiowania danych do i z systemu plików. Aby dowiedzieć się więcej o usłudze Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik systemu plików jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuwanie działania](delete-activity.md)

W szczególności ten łącznik systemu plików obsługuje:

- Kopiowanie plików z/do lokalnego udziału plików komputera lub sieci. Aby użyć udziału plików Linuksa, zainstaluj [sambę](https://www.samba.org/) na serwerze Linux.
- Kopiowanie plików przy użyciu uwierzytelniania **systemu Windows.**
- Kopiowanie plików w stanie jednakowym lub analizowanie/generowanie plików za pomocą [obsługiwanych formatów plików i kodeków kompresji](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla systemu plików.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej systemu plików:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **FileServer**. | Tak |
| host | Określa ścieżkę główną folderu, który chcesz skopiować. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Zobacz [przykłady połączonych definicji usługi i zestawów danych,](#sample-linked-service-and-dataset-definitions) aby zapoznać się z przykładami. | Tak |
| userid | Określ identyfikator użytkownika, który ma dostęp do serwera. | Tak |
| hasło | Określ hasło użytkownika (identyfikator użytkownika). Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

### <a name="sample-linked-service-and-dataset-definitions"></a>Przykładowe połączone definicje usług i zestawów danych

| Scenariusz | "host" w definicji usługi połączonej | "folderPath" w definicji zestawu danych |
|:--- |:--- |:--- |
| Folder lokalny na komputerze runtime integracji: <br/><br/>Przykłady: D:\\ \* lub D:\folder\podfolder\\* |W JSON:`D:\\`<br/>W ui:`D:\` |W JSON: `.\\` lub`folder\\subfolder`<br>W ui `.\` użytkownika: lub`folder\subfolder` |
| Zdalny folder udostępniony: <br/><br/>Przykłady: \\ \\podfolder\\ \* \\ \\folderu\\udostępniania\\\\myserver\\lub folderu udostępniania myserver\\* |W JSON:`\\\\myserver\\share`<br/>W ui:`\\myserver\share` |W JSON: `.\\` lub`folder\\subfolder`<br/>W ui `.\` użytkownika: lub`folder\subfolder` |

>[!NOTE]
>Podczas tworzenia za pośrednictwem interfejsu użytkownika, nie trzeba wprowadzać`\\`podwójne ukośnik odwrotny ( ), aby uciec jak to zrobić za pośrednictwem JSON, określ pojedynczy ukośnik odwrotny.

**Przykład:**

```json
{
    "name": "FileLinkedService",
    "properties": {
        "type": "FileServer",
        "typeProperties": {
            "host": "<host>",
            "userid": "<domain>\\<user>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla systemu `location` plików w ustawieniach w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagany |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Właściwość typu `location` w obszarze w zestawie danych musi być ustawiona na **FileServerLocation**. | Tak      |
| folderPath | Ścieżka do folderu. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania folderu, pomiń to ustawienie i określ w ustawieniach źródła aktywności. | Nie       |
| fileName   | Nazwa pliku pod danym folderPath. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania plików, pomiń to ustawienie i określ w ustawieniach źródła aktywności. | Nie       |

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<File system linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FileServerLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło systemu plików i ujście.

### <a name="file-system-as-source"></a>System plików jako źródło

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla systemu `storeSettings` plików w ustawieniach w źródle kopiowania opartego na formacie:

| Właściwość                 | Opis                                                  | Wymagany                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Właściwość typu `storeSettings` w obszarze musi być ustawiona na **FileServerReadSettings**. | Tak                                           |
| Cykliczne                | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy zauważyć, że gdy cykliczne jest ustawiona na true i ujście jest magazyn oparty na plikach, pusty folder lub podfolder nie jest kopiowany lub tworzony w zlewie. Dozwolone wartości są **prawdziwe** (domyślnie) i **false**. | Nie                                            |
| Ścieżka wieloznacznaFolderPath       | Ścieżka folderu ze znakami wieloznaczowymi do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku); użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach filtru folderów i plików](#folder-and-file-filter-examples). | Nie                                            |
| symboli wieloznacznychFileName         | Nazwa pliku ze znakami wieloznacznymi pod danym folderpath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku); użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [przykładach filtru folderów i plików](#folder-and-file-filter-examples). [Należy pamiętać, że spowoduje to unieważnienie ustawienia fileName] | Tak, `fileName` jeśli nie jest określony w zestawie danych |
| modifiedDatetimeStart    | Filtr plików na podstawie atrybutu: Ostatnia modyfikacja. Pliki zostaną wybrane, jeśli ich ostatni zmodyfikowany czas `modifiedDatetimeStart` `modifiedDatetimeEnd`mieści się w przedziale czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br> Właściwości mogą mieć wartość NULL, co oznacza, że do zestawu danych nie zostanie zastosowany żaden filtr atrybutów pliku.  Gdy `modifiedDatetimeStart` ma wartość `modifiedDatetimeEnd` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa z wartości datetime zostaną wybrane.  Gdy `modifiedDatetimeEnd` ma wartość `modifiedDatetimeStart` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime zostaną wybrane. | Nie                                            |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                            |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem magazynowym jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie                                            |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "FileServerReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="file-system-as-sink"></a>System plików jako zlew

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla systemu `storeSettings` plików w ustawieniach w umywalka kopii oparta na formacie:

| Właściwość                 | Opis                                                  | Wymagany |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Właściwość typu `storeSettings` w obszarze musi być ustawiona na **FileServerWriteSettings**. | Tak      |
| copyBehavior             | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartych na plikach.<br/><br/>Dozwolone wartości to:<br/><b>- PreserveHierarchy (domyślnie):</b>Zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest identyczna ze względną ścieżką pliku docelowego do folderu docelowego.<br/><b>- FlattenHierarchy:</b>Wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie generowane nazwy. <br/><b>- MergeFiles:</b>Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określona, scalona nazwa pliku jest określoną nazwą. W przeciwnym razie jest to nazwa pliku z automatycznym generacją. | Nie       |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem danych jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie       |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "FileServerWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Przykłady filtrów folderów i plików

W tej sekcji opisano wynikowe zachowanie ścieżki folderu i nazwy pliku za pomocą filtrów wieloznacznych.

| folderPath | fileName | Cykliczne | Struktura folderów źródłowych i wynik filtru (pliki **pogrubione** są pobierane)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (pusty, użyj domyślnie) | false | FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5.csv<br/>InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| `Folder*` | (pusty, użyj domyślnie) | true | FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik5.csv**<br/>InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| `Folder*` | `*.csv` | false | FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5.csv<br/>InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| `Folder*` | `*.csv` | true | FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik5.csv**<br/>InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |

### <a name="recursive-and-copybehavior-examples"></a>przykłady rekursywne i copyBehavior

W tej sekcji opisano wynikowe zachowanie operacji Kopiowanie dla różnych kombinacji wartości cyklicznych i copyBehavior.

| Cykliczne | copyBehavior | Struktura folderów źródłowych | Wynikujący cel |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy Folder1 jest tworzony z taką samą strukturą jak źródło:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5. |
| true |spłaszczyćHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku5 |
| true |scalanie plików | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 zawartość są scalane w jeden plik z automatycznie wygenerowaną nazwą pliku |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/>Podfolder1 z plikami File3, File4 i File5 nie są pobierane. |
| false |spłaszczyćHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku2<br/><br/>Podfolder1 z plikami File3, File4 i File5 nie są pobierane. |
| false |scalanie plików | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Zawartość file1 + File2 jest scalana w jeden plik o automatycznie wygenerowanej nazwie pliku. automatycznie wygenerowana nazwa pliku1<br/><br/>Podfolder1 z plikami File3, File4 i File5 nie są pobierane. |

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Usuwanie właściwości działania

Aby dowiedzieć się więcej o właściwościach, sprawdź [Usuń działanie](delete-activity.md)

## <a name="legacy-models"></a>Starsze modele

>[!NOTE]
>Następujące modele są nadal obsługiwane jako — dla zgodności z powrotem. Zaleca się użycie nowego modelu wymienionego w powyższych sekcjach w przyszłości, a interfejs użytkownika tworzenia podajnikiem ADF przełączył się na generowanie nowego modelu.

### <a name="legacy-dataset-model"></a>Starszy model zestawu danych

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **FileShare** |Tak |
| folderPath | Ścieżka do folderu. Filtr symboli wieloznacznych jest obsługiwany, `*` dozwolone symbole wieloznaczne `?` to: (dopasowuje zero lub więcej znaków) i (pasuje do zera lub pojedynczego znaku); użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br/><br/>Przykłady: rootfolder/subfolder/, zobacz więcej przykładów w [przykładowych definicjach połączonych usług i zestawów danych](#sample-linked-service-and-dataset-definitions) oraz [przykładach filtrów folderów i plików](#folder-and-file-filter-examples). |Nie |
| fileName | **Nazwa lub symbol wieloznaczny filtr** dla plików w ramach określonego "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie pliki w folderze. <br/><br/>W przypadku filtru dozwolone symbole wieloznaczne `*` to: `?` (dopasowuje zero lub więcej znaków) i (pasuje do zera lub pojedynczego znaku).<br/>- Przykład 1:`"fileName": "*.csv"`<br/>- Przykład 2:`"fileName": "???20180427.txt"`<br/>Użyj, `^` aby uniknąć, jeśli rzeczywista nazwa pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.<br/><br/>Jeśli nazwa pliku nie jest określona dla wyjściowego zestawu danych i **preserveHierarchy** nie jest określony w ujściu działania, działanie kopiowania automatycznie generuje nazwę pliku z następującym wzorcem: "*Dane.[ identyfikator GUID uruchomienia działania]. [GUID jeśli FlattenHierarchy]. [format, jeśli jest skonfigurowany]. [kompresja, jeśli jest skonfigurowana]*", np. Jeśli kopiujesz ze źródła tabelaryczne przy użyciu nazwy tabeli zamiast kwerendy, wzorzec nazwy to "*[nazwa tabeli].[ w formacie]. [kompresja, jeśli jest skonfigurowana]*", np. |Nie |
| modifiedDatetimeStart | Filtr plików na podstawie atrybutu: Ostatnia modyfikacja. Pliki zostaną wybrane, jeśli ich ostatni zmodyfikowany czas `modifiedDatetimeStart` `modifiedDatetimeEnd`mieści się w przedziale czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że ogólna wydajność przenoszenia danych będzie mieć wpływ, włączając to ustawienie, gdy chcesz wykonać filtr plików z ogromnych ilości plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że do zestawu danych nie zostanie zastosowany żaden filtr atrybutów pliku.  Gdy `modifiedDatetimeStart` ma wartość `modifiedDatetimeEnd` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa z wartości datetime zostaną wybrane.  Gdy `modifiedDatetimeEnd` ma wartość `modifiedDatetimeStart` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime zostaną wybrane.| Nie |
| modifiedDatetimeEnd | Filtr plików na podstawie atrybutu: Ostatnia modyfikacja. Pliki zostaną wybrane, jeśli ich ostatni zmodyfikowany czas `modifiedDatetimeStart` `modifiedDatetimeEnd`mieści się w przedziale czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że ogólna wydajność przenoszenia danych będzie mieć wpływ, włączając to ustawienie, gdy chcesz wykonać filtr plików z ogromnych ilości plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że do zestawu danych nie zostanie zastosowany żaden filtr atrybutów pliku.  Gdy `modifiedDatetimeStart` ma wartość `modifiedDatetimeEnd` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa z wartości datetime zostaną wybrane.  Gdy `modifiedDatetimeEnd` ma wartość `modifiedDatetimeStart` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime zostaną wybrane.| Nie |
| format | Jeśli chcesz **skopiować pliki jako znajdujące się** między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz przeanalizować lub wygenerować pliki w określonym formacie, obsługiwane są następujące typy formatów plików: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **typu** w formacie na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [Sekcje Format tekstu,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [Format Json,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [Format Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [Format orków](supported-file-formats-and-compression-codecs-legacy.md#orc-format)i [Format parkietu.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Nie (tylko w przypadku scenariusza kopiowania binarnego) |
| kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [Obsługiwane formaty plików i kodeki kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**.<br/>Obsługiwane poziomy to: **Optymalne** i **najszybsze**. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ tylko **folderPath.**<br>Aby skopiować pojedynczy plik o podanej nazwie, określ **folderPath** z częścią **folderu** i nazwą pliku z nazwą pliku.<br>Aby skopiować podzbiór plików w folderze, określ **folderPath** z częścią folderu i **fileName** z filtrem wieloznacznym.

>[!NOTE]
>Jeśli używasz "fileFilter" właściwość dla filtru plików, nadal jest obsługiwany w stanie, podczas gdy zaleca się użycie nowej funkcji filtru dodane do "fileName" w przyszłości.

**Przykład:**

```json
{
    "name": "FileSystemDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<file system linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Starszy model źródła działania kopiowania

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **FileSystemSource** |Tak |
| Cykliczne | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. Uwaga, gdy rekursywny jest ustawiony na true i sink jest magazyn oparty na plikach, pusty folder /podfolder nie zostaną skopiowane / utworzone w zlewie.<br/>Dozwolone wartości to: **true** (default), **false** | Nie |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem magazynowym jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<file system input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Starszy model ujścia działania kopiowania

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu ujścia działania kopiowania musi być ustawiona na: **FileSystemSink** |Tak |
| copyBehavior | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartych na plikach.<br/><br/>Dozwolone wartości to:<br/><b>- PreserveHierarchy (domyślnie)</b>: zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest identyczna ze względną ścieżką pliku docelowego do folderu docelowego.<br/><b>- FlattenHierarchy:</b>wszystkie pliki z folderu źródłowego są na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie wygenerowaną nazwę. <br/><b>- MergeFiles:</b>scala wszystkie pliki z folderu źródłowego do jednego pliku. Podczas scalania nie jest wykonywana deduplikacja rekordu. Jeśli nazwa pliku jest określona, scalona nazwa pliku będzie określona nazwa; w przeciwnym razie zostanie wygenerowana automatycznie nazwa pliku. | Nie |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem magazynowym jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<file system output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "FileSystemSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
