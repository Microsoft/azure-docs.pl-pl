---
title: Kopiowanie danych do lub z usługi Azure Data Lake Storage Gen1
description: Dowiedz się, jak kopiować dane z obsługiwanych źródłowych magazynów danych do usługi Azure Data Lake Store lub z usługi Data Lake Store do obsługiwanych magazynów ujścia przy użyciu usługi Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 61bb8fe950de8cd9be91bc76bd24aa0151f3fb79
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415414"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Kopiowanie danych do lub z usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję usługi Azure Data Factory, której używasz:"]
> * [Wersja 1](v1/data-factory-azure-datalake-connector.md)
> * [Bieżąca wersja](connector-azure-data-lake-store.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób kopiowania danych do i z usługi Azure Data Lake Storage Gen1. Aby dowiedzieć się więcej o usłudze Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik usługi Azure Data Lake Storage Gen1 jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md) 
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuwanie działania](delete-activity.md)

W szczególności za pomocą tego złącza można:

- Kopiowanie plików przy użyciu jednej z następujących metod uwierzytelniania: jednostki usługi lub tożsamości zarządzanych dla zasobów platformy Azure.
- Kopiowanie plików w stanie, w jakim jest, analizowanie lub generowanie plików za pomocą [obsługiwanych formatów plików i kodeków kompresji](supported-file-formats-and-compression-codecs.md).
- [Zachowaj listy ACL](#preserve-acls-to-data-lake-storage-gen2) podczas kopiowania do usługi Azure Data Lake Storage Gen2.

> [!IMPORTANT]
> Jeśli kopiujesz dane przy użyciu środowiska uruchomieniowego integracji hostowanego samodzielnie, `<ADLS account name>.azuredatalakestore.net` `login.microsoftonline.com/<tenant>/oauth2/token` skonfiguruj zaporę firmową, aby zezwalała na ruch wychodzący do i na porcie 443. Ten ostatni jest usługa tokenu zabezpieczeń platformy Azure, że środowisko wykonawcze integracji musi komunikować się z, aby uzyskać token dostępu.

## <a name="get-started"></a>Rozpoczęcie pracy

> [!TIP]
> Aby zapoznać się z instrukcjami dotyczącymi używania łącznika usługi Azure Data Lake Store, zobacz [Ładowanie danych do usługi Azure Data Lake Store.](load-azure-data-lake-store.md)

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają informacje o właściwościach, które są używane do definiowania jednostek usługi Data Factory specyficznych dla usługi Azure Data Lake Store.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej usługi Azure Data Lake Store:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość `type` musi być ustawiona na **AzureDataLakeStore**. | Tak |
| dataLakeStoreUri | Informacje o koncie usługi Azure Data Lake Store. Informacje te mają jeden z `https://[accountname].azuredatalakestore.net/webhdfs/v1` następujących `adl://[accountname].azuredatalakestore.net/`formatów: lub . | Tak |
| subscriptionId | Identyfikator subskrypcji platformy Azure, do którego należy konto Usługi Data Lake Store. | Wymagane do zlewu |
| resourceGroupName | Nazwa grupy zasobów platformy Azure, do której należy konto Magazynu usługi Data Lake Store. | Wymagane do zlewu |
| connectVia | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska uruchomieniowego integracji hostowanego samodzielnie, jeśli magazyn danych znajduje się w sieci prywatnej. Jeśli ta właściwość nie jest określona, używany jest domyślny środowiska wykonawczego integracji platformy Azure. |Nie |

### <a name="use-service-principal-authentication"></a>Korzystanie z uwierzytelniania głównego usługi

Aby użyć uwierzytelniania jednostkowego usługi, wykonaj następujące kroki.

1. Zarejestruj jednostkę aplikacji w usłudze Azure Active Directory i udziel jej dostępu do magazynu Usługi Data Lake Store. Aby uzyskać szczegółowe kroki, zobacz [Uwierzytelnianie usługi do usługi](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Zanotuj następujące wartości, których używasz do definiowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Udzielić jednostki usługi odpowiednie uprawnienia. Zobacz przykłady dotyczące działania uprawnień w usłudze Data Lake Storage Gen1 z [kontroli dostępu w usłudze Azure Data Lake Storage Gen1.](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)

    - **Jako źródło**: W**programie** **Dostęp do eksploratora** > danych należy udzielić co najmniej uprawnień **do wykonywania** dla wszystkich folderów nadrzędnych, w tym katalogu głównego, wraz z uprawnieniem **Do odczytu** dla plików do kopiowania. Można dodać do **tego folderu i wszystkie dzieci** do cyklicznego, a następnie dodać jako **uprawnienie dostępu i domyślny wpis uprawnień**. Nie ma żadnych wymagań dotyczących kontroli dostępu na poziomie konta (IAM).
    - **Jako ujście:** W**programie Dostęp** **do eksploratora** > danych należy **udzielić** co najmniej uprawnień do wykonywania dla wszystkich folderów nadrzędnych, w tym katalogu głównego, wraz z uprawnieniem **Zapis** dla folderu ujścia. Można dodać do **tego folderu i wszystkie dzieci** do cyklicznego, a następnie dodać jako **uprawnienie dostępu i domyślny wpis uprawnień**. Jeśli używasz środowiska wykonawczego integracji platformy Azure do kopiowania (zarówno źródło, jak i ujście znajdują się w chmurze), w usłudze IAM należy przyznać co najmniej rolę **czytnika,** aby umożliwić fabrykze danych wykrywanie regionu dla magazynu usługi Data Lake. Jeśli chcesz uniknąć tej roli IAM, jawnie [utwórz środowisko uruchomieniowe integracji platformy Azure](create-azure-integration-runtime.md#create-azure-ir) z lokalizacją magazynu Usługi Data Lake. Jeśli na przykład magazyn usługi Data Lake store znajduje się w Europie Zachodniej, utwórz środowisko uruchomieniowe integracji platformy Azure z lokalizacją ustawioną na "Europa Zachodnia". Skojarz je w połączonej usłudze Usługi usługi Data Lake Store, jak pokazano w poniższym przykładzie.

Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole `SecureString` jako bezpieczne przechowywanie go w fabryce danych lub [odwołanie się do klucza tajnego przechowywanego w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| Dzierżawy | Określ informacje o dzierżawie, takie jak nazwa domeny lub identyfikator dzierżawy, w ramach którego znajduje się aplikacja. Można go pobrać, najeżdżając myszą w prawym górnym rogu witryny Azure portal. | Tak |

**Przykład:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Używanie tożsamości zarządzanych do uwierzytelniania zasobów platformy Azure

Fabryka danych może być skojarzona z [tożsamością zarządzaną dla zasobów platformy Azure,](data-factory-service-identity.md)która reprezentuje tę fabrykę określonych danych. Tej tożsamości zarządzanej można bezpośrednio użyć do uwierzytelniania usługi Data Lake Store, podobnie jak przy użyciu własnej jednostki usługi. Umożliwia tej wyznaczonej fabryce dostęp i kopiowanie danych do lub z usługi Data Lake Store.

Aby użyć tożsamości zarządzanych do uwierzytelniania zasobów platformy Azure, wykonaj następujące kroki.

1. [Pobierz informacje o tożsamości zarządzanej fabrycznie](data-factory-service-identity.md#retrieve-managed-identity) przez skopiowanie wartości "Identyfikator aplikacji tożsamości usługi" wygenerowanej wraz z fabryką.

2. Udziel dostępu do tożsamości zarządzanej do magazynu Usługi Data Lake. Zobacz przykłady dotyczące działania uprawnień w usłudze Data Lake Storage Gen1 z [kontroli dostępu w usłudze Azure Data Lake Storage Gen1.](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)

    - **Jako źródło**: W**programie** **Dostęp do eksploratora** > danych należy udzielić co najmniej uprawnień **do wykonywania** dla wszystkich folderów nadrzędnych, w tym katalogu głównego, wraz z uprawnieniem **Do odczytu** dla plików do kopiowania. Można dodać do **tego folderu i wszystkie dzieci** do cyklicznego, a następnie dodać jako **uprawnienie dostępu i domyślny wpis uprawnień**. Nie ma żadnych wymagań dotyczących kontroli dostępu na poziomie konta (IAM).
    - **Jako ujście:** W**programie Dostęp** **do eksploratora** > danych należy **udzielić** co najmniej uprawnień do wykonywania dla wszystkich folderów nadrzędnych, w tym katalogu głównego, wraz z uprawnieniem **Zapis** dla folderu ujścia. Można dodać do **tego folderu i wszystkie dzieci** do cyklicznego, a następnie dodać jako **uprawnienie dostępu i domyślny wpis uprawnień**. Jeśli używasz środowiska wykonawczego integracji platformy Azure do kopiowania (zarówno źródło, jak i ujście znajdują się w chmurze), w usłudze IAM należy przyznać co najmniej rolę **czytnika,** aby umożliwić fabrykze danych wykrywanie regionu dla magazynu usługi Data Lake. Jeśli chcesz uniknąć tej roli IAM, jawnie [utwórz środowisko uruchomieniowe integracji platformy Azure](create-azure-integration-runtime.md#create-azure-ir) z lokalizacją magazynu Usługi Data Lake. Skojarz je w połączonej usłudze Usługi usługi Data Lake Store, jak pokazano w poniższym przykładzie.

W usłudze Azure Data Factory nie trzeba określać żadnych właściwości oprócz ogólnych informacji magazynu usługi Data Lake Store w połączonej usłudze.

**Przykład:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
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

Następujące właściwości są obsługiwane dla usługi Azure Data `location` Lake Store Gen1 w ustawieniach w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagany |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Właściwość typu `location` w zestawie danych musi być ustawiona na **AzureDataLakeStoreLocation**. | Tak      |
| folderPath | Ścieżka do folderu. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania folderów, pomiń to ustawienie i określ je w ustawieniach źródła aktywności. | Nie       |
| fileName   | Nazwa pliku pod danym folderPath. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania plików, pomiń to ustawienie i określ je w ustawieniach źródła aktywności. | Nie       |

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Potoki](concepts-pipelines-activities.md). Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujście usługi Azure Data Lake Store.

### <a name="azure-data-lake-store-as-source"></a>Usługa Azure Data Lake Store jako źródło

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla usługi Azure Data `storeSettings` Lake Store Gen1 w ustawieniach w źródle kopiowania opartym na formacie:

| Właściwość                 | Opis                                                  | Wymagany                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Właściwość typu `storeSettings` w obszarze musi być ustawiona na **AzureDataLakeStoreReadSettings**. | Tak                                           |
| Cykliczne                | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. Gdy rekursywny jest ustawiony na true i zlew jest magazyn oparty na plikach, pusty folder lub podfolder nie jest kopiowany lub tworzony w zlewie. Dozwolone wartości są **prawdziwe** (domyślnie) i **false**. | Nie                                            |
| Ścieżka wieloznacznaFolderPath       | Ścieżka folderu ze znakami wieloznaczowymi do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku). Użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach filtru folderów i plików](#folder-and-file-filter-examples). | Nie                                            |
| symboli wieloznacznychFileName         | Nazwa pliku ze znakami wieloznacznymi pod danym folderpath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku). Użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. Zobacz więcej przykładów w [przykładach filtru folderów i plików](#folder-and-file-filter-examples). | Tak, `fileName` jeśli nie jest określony w zestawie danych |
| modifiedDatetimeStart    | Filtr plików na podstawie atrybutu Ostatnia modyfikacja. Pliki są wybierane, jeśli ich ostatni zmodyfikowany `modifiedDatetimeStart` `modifiedDatetimeEnd`czas mieści się w zakresie czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br> Właściwości mogą być NULL, co oznacza, że do zestawu danych nie jest stosowany filtr atrybutów pliku. Gdy `modifiedDatetimeStart` ma wartość datetime, ale `modifiedDatetimeEnd` jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa datetime wartość są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość datetime, ale `modifiedDatetimeStart` jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime są zaznaczone. | Nie                                            |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                            |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem magazynowym jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie                                            |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
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
                    "type": "AzureDataLakeStoreReadSettings",
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

### <a name="azure-data-lake-store-as-sink"></a>Usługa Azure Data Lake Store jako zlew

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla usługi Azure Data `storeSettings` Lake Store Gen1 w ustawieniach w umywalka kopii opartej na formacie:

| Właściwość                 | Opis                                                  | Wymagany |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Właściwość typu `storeSettings` w obszarze musi być ustawiona na **AzureDataLakeStoreWriteSettings**. | Tak      |
| copyBehavior             | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartych na plikach.<br/><br/>Dozwolone wartości to:<br/><b>- PreserveHierarchy (domyślnie):</b>Zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest identyczna ze ścieżką względną pliku docelowego do folderu docelowego.<br/><b>- FlattenHierarchy:</b>Wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie generowane nazwy. <br/><b>- MergeFiles:</b>Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określona, scalona nazwa pliku jest określoną nazwą. W przeciwnym razie jest to nazwa pliku z automatycznym generacją. | Nie       |
| expiryDateTime | Określa czas wygaśnięcia plików pisemnych. Czas jest stosowany do czasu UTC w formacie "2020-03-01T08:00:00Z". Domyślnie jest null, co oznacza, że pliki zapisane nigdy nie wygasły. | Nie |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem danych jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie       |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
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
                    "type": "AzureDataLakeStoreWriteSettings",
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
| `Folder*` | (Puste, użyj domyślnie) | false | FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5.csv<br/>InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| `Folder*` | (Puste, użyj domyślnie) | true | FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik5.csv**<br/>InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| `Folder*` | `*.csv` | false | FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5.csv<br/>InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |
| `Folder*` | `*.csv` | true | FolderA ( folderA )<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Plik1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Plik5.csv**<br/>InnyFolderb<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik 6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Przykłady zachowania operacji kopiowania

W tej sekcji opisano wynikowe zachowanie operacji kopiowania dla różnych kombinacji `recursive` i `copyBehavior` wartości.

| Cykliczne | copyBehavior | Struktura folderów źródłowych | Wynikujący cel |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy1 jest tworzony z taką samą strukturą jak źródło:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5. |
| true |spłaszczyćHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku2<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku3<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku4<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku5 |
| true |scalanie plików | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Zawartość File1 + File2 + File3 + File4 + File55 jest scalana w jeden plik o automatycznie zogenerowanej nazwie pliku. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy1 jest tworzony z następującą strukturą:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/>Podfolder1 z plikami File3, File4 i File5 nie są pobierane. |
| false |spłaszczyćHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy1 jest tworzony z następującą strukturą:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku2<br/><br/>Podfolder1 z plikami File3, File4 i File5 nie są pobierane. |
| false |scalanie plików | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy1 jest tworzony z następującą strukturą:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Zawartość File1 + File2 jest scalana w jeden plik o automatycznie generowanej nazwie pliku. autogenerowana nazwa pliku1<br/><br/>Podfolder1 z plikami File3, File4 i File5 nie są pobierane. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Zachowywanie list ACL do pamięci masowej usługi Data Lake Storage Gen2

>[!TIP]
>Aby skopiować dane z usługi Azure Data Lake Storage Gen1 do gen2 w ogóle, zobacz [Kopiowanie danych z usługi Azure Data Lake Storage Gen1 do Gen2 z usługi Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) dla przejścia i najlepszych rozwiązań.

Jeśli chcesz replikować listy kontroli dostępu (ACL) wraz z plikami danych podczas uaktualniania z usługi Data Lake Storage Gen1 do Data Lake Storage Gen2, zobacz [Zachowywanie list ACL z magazynu usługi Data Lake Gen1](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Podczas przekształcania danych w przepływie danych mapowania można odczytywać i zapisywać pliki z usługi Azure Data Lake Storage Gen1 w formacie JSON, Avro, Delimited Text lub Parquet. Aby uzyskać więcej informacji, zobacz [transformację źródła](data-flow-source.md) i [transformację ujścia](data-flow-sink.md) w funkcji przepływu danych mapowania.

### <a name="source-transformation"></a>Transformacja źródła

W transformacji źródła można odczytać z kontenera, folderu lub pojedynczego pliku w usłudze Azure Data Lake Storage Gen1. Karta **Opcje źródła** umożliwia zarządzanie odczytem plików. 

![Opcje źródła](media/data-flow/sourceOptions1.png "Opcje źródła")

**Ścieżka wieloznaczna:** Użycie wzorca symboli wieloznacznych nakaże podajnikowi ADF zapętlenie każdego pasującego folderu i pliku w jednej transformacji źródła. Jest to skuteczny sposób przetwarzania wielu plików w ramach jednego przepływu. Dodaj wiele wzorców dopasowywania symboli wieloznacznych ze znakiem +, który pojawia się po najechaniu kursorem na istniejący wzorzec symboli wieloznacznych.

W kontenerze źródłowym wybierz serię plików pasuszych do wzorca. Tylko kontener można określić w zestawie danych. Ścieżka wieloznaczna musi zatem zawierać również ścieżkę folderu z folderu głównego.

Przykłady symboli wieloznacznych:

* ```*```Reprezentuje dowolny zestaw znaków
* ```**```Reprezentuje zagnieżdżanie katalogów cyklicznych
* ```?```Zastępuje jeden znak
* ```[]```Dopasowuje jedną z kolejnych postaci w nawiasach

* ```/data/sales/**/*.csv```Pobiera wszystkie pliki csv w obszarze /data/sales
* ```/data/sales/20??/**/```Pobiera wszystkie pliki w 20 wieku
* ```/data/sales/*/*/*.csv```Pobiera pliki csv dwa poziomy w obszarze /data/sales
* ```/data/sales/2004/*/12/[XY]1?.csv```Pobiera wszystkie pliki csv w 2004 r. w grudniu, począwszy od X lub Y poprzedzone dwucyfrowym numerem

**Ścieżka główna partycji:** Jeśli foldery podzielone na partycje w ```key=value``` źródle plików mają format (na przykład year=2019), możesz przypisać najwyższy poziom tego drzewa folderów partycji do nazwy kolumny w strumieniu danych przepływu danych.

Najpierw ustaw symbol wieloznaczny, aby uwzględnić wszystkie ścieżki, które są folderami podzielonymi na partycje oraz pliki liściowe, które chcesz odczytać.

![Ustawienia pliku źródłowego partycji](media/data-flow/partfile2.png "Ustawienie pliku partycji")

Użyj ustawienia Ścieżka główna partycji, aby zdefiniować najwyższy poziom struktury folderów. Podczas wyświetlania zawartości danych za pośrednictwem podglądu danych zobaczysz, że podajnik ADF doda rozwiązane partycje znajdujące się na każdym z poziomów folderów.

![Ścieżka główna partycji](media/data-flow/partfile1.png "Podgląd ścieżki głównej partycji")

**Lista plików:** Jest to zestaw plików. Utwórz plik tekstowy zawierający listę plików ścieżek względnych do przetworzenia. Wskaż ten plik tekstowy.

**Kolumna do przechowywania nazwy pliku:** Przechowuj nazwę pliku źródłowego w kolumnie w danych. Wprowadź tutaj nową nazwę kolumny, aby zapisać ciąg nazwy pliku.

**Po zakończeniu:** Wybierz, aby nie robić nic z plikiem źródłowym po uruchomieniu przepływu danych, usunąć plik źródłowy lub przenieść plik źródłowy. Ścieżki dla ruchu są względne.

Aby przenieść pliki źródłowe do innej lokalizacji post-processing, najpierw wybierz "Przenieś" dla operacji pliku. Następnie ustaw katalog "od". Jeśli nie używasz żadnych symboli wieloznacznych dla ścieżki, ustawienie "od" będzie tym samym folderem co folder źródłowy.

Jeśli masz ścieżkę źródłową z symbolami wieloznaczymi, składnia będzie wyglądać następująco poniżej:

```/data/sales/20??/**/*.csv```

Można określić "od" jako

```/data/sales```

I "do" jako

```/backup/priorSales```

W takim przypadku wszystkie pliki, które zostały pozyskane w obszarze /data/sales, są przenoszone do /backup/priorSales.

> [!NOTE]
> Operacje na plikach są uruchamiane tylko po uruchomieniu przepływu danych z uruchomienia potoku (uruchomienie debugowania lub wykonania potoku), które używa działania Wykonaj przepływ danych w potoku. Operacje na plikach *nie są* uruchamiane w trybie debugowania przepływu danych.

**Filtruj według ostatnio zmodyfikowanych:** Można filtrować pliki, które przetwarzasz, określając zakres dat, kiedy zostały ostatnio zmodyfikowane. Wszystkie godziny daty są w czasie UTC. 

### <a name="sink-properties"></a>Właściwości zlewu

W transformacji ujścia można zapisać do kontenera lub folderu w usłudze Azure Data Lake Storage Gen1. Karta **Ustawienia** umożliwia zarządzanie sposóbem pisania plików.

![opcje zlewu](media/data-flow/file-sink-settings.png "opcje zlewu")

**Wyczyść folder:** Określa, czy folder docelowy zostanie wyczyszczony przed zapisaniem danych.

**Opcja nazwy pliku:** Określa, jak pliki docelowe mają nazwy w folderze docelowym. Opcje nazwy pliku to:
   * **Domyślnie**: Zezwalaj spark na nazwy plików na podstawie wartości domyślnych PART.
   * **Wzorzec:** Wprowadź wzorzec, który wylicza pliki wyjściowe na partycję. Na przykład **pożyczki[n].csv** utworzy loans1.csv, loans2.csv i tak dalej.
   * **Na partycję**: Wprowadź jedną nazwę pliku na partycję.
   * **Jako dane w kolumnie**: Ustaw plik wyjściowy na wartość kolumny. Ścieżka jest względem kontenera zestawu danych, a nie folderu docelowego. Jeśli masz ścieżkę folderu w zestawie danych, zostanie zastąpiona.
   * **Dane wyjściowe do pojedynczego pliku**: Łączenie podzielonych na partycje plików wyjściowych w jeden nazwany plik. Ścieżka jest względem folderu zestawu danych. Należy pamiętać, że te merge operacji może ewentualnie zakończyć się niepowodzeniem na podstawie rozmiaru węzła. Ta opcja nie jest zalecana w przypadku dużych zestawów danych.

**Zacytuj wszystkie:** Określa, czy wszystkie wartości mają być ujęte w cudzysłowie

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
| type | Właściwość typu zestawu danych musi być ustawiona na **AzureDataLakeStoreFile**. |Tak |
| folderPath | Ścieżka do folderu w magazynie Usługi Data Lake. Jeśli nie zostanie określony, wskazuje na katalog główny. <br/><br/>Filtr symboli wieloznacznych jest obsługiwany. Dozwolone symbole wieloznaczne to `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku). Użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br/><br/>Na przykład: rootfolder/subfolder/. Zobacz więcej przykładów w [przykładach filtru folderów i plików](#folder-and-file-filter-examples). |Nie |
| fileName | Nazwa lub symbol wieloznaczny filtr dla plików w ramach określonego "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie pliki w folderze. <br/><br/>W przypadku filtru dozwolone są `*` symbole wieloznaczne `?` (dopasowywuj zero lub więcej znaków) i (dopasowuje zero lub pojedynczy znak).<br/>- Przykład 1:`"fileName": "*.csv"`<br/>- Przykład 2:`"fileName": "???20180427.txt"`<br/>Użyj, `^` aby uciec, jeśli rzeczywista nazwa pliku ma symbol wieloznaczny lub ten znak ucieczki wewnątrz.<br/><br/>Jeśli nazwa pliku nie jest określona dla wyjściowego zestawu danych i **preserveHierarchy** nie jest określony w ujściu działania, działanie kopiowania automatycznie generuje nazwę pliku z następującym wzorcem: "*Dane.[ identyfikator GUID uruchomienia działania]. [GUID jeśli FlattenHierarchy]. [format, jeśli jest skonfigurowany]. [kompresja, jeśli jest skonfigurowana]*", na przykład "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". W przypadku kopiowania ze źródła tabelaryczne przy użyciu nazwy tabeli zamiast kwerendy wzorzec nazwy to "*[nazwa tabeli].[ w formacie]. [kompresja, jeśli jest skonfigurowana]*", na przykład "MyTable.csv". |Nie |
| modifiedDatetimeStart | Filtr plików na podstawie atrybutu Ostatnia modyfikacja. Pliki są wybierane, jeśli ich ostatni zmodyfikowany `modifiedDatetimeStart` `modifiedDatetimeEnd`czas mieści się w zakresie czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Na ogólną wydajność przenoszenia danych ma wpływ włączenie tego ustawienia, gdy chcesz wykonać filtr plików z ogromną ilością plików. <br/><br/> Właściwości mogą być NULL, co oznacza, że do zestawu danych nie jest stosowany filtr atrybutów pliku. Gdy `modifiedDatetimeStart` ma wartość datetime, ale `modifiedDatetimeEnd` jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa datetime wartość są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość datetime, ale `modifiedDatetimeStart` jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime są zaznaczone.| Nie |
| modifiedDatetimeEnd | Filtr plików na podstawie atrybutu Ostatnia modyfikacja. Pliki są wybierane, jeśli ich ostatni zmodyfikowany `modifiedDatetimeStart` `modifiedDatetimeEnd`czas mieści się w zakresie czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Na ogólną wydajność przenoszenia danych ma wpływ włączenie tego ustawienia, gdy chcesz wykonać filtr plików z ogromną ilością plików. <br/><br/> Właściwości mogą być NULL, co oznacza, że do zestawu danych nie jest stosowany filtr atrybutów pliku. Gdy `modifiedDatetimeStart` ma wartość datetime, ale `modifiedDatetimeEnd` jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa datetime wartość są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość datetime, ale `modifiedDatetimeStart` jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime są zaznaczone.| Nie |
| format | Jeśli chcesz skopiować pliki, tak jak między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz przeanalizować lub wygenerować pliki w określonym formacie, obsługiwane są następujące typy formatów plików: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**i **ParquetFormat**. Ustaw właściwość **typu** w **formacie** na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [sekcje Format tekstu,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [Format JSON,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [Format Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [Format Orków](supported-file-formats-and-compression-codecs-legacy.md#orc-format)i [Format parkietu.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Nie (tylko w przypadku scenariusza kopiowania binarnego) |
| kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [Obsługiwane formaty plików i kodeki kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Obsługiwane typy to **GZip**, **Deflate**, **BZip2**i **ZipDeflate**.<br/>Obsługiwane poziomy są **optymalne** i **najszybsze.** |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ tylko **folderPath.**<br>Aby skopiować pojedynczy plik o określonej nazwie, określ **folderPath** z częścią folderu i **nazwą pliku** o nazwie pliku.<br>Aby skopiować podzbiór plików w folderze, określ **folderPath** z częścią folderu i **fileName** z filtrem symboli wieloznacznych. 

**Przykład:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
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
| type | Właściwość `type` źródła działania kopiowania musi być ustawiona na **AzureDataLakeStoreSource**. |Tak |
| Cykliczne | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. Gdy `recursive` jest ustawiona na true, a zlew jest magazyn oparty na plikach, pusty folder lub podfolder nie jest kopiowany ani tworzony w zlewie. Dozwolone wartości są **prawdziwe** (domyślnie) i **false**. | Nie |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem danych jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
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
| type | Właściwość `type` ujścia działania kopiowania musi być ustawiona na **AzureDataLakeStoreSink**. |Tak |
| copyBehavior | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartych na plikach.<br/><br/>Dozwolone wartości to:<br/><b>- PreserveHierarchy (domyślnie):</b>Zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest identyczna ze ścieżką względną pliku docelowego do folderu docelowego.<br/><b>- FlattenHierarchy:</b>Wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie generowane nazwy. <br/><b>- MergeFiles:</b>Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określona, scalona nazwa pliku jest określoną nazwą. W przeciwnym razie nazwa pliku jest automatyczniegenerowana. | Nie |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem danych jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
