---
title: Kopiowanie danych z chmury marketingowej usług Salesforce
description: Dowiedz się, jak skopiować dane z chmury marketingowej usług Salesforce do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
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
ms.date: 07/17/2020
ms.openlocfilehash: 1f0fb1ee8580c0c7f6eb30228b65e0a3780ef0a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87076797"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Kopiowanie danych z chmury marketingowej usług Salesforce przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z chmury marketingowej usług Salesforce. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik usługi Salesforce Marketing Cloud jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z chmury marketingowej usługi Salesforce można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Łącznik usługi Salesforce Marketing Cloud obsługuje uwierzytelnianie OAuth 2 i obsługuje zarówno starsze, jak i rozszerzone typy pakietów. Łącznik jest oparty na [interfejsie API REST usługi Salesforce Marketing Cloud](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm).

>[!NOTE]
>Ten łącznik nie obsługuje pobierania obiektów niestandardowych ani niestandardowych rozszerzeń danych.

## <a name="getting-started"></a>Wprowadzenie

Można utworzyć potok z działaniem kopiowania przy użyciu zestawu .NET SDK, zestawu Python SDK, Azure PowerShell, interfejsu API REST lub szablonu Azure Resource Manager. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](quickstart-create-data-factory-dot-net.md) .

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika usługi Salesforce Marketing Cloud.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla połączonej usługi Salesforce w chmurze marketingowej:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **SalesforceMarketingCloud** | Tak |
| connectionProperties | Grupa właściwości, która definiuje sposób nawiązywania połączenia z chmurą marketingową usługi Salesforce. | Tak |
| ***W obszarze `connectionProperties` :*** | | |
| authenticationType | Określa metodę uwierzytelniania, która ma być używana. Dozwolone wartości to `Enhanced sts OAuth 2.0` lub `OAuth_2.0` .<br><br>Starsza wersja chmury marketingowej usługi Salesforce obsługuje tylko `OAuth_2.0` , a jednocześnie rozszerzane potrzeby pakietu `Enhanced sts OAuth 2.0` . <br>Od 1 sierpnia 2019 w chmurze marketingowej usługi Salesforce została usunięta możliwość tworzenia starszych pakietów. Wszystkie nowe pakiety są udoskonalonymi pakietami. | Tak |
| host | W przypadku pakietu rozszerzonego host powinien być [poddomeną](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/your-subdomain-tenant-specific-endpoints.htm) , która jest reprezentowana przez 28-znakowy ciąg zaczynający się od liter "MC", np. `mc563885gzs27c5t9-63k636ttgm` . <br>Dla starszego pakietu, określ `www.exacttargetapis.com` . | Tak |
| clientId | Identyfikator klienta skojarzony z aplikacją usługi Salesforce Marketing Cloud.  | Tak |
| clientSecret | Wpis tajny klienta skojarzony z aplikacją usługi Salesforce Marketing Cloud. Możesz oznaczyć to pole jako element SecureString, aby bezpiecznie przechowywać go w podajniku APD, lub przechowywać wpis tajny w Azure Key Vault i umożliwić odjęcie działania kopiowania ADF z tego miejsca podczas kopiowania danych — Dowiedz się więcej z [poświadczeń sklepu w Key Vault](store-credentials-in-key-vault.md). | Tak |
| useEncryptedEndpoints | Określa, czy punkty końcowe źródła danych są szyfrowane przy użyciu protokołu HTTPS. Wartością domyślną jest true.  | Nie |
| useHostVerification | Określa, czy nazwa hosta ma być wymagana w certyfikacie serwera, aby odpowiadała nazwie hosta serwera podczas łączenia się za pośrednictwem protokołu TLS. Wartością domyślną jest true.  | Nie |
| usePeerVerification | Określa, czy należy zweryfikować tożsamość serwera podczas łączenia za pośrednictwem protokołu TLS. Wartością domyślną jest true.  | Nie |

**Przykład: użycie rozszerzonego uwierzytelniania usługi STS OAuth 2 dla pakietu rozszerzonego** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "<subdomain e.g. mc563885gzs27c5t9-63k636ttgm>",
                "authenticationType": "Enhanced sts OAuth 2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

**Przykład: używanie uwierzytelniania OAuth 2 dla starszego pakietu** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "www.exacttargetapis.com",
                "authenticationType": "OAuth_2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

Jeśli używasz połączonej usługi w chmurze marketingowej w usłudze Salesforce z następującym ładunkiem, nadal jest ona obsługiwana w stanie takim, w jakim będziesz mieć możliwość korzystania z nowego, co spowoduje dodanie ulepszonego wsparcia pakietu.

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId": "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints": true,
            "useHostVerification": true,
            "usePeerVerification": true
        }
    }
}

```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych w chmurze marketingowej usługi Salesforce.

Aby skopiować dane z chmury marketingowej usług Salesforce, ustaw właściwość Type zestawu danych na **SalesforceMarketingCloudObject**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość: **SalesforceMarketingCloudObject** | Tak |
| tableName | Nazwa tabeli. | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło w chmurze marketingowej usługi Salesforce.

### <a name="salesforce-marketing-cloud-as-source"></a>Chmura marketingowa usługi Salesforce jako źródło

Aby skopiować dane z chmury marketingowej usług Salesforce, ustaw typ źródła w działaniu Copy na **SalesforceMarketingCloudSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **SalesforceMarketingCloudSource** | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono "TableName" w zestawie danych) |

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

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
