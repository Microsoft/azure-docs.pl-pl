---
title: Kopiuj dane z i do programu IBM Informix przy użyciu Azure Data Factory
description: Dowiedz się, jak skopiować dane z i do programu IBM Informix przy użyciu działania kopiowania w potoku Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: jingwang
ms.openlocfilehash: af907704862e80a2747ac064b98242a1d9d7edb3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588872"
---
# <a name="copy-data-from-and-to-ibm-informix-using-azure-data-factory"></a>Kopiuj dane z i do programu IBM Informix przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z magazynu danych programu IBM Informix. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik programu Informix jest obsługiwany przez następujące działania:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z źródła programu Informix można kopiować do dowolnego obsługiwanego magazynu danych ujścia lub kopiować z dowolnego obsługiwanego źródłowego magazynu danych do ujścia programu Informix. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .


## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika programu Informix, należy wykonać następujące:

- Skonfiguruj samodzielny Integration Runtime. Aby uzyskać szczegółowe informacje, zobacz artykuł [Integration Runtime samodzielny](create-self-hosted-integration-runtime.md) .
- Zainstaluj sterownik programu Informix ODBC dla magazynu danych na maszynie Integration Runtime. Aby uzyskać szczegółowe informacje na temat instalacji i konfiguracji sterownika, zobacz artykuł [Przewodnik po sterowniku programu Informix ODBC](https://www.ibm.com/support/knowledgecenter/SSGU8G_11.70.0/com.ibm.odbc.doc/odbc.htm) w programie IBM Knowledge Center lub skontaktuj się z zespołem pomocy technicznej firmy IBM w celu uzyskania wskazówek dotyczących instalacji

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika programu Informix.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi programu Informix są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **Informix** | Tak |
| Parametry połączenia | Parametry połączenia ODBC z wyjątkiem części poświadczenia. Można określić parametry połączenia lub użyć systemu DSN (nazwa źródła danych) skonfigurowanego na maszynie Integration Runtime (w związku z tym nadal należy określić część poświadczeń w połączonej usłudze). <br> Możesz również wprowadzić hasło w Azure Key Vault i ściągnąć `password` konfigurację z parametrów połączenia. Aby uzyskać więcej informacji, zobacz temat [poświadczenia sklepu w Azure Key Vault](store-credentials-in-key-vault.md) .| Tak |
| authenticationType | Typ uwierzytelniania używany do nawiązywania połączenia z magazynem danych programu Informix.<br/>Dozwolone wartości to: **podstawowe** i **anonimowe**. | Tak |
| userName | Określ nazwę użytkownika w przypadku korzystania z uwierzytelniania podstawowego. | Nie |
| hasło | Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| poświadczenia | Część poświadczeń dostępu do parametrów połączenia określona w formacie wartości właściwości specyficznej dla sterownika. Oznacz to pole jako element SecureString. | Nie |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Samodzielna Integration Runtime jest wymagana, jak wspomniano w [wymaganiach wstępnych](#prerequisites). |Tak |

**Przykład:**

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Informix",
        "typeProperties": {
            "connectionString": "<Informix connection string or DSN>",
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

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych programu Informix.

Aby skopiować dane z programu Informix, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na: **Informix** | Tak |
| tableName | Nazwa tabeli w programie Informix. | Nie dla źródła (Jeśli określono "zapytanie" w źródle aktywności);<br/>Tak dla ujścia |

**Przykład**

```json
{
    "name": "InformixDataset",
    "properties": {
        "type": "InformixTable",
        "linkedServiceName": {
            "referenceName": "<Informix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło programu Informix.

### <a name="informix-as-source"></a>Informix jako źródło

Aby skopiować dane z programu Informix, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **InformixSource** | Tak |
| query | Użyj zapytania niestandardowego do odczytywania danych. Na przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono "TableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Informix input dataset name>",
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
                "type": "InformixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="informix-as-sink"></a>Informix jako ujścia

Aby skopiować dane do programu Informix, w sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type ujścia działania Copy musi być ustawiona na wartość: **InformixSink** | Tak |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed upływem limitu czasu.<br/>Dozwolone wartości to: TimeSpan. Przykład: "00:30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize.<br/>Dozwolone wartości to: Integer (liczba wierszy). |Nie (domyślnie: 0 — wykryto Autowykrywanie) |
| preCopyScript |Określ zapytanie SQL dla działania kopiowania, które ma zostać wykonane przed zapisaniem danych w magazynie danych w każdym przebiegu. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. |Nie |
| maxConcurrentConnections |Górny limit równoczesnych połączeń ustanowiony dla magazynu danych podczas uruchamiania działania. Określ wartość tylko wtedy, gdy chcesz ograniczyć połączenia współbieżne.| Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Informix output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "InformixSink"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).


## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
