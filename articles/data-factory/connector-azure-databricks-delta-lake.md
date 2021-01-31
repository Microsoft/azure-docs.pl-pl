---
title: Kopiowanie danych do i z Azure Databricks różnicowa Lake
description: Dowiedz się, jak kopiować dane do i z Azure Databricks delty Lake przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/24/2020
ms.openlocfilehash: e32b93c669bffd382b1eb648111f9b8931b07eac
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221163"
---
# <a name="copy-data-to-and-from-azure-databricks-delta-lake-by-using-azure-data-factory"></a>Kopiuj dane do i z Azure Databricks delty Lake przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób korzystania z działania kopiowania w Azure Data Factory do kopiowania danych do i z Azure Databricks różnicowej usługi Lake. Jest ona oparta na [działaniu kopiowania w Azure Data Factory](copy-activity-overview.md) artykule, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten Azure Databricks różnicowej usługi Lake Analytics jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z obsługiwaną tabelą [źródłową/odujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Ogólnie rzecz biorąc, Azure Data Factory obsługuje Delta Lake z następującymi możliwościami, które są zgodne z różnymi potrzebami.

- Działanie kopiowania obsługuje łącznik Azure Databricks różnicowa Lake do kopiowania danych z dowolnego obsługiwanego magazynu danych źródłowych do Azure Databricks tabeli różnicowej usługi Lake i z tabeli różnicowej usługi Lake do dowolnego obsługiwanego magazynu danych ujścia. W celu przeprowadzenia przenoszenia danych wykorzystuje klaster datakostki, patrz szczegóły w [sekcji wymagania wstępne](#prerequisites).
- [Mapowanie przepływu danych](concepts-data-flow-overview.md) obsługuje rodzajowy [Format różnicowy](format-delta.md) w usłudze Azure Storage jako źródło i ujścia do odczytu i zapisu plików różnicowych dla ETL bez kodu, a także działa na zarządzanych Azure Integration Runtime.
- [Działania związane](transform-data-databricks-notebook.md) z przetwarzaniem danych obsługują organizowanie obciążeń skoncentrowanych na kodzie lub obciążenia uczenia maszynowego na szczycie w usłudze Delta Lake.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego Azure Databricks różnicowego, należy skonfigurować klaster w Azure Databricks.

- Aby skopiować dane do programu Delta Lake, działanie Copy wywołuje Azure Databricks klaster, aby odczytywać dane z usługi Azure Storage, czyli oryginalnego źródła lub obszaru przejściowego, w którym Data Factory po raz pierwsze zapisuje dane źródłowe za pomocą wbudowanej kopii przygotowanej. Dowiedz się więcej od usługi [Delta Lake jako ujścia](#delta-lake-as-sink).
- Podobnie, aby skopiować dane z różnicowej usługi Lake, działanie Copy wywołuje Azure Databricks klaster w celu zapisywania danych w usłudze Azure Storage, czyli oryginalnego ujścia lub obszaru przejściowego, z którego Data Factory nadal zapisywać dane do końcowego ujścia za pośrednictwem wbudowanej kopii przygotowanej. Dowiedz się więcej od usługi [Delta Lake jako źródła](#delta-lake-as-source).

Klaster datakostki musi mieć dostęp do obiektu blob platformy Azure lub konta Azure Data Lake Storage Gen2, kontenera/systemu plików używanego dla źródła/ujścia/przemieszczania oraz kontenera/systemu plików, w którym mają zostać zapisane tabele różnicowych Lake.

- Aby skorzystać z **Azure Data Lake Storage Gen2**, można skonfigurować jednostkę **usługi** lub **klucz dostępu konta magazynu** w klastrze datakostki jako część konfiguracji Apache Spark. Postępuj zgodnie z instrukcjami [dostępnymi bezpośrednio z](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-with-service-principal-and-oauth-20) użyciem jednostki usługi lub [uzyskaj dostęp bezpośrednio przy użyciu klucza dostępu konta magazynu](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-using-the-storage-account-access-key).

- Aby korzystać z **usługi Azure Blob Storage**, można skonfigurować **klucz dostępu konta magazynu** lub **token SAS** w klastrze datakostki jako część konfiguracji Apache Spark. Wykonaj kroki opisane w temacie [dostęp do usługi Azure Blob Storage za pomocą interfejsu API RDD](/azure/databricks/data/data-sources/azure/azure-storage#access-azure-blob-storage-using-the-rdd-api).

W trakcie wykonywania działania kopiowania, jeśli skonfigurowany klaster został zakończony, Data Factory automatycznie go uruchamia. Jeśli tworzysz potok przy użyciu interfejsu użytkownika Data Factory tworzenia, w przypadku operacji takich jak Data Preview, musisz mieć klaster na żywo, Data Factory nie uruchomi klastra w Twoim imieniu.

#### <a name="specify-the-cluster-configuration"></a>Określ konfigurację klastra

1. Z listy rozwijanej **tryb klastra** wybierz pozycję **Standardowy**.

2. Z listy rozwijanej **wersja Databricks Runtime** wybierz wersję środowiska uruchomieniowego datakosteks.

3. Włącz funkcję [automatycznej optymalizacji](/azure/databricks/delta/optimizations/auto-optimize) , dodając następujące właściwości do [konfiguracji platformy Spark](/azure/databricks/clusters/configure#spark-config):

   ```
   spark.databricks.delta.optimizeWrite.enabled true
   spark.databricks.delta.autoCompact.enabled true
   ```

4. Skonfiguruj klaster w zależności od potrzeb związanych z integracją i skalowaniem.

Aby uzyskać szczegółowe informacje o konfiguracji klastra, zobacz [Konfigurowanie klastrów](/azure/databricks/clusters/configure).

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które definiują Data Factory jednostek specyficznych dla łącznika Azure Databricks różnicowego Lake.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla usługi Azure Databricks różnicowa Lake.

| Właściwość    | Opis                                                  | Wymagane |
| :---------- | :----------------------------------------------------------- | :------- |
| typ        | Właściwość Type musi być ustawiona na wartość **AzureDatabricksDeltaLake**. | Tak      |
| domena      | Określ adres URL obszaru Azure Databricks, np. `https://adb-xxxxxxxxx.xx.azuredatabricks.net` . |          |
| clusterId   | Określ identyfikator klastra istniejącego klastra. Powinien to być już utworzony interaktywny klaster. <br>Identyfikator klastra interaktywnego klastra można znaleźć w obszarze roboczym datakosteks — > klastrów — > Interactive Nazwa klastra — > Configuration-> Tagi. [Dowiedz się więcej](/azure/databricks/clusters/configure#cluster-tags). |          |
| accessToken | Token dostępu jest wymagany do uwierzytelniania Data Factory Azure Databricks. Token dostępu musi być wygenerowany z obszaru roboczego datakostki. Bardziej szczegółowe kroki znajdowania tokenu dostępu można znaleźć [tutaj](/azure/databricks/dev-tools/api/latest/authentication#generate-token). |          |
| Właściwością connectvia  | [Środowisko Integration Runtime](concepts-integration-runtime.md) , które jest używane do nawiązywania połączenia z magazynem danych. Możesz użyć środowiska Azure Integration Runtime lub własnego środowiska Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. | Nie       |

**Przykład:**

```json
{
    "name": "AzureDatabricksDeltaLakeLinkedService",
    "properties": {
        "type": "AzureDatabricksDeltaLake",
        "typeProperties": {
            "domain": "https://adb-xxxxxxxxx.xx.azuredatabricks.net",
            "clusterId": "<cluster id>",
            "accessToken": {
                "type": "SecureString", 
                "value": "<access token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . 

Następujące właściwości są obsługiwane dla Azure Databricks różnicowego zestawu danych.

| Właściwość  | Opis                                                  | Wymagane                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| typ      | Właściwość Type zestawu danych musi być ustawiona na wartość **AzureDatabricksDeltaLakeDataset**. | Tak                         |
| database | Nazwa bazy danych. |Nie dla źródła, tak dla ujścia  |
| tabela | Nazwa tabeli różnicowej. |Nie dla źródła, tak dla ujścia  |

**Przykład:**

```json
{
    "name": "AzureDatabricksDeltaLakeDataset",
    "properties": {
        "type": "AzureDatabricksDeltaLakeDataset",
        "typeProperties": {
            "database": "<database name>",
            "table": "<delta table name>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez Azure Databricks i ujścia różnicowych źródeł danych.

### <a name="delta-lake-as-source"></a>Delta Lake jako źródło

Aby skopiować dane z Azure Databricks delty Lake, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość                     | Opis                                                  | Wymagane |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| typ                         | Właściwość Type źródła działania Copy musi być ustawiona na wartość **AzureDatabricksDeltaLakeSource**. | Tak      |
| query          | Określ zapytanie SQL do odczytu danych. W przypadku kontroli podróży czasowej postępuj zgodnie z poniższym wzorcem:<br>- `SELECT * FROM events TIMESTAMP AS OF timestamp_expression`<br>- `SELECT * FROM events VERSION AS OF version` | Nie       |
| exportSettings | Ustawienia zaawansowane używane do pobierania danych z tabeli różnicowej. | Nie       |
| ***W obszarze `exportSettings` :** _ |  |  |
| typ | Typ polecenia eksportu, ustawiony na _ * AzureDatabricksDeltaLakeExportCommand * *. | Tak |
| dateFormat | Sformatuj typ daty na ciąg o formacie daty. Niestandardowe formaty dat są zgodne z formatami we [wzorcu DateTime](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Jeśli nie zostanie określony, zostanie użyta wartość domyślna `yyyy-MM-dd` . | Nie |
| timestampFormat | Sformatuj typ sygnatury czasowej na ciąg z formatem sygnatury czasowej. Niestandardowe formaty dat są zgodne z formatami we [wzorcu DateTime](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Jeśli nie zostanie określony, zostanie użyta wartość domyślna `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | Nie |

#### <a name="direct-copy-from-delta-lake"></a>Bezpośrednia kopia z usługi Delta Lake

Jeśli magazyn danych ujścia i format spełniają kryteria opisane w tej sekcji, można użyć działania kopiowania do bezpośredniego kopiowania z Azure Databricks różnicowej tabeli do ujścia. Data Factory sprawdza ustawienia i nie uruchamia działania kopiowania, jeśli nie spełniono następujących kryteriów:

- **Połączona usługa ujścia** to [Magazyn obiektów blob platformy Azure](connector-azure-blob-storage.md) lub [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md). Poświadczenia konta należy wstępnie skonfigurować w konfiguracji klastra Azure Databricks, dowiedzieć się więcej z [wymagań wstępnych](#prerequisites).

- **Format danych ujścia** to **Parquet**, **rozdzielany tekstem** lub **Avro** z poniższymi konfiguracjami, a następnie wskazuje folder, a nie plik.

    - W przypadku formatu **Parquet** kompresji koder-dekoder ma **Brak**, **przyciąganie** lub **gzip**.
    - Format **tekstu rozdzielanego** :
        - `rowDelimiter` jest pojedynczym znakiem.
        - `compression` może to być **none**, **bzip2**, **gzip**.
        - `encodingName` Kodowanie UTF-7 nie jest obsługiwane.
    - W przypadku formatu **Avro** kompresji koder-dekoder ma **wartość None**, **Wklęśnięcie** lub **przyciąganie**.

- W źródle działania kopiowania `additionalColumns` nie określono.
- W przypadku kopiowania danych do rozdzielanego tekstu, w ujścia działania kopiowania `fileExtension` musi być ". csv".
- W mapowaniu działania kopiowania typ konwersja nie jest włączony.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
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
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-delta-lake"></a>Kopia przygotowana z różnicowego Lake

Jeśli magazyn danych ujścia lub format nie jest zgodny z kryteriami kopiowania bezpośredniego, jak wspomniano w ostatniej sekcji, Włącz wbudowaną kopię etapową przy użyciu tymczasowego wystąpienia usługi Azure Storage. Funkcja kopiowania etapowego zapewnia lepszą przepływność. Data Factory eksportuje dane z Azure Databricks różnicowego do magazynu przemieszczania, a następnie kopiuje dane do ujścia, a wreszcie czyści dane tymczasowe z magazynu przemieszczania. Aby uzyskać szczegółowe informacje o kopiowaniu danych przy użyciu przemieszczania, zobacz [przygotowane kopie](copy-activity-performance-features.md#staged-copy) .

Aby użyć tej funkcji, należy utworzyć [połączoną usługę Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) lub [Azure Data Lake Storage Gen2 połączoną usługę](connector-azure-data-lake-storage.md#linked-service-properties) , która odwołuje się do konta magazynu w ramach tymczasowego przemieszczania. Następnie określ `enableStaging` właściwości i `stagingSettings` w działaniu kopiowania.

>[!NOTE]
>Poświadczenia konta magazynu przemieszczania powinny być wstępnie skonfigurowane w konfiguracji klastra Azure Databricks, Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites).

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
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
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="delta-lake-as-sink"></a>Różnica w usłudze Data Lake jako ujścia

Aby skopiować dane do Azure Databricks delty Lake, w sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość      | Opis                                                  | Wymagane |
| :------------ | :----------------------------------------------------------- | :------- |
| typ          | Właściwość Type ujścia działania Copy ustawiona na wartość **AzureDatabricksDeltaLakeSink**. | Tak      |
| preCopyScript | Określ zapytanie SQL dla działania kopiowania, które ma zostać uruchomione przed zapisaniem danych w tabeli różnicowej datakostki w każdym uruchomieniu. Tej właściwości można użyć do oczyszczenia wstępnie załadowanych danych lub dodania instrukcji TRUNCATE TABLE lub próżniowej. | Nie       |
| importSettings | Ustawienia zaawansowane używane do zapisywania danych w tabeli różnicowej. | Nie |
| **_W obszarze `importSettings` :_* _ |                                                              |  |
| typ | Typ polecenia importowania, ustawiony na _ * AzureDatabricksDeltaLakeImportCommand * *. | Tak |
| dateFormat | Sformatuj ciąg jako typ daty z formatem daty. Niestandardowe formaty dat są zgodne z formatami we [wzorcu DateTime](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Jeśli nie zostanie określony, zostanie użyta wartość domyślna `yyyy-MM-dd` . | Nie |
| timestampFormat | Sformatuj ciąg do typu sygnatury czasowej z formatem sygnatury czasowej. Niestandardowe formaty dat są zgodne z formatami we [wzorcu DateTime](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Jeśli nie zostanie określony, zostanie użyta wartość domyślna `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | Nie |

#### <a name="direct-copy-to-delta-lake"></a>Bezpośrednia kopia do różnicowania Lake

Jeśli źródłowy magazyn danych i format są zgodne z kryteriami opisanymi w tej sekcji, można użyć działania kopiowania do bezpośredniego skopiowania ze źródła do Azure Databricks różnicowa Lake. Azure Data Factory sprawdza ustawienia i nie uruchamia działania kopiowania, jeśli nie spełniono następujących kryteriów:

- **Połączona usługa** jest usługą [Azure Blob Storage](connector-azure-blob-storage.md) lub [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md). Poświadczenia konta należy wstępnie skonfigurować w konfiguracji klastra Azure Databricks, dowiedzieć się więcej z [wymagań wstępnych](#prerequisites).

- **Format danych źródłowych** jest **Parquet**, **rozdzielany tekstem** lub **Avro** z następującymi konfiguracjami, a następnie wskazuje folder, a nie plik.

    - W przypadku formatu **Parquet** kompresji koder-dekoder ma **Brak**, **przyciąganie** lub **gzip**.
    - Format **tekstu rozdzielanego** :
        - `rowDelimiter` jest wartością domyślną lub pojedynczym znakiem.
        - `compression` może to być **none**, **bzip2**, **gzip**.
        - `encodingName` Kodowanie UTF-7 nie jest obsługiwane.
    - W przypadku formatu **Avro** kompresji koder-dekoder ma **wartość None**, **Wklęśnięcie** lub **przyciąganie**.

- W źródle działania kopiowania: 

    - `wildcardFileName` zawiera tylko symbol wieloznaczny `*` , ale nie `?` i `wildcardFolderName` nie został określony.
    - `prefix`, `modifiedDateTimeStart` , `modifiedDateTimeEnd` i `enablePartitionDiscovery` nie są określone.
    - `additionalColumns` nie została określona.

- W mapowaniu działania kopiowania typ konwersja nie jest włączony.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            }
        }
    }
]
```

#### <a name="staged-copy-to-delta-lake"></a>Przygotowana kopia do różnicowania Lake

Jeśli źródłowy magazyn danych lub format nie jest zgodny z kryteriami kopiowania bezpośredniego, jak wspomniano w ostatniej sekcji, Włącz wbudowaną kopię etapową przy użyciu tymczasowego wystąpienia usługi Azure Storage. Funkcja kopiowania etapowego zapewnia lepszą przepływność. Data Factory automatycznie konwertuje dane, aby spełniały wymagania dotyczące formatu danych do magazynu przemieszczania, a następnie ładować dane do usługi Delta Lake. Na koniec czyści dane tymczasowe z magazynu. Aby uzyskać szczegółowe informacje o kopiowaniu danych przy użyciu przemieszczania, zobacz [przygotowana kopia](copy-activity-performance-features.md#staged-copy) .

Aby użyć tej funkcji, należy utworzyć [połączoną usługę Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) lub [Azure Data Lake Storage Gen2 połączoną usługę](connector-azure-data-lake-storage.md#linked-service-properties) , która odwołuje się do konta magazynu w ramach tymczasowego przemieszczania. Następnie określ `enableStaging` właściwości i `stagingSettings` w działaniu kopiowania.

>[!NOTE]
>Poświadczenia konta magazynu przemieszczania powinny być wstępnie skonfigurowane w konfiguracji klastra Azure Databricks, Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites).

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="monitoring"></a>Monitorowanie

Azure Data Factory zapewnia takie samo [środowisko monitorowania działania kopiowania](copy-activity-monitoring.md) jak inne łączniki. Ponadto, ponieważ ładowanie danych z/do Delty Lake jest uruchomione w klastrze Azure Databricks, można [wyświetlić szczegółowe dzienniki klastrów](/azure/databricks/clusters/clusters-manage#--view-cluster-logs) i [monitorować wydajność](/azure/databricks/clusters/clusters-manage#--monitor-performance).

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby uzyskać więcej informacji o właściwościach, zobacz [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki

Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Data Factory można znaleźć w temacie [obsługiwane magazyny i formaty danych](copy-activity-overview.md#supported-data-stores-and-formats).
