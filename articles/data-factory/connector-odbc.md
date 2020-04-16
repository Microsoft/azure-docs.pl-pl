---
title: Kopiowanie danych ze źródeł ODBC przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak skopiować dane ze źródeł OData do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: jingwang
ms.openlocfilehash: da5c53f8953960c382070be658add2877fff3f8c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416894"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Kopiowanie danych z i do magazynów danych ODBC przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-odbc-connector.md)
> * [Aktualna wersja](connector-odbc.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do magazynu danych ODBC. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik ODBC jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane ze źródła ODBC do dowolnego obsługiwanego magazynu danych ujścia lub skopiować z dowolnego obsługiwanego magazynu danych źródłowych do ujścia ODBC. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

W szczególności ten łącznik ODBC obsługuje kopiowanie danych z/do **dowolnego magazynów danych zgodnych z ODBC** przy użyciu uwierzytelniania **podstawowego** lub **anonimowego.** Wymagany jest **64-bitowy sterownik ODBC.**

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z tego złącza ODBC, należy:

- Konfigurowanie środowiska wykonawczego integracji hostowanego samodzielnie. Zobacz [self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) artykułu, aby uzyskać szczegółowe informacje.
- Zainstaluj 64-bitowy sterownik ODBC dla magazynu danych na komputerze integration runtime.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika ODBC.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej ODBC:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **Odbc** | Tak |
| Parametry połączenia | Parametry połączenia z wyłączeniem części poświadczeń. Można określić ciąg połączenia `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`z wzorcem, takim jak , lub użyć systemu DSN `"DSN=<name of the DSN on IR machine>;"` (nazwa źródła danych), który został skonfigurowany na komputerze środowiska wykonawczego integracji (nadal należy odpowiednio określić część poświadczeń w połączonej usłudze).<br>Można również umieścić hasło w usłudze `password` Azure Key Vault i wyciągnąć konfigurację z ciągu połączenia.Więcej informacji można znaleźć [w witrynie Store credentials w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| authenticationType | Typ uwierzytelniania używany do łączenia się z magazynem danych ODBC.<br/>Dozwolone wartości to: **Podstawowe** i **Anonimowe**. | Tak |
| userName | Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego. | Nie |
| hasło | Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Nie |
| poświadczenia | Część poświadczeń dostępu ciągu połączenia określona w formacie właściwości-wartość specyficzne dla sterownika. Przykład: `"RefreshToken=<secret refresh token>;"`. Oznacz to pole jako SecureString. | Nie |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Środowisko wykonawcze integracji hostowane samodzielnie jest wymagane, jak wspomniano w [wymaganiach wstępnych.](#prerequisites) |Tak |

**Przykład 1: korzystanie z uwierzytelniania podstawowego**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

**Przykład 2: używanie uwierzytelniania anonimowego**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "<connection string>",
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych ODBC.

Aby skopiować dane z/do magazynu danych zgodnych z ODBC, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **OdbcTable** | Tak |
| tableName | Nazwa tabeli w magazynie danych ODBC. | Nie dla źródła (jeśli określono "zapytanie" w źródle działania);<br/>Tak dla zlewu |

**Przykład**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "OdbcTable",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

Jeśli używasz `RelationalTable` wpisanego zestawu danych, jest on nadal obsługiwany w stanie gotowym do użycia, podczas gdy zaleca się użycie nowego w przyszłości.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło ODBC.

### <a name="odbc-as-source"></a>ODBC jako źródło

Aby skopiować dane z magazynu danych zgodnych z ODBC, w sekcji **źródła** działania kopiowania obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **OdbcSource** | Tak |
| query | Użyj niestandardowej kwerendy SQL, aby odczytać dane. Na przykład: `"SELECT * FROM MyTable"`. | Nie (jeśli określono "nazwa tabela" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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
                "type": "OdbcSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Jeśli używasz `RelationalSource` wpisanego źródła, jest ono nadal obsługiwane w stanie as-is, podczas gdy zaleca się użycie nowego w przyszłości.

### <a name="odbc-as-sink"></a>ODBC jako zlew

Aby skopiować dane do magazynu danych zgodnych z ODBC, ustaw typ ujścia w działaniu kopiowania na **OdbcSink**. Następujące właściwości są obsługiwane w sekcji **ujście** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu ujścia działania kopiowania musi być ustawiona na: **OdbcSink** | Tak |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed jej limitem czasu.<br/>Dozwolone wartości to: timespan. Przykład: "00:30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize.<br/>Dozwolone wartości to: liczba całkowita (liczba wierszy). |Nie (wartość domyślna to 0 - wykryto automatycznie) |
| preCopyScript |Określ kwerendę SQL dla działania kopiowania do wykonania przed zapisaniem danych do magazynu danych w każdym uruchomieniu. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. |Nie |

> [!NOTE]
> Dla "writeBatchSize", jeśli nie jest ustawiona (automatycznie wykryte), działanie kopiowania najpierw wykrywa, czy sterownik obsługuje operacje wsadowe i ustawić go na 10000, jeśli nie, lub ustawić go na 1, jeśli nie. Jeśli jawnie ustawić wartość inną niż 0, działanie kopiowania honoruje wartość i kończy się niepowodzeniem w czasie wykonywania, jeśli sterownik nie obsługuje operacji wsadowych.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="sap-hana-sink"></a>ZlewOZmywak SAP HANA

>[!NOTE]
>Aby skopiować dane z magazynu danych SAP HANA, zapoznaj się z macierzystym [łącznika SAP HANA](connector-sap-hana.md). Aby skopiować dane do sap HANA, postępuj zgodnie z tą instrukcją, aby użyć łącznika ODBC. Należy zauważyć, że połączone usługi dla łącznika SAP HANA i łącznika ODBC są innego typu, dlatego nie można ponownie używać.
>

Dane można kopiować do bazy danych SAP HANA przy użyciu ogólnego łącznika ODBC.

Skonfiguruj własny środowisko wykonawcze integracji na komputerze z dostępem do magazynu danych. Środowisko wykonawcze integracji używa sterownika ODBC dla sap HANA do łączenia się z magazynem danych. W związku z tym należy zainstalować sterownik, jeśli nie jest jeszcze zainstalowany na tym samym komputerze. Szczegółowe informacje można znaleźć w sekcji [Wymagania wstępne.](#prerequisites)

Przed użyciem ujścia SAP HANA w rozwiązaniu fabryki danych sprawdź, czy środowisko wykonawcze integracji może łączyć się z magazynem danych, korzystając z instrukcji w sekcji [Rozwiązywanie problemów z łącznością.](#troubleshoot-connectivity-issues)

Utwórz usługę połączoną ODBC, aby połączyć magazyn danych SAP HANA z fabryką danych platformy Azure, jak pokazano w poniższym przykładzie:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Przeczytaj artykuł od początku, aby uzyskać szczegółowy przegląd przy użyciu magazynów danych ODBC jako magazynów danych źródła/ujścia w operacji kopiowania.

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).


## <a name="troubleshoot-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

Aby rozwiązać problemy z połączeniem, użyj karty **Diagnostyka** **programu Integration Runtime Configuration Manager**.

1. Uruchom **program Integration Runtime Configuration Manager**.
2. Przełącz się do karty **Diagnostyka.**
3. W sekcji "Połączenie testowe" wybierz **typ** magazynu danych (usługa połączona).
4. Określ **parametry połączenia** używane do łączenia się z magazynem danych, wybierz **uwierzytelnianie** i wprowadź **nazwę użytkownika,** **hasło**i/lub **poświadczenia**.
5. Kliknij **przycisk Testuj połączenie,** aby przetestować połączenie z magazynem danych.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
