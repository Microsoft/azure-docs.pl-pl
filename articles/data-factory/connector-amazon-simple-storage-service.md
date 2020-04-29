---
title: Kopiowanie danych z usługi Amazon Simple Storage Service (S3)
description: Informacje o sposobie kopiowania danych z usługi Amazon Simple Storage Service (S3) do obsługiwanych magazynów danych ujścia przy użyciu Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.openlocfilehash: 6975c876bb31e36b7b6268d284f747fd51803d63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415470"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Kopiowanie danych z prostej usługi magazynu Amazon przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
>
> * [Wersja 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Bieżąca wersja](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób kopiowania danych z usługi Amazon Simple Storage Service (Amazon S3). Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).

>[!TIP]
>Aby uzyskać informacje na temat scenariusza migracji danych z usługi Amazon S3 do magazynu Azure, Dowiedz się więcej na temat [używania Azure Data Factory do migrowania danych z usługi Amazon S3 do magazynu Azure](data-migration-guidance-s3-azure-storage.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik usługi Amazon S3 jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuń działanie](delete-activity.md)

W odniesieniu do tego łącznika usługi Amazon S3 obsługuje kopiowanie plików jako pliki lub analizowanie plików z [obsługiwanymi formatami plików i kodekami kompresji](supported-file-formats-and-compression-codecs.md). Można również wybrać opcję [zachowywania metadanych plików podczas kopiowania](#preserve-metadata-during-copy). Łącznik używa [sygnatury AWS w wersji 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) do uwierzytelniania żądań w usłudze S3.

>[!TIP]
>Za pomocą tego łącznika Amazon S3 można kopiować dane ze **wszystkich dostawców magazynu zgodnych ze standardem S3** , np. [magazynu w chmurze Google](connector-google-cloud-storage.md). Określ odpowiedni adres URL usługi w konfiguracji połączonej usługi.

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby skopiować dane z usługi Amazon S3, upewnij się, że masz przyznane następujące uprawnienia:

- **W przypadku wykonywania działania kopiowania:** `s3:GetObject` : `s3:GetObjectVersion` i dla operacji obiektów Amazon S3.
- **W przypadku Data Factory tworzenia graficznego interfejsu użytkownika**: `s3:ListAllMyBuckets` i `s3:ListBucket` / `s3:GetBucketLocation` dla operacji zasobnika usługi Amazon S3 wymagane są także uprawnienia do operacji takich jak połączenie testowe i przeglądanie/nawigowanie w ścieżkach plików. Jeśli nie chcesz udzielić tych uprawnień, Pomiń połączenie testowe na stronie tworzenia połączonej usługi i określ ścieżkę bezpośrednio w ustawieniach zestawu danych.

Aby uzyskać szczegółowe informacje na temat pełnej listy uprawnień usługi Amazon S3, zobacz [Określanie uprawnień w zasadach](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla usługi Amazon S3.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Amazon S3 są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi być ustawiona na wartość **AmazonS3**. | Tak |
| accessKeyId | Identyfikator klucza dostępu tajnego. |Tak |
| secretAccessKey | Sam klucz dostępu tajnego. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). |Tak |
| serviceUrl | Określ niestandardowy punkt końcowy S3, jeśli kopiujesz dane z dostawcy magazynu zgodnego ze standardem S3, innego niż Oficjalna usługa Amazon S3. Na przykład, aby skopiować dane z magazynu Google Cloud Storage, `https://storage.googleapis.com`Określ. | Nie |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

>[!TIP]
>Określ niestandardowy adres URL usługi S3, jeśli kopiujesz dane z magazynu zgodnego z S3, innego niż Oficjalna usługa Amazon S3.

>[!NOTE]
>Ten łącznik wymaga kluczy dostępu dla konta usługi IAM do kopiowania danych z usługi Amazon S3. [Tymczasowe poświadczenia zabezpieczeń](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) nie są obsługiwane.
>

Oto przykład:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w przypadku usługi Amazon S3 `location` w obszarze Ustawienia w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagany |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Właściwość `location` Type w elemencie DataSet musi być ustawiona na wartość **AmazonS3Location**. | Tak      |
| zasobnikname | Nazwa zasobnika S3.                                          | Tak      |
| folderPath | Ścieżka do folderu w danym przedziale. Jeśli chcesz użyć symboli wieloznacznych do filtrowania folderów, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |
| fileName   | Nazwa pliku w podanym zasobniku + folderPath. Jeśli chcesz użyć symboli wieloznacznych do filtrowania plików, pomiń to ustawienie i określ ustawienia źródła działania. | Nie       |
| Wersja | Wersja obiektu S3, jeśli włączono obsługę wersji S3. Jeśli nie zostanie określony, zostanie pobrana najnowsza wersja. |Nie |

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło usługi Amazon S3.

### <a name="amazon-s3-as-source"></a>Amazon S3 jako źródło

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w przypadku usługi Amazon S3 `storeSettings` w obszarze Ustawienia źródła kopiowania opartego na formacie:

| Właściwość                 | Opis                                                  | Wymagany                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | Właściwość Type w obszarze `storeSettings` musi być ustawiona na wartość **AmazonS3ReadSettings**. | Tak                                                         |
| rozpoznawania                | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy pamiętać, że gdy wartość cykliczna jest ustawiona na wartość true, a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia. Dozwolone wartości to **true** (wartość domyślna) i **false**. | Nie                                                          |
| prefiks                   | Prefiks klucza obiektu S3 w danym zasobniku skonfigurowanym w zestawie danych do filtrowania obiektów źródłowych. Zaznaczone obiekty, których klucze zaczynają się od tego prefiksu. <br>Stosuje się tylko `wildcardFolderPath` wtedy `wildcardFileName` , gdy nie określono właściwości i. | Nie                                                          |
| wildcardFolderPath       | Ścieżka folderu z symbolami wieloznacznymi w ramach danego zasobnika skonfigurowanych w zestawie danych do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to `*` : (dopasowuje zero lub więcej znaków `?` ) i (dopasowuje zero lub pojedynczy znak); Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Nie                                                          |
| wildcardFileName         | Nazwa pliku zawierająca symbole wieloznaczne w podanym zasobniku + folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to `*` : (dopasowuje zero lub więcej znaków `?` ) i (dopasowuje zero lub pojedynczy znak); Użyj `^` do ucieczki, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Tak, `fileName` Jeśli w zestawie `prefix` danych i nie zostały określone |
| modifiedDatetimeStart    | Filtr plików oparty na atrybucie: ostatnio modyfikowane. Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, `modifiedDatetimeEnd` ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, `modifiedDatetimeStart` ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana. | Nie                                                          |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                                          |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem magazynu. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie                                                          |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
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

## <a name="preserve-metadata-during-copy"></a>Zachowaj metadane podczas kopiowania

Podczas kopiowania plików z usługi Amazon S3 do Azure Data Lake Storage Gen2/obiektów blob platformy Azure można wybrać opcję zachowywania metadanych plików wraz z danymi. Dowiedz się więcej na temat [zachowywania metadanych](copy-activity-preserve-metadata.md#preserve-metadata).

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

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **AmazonS3Object** |Tak |
| zasobnikname | Nazwa zasobnika S3. Filtr symboli wieloznacznych nie jest obsługiwany. |Tak dla działania kopiowania/wyszukiwania, nie dla działania GetMetadata |
| key | **Nazwa lub filtr symboli wieloznacznych** klucza obiektu S3 w określonym przedziale. Stosuje się tylko wtedy, gdy właściwość "prefix" nie została określona. <br/><br/>Filtr symboli wieloznacznych jest obsługiwany dla części folderu i nazwy pliku. Dozwolone symbole wieloznaczne to `*` : (dopasowuje zero lub więcej znaków `?` ) i (dopasowuje zero lub pojedynczy znak).<br/>-Przykład 1:`"key": "rootfolder/subfolder/*.csv"`<br/>-Przykład 2:`"key": "rootfolder/subfolder/???20180427.txt"`<br/>Zobacz więcej przykładowych [przykładów w folderach i filtrach plików](#folder-and-file-filter-examples). Użyj `^` , aby wyjść, jeśli rzeczywista nazwa folderu/pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. |Nie |
| prefiks | Prefiks klucza obiektu S3. Zaznaczone obiekty, których klucze zaczynają się od tego prefiksu. Stosuje się tylko wtedy, gdy właściwość "Key" nie została określona. |Nie |
| Wersja | Wersja obiektu S3, jeśli włączono obsługę wersji S3. Jeśli nie zostanie określony, zostanie pobrana najnowsza wersja. |Nie |
| modifiedDatetimeStart | Filtr plików oparty na atrybucie: ostatnio modyfikowane. Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że będzie to miało wpływ na ogólną wydajność przenoszenia danych przez włączenie tego ustawienia, jeśli chcesz, aby filtr plików był objęty dużą ilością plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, `modifiedDatetimeEnd` ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, `modifiedDatetimeStart` ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.| Nie |
| modifiedDatetimeEnd | Filtr plików oparty na atrybucie: ostatnio modyfikowane. Pliki zostaną wybrane, jeśli ich czas ostatniej modyfikacji należy do przedziału czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd`. Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br/><br/> Należy pamiętać, że będzie to miało wpływ na ogólną wydajność przenoszenia danych przez włączenie tego ustawienia, jeśli chcesz, aby filtr plików był objęty dużą ilością plików. <br/><br/> Właściwości mogą mieć wartość NULL, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, `modifiedDatetimeEnd` ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime, zostaną zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, `modifiedDatetimeStart` ale jest wartością null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.| Nie |
| format | Jeśli chcesz **skopiować pliki** między magazynami opartymi na plikach (kopia binarna), Pomiń sekcję format w definicjach zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz analizować lub generować pliki o określonym formacie, obsługiwane są następujące typy formatu plików: **TextFormat**, **formatu jsonformat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw właściwość **Type** w polu Format na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstowy](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Format JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Format Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Format Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)i sekcje [formatu Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nie (tylko w przypadku scenariusza kopiowania binarnego) |
| kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekoder kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Obsługiwane typy to: **gzip**, **Wklęśnięcie**, **BZip2**i **ZipDeflate**.<br/>Obsługiwane poziomy to: **optymalne** i **najszybszy**. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, należy określić **zasobnik** dla zasobnika i **prefiks** dla części folderu.<br>Aby skopiować pojedynczy plik o podaną nazwę, określ wartość **dla zasobnika** i **klucza** dla części folderu oraz nazwę pliku.<br>Aby skopiować podzestaw plików w folderze, określ wartość **zasobnika** dla zasobnika i **klucza** dla części folderu oraz filtr symboli wieloznacznych.

**Przykład: używanie prefiksu**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
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

**Przykład: używanie klucza i wersji (opcjonalnie)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
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

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **FileSystemSource** |Tak |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Uwaga gdy wartość cykliczna jest ustawiona na wartość true, a obiekt sink jest magazynem opartym na plikach, pusty folder/podfolder nie zostanie skopiowany/utworzony w ujścia.<br/>Dozwolone wartości to: **true** (wartość domyślna), **Fałsz** | Nie |
| maxConcurrentConnections | Liczba połączeń, które mają być jednocześnie połączone z magazynem danych. Określ tylko wtedy, gdy chcesz ograniczyć współbieżne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
