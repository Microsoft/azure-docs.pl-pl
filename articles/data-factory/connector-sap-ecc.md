---
title: Kopiowanie danych z SAP ECC
description: Dowiedz się, jak skopiować dane z SAP ECC do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: ad26fca94527864af10bb0051336c372ea65b3e0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413794"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Kopiowanie danych z sap ECC przy użyciu usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z składnika SAP Enterprise Central Component (ECC). Aby uzyskać więcej informacji, zobacz [Omówienie działania kopiowania](copy-activity-overview.md).

>[!TIP]
>Aby dowiedzieć się, że usługa ADF jest obsługiwana w scenariuszu integracji danych SAP, zobacz [integracja danych SAP przy użyciu oficjalnych dokumentów usługi Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) ze szczegółowym wprowadzeniem, porównaniem i wskazówkami.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik SAP ECC jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane z SAP ECC do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła lub pochłaniacze przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności to złącze SAP ECC obsługuje:

- Kopiowanie danych z SAP ECC na SAP NetWeaver w wersji 7.0 i nowszych.
- Kopiowanie danych z obiektów ujawnionych przez usługi SAP ECC OData, takie jak:

  - tabel lub widoków SAP.
  - Obiekty interfejsu programowania aplikacji biznesowych [BAPI].
  - Ekstraktory danych.
  - Dane lub dokumenty pośrednie (IDOCs) wysyłane do SAP Process Integration (PI), które mogą być odbierane jako OData za pośrednictwem względnych kart.

- Kopiowanie danych przy użyciu uwierzytelniania podstawowego.

>[!TIP]
>Aby skopiować dane z SAP ECC za pośrednictwem tabeli lub widoku SAP, użyj łącznika [tabeli SAP,](connector-sap-table.md) który jest szybszy i bardziej skalowalny.

## <a name="prerequisites"></a>Wymagania wstępne

Ogólnie rzecz biorąc SAP ECC udostępnia jednostki za pośrednictwem usług OData za pośrednictwem bramy SAP. Aby korzystać z tego łącznika SAP ECC, należy:

- **Skonfiguruj bramę SAP**. W przypadku serwerów z wersjami SAP NetWeaver późniejszymi niż 7.4 brama SAP jest już zainstalowana. W przypadku wcześniejszych wersji należy zainstalować osadzoną bramę SAP lub system koncentratora BRAMY SAP przed ujawnieniem danych SAP ECC za pośrednictwem usług OData. Aby skonfigurować sap gateway, zobacz [podręcznik instalacji](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Uaktywnianie i skonfigurowanie usługi SAP OData**. Usługę OData można aktywować za pośrednictwem TCODE SICF w kilka sekund. Można również skonfigurować, które obiekty muszą być narażone. Aby uzyskać więcej informacji, zobacz [wskazówki krok po kroku](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika SAP ECC.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej SAP ECC:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| `type` | Właściwość `type` musi być `SapEcc`ustawiona na . | Tak |
| `url` | Adres URL usługi SAP ECC OData. | Tak |
| `username` | Nazwa użytkownika używana do łączenia się z SAP ECC. | Nie |
| `password` | Hasło w postaci zwykłego tekstu używane do łączenia się z SAP ECC. | Nie |
| `connectVia` | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie określisz środowiska uruchomieniowego, używany jest domyślny środowiska wykonawczego integracji platformy Azure. | Nie |

### <a name="example"></a>Przykład

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [Zestawy danych](concepts-datasets-linked-services.md). Poniższa sekcja zawiera listę właściwości obsługiwanych przez zestaw danych SAP ECC.

Aby skopiować dane z SAP `type` ECC, ustaw `SapEccResource`właściwość zestawu danych na .

Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| `path` | Ścieżka jednostki OData SAP ECC. | Tak |

### <a name="example"></a>Przykład

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Potoki](concepts-pipelines-activities.md). Poniższa sekcja zawiera listę właściwości obsługiwanych przez źródło SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC jako źródło

Aby skopiować dane z SAP `type` ECC, ustaw właściwość w `source` sekcji działania kopiowania na `SapEccSource`.

Następujące właściwości są obsługiwane w sekcji działania `source` kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| `type` | Właściwość `type` sekcji działania kopiowania musi być ustawiona `source` na `SapEccSource`. | Tak |
| `query` | Opcje kwerendy OData do filtrowania danych. Przykład:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Łącznik SAP ECC kopiuje dane z połączonego adresu URL:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Aby uzyskać więcej informacji, zobacz [Składniki adresu URL OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nie |

### <a name="example"></a>Przykład

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>Mapowania typów danych dla SAP ECC

Podczas kopiowania danych z SAP ECC, następujące mapowania są używane z typów danych OData dla danych SAP ECC do usługi Azure Data Factory tymczasowych typów danych. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia, zobacz [Mapowanie schematu i typów danych](copy-activity-schema-and-type-mapping.md).

| Typ danych OData | Tymczasowy typ danych data factory |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Złożone typy danych nie są obecnie obsługiwane.

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
