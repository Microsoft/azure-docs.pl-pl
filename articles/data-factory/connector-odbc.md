---
title: Kopiowanie danych z i do magazynów danych ODBC przy użyciu Azure Data Factory
description: Dowiedz się, jak skopiować dane z i do magazynów danych ODBC przy użyciu działania kopiowania w potoku Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: jingwang
ms.openlocfilehash: 9b73e10b0ed539879e9a32d3961b6375828cc153
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389624"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Kopiowanie danych z i do magazynów danych ODBC przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-odbc-connector.md)
> * [Bieżąca wersja](connector-odbc.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z i do magazynu danych ODBC. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik ODBC jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Możesz skopiować dane ze źródła ODBC do dowolnego obsługiwanego magazynu danych ujścia lub skopiować z dowolnego obsługiwanego źródłowego magazynu danych do ujścia ODBC. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Ten łącznik ODBC obsługuje kopiowanie danych z/do **wszelkich magazynów danych zgodnych z ODBC** przy użyciu uwierzytelniania **podstawowego** lub **anonimowego** . Wymagany jest **64-bitowy sterownik ODBC** . W przypadku ujścia ODBC moduł ADF obsługuje standard ODBC w wersji 2,0.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika ODBC, należy wykonać następujące:

- Skonfiguruj samodzielny Integration Runtime. Aby uzyskać szczegółowe informacje, zobacz artykuł [Integration Runtime samodzielny](create-self-hosted-integration-runtime.md) .
- Zainstaluj 64-bitowy sterownik ODBC dla magazynu danych na maszynie Integration Runtime.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika ODBC.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi ODBC są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi być ustawiona na wartość: **ODBC** | Tak |
| Parametry połączenia | Parametry połączenia z wyjątkiem części poświadczenia. Można określić parametry połączenia z wzorcem `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"` lub użyć systemowej nazwy DSN (źródła danych) skonfigurowanego na maszynie Integration Runtime przy użyciu programu `"DSN=<name of the DSN on IR machine>;"` (należy odpowiednio określić część Credential w połączonej usłudze).<br>Możesz również wprowadzić hasło w Azure Key Vault i ściągnąć `password` konfigurację z parametrów połączenia. Aby uzyskać więcej informacji, zobacz temat [poświadczenia sklepu w Azure Key Vault](store-credentials-in-key-vault.md) .| Tak |
| authenticationType | Typ uwierzytelniania używany do nawiązywania połączenia z magazynem danych ODBC.<br/>Dozwolone wartości to: **podstawowe** i **anonimowe**. | Tak |
| userName | Określ nazwę użytkownika w przypadku korzystania z uwierzytelniania podstawowego. | Nie |
| hasło | Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| poświadczenia | Część poświadczeń dostępu do parametrów połączenia określona w formacie wartości właściwości specyficznej dla sterownika. Przykład: `"RefreshToken=<secret refresh token>;"`. Oznacz to pole jako element SecureString. | Nie |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Samodzielna Integration Runtime jest wymagana, jak wspomniano w [wymaganiach wstępnych](#prerequisites). |Tak |

**Przykład 1: używanie uwierzytelniania podstawowego**

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych ODBC.

Aby skopiować dane z magazynu danych zgodnego ze standardem ODBC, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na: **ODBC** | Tak |
| tableName | Nazwa tabeli w magazynie danych ODBC. | Nie dla źródła (Jeśli określono "zapytanie" w źródle aktywności);<br/>Tak dla ujścia |

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

Jeśli używasz `RelationalTable` określonego zestawu danych, jest on nadal obsługiwany w stanie takim, w jakim będziesz mieć możliwość korzystania z nowej usługi.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło ODBC.

### <a name="odbc-as-source"></a>ODBC jako źródło

Aby skopiować dane z magazynu danych zgodnego z ODBC, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **OdbcSource** | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Na przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono "TableName" w zestawie danych) |

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

Jeśli używasz `RelationalSource` typu source, nadal jest ono obsługiwane w stanie takim, w jakim będziesz mieć możliwość użycia nowego.

### <a name="odbc-as-sink"></a>ODBC jako ujścia

Aby skopiować dane do magazynu danych zgodnego ze standardem ODBC, ustaw typ ujścia w działaniu Copy na **OdbcSink**. W sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type ujścia działania Copy musi być ustawiona na wartość: **OdbcSink** | Tak |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed upływem limitu czasu.<br/>Dozwolone wartości to: TimeSpan. Przykład: "00:30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize.<br/>Dozwolone wartości to: Integer (liczba wierszy). |Nie (domyślnie: 0 — wykryto Autowykrywanie) |
| preCopyScript |Określ zapytanie SQL dla działania kopiowania, które ma zostać wykonane przed zapisaniem danych w magazynie danych w każdym przebiegu. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. |Nie |

> [!NOTE]
> W przypadku elementu "writeBatchSize", jeśli nie jest ustawiony (Autowykrywanie), działanie kopiowania najpierw wykrywa, czy sterownik obsługuje operacje wsadowe, i ustawia go na 10000, jeśli tak, lub jeśli nie, ustawia go na 1. Jeśli jawnie ustawisz wartość inną niż 0, działanie kopiowania uznaje wartość i kończy się niepowodzeniem w czasie wykonywania, jeśli sterownik nie obsługuje operacji wsadowych.

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

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="troubleshoot-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

Aby rozwiązać problemy z połączeniem, Użyj karty **Diagnostyka** w **Integration Runtime Configuration Manager**.

1. Uruchom **Configuration Manager Integration Runtime**.
2. Przejdź na kartę **Diagnostyka** .
3. W sekcji "Testuj połączenie" Wybierz **Typ** magazynu danych (połączona usługa).
4. Określ **Parametry połączenia** , które są używane do nawiązywania połączenia z magazynem danych, **Wybierz uwierzytelnianie** i wprowadź **nazwę użytkownika**, **hasło** i/lub **poświadczenia**.
5. Kliknij przycisk **Test connection** , aby przetestować połączenie z magazynem danych.

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
