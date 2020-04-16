---
title: Kopiowanie danych ze źródeł programu Microsoft Access
description: Dowiedz się, jak kopiować dane ze źródeł programu Microsoft Access do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/27/2019
ms.openlocfilehash: fc2179efcda4ee11dda3b424b16a072a2bb2c26e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418188"
---
# <a name="copy-data-from-and-to-microsoft-access-data-stores-using-azure-data-factory"></a>Kopiowanie danych z i do magazynów danych programu Microsoft Access przy użyciu usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak skopiować dane z magazynu danych programu Microsoft Access za pomocą działania kopiowania w usłudze Azure Data Factory. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik programu Microsoft Access jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Dane można kopiować ze źródła programu Microsoft Access do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z tego łącznika programu Microsoft Access, należy:

- Konfigurowanie środowiska wykonawczego integracji hostowanego samodzielnie. Zobacz [self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) artykułu, aby uzyskać szczegółowe informacje.
- Zainstaluj sterownik ODBC programu Microsoft Access dla magazynu danych na komputerze runtime integracji.

>[!NOTE]
>Microsoft Access 2016 wersja sterownika ODBC nie działa z tym łącznikiem. Zamiast tego użyj wersji sterownika 2013 lub 2010.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek usługi Data Factory specyficznych dla łącznika programu Microsoft Access.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

W przypadku połączonej usługi programu Microsoft Access są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **MicrosoftAccess** | Tak |
| Parametry połączenia | Parametry połączenia ODBC z wyłączeniem części poświadczeń. Można określić parametry połączenia lub użyć systemu DSN (nazwa źródła danych) skonfigurowany na komputerze środowiska wykonawczego integracji (nadal trzeba określić część poświadczeń w połączonej usłudze odpowiednio).<br> Można również umieścić hasło w usłudze `password` Azure Key Vault i wyciągnąć konfigurację z ciągu połączenia.Więcej informacji można znaleźć [w witrynie Store credentials w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| authenticationType | Typ uwierzytelniania używany do łączenia się z magazynem danych programu Microsoft Access.<br/>Dozwolone wartości to: **Podstawowe** i **Anonimowe**. | Tak |
| userName | Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego. | Nie |
| hasło | Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Nie |
| poświadczenia | Część poświadczeń dostępu ciągu połączenia określona w formacie właściwości-wartość specyficzne dla sterownika. Oznacz to pole jako SecureString. | Nie |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Środowisko wykonawcze integracji hostowane samodzielnie jest wymagane, jak wspomniano w [wymaganiach wstępnych.](#prerequisites) |Tak |

**Przykład:**

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Microsoft Access",
        "typeProperties": {
            "connectionString": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych programu Microsoft Access.

Aby skopiować dane z programu Microsoft Access, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **MicrosoftAccessTable** | Tak |
| tableName | Nazwa tabeli w programie Microsoft Access. | Nie dla źródła (jeśli określono "zapytanie" w źródle działania);<br/>Tak dla zlewu |

**Przykład**

```json
{
    "name": "MicrosoftAccessDataset",
    "properties": {
        "type": "MicrosoftAccessTable",
        "linkedServiceName": {
            "referenceName": "<Microsoft Access linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło programu Microsoft Access.

### <a name="microsoft-access-as-source"></a>Program Microsoft Access jako źródło

Aby skopiować dane z magazynu danych zgodnych z programem Microsoft Access, w sekcji **źródła** działania kopiowania obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **MicrosoftAccessSource** | Tak |
| query | Użyj kwerendy niestandardowej, aby odczytać dane. Na przykład: `"SELECT * FROM MyTable"`. | Nie (jeśli określono "nazwa tabela" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromMicrosoftAccess",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Microsoft Access input dataset name>",
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
                "type": "MicrosoftAccessSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
