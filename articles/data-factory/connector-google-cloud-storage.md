---
title: Kopiowanie danych z magazynu Google Cloud Storage przy użyciu Azure Data Factory
description: Informacje o sposobie kopiowania danych z magazynu Google Cloud Storage do obsługiwanych magazynów danych ujścia przy użyciu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: jingwang
ms.openlocfilehash: b8e518ba23c877ee80197ad94a6bc01b23b044a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588995"
---
# <a name="copy-data-from-google-cloud-storage-by-using-azure-data-factory"></a>Kopiowanie danych z magazynu Google Cloud Storage przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób kopiowania danych z usługi Google Cloud Storage (operacje odzyskiwania pamięci). Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik usługi Google Cloud Storage jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuń działanie](delete-activity.md)

W odniesieniu do tego łącznika usługi Google Cloud Storage obsługuje kopiowanie plików jako pliki lub analizowanie plików z [obsługiwanymi formatami plików i kodekami kompresji](supported-file-formats-and-compression-codecs.md). Wykorzystuje ona współdziałanie zgodne ze standardem S3 operacje odzyskiwania pamięci.

## <a name="prerequisites"></a>Wymagania wstępne

Na Twoim koncie usługi Google Cloud Storage wymagana jest następująca konfiguracja:

1. Włącz współdziałanie dla konta usługi Google Cloud Storage
2. Ustaw projekt domyślny zawierający dane, które mają zostać skopiowane z docelowego zasobnika operacje odzyskiwania pamięci.
3. Utwórz konto usługi i zdefiniuj odpowiednie poziomy uprawnień przy użyciu usługi IAM w chmurze w witrynie GCP. 
4. Generuj klucze dostępu dla tego konta usługi.

