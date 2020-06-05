---
title: Kopiuj dane z/do śniegu
description: Dowiedz się, jak kopiować dane z i do płatnych śniegów przy użyciu Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: fd067a0a0e99291dd4cea924a15c1157bc3b9f9f
ms.sourcegitcommit: c052c99fd0ddd1171a08077388d221482026cd58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84425502"
---
# <a name="copy-data-from-and-to-snowflake-by-using-azure-data-factory"></a>Kopiuj dane z i do płatne za pomocą Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób używania działania kopiowania w programie Azure Data Factory do kopiowania danych z i do płatka śniegu. Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik płatnego śniegu jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną tabelą macierzy źródłowej/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

W przypadku działania kopiowania ten łącznik płatny śnieg obsługuje następujące funkcje:

- Skopiuj dane z płatnych śniegów, które wykorzystują kopię płatną śniegu [do polecenia [Location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) , aby osiągnąć najlepszą wydajność.
- Skopiuj dane do płatnych śniegów, które wykorzystują kopiowanie płatne [do [Table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) , aby osiągnąć najlepszą wydajność. Obsługuje ona płatki śnieg na platformie Azure.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które definiują Data Factory jednostek specyficznych dla łącznika płatka.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla połączonej usługi płatnej śniegu:

| Właściwość         | Opis                                                  | Wymagane |
| :--------------- | :----------------------------------------------------------- | :------- |
| typ             | Właściwość Type musi mieć wartość **Płatne**.              | Tak      |
| Parametry połączenia | Skonfiguruj [pełną nazwę konta](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) (w tym dodatkowe segmenty, które identyfikują region i platformę w chmurze), nazwę użytkownika, hasło, bazę danych i magazyn. Określ parametry połączenia JDBC, aby połączyć się z wystąpieniem płatnego śniegu. Możesz również wprowadzić hasło w Azure Key Vault. Zapoznaj się z przykładami poniżej tabeli i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów.| Tak      |
| Właściwością connectvia       | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub własnego środowiska Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. | Nie       |

**Przykład:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&password=<password>&db=<database>&warehouse=<warehouse>(optional)"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Hasło w Azure Key Vault:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>(optional)",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                }, 
                "secretName": "<secretName>"
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

Następujące właściwości są obsługiwane dla zestawu danych płatka śniegu:

| Właściwość  | Opis                                                  | Wymagane                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| typ      | Właściwość Type zestawu danych musi być ustawiona na **Płatne**. | Tak                         |
| schematy | Nazwa schematu. |Nie dla źródła, tak dla ujścia  |
| tabela | Nazwa tabeli/widoku. |Nie dla źródła, tak dla ujścia  |

**Przykład:**

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości, które są obsługiwane przez źródło i ujścia płatnych śniegu.

### <a name="snowflake-as-the-source"></a>Płatka śniegu jako źródło

Łącznik płatny śniegu wykorzystuje kopię płatną śniegu w poleceniu [[Location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) poniżej, aby osiągnąć najlepszą wydajność.

* Jeśli magazyn i format danych ujścia są natywnie obsługiwane przez polecenie COPY płatka śniegu, można użyć działania kopiowania do bezpośredniego skopiowania z płatnej do ujścia. Aby uzyskać szczegółowe informacje, zobacz [Direct Copy from płatne](#direct-copy-from-snowflake).
* W przeciwnym razie użyj wbudowanej [kopii przygotowanej z płatka śniegu](#staged-copy-from-snowflake).

Aby skopiować dane z płatnych śniegów, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość                     | Opis                                                  | Wymagane |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| typ                         | Właściwość Type źródła działania Copy musi być ustawiona na wartość **SnowflakeSource**. | Tak      |
| query          | Określa zapytanie SQL służące do odczytywania danych z płatki śniegu. | Nie       |
| exportSettings | Ustawienia zaawansowane używane do pobierania danych z płatki śniegu. Można skonfigurować obsługiwane przez kopiowanie do polecenia, które zostanie przekazane przez ADF, gdy wywoła instrukcję. | Nie       |
| ***W obszarze `exportSettings` :*** |  |  |
| typ | Typ polecenia eksportu, ustawiony na **SnowflakeExportCopyCommand**. | Tak |
| additionalCopyOptions | Dodatkowe opcje kopiowania, które są dostępne jako słownik par klucz-wartość. Przykłady: MAX_FILE_SIZE, Zastąp. Dowiedz się więcej z [opcji kopiowania płatnych śniegu](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions). | Nie |
| additionalFormatOptions | Dodatkowe opcje formatu pliku dostępne do kopiowania polecenia, dostarczone jako słownik par klucz-wartość. Przykłady: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Dowiedz się więcej z [opcji typu format płatne](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions). | Nie |

#### <a name="direct-copy-from-snowflake"></a>Bezpośrednia kopia z płatki śnieg

Jeśli magazyn danych ujścia i format spełniają kryteria opisane w tej sekcji, można użyć działania kopiowania do bezpośredniego kopiowania z płatnych do ujścia. Azure Data Factory sprawdza ustawienia i uruchamia działanie kopiowania w przypadku niespełnienia kryteriów.

1. **Połączona usługa ujścia** to typ [**obiektu blob platformy Azure**](connector-azure-blob-storage.md) z uwierzytelnianiem **sygnatury dostępu współdzielonego** .

2. **Format danych ujścia** jest **Parquet** lub **rozdzielany tekstem**z następującymi konfiguracjami:

   - W przypadku formatu **Parquet** kompresji koder-dekoder ma **wartość None**, **przyciąganie**lub **LZO**.
   - Format **tekstu rozdzielanego** :
     - `rowDelimiter`jest **\r\n**lub pojedynczym znakiem.
     - `compression`nie może to być **kompresja**, **gzip**, **bzip2**lub **Wklęśnięcie**.
     - `encodingName`jest pozostawiony jako domyślny lub ustawiony na **UTF-8**.
     - `quoteChar`jest **podwójnym cudzysłowem**, **pojedynczym cudzysłowem** lub **pustym ciągiem** (bez cudzysłowu).
3. W źródle działania kopiowania `additionalColumns` nie określono.
4. Nie określono mapowania kolumn.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>Przygotowana kopia z płatka śniegu

Gdy magazyn danych ujścia lub format nie jest natywnie zgodny z poleceniem COPY płatne za pomocą polecenia Kopiuj, jak wspomniano w ostatniej sekcji, Włącz wbudowaną kopię etapową za pośrednictwem tymczasowego wystąpienia usługi Azure Blob Storage. Funkcja kopiowania etapowego zapewnia lepszą przepływność — Data Factory eksportuje dane z płatnej ilości śniegu do magazynu przemieszczania, a następnie kopiuje dane do ujścia. na koniec czyści dane tymczasowe z magazynu przemieszczania. Aby uzyskać szczegółowe informacje o kopiowaniu danych za pośrednictwem przemieszczania, zobacz [przygotowane kopie](copy-activity-performance-features.md#staged-copy) .

Aby użyć tej funkcji, Utwórz [połączoną usługę azure BLOB Storage](connector-azure-blob-storage.md#linked-service-properties) , która odwołuje się do konta usługi Azure Storage jako tymczasowego przemieszczania. Następnie określ `enableStaging` właściwości i `stagingSettings` w działaniu kopiowania.

> [!NOTE]
>
> Połączona usługa Azure Blob Storage musi korzystać z uwierzytelniania sygnatury dostępu współdzielonego, zgodnie z wymaganiami przez polecenie COPY płatne. 

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
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

### <a name="snowflake-as-sink"></a>Płatka śniegu jako ujścia

Łącznik płatnego śniegu wykorzystuje kopię płatną śniegu w poleceniu [[Table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) poniżej, aby osiągnąć najlepszą wydajność. Obsługuje zapisywanie danych w postaci śniegu na platformie Azure.

* Jeśli źródłowy magazyn danych i format są natywnie obsługiwane przez polecenie kopiowania śniegu, można użyć działania kopiowania do bezpośredniego skopiowania ze źródła do płaty śniegu. Aby uzyskać szczegółowe informacje, zobacz [Direct Copy to płatne](#direct-copy-to-snowflake).
* W przeciwnym razie użyj wbudowanej [kopii przygotowanej do śniegu](#staged-copy-to-snowflake).

Aby skopiować dane do płatnych śniegów, w sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość          | Opis                                                  | Wymagane                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| typ              | Właściwość Type ujścia działania Copy musi być ustawiona na wartość **SnowflakeSink**. | Tak                                           |
| preCopyScript     | Określ zapytanie SQL dla działania kopiowania, które ma zostać uruchomione przed zapisaniem danych do śniegu w każdym przebiegu. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. | Nie                                            |
| importSettings | *Ustawienia zaawansowane służące do zapisywania danych w płatki śniegu. Można skonfigurować obsługiwane przez kopiowanie do polecenia, które zostanie przekazane przez ADF, gdy wywoła instrukcję.* | *Nie* |
| ***W obszarze `importSettings` :*** |                                                              |  |
| typ | Typ polecenia importowania, ustawiony na **SnowflakeImportCopyCommand**. | Tak |
| additionalCopyOptions | Dodatkowe opcje kopiowania, które są dostępne jako słownik par klucz-wartość. Przykłady: ON_ERROR, FORCE, LOAD_UNCERTAIN_FILES. Dowiedz się więcej z [opcji kopiowania płatnych śniegu](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions). | Nie |
| additionalFormatOptions | Dodatkowe opcje formatu pliku dostępne do kopiowania polecenia, dostarczone jako słownik par klucz-wartość. Przykłady: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Dowiedz się więcej z [opcji typu format płatne](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions). | Nie |

#### <a name="direct-copy-to-snowflake"></a>Bezpośrednia kopia na płatne

Jeśli źródłowy magazyn danych i format spełniają kryteria opisane w tej sekcji, możesz użyć działania kopiowania, aby bezpośrednio skopiować dane ze źródła do płatnego dnia. Azure Data Factory sprawdza ustawienia i uruchamia działanie kopiowania w przypadku niespełnienia kryteriów.

1. **Połączona usługa** jest usługą [**Azure Blob**](connector-azure-blob-storage.md) Type z uwierzytelnianiem **sygnatury dostępu współdzielonego** .

2. **Format danych źródłowych** jest **Parquet** lub **rozdzielany tekstem** z następującymi konfiguracjami:

   - W przypadku formatu **Parquet** kompresji koder-dekoder ma **wartość Brak**lub **przyciąganie**.

   - Format **tekstu rozdzielanego** :
     - `rowDelimiter`jest **\r\n**lub pojedynczym znakiem. Jeśli ogranicznik wiersza nie jest "\r\n", `firstRowAsHeader` musi mieć **wartość false**i `skipLineCount` nie został określony.
     - `compression`nie może to być **kompresja**, **gzip**, **bzip2**lub **Wklęśnięcie**.
     - `encodingName`jest pozostawiony jako domyślny lub ustawiony na wartość "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "BIG5", "EUC-JP", "EUC-KR", "GB18030", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "Windows-1250", "Windows-1251", "Windows-1252", "Windows-1253", "Windows-1254"
     - `quoteChar`jest **podwójnym cudzysłowem**, **pojedynczym cudzysłowem** lub **pustym ciągiem** (bez cudzysłowu).

3. W źródle działania kopiowania, 

   -  `additionalColumns`nie została określona.
   - Jeśli źródło jest folderem, `recursive` należy ustawić na wartość true.
   - `prefix`, `modifiedDateTimeStart` , `modifiedDateTimeEnd` nie są określone.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>Kopia przygotowana do śniegu

Gdy magazyn danych ujścia lub format nie jest natywnie zgodny z poleceniem COPY płatne za pomocą polecenia Kopiuj, jak wspomniano w ostatniej sekcji, Włącz wbudowaną kopię etapową za pośrednictwem tymczasowego wystąpienia usługi Azure Blob Storage. Funkcja kopiowania etapowego zapewnia lepszą przepływność — Data Factory automatycznie konwertuje dane, aby spełniały wymagania dotyczące formatu danych płatnych śniegu. Następnie wywołuje polecenie COPY, aby załadować dane do płatnych śniegów. Na koniec czyści dane tymczasowe z magazynu obiektów BLOB. Aby uzyskać szczegółowe informacje o kopiowaniu danych za pośrednictwem przemieszczania, zobacz [przygotowane kopie](copy-activity-performance-features.md#staged-copy) .

Aby użyć tej funkcji, Utwórz [połączoną usługę azure BLOB Storage](connector-azure-blob-storage.md#linked-service-properties) , która odwołuje się do konta usługi Azure Storage jako tymczasowego przemieszczania. Następnie określ `enableStaging` właściwości i `stagingSettings` w działaniu kopiowania.

> [!NOTE]
>
> Połączona usługa Azure Blob Storage musi korzystać z uwierzytelniania sygnatury dostępu współdzielonego, zgodnie z wymaganiami przez polecenie COPY płatne.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
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


## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki

Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny i formaty danych](copy-activity-overview.md#supported-data-stores-and-formats).
