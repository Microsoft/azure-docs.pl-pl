---
title: Kopiowanie danych z impala przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak skopiować dane z Impala do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku fabryki danych.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: b70db03e03ce914ea1d81d94cd2803a36eccfc88
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418222"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory"></a>Kopiowanie danych z impala przy użyciu usługi Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z impala. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Impala jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane z Impala do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła lub pochłaniacze przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

Usługa Data Factory udostępnia wbudowany sterownik umożliwiający łączność. W związku z tym nie trzeba ręcznie zainstalować sterownik, aby użyć tego łącznika.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania elementów fabryki danych specyficznych dla łącznika Impala.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej Impala.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **Impala**. | Tak |
| host | Adres IP lub nazwa hosta serwera Impala (czyli 192.168.222.160).  | Tak |
| port | Port TCP używany przez serwer Impala do nasłuchiwać połączeń klientów. Wartość domyślna to 21050.  | Nie |
| authenticationType | Typ uwierzytelniania do użycia. <br/>Dozwolone wartości to **Anonymous**, **SASLUsername**i **UsernameAndPassword**. | Tak |
| nazwa użytkownika | Nazwa użytkownika używana do uzyskiwania dostępu do serwera Impala. Wartość domyślna jest anonimowa podczas korzystania z saslUsername.  | Nie |
| hasło | Hasło, które odpowiada nazwie użytkownika podczas korzystania z UsernameAndPassword. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Nie |
| Enablessl | Określa, czy połączenia z serwerem są szyfrowane przy użyciu protokołu TLS. Wartość domyślna to **fałsz**.  | Nie |
| trustedCertPath | Pełna ścieżka pliku pem zawierającego zaufane certyfikaty urzędu certyfikacji używane do weryfikacji serwera podczas łączenia się za pomocą protokołu TLS. Tę właściwość można ustawić tylko wtedy, gdy używasz protokołu TLS w samodzielnym czasie działania integracji. Wartością domyślną jest plik cacerts.pem zainstalowany wraz ze środowiska wykonawczego integracji.  | Nie |
| useSystemTrustStore | Określa, czy certyfikat urzędu certyfikacji ma być używany z magazynu zaufania systemu, czy z określonego pliku PEM. Wartość domyślna to **fałsz**.  | Nie |
| allowHostNameCNMismatch | Określa, czy nazwa certyfikatu TLS/SSL wystawiona przez urząd certyfikacji ma być zgodna z nazwą hosta serwera podczas łączenia się za pomocą protokołu TLS. Wartość domyślna to **fałsz**.  | Nie |
| allowSelfSignedServerCert | Określa, czy zezwolić na certyfikaty z podpisem własnym z serwera. Wartość domyślna to **fałsz**.  | Nie |
| connectVia | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

**Przykład:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Impala.

Aby skopiować dane z impala, ustaw właściwość typu zestawu danych na **ImpalaObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **ImpalaObject** | Tak |
| Schematu | Nazwa schematu. |Nie (jeśli określono "zapytanie" w źródle działania)  |
| tabela | Nazwa tabeli. |Nie (jeśli określono "zapytanie" w źródle działania)  |
| tableName | Nazwa tabeli ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z powrotem. Użyj `schema` `table` i dla nowego obciążenia. | Nie (jeśli określono "zapytanie" w źródle działania) |

**Przykład**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez typ źródła Impala.

### <a name="impala-as-a-source-type"></a>Impala jako typ źródła

Aby skopiować dane z impala, ustaw typ źródła w działaniu kopiowania na **ImpalaSource**. Następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na **ImpalaSource**. | Tak |
| query | Użyj niestandardowej kwerendy SQL, aby odczytać dane. Może to być na przykład `"SELECT * FROM MyTable"`. | Nie (jeśli określono "nazwa tabela" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
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
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w fabryce danych, zobacz [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
