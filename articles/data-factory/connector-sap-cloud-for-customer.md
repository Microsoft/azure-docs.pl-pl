---
title: Kopiowanie danych z/do sap cloud dla klienta
description: Dowiedz się, jak kopiować dane z usługi SAP Cloud for Customer do obsługiwanych magazynów danych ujścia (lub) z obsługiwanych magazynów danych źródłowych do sap cloud for customer przy użyciu usługi Data Factory.
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
ms.date: 09/02/2019
ms.openlocfilehash: 1d3772a17d0429d9b3a5bf95d2060f2dfbbbafe1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418052"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Kopiowanie danych z usługi SAP Cloud for Customer (C4C) przy użyciu usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z/do usługi SAP Cloud for Customer (C4C). Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

>[!TIP]
>Aby dowiedzieć się, że usługa ADF jest obsługiwana w scenariuszu integracji danych SAP, zobacz [integracja danych SAP przy użyciu oficjalnych dokumentów usługi Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) ze szczegółowym wprowadzeniem, porównaniem i wskazówkami.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik SAP Cloud dla klienta jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane z sap cloud dla klienta do dowolnego obsługiwanego magazynu danych ujścia lub skopiować dane z dowolnego obsługiwanego magazynu danych źródłowych do SAP Cloud for Customer. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

W szczególności ten łącznik umożliwia usługi Azure Data Factory kopiowanie danych z/do sap cloud dla klienta, w tym SAP Cloud for Sales, SAP Cloud for Service i SAP Cloud for Social Engagement.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika SAP Cloud dla klienta.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej z usługą SAP Cloud dla klientów:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **SapCloudForCustomer**. | Tak |
| url | Adres URL usługi SAP C4C OData. | Tak |
| nazwa użytkownika | Określ nazwę użytkownika, który ma łączyć się z urządzeniem SAP C4C. | Tak |
| hasło | Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. | Nie dla źródła, tak dla zlewu |

>[!IMPORTANT]
>Aby skopiować dane do usługi SAP Cloud for Customer, należy jawnie [utworzyć usługę Azure IR](create-azure-integration-runtime.md#create-azure-ir) z lokalizacją w pobliżu usługi SAP Cloud dla klienta i skojarzyć w połączonej usłudze w następującym przykładzie:

**Przykład:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
            "username": "<username>",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych SAP Cloud dla klientów.

Aby skopiować dane z sap cloud dla klienta, ustaw właściwość typu zestawu danych na **SapCloudForCustomerResource**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **SapCloudForCustomerResource** |Tak |
| ścieżka | Określ ścieżkę jednostki OData SAP C4C. |Tak |

**Przykład:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez SAP Cloud dla źródła klienta.

### <a name="sap-c4c-as-source"></a>SAP C4C jako źródło

Aby skopiować dane z sap cloud dla klienta, ustaw typ źródła w działaniu kopiowania na **SapCloudForCustomerSource**. Następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **SapCloudForCustomerSource**  | Tak |
| query | Określ niestandardową kwerendę OData do odczytu danych. | Nie |

Przykładowa kwerenda, aby uzyskać dane dla określonego dnia:`"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP C4C jako zlew

Aby skopiować dane do usługi SAP Cloud for Customer, ustaw typ ujścia w działaniu kopiowania na **SapCloudForCustomerSink**. Następujące właściwości są obsługiwane w sekcji **ujście** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **SapCloudForCustomerSink**  | Tak |
| writeZachody | Zachowanie zapisu operacji. Może to być "Wstaw", "Aktualizacja". | Nie. Domyślnie "Wstaw". |
| writeBatchSize | Rozmiar partii operacji zapisu. Rozmiar partii, aby uzyskać najlepszą wydajność może być inna dla różnych tabeli lub serwera. | Nie. Wartość domyślna 10. |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Mapowanie typów danych dla SAP Cloud dla klienta

Podczas kopiowania danych z usługi SAP Cloud for Customer używane są następujące mapowania z typów danych SAP Cloud for Customer do tymczasowych typów danych usługi Azure Data Factory. Zobacz [Mapowania schematu i typu danych,](copy-activity-schema-and-type-mapping.md) aby dowiedzieć się, jak aktywność kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| Typ danych OData SAP C4C | Tymczasowy typ danych fabryki danych |
|:--- |:--- |
| Edm.Binary (Edm.Binary) | Bajt[] |
| Edm.Boolean | Wartość logiczna |
| Edm.Byte (Edm.Byte) | Bajt[] |
| Edm.DateTime | DateTime |
| Edm.Decimal (Edm.Decimal) | Wartość dziesiętna |
| Edm.Double | Double |
| Edm.Single (Edm.Pojedyncze) | Single |
| Edm.Guid (Edm.Guid) | Guid (identyfikator GUID) |
| Edm.Int16 (Edm.Int16) | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte (Edm.SByte) | Int16 |
| Edm.String | Ciąg |
| Edm.Time (Edm.Time) | przedział_czasu |
| Edm.DateTimeOffset | Datetimeoffset |


## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
