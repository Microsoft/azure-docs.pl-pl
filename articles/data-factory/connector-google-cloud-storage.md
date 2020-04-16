---
title: Kopiowanie danych z usługi Google Cloud Storage przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak kopiować dane z Google Cloud Storage do obsługiwanych magazynów danych ujścia przy użyciu usługi Azure Data Factory.
services: data-factory
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 2ee0fc7f7b7e3ef465a43eed2bd47f33e87162b7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417299"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Kopiowanie danych z usługi Google Cloud Storage przy użyciu usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak kopiować dane z Google Cloud Storage. Aby dowiedzieć się więcej o usłudze Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Google Cloud Storage jest obsługiwany w następujących działaniach:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuwanie działania](delete-activity.md)

W szczególności ten łącznik Google Cloud Storage obsługuje kopiowanie plików w stanie takim, w jakim są, lub analizowanie plików za pomocą [obsługiwanych formatów plików i kodeków kompresji.](supported-file-formats-and-compression-codecs.md)

>[!NOTE]
>Kopiowanie danych z Google Cloud Storage wykorzystuje [złącze Amazon S3](connector-amazon-simple-storage-service.md) z odpowiednim niestandardowym punktem końcowym S3, ponieważ Google Cloud Storage zapewnia współdziałanie zgodne z S3.

## <a name="prerequisites"></a>Wymagania wstępne

Na koncie Google Cloud Storage wymagana jest następująca konfiguracja:

1. Włączanie współdziałania konta Google Cloud Storage
2. Ustawianie projektu domyślnego zawierającego dane, które chcesz skopiować
3. Utwórz klucz dostępu.

