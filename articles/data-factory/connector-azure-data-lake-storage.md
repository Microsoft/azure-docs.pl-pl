---
title: Kopiowanie i przekształcanie danych w usłudze Azure Data Lake Storage Gen2
description: Dowiedz się, jak kopiować dane do i z usługi Azure Data Lake Storage Gen2 i przekształcać dane w usłudze Azure Data Lake Storage Gen2 przy użyciu usługi Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: 2e4e554bb6564adb8c6722533e127a758e5a9c24
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415396"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Kopiowanie i przekształcanie danych w usłudze Azure Data Lake Storage Gen2 przy użyciu usługi Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Usługa Azure Data Lake Storage Gen2 (ADLS Gen2) to zestaw funkcji dedykowanych do analizy dużych zbiorów danych wbudowanych w [magazyn obiektów Blob platformy Azure.](../storage/blobs/storage-blobs-introduction.md) Można go używać do interfejsu z danymi przy użyciu zarówno systemu plików, jak i paradygmatów przechowywania obiektów.

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do usługi Azure Data Lake Storage Gen2 oraz do przekształcania danych w usłudze Azure Data Lake Storage Gen2. Aby dowiedzieć się więcej o usłudze Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

>[!TIP]
>W przypadku scenariusza migracji usługi data lake lub hurtowni danych dowiedz się więcej od [narzędzia Azure Data Factory do migracji danych z usługi Data Lake lub hurtowni danych na platformę Azure.](data-migration-guidance-overview.md)

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik usługi Azure Data Lake Storage Gen2 jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)
- [Usuwanie działania](delete-activity.md)

W przypadku działania kopiowania za pomocą tego łącznika można:

