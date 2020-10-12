---
title: Kopiowanie i Przekształcanie danych w śniegu
description: Informacje o kopiowaniu i przekształcaniu danych w śniegu przy użyciu Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/28/2020
ms.openlocfilehash: 5bb5599c6ab6e630e0f26c6d4a13e9c9af8a15a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91405177"
---
# <a name="copy-and-transform-data-in-snowflake-by-using-azure-data-factory"></a>Skopiuj i Przekształć dane w śniegu przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób korzystania z działania kopiowania w Azure Data Factory do kopiowania danych z i do płaty śnieg oraz do przekształcania danych w płatki śniegu. Aby uzyskać więcej informacji na temat Data Factory, zobacz [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik płatnego śniegu jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z obsługiwaną tabelą [źródłową/odujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

W przypadku działania kopiowania ten łącznik płatny śnieg obsługuje następujące funkcje:

- Skopiuj dane z płatnych śniegów, które wykorzystują kopiowanie śniegu [do polecenia [Location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) w celu osiągnięcia najlepszej wydajności.
- Skopiuj dane do płatnych śniegów, które wykorzystują kopiowanie płatne [do [Table] w](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) celu uzyskania najlepszej wydajności. Obsługuje ona płatki śnieg na platformie Azure. 

W przypadku korzystania z obszaru roboczego usługi Azure Synapse Analytics jest nieobsługiwany płata śniegu.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które definiują Data Factory jednostek specyficznych dla łącznika płatka.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

W przypadku usługi połączonej z płatnym śniegu są obsługiwane następujące właściwości.

| Właściwość         | Opis                                                  | Wymagane |
| :--------------- | :----------------------------------------------------------- | :------- |
| typ             | Właściwość Type musi mieć wartość **Płatne**.              | Tak      |
| Parametry połączenia | Określa informacje, które są konieczne do nawiązania połączenia z wystąpieniem płatnego śniegu. Możesz wybrać opcję umieszczenia hasła lub całego ciągu połączenia w Azure Key Vault. Aby uzyskać więcej informacji, zapoznaj się z przykładami poniżej tabeli oraz [poświadczeniami sklepu w Azure Key Vault](store-credentials-in-key-vault.md) artykule.<br><br>Niektóre typowe ustawienia:<br>- **Nazwa konta:**  [Pełna nazwa konta](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) Twojego konta płatnego śniegu (w tym dodatkowe segmenty, które identyfikują region i platformę chmury), np. xy12345. wschód-US-2. Azure.<br/>- **Nazwa użytkownika:** Nazwa logowania użytkownika dla połączenia.<br>- **Hasło:** Hasło użytkownika.<br>- **Baza danych:** Domyślna baza danych do użycia po nawiązaniu połączenia. Powinna to być istniejąca baza danych, dla której określona rola ma uprawnienia.<br>- **Magazyn:** Magazyn wirtualny, który ma być używany po nawiązaniu połączenia. Powinien być istniejącym magazynem, dla którego określona rola ma uprawnienia.<br>- **Rola:** Domyślna rola kontroli dostępu do użycia w sesji płatnych śniegu. Określona rola powinna być istniejącą rolą, która została już przypisana do określonego użytkownika. Rola domyślna to PUBLIC. | Tak      |
| Właściwością connectvia       | [Środowisko Integration Runtime](concepts-integration-runtime.md) , które jest używane do nawiązywania połączenia z magazynem danych. Możesz użyć środowiska Azure Integration Runtime lub własnego środowiska Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. | Nie       |

**Przykład:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
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

**Hasło w Azure Key Vault:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
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

Następujące właściwości są obsługiwane dla zestawu danych płatka śniegu.

| Właściwość  | Opis                                                  | Wymagane                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| typ      | Właściwość Type zestawu danych musi być ustawiona na **Płatne**. | Tak                         |
| schema | Nazwa schematu. Należy pamiętać, że w nazwie schematu jest rozróżniana wielkość liter. |Nie dla źródła, tak dla ujścia  |
| tabela | Nazwa tabeli/widoku. Zwróć uwagę na to, że w nazwie tabeli jest rozróżniana wielkość liter. |Nie dla źródła, tak dla ujścia  |

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

Łącznik płatnego śniegu wykorzystuje kopię płatną śniegu w poleceniu [[Location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) , aby osiągnąć najlepszą wydajność.

Jeśli magazyn i format danych ujścia są natywnie obsługiwane przez polecenie COPY płatka śniegu, możesz użyć działania kopiowania, aby bezpośrednio skopiować dane z płatnej do ujścia. Aby uzyskać szczegółowe informacje, zobacz [Direct Copy from płatne](#direct-copy-from-snowflake). W przeciwnym razie użyj wbudowanej [kopii przygotowanej z płatka śniegu](#staged-copy-from-snowflake).

Aby skopiować dane z płatnych śniegów, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość                     | Opis                                                  | Wymagane |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| typ                         | Właściwość Type źródła działania Copy musi być ustawiona na wartość **SnowflakeSource**. | Tak      |
| query          | Określa zapytanie SQL służące do odczytywania danych z płatki śniegu. Jeśli nazwy schematu, tabeli i kolumn zawierają małe litery, należy pomniejszyć identyfikator obiektu w kwerendzie, np. `select * from "schema"."myTable"` .<br>Wykonywanie procedury składowanej nie jest obsługiwane. | Nie       |
| exportSettings | Ustawienia zaawansowane używane do pobierania danych z płatki śniegu. Można skonfigurować te obsługiwane przez KOPIę w poleceniu, które Data Factory zostanie przekazane po wywołaniu instrukcji. | Nie       |
| ***W obszarze `exportSettings` :*** |  |  |
| typ | Typ polecenia eksportu, ustawiony na **SnowflakeExportCopyCommand**. | Tak |
| additionalCopyOptions | Dodatkowe opcje kopiowania, które są dostępne jako słownik par klucz-wartość. Przykłady: MAX_FILE_SIZE, Zastąp. Aby uzyskać więcej informacji, zobacz [Opcje kopiowania płatka śniegu](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions). | Nie |
| additionalFormatOptions | Dodatkowe opcje formatu pliku, które są dostępne do kopiowania polecenia jako słownik par klucz-wartość. Przykłady: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Aby uzyskać więcej informacji, zobacz [Opcje formatu śniegu](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions). | Nie |

#### <a name="direct-copy-from-snowflake"></a>Bezpośrednia kopia z płatki śnieg

Jeśli magazyn danych ujścia i format spełniają kryteria opisane w tej sekcji, możesz użyć działania kopiowania do bezpośredniego kopiowania z płatnych do ujścia. Data Factory sprawdza ustawienia i nie uruchamia działania kopiowania, jeśli nie spełniono następujących kryteriów:

- **Połączona usługa ujścia** to [**Magazyn obiektów blob platformy Azure**](connector-azure-blob-storage.md) z uwierzytelnianiem za pomocą **sygnatury dostępu współdzielonego** .

- **Format danych ujścia** to **Parquet**, **rozdzielany tekstem**lub **JSON** z następującymi konfiguracjami:

    - W przypadku formatu **Parquet** kompresji koder-dekoder ma **wartość None**, **przyciąganie**lub **LZO**.
    - Format **tekstu rozdzielanego** :
        - `rowDelimiter` jest **\r\n**lub pojedynczym znakiem.
        - `compression` nie może to być **kompresja**, **gzip**, **bzip2**lub **Wklęśnięcie**.
        - `encodingName` jest pozostawiony jako domyślny lub ustawiony na **UTF-8**.
        - `quoteChar` jest **podwójnym cudzysłowem**, **pojedynczym cudzysłowem**lub **pustym ciągiem** (bez cudzysłowu).
    - W przypadku formatu **JSON** kopia bezpośrednia obsługuje tylko przypadek, gdy źródłowa tabela płatnych lub wynik zapytania zawiera tylko jedną kolumnę, a typ danych tej kolumny to **Variant**, **Object**lub **Array**.
        - `compression` nie może to być **kompresja**, **gzip**, **bzip2**lub **Wklęśnięcie**.
        - `encodingName` jest pozostawiony jako domyślny lub ustawiony na **UTF-8**.
        - `filePattern` w obszarze ujścia działania kopiowania jest pozostawiona wartość domyślna lub ustawiona na **setOfObjects**.

- W źródle działania kopiowania `additionalColumns` nie określono.
- Nie określono mapowania kolumn.

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
                "sqlReaderQuery": "SELECT * FROM MYTABLE",
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

Gdy magazyn danych ujścia lub format nie jest natywnie zgodny z poleceniem COPY płatne w postaci śniegu, jak wspomniano w ostatniej sekcji, Włącz wbudowaną kopię etapową przy użyciu tymczasowego wystąpienia usługi Azure Blob Storage. Funkcja kopiowania etapowego zapewnia lepszą przepływność. Data Factory eksportuje dane z płatnej śniegu do magazynu przemieszczania, a następnie kopiuje dane do ujścia, a wreszcie czyści dane tymczasowe z magazynu przemieszczania. Aby uzyskać szczegółowe informacje o kopiowaniu danych przy użyciu przemieszczania, zobacz [przygotowane kopie](copy-activity-performance-features.md#staged-copy) .

Aby użyć tej funkcji, Utwórz [połączoną usługę Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) , która odwołuje się do konta usługi Azure Storage jako tymczasowego przemieszczania. Następnie określ `enableStaging` właściwości i `stagingSettings` w działaniu kopiowania.

> [!NOTE]
> Połączona usługa Azure Blob Storage musi używać uwierzytelniania sygnatury dostępu współdzielonego, zgodnie z wymaganiami polecenia COPY płatne. 

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

Łącznik płatnego śniegu wykorzystuje kopię płatną śniegu w poleceniu [[Table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) , aby osiągnąć najlepszą wydajność. Obsługuje zapisywanie danych w postaci śniegu na platformie Azure.

Jeśli źródłowy magazyn danych i format są natywnie obsługiwane przez polecenie kopiowania śniegu, możesz użyć działania kopiowania, aby bezpośrednio skopiować ze źródła do płatnego dnia. Aby uzyskać szczegółowe informacje, zobacz [Direct Copy to płatne](#direct-copy-to-snowflake). W przeciwnym razie użyj wbudowanej [kopii przygotowanej do śniegu](#staged-copy-to-snowflake).

Aby skopiować dane do płatnych śniegów, w sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości.

| Właściwość          | Opis                                                  | Wymagane                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| typ              | Właściwość Type ujścia działania Copy ustawiona na wartość **SnowflakeSink**. | Tak                                           |
| preCopyScript     | Określ zapytanie SQL dla działania kopiowania, które ma zostać uruchomione przed zapisaniem danych do śniegu w każdym przebiegu. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. | Nie                                            |
| importSettings | Ustawienia zaawansowane służące do zapisywania danych w płatki śniegu. Można skonfigurować te obsługiwane przez KOPIę w poleceniu, które Data Factory zostanie przekazane po wywołaniu instrukcji. | Nie |
| ***W obszarze `importSettings` :*** |                                                              |  |
| typ | Typ polecenia importowania, ustawiony na **SnowflakeImportCopyCommand**. | Tak |
| additionalCopyOptions | Dodatkowe opcje kopiowania, które są dostępne jako słownik par klucz-wartość. Przykłady: ON_ERROR, FORCE, LOAD_UNCERTAIN_FILES. Aby uzyskać więcej informacji, zobacz [Opcje kopiowania płatka śniegu](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions). | Nie |
| additionalFormatOptions | Dodatkowe opcje formatu pliku dostarczone do polecenia COPY, dostarczone jako słownik par klucz-wartość. Przykłady: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Aby uzyskać więcej informacji, zobacz [Opcje formatu śniegu](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions). | Nie |

#### <a name="direct-copy-to-snowflake"></a>Bezpośrednia kopia na płatne

Jeśli źródłowy magazyn danych i format spełniają kryteria opisane w tej sekcji, możesz użyć działania kopiowania do bezpośredniego skopiowania ze źródła do płaty śniegu. Azure Data Factory sprawdza ustawienia i nie uruchamia działania kopiowania, jeśli nie spełniono następujących kryteriów:

- **Połączona usługa** jest usługą [**Azure Blob Storage**](connector-azure-blob-storage.md) z uwierzytelnianiem za pomocą **sygnatury dostępu współdzielonego** .

- **Format danych źródłowych** to **Parquet**, **rozdzielany tekstem**lub **JSON** z następującymi konfiguracjami:

    - W przypadku formatu **Parquet** kompresji koder-dekoder ma **wartość Brak**lub **przyciąganie**.

    - Format **tekstu rozdzielanego** :
        - `rowDelimiter` jest **\r\n**lub pojedynczym znakiem. Jeśli ogranicznik wiersza nie jest "\r\n", `firstRowAsHeader` musi mieć **wartość false**i `skipLineCount` nie został określony.
        - `compression` nie może to być **kompresja**, **gzip**, **bzip2**lub **Wklęśnięcie**.
        - `encodingName` jest pozostawiony jako domyślny lub ustawiony na wartość "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "BIG5", "EUC-JP", "EUC-KR", "GB18030", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "Windows-1250", "Windows-1251", "Windows-1252", "Windows-1253", "Windows-1254"
        - `quoteChar` jest **podwójnym cudzysłowem**, **pojedynczym cudzysłowem**lub **pustym ciągiem** (bez cudzysłowu).
    - W przypadku formatu **JSON** kopia bezpośrednia obsługuje tylko przypadek, w którym tylko tabela "ujścia płatne" ma tylko jedną kolumnę, a typ danych tej kolumny to **Variant**, **Object**lub **Array**.
        - `compression` nie może to być **kompresja**, **gzip**, **bzip2**lub **Wklęśnięcie**.
        - `encodingName` jest pozostawiony jako domyślny lub ustawiony na **UTF-8**.
        - Nie określono mapowania kolumn.

- W źródle działania kopiowania: 

   -  `additionalColumns` nie została określona.
   - Jeśli źródło jest folderem, `recursive` jest ustawione na wartość true.
   - `prefix`, `modifiedDateTimeStart` , `modifiedDateTimeEnd` i `enablePartitionDiscovery` nie są określone.

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

Jeśli źródłowy magazyn danych lub format nie jest natywnie zgodny z poleceniem COPY płatne śnieg, jak wspomniano w ostatniej sekcji, Włącz wbudowaną kopię etapową przy użyciu tymczasowego wystąpienia magazynu obiektów blob platformy Azure. Funkcja kopiowania etapowego zapewnia lepszą przepływność. Data Factory automatycznie konwertuje dane w celu spełnienia wymagań dotyczących formatu danych płatnych śniegu. Następnie wywołuje polecenie COPY, aby załadować dane do płatnych śniegów. Na koniec czyści dane tymczasowe z magazynu obiektów BLOB. Aby uzyskać szczegółowe informacje o kopiowaniu danych przy użyciu przemieszczania, zobacz [przygotowana kopia](copy-activity-performance-features.md#staged-copy) .

Aby użyć tej funkcji, Utwórz [połączoną usługę Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) , która odwołuje się do konta usługi Azure Storage jako tymczasowego przemieszczania. Następnie określ `enableStaging` właściwości i `stagingSettings` w działaniu kopiowania.

> [!NOTE]
> Połączona usługa Azure Blob Storage musi używać uwierzytelniania sygnatury dostępu współdzielonego, zgodnie z wymaganiami polecenia COPY płatne.

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

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Podczas przekształcania danych w mapowaniu przepływu danych można odczytywać i zapisywać w tabelach w śniegu. Aby uzyskać więcej informacji, zobacz [przekształcenie źródłowe](data-flow-source.md) i [przekształcanie ujścia](data-flow-sink.md) w mapowaniu przepływów danych. Możesz wybrać użycie zestawu danych płatnych śniegu lub [wbudowanego zestawu danych](data-flow-source.md#inline-datasets) jako źródła i typu ujścia.

### <a name="source-transformation"></a>Transformacja źródła

Poniższa tabela zawiera listę właściwości obsługiwanych przez źródło śniegu. Można edytować te właściwości na karcie **Opcje źródła** . Łącznik używa [wewnętrznego transferu danych](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)w postaci śniegu.

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabela | Jeśli wybierzesz opcję tabela jako dane wejściowe, przepływ danych pobierze wszystkie dane z tabeli określonej w zestawie danych płatnych śniegu lub w opcjach źródła przy użyciu wbudowanego zestawu danych. | Nie | Ciąg | *(tylko w przypadku wbudowanego zestawu danych)*<br>tableName<br>schemaName |
| Zapytanie | Jeśli wybierzesz pozycję zapytanie jako dane wejściowe, wprowadź zapytanie w celu pobrania danych z płatnej. To ustawienie przesłania każdą tabelę wybraną w zestawie danych.<br>Jeśli nazwy schematu, tabeli i kolumn zawierają małe litery, należy pomniejszyć identyfikator obiektu w kwerendzie, np. `select * from "schema"."myTable"` . | Nie | Ciąg | query |

#### <a name="snowflake-source-script-examples"></a>Przykłady skryptów źródłowych płatnych śniegów

W przypadku użycia zestawu danych płatnych śniegu jako typu źródła skojarzony skrypt przepływu danych jest:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    query: 'select * from MYTABLE',
    format: 'query') ~> SnowflakeSource
```

Jeśli używasz wbudowanego zestawu danych, skojarzony skrypt przepływu danych to:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'query',
    query: 'select * from MYTABLE',
    store: 'snowflake') ~> SnowflakeSource
```

### <a name="sink-transformation"></a>Przekształcanie ujścia

Poniższa tabela zawiera listę właściwości obsługiwanych przez ujścia śniegu. Te właściwości można edytować na karcie **Ustawienia** . W przypadku korzystania z wbudowanego zestawu danych zostaną wyświetlone dodatkowe ustawienia, które są takie same jak właściwości opisane w sekcji [Właściwości zestawu danych](#dataset-properties) . Łącznik używa [wewnętrznego transferu danych](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)w postaci śniegu.

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update — Metoda | Określ, które operacje są dozwolone w miejscu docelowym płatka śniegu.<br>Aby zaktualizować, upsert lub usunąć wiersze, [przekształcenie ALTER Row](data-flow-alter-row.md) jest wymagane, aby można było oznaczyć wiersze dla tych działań. | Tak | `true` lub `false` | usuwaln <br/>wstawialny <br/>aktualizowalne <br/>upsertable |
| Kolumny klucza | W przypadku aktualizacji, upserts i usunięć należy ustawić kolumnę klucza lub kolumny, aby określić, który wiersz ma być zmieniany. | Nie | Tablica | keys |
| Akcja tabeli | Określa, czy należy ponownie utworzyć lub usunąć wszystkie wiersze z tabeli docelowej przed zapisem.<br>- **Brak**: w tabeli nie zostanie wykonana żadna akcja.<br>- **Utwórz ponownie**: tabela zostanie porzucona i utworzona ponownie. Wymagane w przypadku dynamicznego tworzenia nowej tabeli.<br>- **Obcinanie**: wszystkie wiersze z tabeli docelowej zostaną usunięte. | Nie | `true` lub `false` | Utwórz ponownie<br/>obciąć |

#### <a name="snowflake-sink-script-examples"></a>Przykłady skryptów ujścia płatka śniegu

W przypadku używania zestawu danych płatnych śniegu jako typu ujścia, skojarzony skrypt przepływu danych to:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:true,
    insertable:true,
    updateable:true,
    upsertable:false,
    keys:['movieId'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

Jeśli używasz wbudowanego zestawu danych, skojarzony skrypt przepływu danych to:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    format: 'table',
    tableName: 'table',
    schemaName: 'schema',
    deletable: true,
    insertable: true,
    updateable: true,
    upsertable: false,
    store: 'snowflake',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby uzyskać więcej informacji o właściwościach, zobacz [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki

Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Data Factory można znaleźć w temacie [obsługiwane magazyny i formaty danych](copy-activity-overview.md#supported-data-stores-and-formats).
