---
title: Kopiowanie danych z usługi Salesforce Marketing Cloud
description: Dowiedz się, jak skopiować dane z salesforce marketing cloud do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/25/2019
ms.openlocfilehash: 1a5a2682198f9ce9f5cb39f21e244c723ca513d9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416648"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Kopiowanie danych z usługi Salesforce Marketing Cloud przy użyciu usługi Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z usługi Salesforce Marketing Cloud. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Salesforce Marketing Cloud jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Dane z salesforce marketing cloud można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

Łącznik Salesforce Marketing Cloud obsługuje uwierzytelnianie OAuth 2. Jest on zbudowany na szczycie [Interfejsu API REST Salesforce Marketing Cloud.](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm)

>[!NOTE]
>Ten łącznik nie obsługuje pobierania obiektów niestandardowych lub niestandardowych rozszerzeń danych.

## <a name="getting-started"></a>Wprowadzenie

Potoku można utworzyć z aktywnością kopiowania przy użyciu narzędzia .NET SDK, zestaw SDK języka Python, usługi Azure PowerShell, interfejsu API REST lub szablonu usługi Azure Resource Manager. Zobacz [Kopiowanie samouczka aktywności,](quickstart-create-data-factory-dot-net.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika Salesforce Marketing Cloud.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane w przypadku usługi połączonej Salesforce Marketing Cloud:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **SalesforceMarketingCloud** | Tak |
| clientId | Identyfikator klienta skojarzony z aplikacją Salesforce Marketing Cloud.  | Tak |
| clientSecret | Klucz tajny klienta skojarzony z aplikacją Salesforce Marketing Cloud. Możesz zaznaczyć to pole jako SecureString, aby bezpiecznie przechowywać je w usłudze ADF lub przechowywać hasło w usłudze Azure Key Vault i pozwolić aktywność kopiowania ADF pobierać stamtąd podczas wykonywania kopiowania danych — dowiedz się więcej z [poświadczenia sklepu w magazynie kluczy](store-credentials-in-key-vault.md). | Tak |
| useEncryptedEndpoints | Określa, czy punkty końcowe źródła danych są szyfrowane przy użyciu protokołu HTTPS. Wartością domyślną jest true.  | Nie |
| useHostVerification | Określa, czy nazwa hosta w certyfikacie serwera ma być zgodna z nazwą hosta serwera podczas łączenia się za korzystając z protokołu TLS. Wartością domyślną jest true.  | Nie |
| usePeerVerification | Określa, czy podczas nawiązywania połączenia zawersyfikowany przez TLS ma być weryfikowany przez tożsamość serwera. Wartością domyślną jest true.  | Nie |

**Przykład:**

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true
        }
    }
}

```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Salesforce Marketing Cloud.

Aby skopiować dane z usługi Salesforce Marketing Cloud, ustaw właściwość typu zestawu danych na **SalesforceMarketingCloudObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **SalesforceMarketingCloudObject** | Tak |
| tableName | Nazwa tabeli. | Nie (jeśli określono "zapytanie" w źródle działania) |

**Przykład**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Salesforce Marketing Cloud.

### <a name="salesforce-marketing-cloud-as-source"></a>Salesforce Marketing Cloud jako źródło

Aby skopiować dane z usługi Salesforce Marketing Cloud, ustaw typ źródła w działaniu kopiowania na **SalesforceMarketingCloudSource**. Następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **SalesforceMarketingCloudSource** | Tak |
| query | Użyj niestandardowej kwerendy SQL, aby odczytać dane. Na przykład: `"SELECT * FROM MyTable"`. | Nie (jeśli określono "nazwa tabela" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
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