- Kopiowanie danych z/do usługi Azure Data Lake Storage Gen2 przy użyciu klucza konta, jednostki usługi lub tożsamości zarządzanych dla uwierzytelniania zasobów platformy Azure.
- Kopiowanie plików w stanie, analizowanie lub generowanie plików za pomocą [obsługiwanych formatów plików i kodeków kompresji](supported-file-formats-and-compression-codecs.md).
- [Zachowaj metadane pliku podczas kopiowania](#preserve-metadata-during-copy).
- [Zachowaj listy ACL](#preserve-acls) podczas kopiowania z usługi Azure Data Lake Storage Gen1/Gen2.

>[!IMPORTANT]
>Jeśli włączysz opcję **Zezwalaj zaufanym usługom firmy Microsoft** na dostęp do tego konta magazynu w ustawieniach zapory usługi Azure Storage i chcesz połączyć się ze środowiskiem pamięci masowej Usługi Data Lake Gen2, musisz użyć [uwierzytelniania tożsamości zarządzanej](#managed-identity) dla usługi ADLS Gen2.


## <a name="get-started"></a>Rozpoczęcie pracy

>[!TIP]
>Aby zapoznać się z instrukcjami dotyczącymi używania łącznika Data Lake Storage Gen2, zobacz [Ładowanie danych do usługi Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla usługi Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Łącznik usługi Azure Data Lake Storage Gen2 obsługuje następujące typy uwierzytelniania. Szczegółowe informacje można znaleźć w odpowiednich sekcjach:

- [Uwierzytelnianie klucza konta](#account-key-authentication)
- [Uwierzytelnianie jednostki usługi](#service-principal-authentication)
- [Tożsamości zarządzane do uwierzytelniania zasobów platformy Azure](#managed-identity)

>[!NOTE]
>Podczas korzystania z PolyBase do ładowania danych do magazynu danych SQL, jeśli źródło Data Lake Storage Gen2 jest skonfigurowany z punktem końcowym sieci wirtualnej, należy użyć uwierzytelnienia tożsamości zarządzanej zgodnie z wymaganiami PolyBase. Zobacz sekcję [uwierzytelniania tożsamości zarządzanej](#managed-identity) z więcej wymagań wstępnych konfiguracji.

### <a name="account-key-authentication"></a>Uwierzytelnianie klucza konta

Aby użyć uwierzytelniania klucza konta magazynu, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **AzureBlobFS**. |Tak |
| url | Punkt końcowy dla data lake storage `https://<accountname>.dfs.core.windows.net`gen2 ze wzorcem . | Tak |
| accountKey (Klucz konta) | Klucz konta dla usługi Data Lake Storage Gen2. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) |Tak |
| connectVia | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska uruchomieniowego integracji hostowanego samodzielnie, jeśli magazyn danych znajduje się w sieci prywatnej. Jeśli ta właściwość nie jest określona, używany jest domyślny środowiska wykonawczego integracji platformy Azure. |Nie |

>[!NOTE]
>Pomocniczy punkt końcowy systemu plików ADLS nie jest obsługiwany podczas korzystania z uwierzytelniania klucza konta. Można użyć innych typów uwierzytelniania.

**Przykład:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Aby użyć uwierzytelniania jednostkowego usługi, wykonaj następujące kroki.

1. Zarejestruj jednostkę aplikacji w usłudze Azure Active Directory (Azure AD), wykonując kroki opisane w [Rejestrze aplikacji u dzierżawy usługi Azure AD.](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) Zanotuj następujące wartości, których używasz do definiowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Udzielić jednostki usługi odpowiednie uprawnienia. Zobacz przykłady działania uprawnień w programie Data Lake Storage Gen2 z [list kontroli dostępu w plikach i katalogach](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Jako źródło**: W Eksploratorze magazynu przyznaj co najmniej uprawnienie **Wykonywanie** dla wszystkich folderów nadrzędnych i systemu plików, wraz z uprawnieniem **Do odczytu** dla plików do kopiowania. Alternatywnie w kontroli dostępu (IAM), przyznać co najmniej **roli czytnika danych obiektów blob magazynu.**
    - **Jako ujście:** W Eksploratorze magazynu przyznaj co najmniej uprawnienie **Wykonywanie** dla wszystkich folderów nadrzędnych i systemu plików, wraz z uprawnieniem **Zapis** dla folderu ujścia. Alternatywnie w kontroli dostępu (IAM), przyznać co najmniej **roli współautora danych obiektów blob magazynu.**

>[!NOTE]
>Jeśli używasz interfejsu użytkownika fabryki danych do tworzenia, a podmiot usługi nie jest ustawiony z rolą "Czytnik danych/współautor obiektów blob magazynu" w usłudze IAM, podczas wykonywania połączenia testowego lub przeglądania/nawigacji folderów, wybierz opcję "Przetestuj połączenie ze ścieżką pliku" lub "Przeglądaj z określonej ścieżki" i określ ścieżkę z uprawnieniem **Odczyt + Wykonywanie,** aby kontynuować.

Te właściwości są obsługiwane dla połączonej usługi:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **AzureBlobFS**. |Tak |
| url | Punkt końcowy dla data lake storage `https://<accountname>.dfs.core.windows.net`gen2 ze wzorcem . | Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole `SecureString` jako bezpieczne przechowywanie w fabryce danych. Możesz też [odwołać się do klucza tajnego przechowywanego w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| Dzierżawy | Określ informacje o dzierżawie (nazwa domeny lub identyfikator dzierżawy), w którym znajduje się aplikacja. Pobierz go, najeżdżając myszą w prawym górnym rogu witryny Azure portal. | Tak |
| connectVia | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska uruchomieniowego integracji hostowanego samodzielnie, jeśli magazyn danych znajduje się w sieci prywatnej. Jeśli nie zostanie określony, używany jest domyślny środowiska wykonawczego integracji platformy Azure. |Nie |

**Przykład:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Tożsamości zarządzane do uwierzytelniania zasobów platformy Azure

Fabryka danych może być skojarzona z [tożsamością zarządzaną dla zasobów platformy Azure,](data-factory-service-identity.md)która reprezentuje tę fabrykę określonych danych. Tej tożsamości zarządzanej można bezpośrednio użyć do uwierzytelniania Usługi Data Lake Storage Gen2, podobnie jak przy użyciu własnej jednostki usługi. Umożliwia to wyznaczonej fabryce dostęp i kopiowanie danych do lub z usługi Data Lake Storage Gen2.

Aby użyć tożsamości zarządzanych do uwierzytelniania zasobów platformy Azure, wykonaj następujące kroki.

1. [Pobierz informacje o tożsamości zarządzanej przez fabrykę danych,](data-factory-service-identity.md#retrieve-managed-identity) kopiując wartość **identyfikatora obiektu tożsamości zarządzanej** wygenerowanej wraz z fabryką.

2. Udziel tożsamości zarządzanej odpowiednie uprawnienia. Zobacz przykłady działania uprawnień w programie Data Lake Storage Gen2 z [list kontroli dostępu w plikach i katalogach.](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Jako źródło**: W Eksploratorze magazynu przyznaj co najmniej uprawnienie **Wykonywanie** dla wszystkich folderów nadrzędnych i systemu plików, wraz z uprawnieniem **Do odczytu** dla plików do kopiowania. Alternatywnie w kontroli dostępu (IAM), przyznać co najmniej **roli czytnika danych obiektów blob magazynu.**
    - **Jako ujście:** W Eksploratorze magazynu przyznaj co najmniej uprawnienie **Wykonywanie** dla wszystkich folderów nadrzędnych i systemu plików, wraz z uprawnieniem **Zapis** dla folderu ujścia. Alternatywnie w kontroli dostępu (IAM), przyznać co najmniej **roli współautora danych obiektów blob magazynu.**

>[!NOTE]
>Jeśli używasz interfejsu użytkownika fabryki danych do tworzenia, a tożsamość zarządzana nie jest ustawiona z rolą "Czytnik danych/współautor obiektów blob magazynu" w usłudze IAM, podczas wykonywania połączenia testowego lub przeglądania/nawigacji folderów, wybierz opcję "Przetestuj połączenie ze ścieżką pliku" lub "Przeglądaj z określonej ścieżki" i określ ścieżkę z uprawnieniem **Odczyt + Wykonywanie,** aby kontynuować.

>[!IMPORTANT]
>Jeśli używasz PolyBase do ładowania danych z usługi Data Lake Storage Gen2 do usługi SQL Data Warehouse, podczas korzystania z uwierzytelniania tożsamości zarządzanej dla usługi Data Lake Storage Gen2, upewnij się, że należy wykonać kroki 1 i 2 w [tych wskazówkach](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) do 1) zarejestrować serwer bazy danych SQL z usługi Azure Active Directory (Azure AD) i 2) przypisać rolę współautora danych obiektów magazynu do serwera bazy danych SQL; pozostałe są obsługiwane przez fabrykę danych. Jeśli usługa Data Lake Storage Gen2 jest skonfigurowana z punktem końcowym usługi Azure Virtual Network, aby załadować z niego usługę PolyBase, należy użyć uwierzytelnienia tożsamości zarządzanej zgodnie z wymaganiami polybase.

Te właściwości są obsługiwane dla połączonej usługi:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **AzureBlobFS**. |Tak |
| url | Punkt końcowy dla data lake storage `https://<accountname>.dfs.core.windows.net`gen2 ze wzorcem . | Tak |
| connectVia | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska uruchomieniowego integracji hostowanego samodzielnie, jeśli magazyn danych znajduje się w sieci prywatnej. Jeśli nie zostanie określony, używany jest domyślny środowiska wykonawczego integracji platformy Azure. |Nie |

**Przykład:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [Zestawy danych](concepts-datasets-linked-services.md).

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla usługi Data `location` Lake Storage Gen2 w ustawieniach w zestawie danych opartym na formacie:

| Właściwość   | Opis                                                  | Wymagany |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Właściwość typu `location` w zestawie danych musi być ustawiona na **AzureBlobFSLocation**. | Tak      |
| system plików | Nazwa systemu plików Data Lake Storage Gen2.                              | Nie       |
| folderPath | Ścieżka do folderu w danym systemie plików. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania folderów, pomiń to ustawienie i określ je w ustawieniach źródła aktywności. | Nie       |
| fileName   | Nazwa pliku pod danym plikiemSystem + folderPath. Jeśli chcesz użyć symbolu wieloznacznego do filtrowania plików, pomiń to ustawienie i określ je w ustawieniach źródła aktywności. | Nie       |

**Przykład:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Kopiowanie konfiguracji działań](copy-activity-overview.md#configuration) oraz [Potoki i działania](concepts-pipelines-activities.md). Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujście magazynu usługi Data Lake.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Usługa Azure Data Lake Storage Gen2 jako typ źródła

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla usługi Data `storeSettings` Lake Storage Gen2 w ustawieniach w źródle kopiowania opartym na formacie:

| Właściwość                 | Opis                                                  | Wymagany                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Właściwość typu `storeSettings` w obszarze musi być ustawiona na **AzureBlobFSReadSettings**. | Tak                                           |
| Cykliczne                | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. Gdy rekursywny jest ustawiony na true i zlew jest magazyn oparty na plikach, pusty folder lub podfolder nie jest kopiowany lub tworzony w zlewie. Dozwolone wartości są **prawdziwe** (domyślnie) i **false**. | Nie                                            |
| Ścieżka wieloznacznaFolderPath       | Ścieżka folderu ze znakami wieloznaczami w ramach danego systemu plików skonfigurowanego w zestawie danych do filtrowania folderów źródłowych. <br>Dozwolone symbole wieloznaczne to `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku). Użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. <br>Zobacz więcej przykładów w [przykładach filtru folderów i plików](#folder-and-file-filter-examples). | Nie                                            |
| symboli wieloznacznychFileName         | Nazwa pliku ze znakami wieloznacznymi w ramach danego systemu plików + folderPath/wildcardFolderPath do filtrowania plików źródłowych. <br>Dozwolone symbole wieloznaczne to `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku). Użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki wewnątrz. Zobacz więcej przykładów w [przykładach filtru folderów i plików](#folder-and-file-filter-examples). | Tak, `fileName` jeśli nie jest określony w zestawie danych |
| modifiedDatetimeStart    | Filtr plików na podstawie atrybutu Ostatnia modyfikacja. Pliki są wybierane, jeśli ich ostatni zmodyfikowany `modifiedDatetimeStart` `modifiedDatetimeEnd`czas mieści się w zakresie czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br> Właściwości mogą być NULL, co oznacza, że żaden filtr atrybutu pliku nie jest stosowany do zestawu danych. Gdy `modifiedDatetimeStart` ma wartość datetime, ale `modifiedDatetimeEnd` jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa datetime wartość są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość datetime, ale `modifiedDatetimeStart` jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime są zaznaczone. | Nie                                            |
| modifiedDatetimeEnd      | Tak samo jak powyżej.                                               | Nie                                            |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem magazynowym jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie                                            |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
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
                    "type": "AzureBlobFSReadSettings",
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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Usługa Azure Data Lake Storage Gen2 jako typ ujścia

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Następujące właściwości są obsługiwane dla usługi Data `storeSettings` Lake Storage Gen2 w ustawieniach w umywalka kopii oparta na formacie:

| Właściwość                 | Opis                                                  | Wymagany |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Właściwość typu `storeSettings` w obszarze musi być ustawiona na **AzureBlobFSWriteSettings**. | Tak      |
| copyBehavior             | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartych na plikach.<br/><br/>Dozwolone wartości to:<br/><b>- PreserveHierarchy (domyślnie):</b>Zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest identyczna ze ścieżką względną pliku docelowego do folderu docelowego.<br/><b>- FlattenHierarchy:</b>Wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie generowane nazwy. <br/><b>- MergeFiles:</b>Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określona, scalona nazwa pliku jest określoną nazwą. W przeciwnym razie jest to nazwa pliku z automatycznym generacją. | Nie       |
| blockSizeInMB | Określ rozmiar bloku w MB używany do zapisywania danych w ADLS Gen2. Dowiedz się więcej [o blokowych obiektach blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Dozwolona wartość wynosi **od 4 do 100 MB**. <br/>Domyślnie podajnik ADF automatycznie określa rozmiar bloku na podstawie typu i danych magazynu źródłowego. W przypadku kopii niebinarnej do ADLS Gen2 domyślny rozmiar bloku wynosi 100 MB, aby zmieścić się w co najwyżej 4,95 TB danych. Może nie być optymalne, gdy dane nie są duże, zwłaszcza w przypadku korzystania z self-hosted Integration Runtime ze słabą siecią, co powoduje limit czasu operacji lub problem z wydajnością. Można jawnie określić rozmiar bloku, podczas gdy upewnij się, blockSizeInMB * 50000 jest wystarczająco duży, aby przechowywać dane, w przeciwnym razie uruchomienie działania kopiowania zakończy się niepowodzeniem. | Nie |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem danych jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie       |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
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
                    "type": "AzureBlobFSWriteSettings",
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

### <a name="some-recursive-and-copybehavior-examples"></a>Niektóre przykłady cykliczne i copyBehavior

W tej sekcji opisano wynikowe zachowanie operacji kopiowania dla różnych kombinacji wartości cyklicznych i copyBehavior.

| Cykliczne | copyBehavior | Struktura folderów źródłowych | Wynikujący cel |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy1 jest tworzony z taką samą strukturą jak źródło:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 |
| true |spłaszczyćHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku2<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku3<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku4<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku5 |
| true |scalanie plików | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Zawartość File1 + File2 + File3 + File4 + File5 są scalane w jeden plik o automatycznie zogenerowanej nazwie pliku. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/>Podfolder1 z plikami File3, File4 i File5 nie jest odbierany. |
| false |spłaszczyćHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;autogenerowana nazwa pliku2<br/><br/>Podfolder1 z plikami File3, File4 i File5 nie jest odbierany. |
| false |scalanie plików | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5 | Folder docelowy1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Zawartość file1 + File2 jest scalana w jeden plik o automatycznie generowanej nazwie pliku. autogenerowana nazwa pliku1<br/><br/>Podfolder1 z plikami File3, File4 i File5 nie jest odbierany. |

## <a name="preserve-metadata-during-copy"></a>Zachowywanie metadanych podczas kopiowania

Podczas kopiowania plików z amazon S3/Azure Blob/Azure Data Lake Storage Gen2 do usługi Azure Data Lake Storage Gen2/Azure Blob, można zachować metadane pliku wraz z danymi. Dowiedz się więcej z [tematu Zachowywanie metadanych](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a>Zachowywanie list ACL z pamięci masowej Data Lake Gen1/Gen2

Podczas kopiowania plików z usługi Azure Data Lake Storage Gen1/Gen2 do Gen2, można zachować listy kontroli dostępu POSIX (Listy kontroli dostępu) wraz z danymi. Dowiedz się więcej od [zachowaj listy ACL od danych Lake Storage Gen1/Gen2 do Gen2](copy-activity-preserve-metadata.md#preserve-acls).

>[!TIP]
>Aby skopiować dane z usługi Azure Data Lake Storage Gen1 do gen2 w ogóle, zobacz [Kopiowanie danych z usługi Azure Data Lake Storage Gen1 do Gen2 z usługi Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) dla przejścia i najlepszych rozwiązań.

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Podczas przekształcania danych w przepływie danych mapowania można odczytywać i zapisywać pliki z usługi Azure Data Lake Storage Gen2 w formacie JSON, Avro, Delimited Text lub Parquet. Aby uzyskać więcej informacji, zobacz [transformację źródła](data-flow-source.md) i [transformację ujścia](data-flow-sink.md) w funkcji przepływu danych mapowania.

### <a name="source-transformation"></a>Transformacja źródła

W transformacji źródła można odczytać z kontenera, folderu lub pojedynczego pliku w usłudze Azure Data Lake Storage Gen2. Karta **Opcje źródła** umożliwia zarządzanie odczytem plików. 

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

W transformacji ujścia można zapisać do kontenera lub folderu w usłudze Azure Data Lake Storage Gen2. Karta **Ustawienia** umożliwia zarządzanie sposóbem pisania plików.

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
| type | Właściwość typu zestawu danych musi być ustawiona na **AzureBlobFSFile**. |Tak |
| folderPath | Ścieżka do folderu w aplikacji Data Lake Storage Gen2. Jeśli nie zostanie określony, wskazuje na katalog główny. <br/><br/>Filtr symboli wieloznacznych jest obsługiwany. Dozwolone symbole wieloznaczne to `*` (dopasowuje zero lub więcej znaków) i `?` (pasuje do zera lub pojedynczego znaku). Użyj, `^` aby uciec, jeśli rzeczywista nazwa folderu ma symbol wieloznaczny lub ten znak ucieczki jest wewnątrz. <br/><br/>Przykłady: system plików/folder/. Zobacz więcej przykładów w [przykładach filtru folderów i plików](#folder-and-file-filter-examples). |Nie |
| fileName | Nazwa lub symbol wieloznaczny filtr dla plików w ramach określonego "folderPath". Jeśli nie określisz wartości dla tej właściwości, zestaw danych wskazuje wszystkie pliki w folderze. <br/><br/>W przypadku filtru dozwolone są `*` symbole wieloznaczne `?` (dopasowywuj zero lub więcej znaków) i (dopasowuje zero lub pojedynczy znak).<br/>- Przykład 1:`"fileName": "*.csv"`<br/>- Przykład 2:`"fileName": "???20180427.txt"`<br/>Użyj, `^` aby uciec, jeśli rzeczywista nazwa pliku ma symbol wieloznaczny lub ten znak ucieczki jest wewnątrz.<br/><br/>Jeśli nazwa pliku nie jest określona dla wyjściowego zestawu danych i **preserveHierarchy** nie jest określony w ujściu działania, działanie kopiowania automatycznie generuje nazwę pliku z następującym wzorcem: "*Dane.[ identyfikator GUID uruchomienia działania]. [GUID jeśli FlattenHierarchy]. [format, jeśli jest skonfigurowany]. [kompresja, jeśli jest skonfigurowana]*", na przykład "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". W przypadku kopiowania ze źródła tabelaryczne przy użyciu nazwy tabeli zamiast kwerendy wzorzec nazwy to "*[nazwa tabeli].[ w formacie]. [kompresja, jeśli jest skonfigurowana]*", na przykład "MyTable.csv". |Nie |
| modifiedDatetimeStart | Filtr plików na podstawie atrybutu Ostatnia modyfikacja. Pliki są wybierane, jeśli ich ostatni zmodyfikowany `modifiedDatetimeStart` `modifiedDatetimeEnd`czas mieści się w zakresie czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Na ogólną wydajność przenoszenia danych ma wpływ włączenie tego ustawienia, gdy chcesz wykonać filtr plików z ogromną ilością plików. <br/><br/> Właściwości mogą być NULL, co oznacza, że do zestawu danych nie jest stosowany filtr atrybutów pliku. Gdy `modifiedDatetimeStart` ma wartość datetime, ale `modifiedDatetimeEnd` jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa datetime wartość są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość datetime, ale `modifiedDatetimeStart` jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime są zaznaczone.| Nie |
| modifiedDatetimeEnd | Filtr plików na podstawie atrybutu Ostatnia modyfikacja. Pliki są wybierane, jeśli ich ostatni zmodyfikowany `modifiedDatetimeStart` `modifiedDatetimeEnd`czas mieści się w zakresie czasu między i . Czas jest stosowany do strefy czasowej UTC w formacie "2018-12-01T05:00:00Z". <br/><br/> Na ogólną wydajność przenoszenia danych ma wpływ włączenie tego ustawienia, gdy chcesz wykonać filtr plików z ogromną ilością plików. <br/><br/> Właściwości mogą być NULL, co oznacza, że do zestawu danych nie jest stosowany filtr atrybutów pliku. Gdy `modifiedDatetimeStart` ma wartość datetime, ale `modifiedDatetimeEnd` jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest większa lub równa datetime wartość są zaznaczone. Gdy `modifiedDatetimeEnd` ma wartość datetime, ale `modifiedDatetimeStart` jest null, oznacza to, że pliki, których ostatni zmodyfikowany atrybut jest mniejsza niż wartość datetime są zaznaczone.| Nie |
| format | Jeśli chcesz skopiować pliki, tak jak między magazynami opartymi na plikach (kopia binarna), pomiń sekcję formatu w definicjach wejściowych i wyjściowych zestawów danych.<br/><br/>Jeśli chcesz przeanalizować lub wygenerować pliki w określonym formacie, obsługiwane są następujące typy formatów plików: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**i **ParquetFormat**. Ustaw właściwość **typu** w **formacie** na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [sekcje Format tekstu,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [Format JSON,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [Format Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [FORMAT ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format)i [Format parkietu.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Nie (tylko w przypadku scenariusza kopiowania binarnego) |
| kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [Obsługiwane formaty plików i kodeki kompresji](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Obsługiwane typy to **GZip**, **Deflate**, **BZip2**i **ZipDeflate**.<br/>Obsługiwane poziomy są **optymalne** i **najszybsze.** |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ tylko **folderPath.**<br>Aby skopiować pojedynczy plik o podanej nazwie, określ **folderPath** z częścią folderu i **nazwą pliku** o nazwie pliku.<br>Aby skopiować podzbiór plików w folderze, określ **folderPath** z częścią folderu i **fileName** z filtrem symboli wieloznacznych. 

**Przykład:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
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
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na **AzureBlobFSSource**. |Tak |
| Cykliczne | Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. Gdy rekursywny jest ustawiony na true i zlew jest magazyn oparty na plikach, pusty folder lub podfolder nie jest kopiowany lub tworzony w zlewie.<br/>Dozwolone wartości są **prawdziwe** (domyślnie) i **false**. | Nie |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem danych jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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
                "type": "AzureBlobFSSource",
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
| type | Właściwość typu ujścia działania kopiowania musi być ustawiona na **AzureBlobFSSink**. |Tak |
| copyBehavior | Definiuje zachowanie kopiowania, gdy źródłem są pliki z magazynu danych opartych na plikach.<br/><br/>Dozwolone wartości to:<br/><b>- PreserveHierarchy (domyślnie):</b>Zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest identyczna ze ścieżką względną pliku docelowego do folderu docelowego.<br/><b>- FlattenHierarchy:</b>Wszystkie pliki z folderu źródłowego znajdują się na pierwszym poziomie folderu docelowego. Pliki docelowe mają automatycznie generowane nazwy. <br/><b>- MergeFiles:</b>Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku jest określona, scalona nazwa pliku jest określoną nazwą. W przeciwnym razie jest to nazwa pliku z automatycznym generacją. | Nie |
| maxConcurrentConnections (Połączenie maksymalne) | Liczba połączeń do łączenia się z magazynem danych jednocześnie. Określ tylko wtedy, gdy chcesz ograniczyć jednoczesne połączenie z magazynem danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w fabryce danych, zobacz [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
