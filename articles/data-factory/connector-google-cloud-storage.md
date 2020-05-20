---
title: Kopiowanie danych z magazynu Google Cloud Storage przy użyciu Azure Data Factory
description: Informacje o sposobie kopiowania danych z magazynu Google Cloud Storage do obsługiwanych magazynów danych ujścia przy użyciu Azure Data Factory.
services: data-factory
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: jingwang
ms.openlocfilehash: fb33e05eae52ebc0274296b5c18783233e3e8969
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657108"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Kopiowanie danych z magazynu Google Cloud Storage przy użyciu Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób kopiowania danych z magazynu Google Cloud Storage. Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik usługi Google Cloud Storage jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuń działanie](delete-activity.md)

Ten łącznik usługi Google Cloud Storage obsługuje kopiowanie plików jako plik lub analizowanie plików z [obsługiwanymi formatami plików i kodekami kompresji](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Kopiowanie danych z usługi Google Cloud Storage korzysta z [łącznika Amazon S3](connector-amazon-simple-storage-service.md) z odpowiednim niestandardowym punktem końcowym S3, ponieważ usługa Google Cloud Storage zapewnia współdziałanie zgodne z S3.

## <a name="prerequisites"></a>Wymagania wstępne

Na Twoim koncie usługi Google Cloud Storage wymagane jest następujące ustawienie:

1. Włącz współdziałanie dla konta usługi Google Cloud Storage
2. Ustaw projekt domyślny zawierający dane, które chcesz skopiować
3. Utwórz klucz dostępu.

![Pobieranie klucza dostępu do magazynu Google Cloud Storage](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby skopiować dane z magazynu Google Cloud Storage, upewnij się, że masz przyznane następujące uprawnienia:

- **W przypadku wykonywania działania kopiowania:**: `s3:GetObject` i `s3:GetObjectVersion` dla operacji obiektu.
- **W przypadku Data Factory tworzenia graficznego interfejsu użytkownika**: `s3:ListAllMyBuckets` i `s3:ListBucket` / `s3:GetBucketLocation` dla operacji zasobnika są również wymagane w przypadku operacji takich jak połączenie testowe i przeglądanie/nawigowanie w ścieżkach plików. Jeśli nie chcesz udzielić tych uprawnień, Pomiń połączenie testowe na stronie tworzenia połączonej usługi i określ ścieżkę bezpośrednio w ustawieniach zestawu danych.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla usługi Google Cloud Storage.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla połączonej usługi Google Cloud Storage:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi być ustawiona na wartość **GoogleCloudStorage**. | Yes |
| accessKeyId | Identyfikator klucza dostępu tajnego. Aby znaleźć klucz dostępu i wpis tajny, zobacz [wymagania wstępne](#prerequisites). |Yes |
| secretAccessKey | Sam klucz dostępu tajnego. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| serviceUrl | Określ niestandardowy punkt końcowy S3 jako **`https://storage.googleapis.com`** . | Yes |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

Oto przykład:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w usłudze Google Cloud Storage w obszarze `location` Ustawienia w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagane |
| ---------- | ------------------------------------------------------------ | -------- |
| typ       | Właściwość Type w `location` elemencie DataSet musi być ustawiona na wartość **AmazonS3Location**. | Yes      |
| zasobnikname | Nazwa zasobnika S3.                                          | Yes      |
| folderPath | Ścieżka do folderu w danym przedziale. Jeśli chcesz użyć symboli wieloznacznych do filtrowania folderów, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |
| fileName   | Nazwa pliku w podanym zasobniku + folderPath. Jeśli chcesz użyć symboli wieloznacznych do filtrowania plików, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez Źródło magazynu Google Cloud.

### <a name="google-cloud-storage-as-source"></a>Magazyn w chmurze Google jako źródło

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w usłudze Google Cloud Storage w obszarze `storeSettings` Ustawienia w źródle kopiowania opartego na formacie:

| Właściwość                 | Opis                                                  | Wymagane                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| typ                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **AmazonS3ReadSettings**. | Yes                                                         |
| ***Zlokalizuj pliki do skopiowania:*** |  |  |
| Opcja 1: ścieżka statyczna<br> | Kopiuj z podanego zasobnika lub folderu/ścieżki pliku określonego w zestawie danych. Jeśli chcesz skopiować wszystkie pliki z przedziału/folderu, należy również określić `wildcardFileName` jako `*` . |  |
| Opcja 2: symbol wieloznaczny<br>- wildcardFolderPath | Ścieżka folderu z symbolami wieloznacznymi w ramach danego zasobnika skonfigurowanych w zestawie danych do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Nie                                            |
| Opcja 2: symbol wieloznaczny<br>- wildcardFileName | Nazwa pliku zawierająca symbole wieloznaczne w podanym zasobniku + folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak); Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Yes |
| Opcja 3: Lista plików<br>- fileListPath | Wskazuje, aby skopiować dany zestaw plików. Wskaż plik tekstowy, który zawiera listę plików, które chcesz skopiować, jeden plik na wiersz, który jest ścieżką względną do ścieżki skonfigurowanej w zestawie danych.<br/>W przypadku korzystania z tej opcji nie należy określać nazwy pliku w zestawie danych. Zobacz więcej przykładów na [listach plików](#file-list-examples). |Nie |
| ***Ustawienia dodatkowe:*** |  | |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy pamiętać, że gdy wartość cykliczna jest ustawiona na wartość true, a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia. <br>Dozwolone wartości to **true** (wartość domyślna) i **false**.<br>Ta właściwość nie ma zastosowania podczas konfigurowania `fileListPath` . |Nie |
| modifiedDatetimeStart    | Filtr plików oparty na atrybucie: ostatnio modyfikowane. <br>Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime `modifiedDatetimeEnd` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime `modifiedDatetimeStart` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.<br/>Ta właściwość nie ma zastosowania podczas konfigurowania `fileListPath` . | Nie                                            |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                                          |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie                                                          |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
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
                    "type": "AmazonS3ReadSettings",
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

### <a name="folder-and-file-filter-examples"></a>Przykłady filtrów folderów i plików

W tej sekcji opisano skutki zachowania ścieżki folderu i nazwy pliku z filtrami symboli wieloznacznych.

| porcj | key | rozpoznawania | Źródłowa Struktura folderu i wynik filtru (pliki pogrubione są pobierane)|
|:--- |:--- |:--- |:--- |
| porcj | `Folder*/*` | fałsz | porcj<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| porcj | `Folder*/*` | true | porcj<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| porcj | `Folder*/*.csv` | fałsz | porcj<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| porcj | `Folder*/*.csv` | true | porcj<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="file-list-examples"></a>Przykłady listy plików

W tej sekcji opisano wyniki działania dotyczące korzystania ze ścieżki listy plików w źródle działania kopiowania.

Przy założeniu, że masz następującą źródłową strukturę folderów i chcesz skopiować pliki w pogrubieniu:

| Przykładowa struktura źródła                                      | Zawartość w FileListToCopy. txt                             | Konfiguracja ADF                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| porcj<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadane<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy. txt | Plik1. csv<br>Subfolder1/file3. csv<br>Subfolder1/File5. csv | **W zestawie danych:**<br>Porcj`bucket`<br>-Ścieżka folderu:`FolderA`<br><br>**W źródle działania kopiowania:**<br>-Ścieżka listy plików:`bucket/Metadata/FileListToCopy.txt` <br><br>Ścieżka listy plików wskazuje plik tekstowy w tym samym magazynie danych, który zawiera listę plików do skopiowania, jeden plik na wiersz ze ścieżką względną do ścieżki skonfigurowanej w zestawie danych. |

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby uzyskać szczegółowe informacje na temat właściwości, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Usuń właściwości działania

Aby uzyskać szczegółowe informacje na temat właściwości, zaznacz pozycję [Usuń działanie](delete-activity.md) .

## <a name="legacy-models"></a>Starsze modele

>[!NOTE]
>Następujące modele są nadal obsługiwane w celu zapewnienia zgodności z poprzednimi wersjami. Użytkownik chce użyć nowego modelu wymienionego w powyższych sekcjach, przechodząc do przodu, a interfejs użytkownika tworzenia ADF został przełączony w celu wygenerowania nowego modelu.

### <a name="legacy-dataset-model"></a>Model starszego zestawu danych

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość: **AmazonS3Object** |Yes |
| zasobnikname | Nazwa zasobnika S3. Filtr symboli wieloznacznych nie jest obsługiwany. |Tak dla działania kopiowania/wyszukiwania, nie dla działania GetMetadata |
| key | **Nazwa lub filtr symboli wieloznacznych** klucza obiektu S3 w określonym przedziale. Stosuje się tylko wtedy, gdy właściwość "prefix" nie została określona. <br/><br/>Filtr symboli wieloznacznych jest obsługiwany dla części folderu i nazwy pliku. Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak).<br/>-Przykład 1:`"key": "rootfolder/subfolder/*.csv"`<br/>-Przykład 2:`"key": "rootfolder/subfolder/???20180427.txt"`<br/>Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). Użyj `^` , aby wyjść, jeśli rzeczywista nazwa folderu/pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. |Nie |
| prefiks | Prefiks klucza obiektu S3. Zaznaczone obiekty, których klucze zaczynają się od tego prefiksu. Stosuje się tylko wtedy, gdy właściwość "Key" nie została określona. |Nie |
| version | Wersja obiektu S3, jeśli włączono obsługę wersji S3. |Nie |
| modifiedDatetimeStart | Filtr plików oparty na atrybucie: ostatnio modyfikowane. Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime `modifiedDatetimeEnd` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime `modifiedDatetimeStart` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.| Nie |
| modifiedDatetimeEnd | Filtr plików oparty na atrybucie: ostatnio modyfikowane. Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime `modifiedDatetimeEnd` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime `modifiedDatetimeStart` , ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.| Nie |
| format | Jeśli chcesz **skopiować pliki** między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować lub generować pliki o określonym formacie, obsługiwane są następujące typy formatu plików: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **Type** w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstowy](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Format JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Format Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)i sekcje [formatu Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nie (tylko w przypadku scenariusza kopiowania binarnego) |
| kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekoder kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Obsługiwane typy to: **gzip**, **Wklęśnięcie**, **BZip2**i **ZipDeflate**.<br/>Obsługiwane poziomy to: **optymalne** i **najszybszy**. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, należy określić **zasobnik** dla zasobnika i **prefiks** dla części folderu.<br>Aby skopiować pojedynczy plik o podaną nazwę, określ wartość **dla zasobnika** i **klucza** dla części folderu oraz nazwę pliku.<br>Aby skopiować podzestaw plików w folderze, określ wartość **zasobnika** dla zasobnika i **klucza** dla części folderu oraz filtr symboli wieloznacznych.

**Przykład: używanie prefiksu**

```json
{
    "name": "GoogleCloudStorageDataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
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

### <a name="legacy-copy-activity-source-model"></a>Model źródłowy starszego działania kopiowania

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **FileSystemSource** |Yes |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Uwaga gdy wartość cykliczna jest ustawiona na wartość true, a obiekt sink jest magazynem opartym na plikach, pusty folder/podfolder nie zostanie skopiowany/utworzony w ujścia.<br/>Dozwolone wartości to: **true** (wartość domyślna), **Fałsz** | Nie |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
