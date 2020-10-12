---
title: Kopiowanie danych z usługi Phoenix przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych z platformy Phoenix do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
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
ms.openlocfilehash: d236b9f8800b644a0aa51597d01df1c1442475ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81416773"
---
# <a name="copy-data-from-phoenix-using-azure-data-factory"></a>Kopiowanie danych z usługi Phoenix przy użyciu Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z platformy Phoenix. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Phoenix jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z usługi Phoenix można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory udostępnia wbudowany sterownik umożliwiający połączenie, dlatego nie trzeba ręcznie instalować żadnego sterownika przy użyciu tego łącznika.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika Phoenix.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane w przypadku usługi połączonej z Phoenix:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **Phoenix** | Tak |
| host | Adres IP lub nazwa hosta serwera Phoenix. (to jest, 192.168.222.160)  | Tak |
| port | Port TCP, którego serwer Phoenix używa do nasłuchiwania połączeń klientów. Wartość domyślna to 8765. W przypadku nawiązywania połączenia z usługą Azure HDInsight określ port jako 443. | Nie |
| httpPath | Częściowy adres URL odpowiadający serwerowi Phoenix. (czyli/Gateway/Sandbox/Phoenix/version). Określ, `/hbasephoenix0` czy jest używany klaster usługi HDInsight.  | Nie |
| authenticationType | Mechanizm uwierzytelniania używany do nawiązywania połączenia z serwerem Phoenix. <br/>Dozwolone wartości to: **Anonymous**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Tak |
| nazwa użytkownika | Nazwa użytkownika używana do nawiązywania połączenia z serwerem Phoenix.  | Nie |
| hasło | Hasło odpowiadające nazwie użytkownika. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| enableSsl | Określa, czy połączenia z serwerem są szyfrowane przy użyciu protokołu TLS. Wartość domyślna to false.  | Nie |
| trustedCertPath | Pełna ścieżka pliku PEM zawierającego certyfikaty zaufanych urzędów certyfikacji w celu zweryfikowania serwera podczas łączenia za pośrednictwem protokołu TLS. Tę właściwość można ustawić tylko w przypadku korzystania z protokołu TLS na samoobsługowym środowisku IR. Wartość domyślna to plik cacerts. pem instalowany z programem IR.  | Nie |
| useSystemTrustStore | Określa, czy certyfikat urzędu certyfikacji ma być używany z magazynu zaufania systemu czy z określonego pliku PEM. Wartość domyślna to false.  | Nie |
| allowHostNameCNMismatch | Określa, czy ma być wymagana nazwa certyfikatu TLS/SSL wystawionego przez urząd certyfikacji w celu dopasowania do nazwy hosta serwera podczas łączenia za pośrednictwem protokołu TLS. Wartość domyślna to false.  | Nie |
| allowSelfSignedServerCert | Określa, czy zezwalać na certyfikaty z podpisem własnym z serwera. Wartość domyślna to false.  | Nie |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

>[!NOTE]
>Jeśli klaster nie obsługuje sesji programu Sticky Notes, np. HDInsight, jawnie Dodaj indeks węzła na końcu ustawienia ścieżki http, np. Określ `/hbasephoenix0` zamiast `/hbasephoenix` .

**Przykład:**

```json
{
    "name": "PhoenixLinkedService",
    "properties": {
        "type": "Phoenix",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "443",
            "httpPath" : "/hbasephoenix0",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych w Phoenix.

Aby skopiować dane z Phoenix, ustaw właściwość Type zestawu danych na **phoenixobject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość: **phoenixobject** | Tak |
| schema | Nazwa schematu. |Nie (Jeśli określono "zapytanie" w źródle aktywności)  |
| tabela | Nazwa tabeli. |Nie (Jeśli określono "zapytanie" w źródle aktywności)  |
| tableName | Nazwa tabeli ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z poprzednimi wersjami. Użyj `schema` i `table` dla nowego obciążenia. | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

**Przykład**

```json
{
    "name": "PhoenixDataset",
    "properties": {
        "type": "PhoenixObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Phoenix linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło w Phoenix.

### <a name="phoenix-as-source"></a>Phoenix jako źródło

Aby skopiować dane z Phoenix, ustaw typ źródła w działaniu Copy na **PhoenixSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **PhoenixSource** | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono "TableName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromPhoenix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Phoenix input dataset name>",
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
                "type": "PhoenixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