![Pobieranie klucza dostępu do przestrzeni dyskowej Google Cloud Storage](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby skopiować dane z Google Cloud Storage, upewnij się, że otrzymałeś następujące uprawnienia:

- **Do wykonywania działania kopiowania:**: `s3:GetObject` i `s3:GetObjectVersion` dla operacji obiektowych.
- **W przypadku tworzenia interfejsu GUI fabryki danych**i `s3:ListAllMyBuckets` `s3:ListBucket` / `s3:GetBucketLocation` dla operacji zasobników uprawnienia są dodatkowo wymagane dla operacji, takich jak połączenie testowe i przeglądanie/nawigacja ścieżek plików. Jeśli nie chcesz udzielić tych uprawnień, pomiń połączenie testowe na stronie tworzenia połączonej usługi i określ ścieżkę bezpośrednio w ustawieniach zestawu danych.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Poniższe sekcje zawierają szczegółowe informacje o właściwościach używanych do definiowania jednostek fabryki danych specyficznych dla Google Cloud Storage.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

W usłudze połączonej z Google Cloud Storage obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **GoogleCloudStorage**. | Tak |
| identyfikator klucza accessKeyId | Identyfikator tajnego klucza dostępu. Aby znaleźć klucz dostępu i klucz tajny, zobacz [Wymagania wstępne](#prerequisites). |Tak |
| klucz tajnyAccess | Sam klucz dostępu tajnego. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) |Tak |
| usługaUrl | Określ niestandardowy punkt **`https://storage.googleapis.com`** końcowy S3 jako . | Tak |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie (jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

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

Następujące właściwości są obsługiwane w Google `location` Cloud Storage w ustawieniach w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagany |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Właściwość typu `location` w obszarze w zestawie danych musi być ustawiona na **AmazonS3Location**. | Tak      |
| nazwa łyżki | Nazwa zasobnika S3.                                          | Tak      |
| folderPath | Ścieżka do folderu pod danym zasobnikiem. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania folderu, pomiń to ustawienie i określ w ustawieniach źródła aktywności. | Nie       |
| fileName   | Nazwa pliku pod danym zasobnikiem + folderPath. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania plików, pomiń to ustawienie i określ w ustawieniach źródła aktywności. | Nie       |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Google Cloud Storage.

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage jako źródło

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w Google `storeSettings` Cloud Storage w ustawieniach w źródle kopii opartych na formacie:

| Właściwość                 | Opis                                                  | Wymagany                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | Właściwość typu `storeSettings` w obszarze musi być ustawiona na **AmazonS3ReadSettings**. | Tak                                                         |
| Cykliczne                | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy zauważyć, że gdy cykliczne jest ustawiona na true i ujście jest magazyn oparty na plikach, pusty folder lub podfolder nie jest kopiowany lub tworzony w zlewie. Dozwolone wartości są **prawdziwe** (domyślnie) i **false**. | Nie                                                          |
| Prefiks                   | Prefiks klucza obiektu S3 w danym zasobniku skonfigurowanym w zestawie danych do filtrowania obiektów źródłowych. Obiekty, których klucze zaczynają się od tego prefiksu są zaznaczone. Ma zastosowanie `wildcardFolderPath` tylko `wildcardFileName` wtedy, gdy i właściwości nie są określone. |                                                             |
| Ścieżka wieloznacznaFolderPath       | Ścieżka folderu ze znakami wieloznaczymi pod danym zasobnikiem skonfigurowanym w zestawie danych do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku); użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach filtru folderów i plików](#folder-and-file-filter-examples). | Nie                                                          |
| symboli wieloznacznychFileName         | Nazwa pliku ze znakami wieloznacznymi pod danym zasobnikiem + folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku); użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [przykładach filtru folderów i plików](#folder-and-file-filter-examples). | Tak, `fileName` jeśli w `prefix` zestawie danych i nie są określone |
| modifiedDatetimeStart    | Filtr plików na podstawie atrybutu: Ostatnia modyfikacja. Pliki zostaną wybrane, jeśli ich ostatni zmodyfikowany czas `modifiedDatetimeStart` `modifiedDatetimeEnd`mieści się w przedziale czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br> Właściwości mogą mieć wartość NULL, co oznacza, że do zestawu danych nie zostanie zastosowany żaden filtr atrybutów pliku.  Gdy `modifiedDatetimeStart` ma wartość `modifiedDatetimeEnd` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa z wartości datetime zostaną wybrane.  Gdy `modifiedDatetimeEnd` ma wartość `modifiedDatetimeStart` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime zostaną wybrane. | Nie                                                          |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                                          |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem magazynowym jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie                                                          |

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

W tej sekcji opisano wynikowe zachowanie ścieżki folderu i nazwy pliku za pomocą filtrów wieloznacznych.

| Wiadro | key | Cykliczne | Struktura folderów źródłowych i wynik filtru (pliki pogrubione są pobierane)|
|:--- |:--- |:--- |:--- |
| Wiadro | `Folder*/*` | false | Wiadro<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| Wiadro | `Folder*/*` | true | Wiadro<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| Wiadro | `Folder*/*.csv` | false | Wiadro<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| Wiadro | `Folder*/*.csv` | true | Wiadro<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |

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
| type | Właściwość typu zestawu danych musi być ustawiona na: **AmazonS3Object** |Tak |
| nazwa łyżki | Nazwa zasobnika S3. Filtr symboli wieloznacznych nie jest obsługiwany. |Tak dla działania Kopiowania/odnośnika, nie dla działania GetMetadata |
| key | Nazwa **lub symbol wieloznaczny klucza** obiektu S3 w określonym zasobniku. Ma zastosowanie tylko wtedy, gdy właściwość "prefiks" nie jest określona. <br/><br/>Filtr symboli wieloznacznych jest obsługiwany zarówno dla części folderu, jak i części nazwy pliku. Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku).<br/>- Przykład 1:`"key": "rootfolder/subfolder/*.csv"`<br/>- Przykład 2:`"key": "rootfolder/subfolder/???20180427.txt"`<br/>Zobacz więcej przykładów w [przykładach filtru folderów i plików](#folder-and-file-filter-examples). Użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu/pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. |Nie |
| Prefiks | Prefiks klucza obiektu S3. Obiekty, których klucze zaczynają się od tego prefiksu są zaznaczone. Ma zastosowanie tylko wtedy, gdy właściwość "key" nie jest określona. |Nie |
| version | Wersja obiektu S3, jeśli jest włączona wersja S3. |Nie |
| modifiedDatetimeStart | Filtr plików na podstawie atrybutu: Ostatnia modyfikacja. Pliki zostaną wybrane, jeśli ich ostatni zmodyfikowany czas `modifiedDatetimeStart` `modifiedDatetimeEnd`mieści się w przedziale czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że do zestawu danych nie zostanie zastosowany żaden filtr atrybutów pliku.  Gdy `modifiedDatetimeStart` ma wartość `modifiedDatetimeEnd` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa z wartości datetime zostaną wybrane.  Gdy `modifiedDatetimeEnd` ma wartość `modifiedDatetimeStart` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime zostaną wybrane.| Nie |
| modifiedDatetimeEnd | Filtr plików na podstawie atrybutu: Ostatnia modyfikacja. Pliki zostaną wybrane, jeśli ich ostatni zmodyfikowany czas `modifiedDatetimeStart` `modifiedDatetimeEnd`mieści się w przedziale czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że do zestawu danych nie zostanie zastosowany żaden filtr atrybutów pliku.  Gdy `modifiedDatetimeStart` ma wartość `modifiedDatetimeEnd` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa z wartości datetime zostaną wybrane.  Gdy `modifiedDatetimeEnd` ma wartość `modifiedDatetimeStart` datetime, ale jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime zostaną wybrane.| Nie |
| format | Jeśli chcesz **skopiować pliki jako znajdujące się** między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz przeanalizować lub wygenerować pliki w określonym formacie, obsługiwane są następujące typy formatów plików: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **typu** w formacie na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [Sekcje Format tekstu,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [Format Json,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [Format Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [Format orków](supported-file-formats-and-compression-codecs-legacy.md#orc-format)i [Format parkietu.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Nie (tylko w przypadku scenariusza kopiowania binarnego) |
| kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [Obsługiwane formaty plików i kodeki kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**i **ZipDeflate**.<br/>Obsługiwane poziomy to: **Optymalne** i **najszybsze**. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, należy określić **bucketName** dla zasobnika i **prefiksu** dla części folderu.<br>Aby skopiować pojedynczy plik o podanej nazwie, należy określić **bucketName** dla zasobnika i **klucza** dla części folderu plus nazwa pliku.<br>Aby skopiować podzbiór plików w folderze, należy określić **bucketName** dla zasobnika i **klucza** dla części folderu oraz filtru symboli wieloznacznych.

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
Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