![Pobieranie klucza dostępu do magazynu Google Cloud Storage](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby skopiować dane z magazynu Google Cloud Storage, upewnij się, że masz przyznane następujące uprawnienia do operacji obiektów: ` storage.objects.get` i ` storage.objects.list` .

Jeśli używasz interfejsu użytkownika Data Factory do tworzenia, ` storage.buckets.list` wymagane jest dodatkowe uprawnienie do wykonywania operacji, takich jak testowanie połączenia z połączoną usługą i przeglądanie z poziomu katalogu głównego. Jeśli nie chcesz udzielić tego uprawnienia, możesz wybrać opcje "Test connection do ścieżki pliku" lub "Przeglądaj z określonej ścieżki" w interfejsie użytkownika.

Aby uzyskać pełną listę ról usługi Google Cloud Storage i skojarzonych z nimi uprawnień, zobacz [role usługi IAM dla magazynu](https://cloud.google.com/storage/docs/access-control/iam-roles) w chmurze w witrynie Google Cloud.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla usługi Google Cloud Storage.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane przez połączone usługi Google Cloud Storage:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** musi być ustawiona na wartość **GoogleCloudStorage**. | Tak |
| accessKeyId | Identyfikator klucza dostępu tajnego. Aby znaleźć klucz dostępu i wpis tajny, zobacz [wymagania wstępne](#prerequisites). |Tak |
| secretAccessKey | Sam klucz dostępu tajnego. Oznacz to pole jako element **SecureString** , aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). |Tak |
| serviceUrl | Określ niestandardowy punkt końcowy operacje odzyskiwania pamięci `https://storage.googleapis.com` . | Tak |
| Właściwością connectvia | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć środowiska Azure Integration Runtime lub własnego środowiska Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli ta właściwość nie jest określona, usługa używa domyślnego środowiska Azure Integration Runtime. |Nie |

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
| typ       | Właściwość **typu** w `location` elemencie DataSet musi być ustawiona na **GoogleCloudStorageLocation**. | Tak      |
| zasobnikname | Nazwa zasobnika operacje odzyskiwania pamięci.                                          | Tak      |
| folderPath | Ścieżka do folderu w danym przedziale. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania folderu, pomiń to ustawienie i określ je w obszarze Ustawienia źródła działania. | Nie       |
| fileName   | Nazwa pliku w podanym przedziale i ścieżce folderu. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania plików, pomiń to ustawienie i określ je w obszarze Ustawienia źródła działania. | Nie       |

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
                "type": "GoogleCloudStorageLocation",
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

### <a name="google-cloud-storage-as-a-source-type"></a>Magazyn w chmurze Google jako typ źródła

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane w usłudze Google Cloud Storage w obszarze `storeSettings` Ustawienia w źródle kopii opartego na formacie:

| Właściwość                 | Opis                                                  | Wymagane                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| typ                     | Właściwość **Type** w obszarze `storeSettings` musi być ustawiona na wartość **GoogleCloudStorageReadSettings**. | Tak                                                         |
| ***Zlokalizuj pliki do skopiowania:*** |  |  |
| Opcja 1: ścieżka statyczna<br> | Kopiuj z podanego zasobnika lub folderu/ścieżki pliku określonego w zestawie danych. Jeśli chcesz skopiować wszystkie pliki z przedziału lub folderu, należy również określić `wildcardFileName` jako `*` . |  |
| Opcja 2: prefiks operacje odzyskiwania pamięci<br>-prefix | Prefiks nazwy klucza operacje odzyskiwania pamięci w danym zasobniku skonfigurowanym w zestawie danych do filtrowania źródłowych plików operacje odzyskiwania pamięci. OPERACJE odzyskiwania pamięci klucze, których nazwy rozpoczynają `bucket_in_dataset/this_prefix` się od są zaznaczone. Wykorzystuje filtr po stronie usługi operacje odzyskiwania pamięci, który zapewnia lepszą wydajność niż filtr symboli wieloznacznych. | Nie |
| Opcja 3: symbol wieloznaczny<br>- wildcardFolderPath | Ścieżka folderu z symbolami wieloznacznymi w ramach danego zasobnika skonfigurowanych w zestawie danych do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak). Użyj `^` do ucieczki, jeśli nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Nie                                            |
| Opcja 3: symbol wieloznaczny<br>- wildcardFileName | Nazwa pliku z symbolami wieloznacznymi w danym przedziale i ścieżce folderu (lub ścieżki do symboli wieloznacznych) do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to: `*` (dopasowuje zero lub więcej znaków) i `?` (dopasowuje zero lub pojedynczy znak). Użyj `^` do ucieczki, jeśli nazwa pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.  Zobacz więcej przykładów w [przykładach folderów i filtrów plików](#folder-and-file-filter-examples). | Tak |
| Opcja 3: Lista plików<br>- fileListPath | Wskazuje, aby skopiować dany zestaw plików. Wskaż plik tekstowy zawierający listę plików, które chcesz skopiować, jeden plik w wierszu, który jest ścieżką względną do ścieżki skonfigurowanej w zestawie danych.<br/>W przypadku korzystania z tej opcji nie należy określać nazwy pliku w zestawie danych. Zobacz więcej przykładów na [listach plików](#file-list-examples). |Nie |
| ***Ustawienia dodatkowe:*** |  | |
| rozpoznawania | Wskazuje, czy dane są odczytane cyklicznie z podfolderów, czy tylko z określonego folderu. Należy pamiętać, że gdy wartość **cykliczna** jest ustawiona na **wartość true** , a ujścia jest magazynem opartym na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w ujścia. <br>Dozwolone wartości to **true** (wartość domyślna) i **false**.<br>Ta właściwość nie ma zastosowania podczas konfigurowania `fileListPath` . |Nie |
| deleteFilesAfterCompletion | Wskazuje, czy pliki binarne zostaną usunięte z magazynu źródłowego po pomyślnym przeniesieniu do magazynu docelowego. Plik jest usuwany dla każdego pliku, więc w przypadku niepowodzenia działania kopiowania niektóre pliki zostały już skopiowane do lokalizacji docelowej i usunięte ze źródła, podczas gdy inne nadal pozostają w magazynie źródłowym. <br/>Ta właściwość jest prawidłowa tylko w scenariuszu kopiowania plików binarnych. Wartość domyślna: false. |Nie |
| modifiedDatetimeStart    | Pliki są filtrowane na podstawie atrybutu: Ostatnia modyfikacja. <br>Pliki zostaną wybrane, jeśli czas ostatniej modyfikacji mieści się w przedziale czasu między `modifiedDatetimeStart` i `modifiedDatetimeEnd` . Czas jest stosowany do strefy czasowej UTC w formacie "2018 r-12-01T05:00:00Z". <br> Właściwości mogą mieć **wartość null**, co oznacza, że żaden filtr atrybutu pliku nie zostanie zastosowany do zestawu danych.  Gdy `modifiedDatetimeStart` ma wartość DateTime, ale `modifiedDatetimeEnd` jest **równa null**, to pliki, których ostatni zmodyfikowany atrybut jest większy lub równy wartości DateTime zostanie zaznaczone.  Gdy `modifiedDatetimeEnd` ma wartość DateTime, ale `modifiedDatetimeStart` jest **równa null**, pliki, których ostatni zmodyfikowany atrybut jest mniejszy niż wartość DateTime zostanie wybrana.<br/>Ta właściwość nie ma zastosowania podczas konfigurowania `fileListPath` . | Nie                                            |
| modifiedDatetimeEnd      | Jak wyżej.                                               | Nie                                                          |
| enablePartitionDiscovery | W przypadku plików, które są partycjonowane, określ, czy przeanalizować partycje ze ścieżki pliku i dodać je jako dodatkowe kolumny źródłowe.<br/>Dozwolone wartości to **false** (wartość domyślna) i **true**. | Nie                                            |
| partitionRootPath | Gdy odnajdywanie partycji jest włączone, określ bezwzględną ścieżkę katalogu głównego, aby odczytać partycjonowane foldery jako kolumny danych.<br/><br/>Jeśli nie jest określony, domyślnie,<br/>— Jeśli używasz ścieżki pliku w zestawie danych lub liście plików w źródle, ścieżka katalogu głównego partycji jest ścieżką skonfigurowaną w zestawie danych.<br/>— Jeśli używasz wieloznacznego filtru folderów, ścieżka katalogu głównego partycji jest ścieżką podrzędną przed pierwszym symbolem wieloznacznym.<br/><br/>Na przykład przy założeniu, że ścieżka w zestawie danych jest konfigurowana jako "root/folder/Year = 2020/miesiąc = 08/Day = 27":<br/>— W przypadku określenia ścieżki katalogu głównego partycji jako "root/folder/Year = 2020" działanie Copy (kopiowanie) wygeneruje dwie kolejne kolumny `month` i `day` wartości "08" i "27" (oprócz kolumn wewnątrz plików).<br/>— Jeśli ścieżka katalogu głównego partycji nie zostanie określona, nie zostanie wygenerowane żadne dodatkowe kolumny. | Nie                                            |
| maxConcurrentConnections |Górny limit równoczesnych połączeń ustanowiony dla magazynu danych podczas uruchamiania działania. Określ wartość tylko wtedy, gdy chcesz ograniczyć połączenia współbieżne.| Nie                                                          |

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
                    "type": "GoogleCloudStorageReadSettings",
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
| porcj | `Folder*/*` | fałsz | porcj<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| porcj | `Folder*/*` | true | porcj<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsna**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| porcj | `Folder*/*.csv` | fałsz | porcj<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| porcj | `Folder*/*.csv` | true | porcj<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Przykłady listy plików

W tej sekcji opisano wyniki działania dotyczące korzystania ze ścieżki listy plików w źródle działania kopiowania.

Załóżmy, że masz następującą źródłową strukturę folderów i chcesz skopiować pliki w pogrubieniu:

| Przykładowa struktura źródła                                      | Zawartość w FileListToCopy.txt                             | Konfiguracja Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| porcj<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsna<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadane<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **W zestawie danych:**<br>Porcj `bucket`<br>-Ścieżka folderu: `FolderA`<br><br>**W źródle działania kopiowania:**<br>-Ścieżka listy plików: `bucket/Metadata/FileListToCopy.txt` <br><br>Ścieżka listy plików wskazuje plik tekstowy w tym samym magazynie danych, który zawiera listę plików do skopiowania, jeden plik w wierszu, ze ścieżką względną do ścieżki skonfigurowanej w zestawie danych. |

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby uzyskać szczegółowe informacje na temat właściwości, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Usuń właściwości działania

Aby uzyskać szczegółowe informacje na temat właściwości, zaznacz opcję [Usuń działanie](delete-activity.md).

## <a name="legacy-models"></a>Starsze modele

Jeśli korzystasz z łącznika Amazon S3 do kopiowania danych z usługi Google Cloud Storage, nadal jest ona obsługiwana w przypadku zgodności z poprzednimi wersjami. Zalecamy użycie nowego modelu wymienionego wcześniej. Interfejs użytkownika tworzenia Data Factory został przełączony w celu wygenerowania nowego modelu.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych, które działanie kopiowania w Azure Data Factory obsługuje jako źródła i ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
