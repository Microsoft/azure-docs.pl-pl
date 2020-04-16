---
title: Kopiowanie danych ze źródeł OData przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak skopiować dane ze źródeł OData do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
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
ms.openlocfilehash: c2fe6b6cc7b52dda9f2beffa444f1965723ea92a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416924"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Kopiowanie danych ze źródła OData przy użyciu usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-odata-connector.md)
> * [Bieżąca wersja](connector-odata.md)

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych ze źródła OData. Artykuł opiera się na [copy activity w usłudze Azure Data Factory](copy-activity-overview.md), który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik OData jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane ze źródła OData do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i pochłaniacze, zobacz [Obsługiwane magazyny danych i formaty](copy-activity-overview.md#supported-data-stores-and-formats).

W szczególności ten łącznik OData obsługuje:

- OData w wersji 3.0 i 4.0.
- Kopiowanie danych przy użyciu jednego z następujących uwierzytelniania: **Anonimowy**, **Podstawowy,** **Windows**i **AAD service principal**.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, których można użyć do zdefiniowania jednostek fabryki danych, które są specyficzne dla łącznika OData.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej OData:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** musi być ustawiona na **OData**. |Tak |
| url | Główny adres URL usługi OData. |Tak |
| authenticationType | Typ uwierzytelniania używanego do łączenia się ze źródłem OData. Dozwolone wartości to **Anonymous**, **Basic**, **Windows**i **AadServicePrincipal**. OAuth oparte na użytkownikach nie jest obsługiwane. | Tak |
| userName | Określ **nazwę użytkownika,** jeśli używasz uwierzytelniania podstawowego lub uwierzytelniania systemu Windows. | Nie |
| hasło | Określ **hasło** dla konta użytkownika określonego dla **użytkownika .** Oznacz to pole jako typ **SecureString,** aby bezpiecznie przechowywać go w fabryce danych. Można również [odwoływać się do klucza tajnego przechowywanego w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Nie |
| servicePrincipalId | Określ identyfikator klienta aplikacji usługi Azure Active Directory. | Nie |
| aadServicePrincipalCredentialType | Określ typ poświadczeń, który ma być używany do uwierzytelniania jednostkowego usługi. Dozwolone wartości `ServicePrincipalKey` to: `ServicePrincipalCert`lub . | Nie |
| servicePrincipalKey | Określ klucz aplikacji usługi Azure Active Directory. Oznacz to pole jako **SecureString,** aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Nie |
| servicePrincipalEmbeddedCert | Określ zakodowany certyfikat base64 aplikacji zarejestrowanej w usłudze Azure Active Directory. Oznacz to pole jako **SecureString,** aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Nie |
| usługaPrincipalEmbeddedCertPassword | Określ hasło certyfikatu, jeśli certyfikat jest zabezpieczony hasłem. Oznacz to pole jako **SecureString,** aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md)  | Nie|
| Dzierżawy | Określ informacje o dzierżawie (nazwa domeny lub identyfikator dzierżawy), w którym znajduje się aplikacja. Pobierz go, najeżdżając myszą w prawym górnym rogu witryny Azure portal. | Nie |
| aadResourceId | Określ zasób AAD, którego żądasz do autoryzacji.| Nie |
| connectVia | [Środowisko wykonawcze integracji](concepts-integration-runtime.md) do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie zostanie określony, używany jest domyślny środowiska wykonawczego integracji platformy Azure. |Nie |

**Przykład 1: Korzystanie z uwierzytelniania anonimowego**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład 2: Korzystanie z uwierzytelniania podstawowego**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
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

**Przykład 3: Korzystanie z uwierzytelniania systemu Windows**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
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

**Przykład 4: Korzystanie z uwierzytelniania klucza głównego usługi**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**Przykład 5: Korzystanie z uwierzytelniania certyfikatu jednostki usługi**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Ta sekcja zawiera listę właściwości, które obsługuje zestaw danych OData.

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Zestawy danych i połączone usługi](concepts-datasets-linked-services.md). 

Aby skopiować dane z odata, ustaw właściwość **typu** zestawu danych na **ODataResource**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** zestawu danych musi być ustawiona na **ODataResource**. | Tak |
| ścieżka | Ścieżka do zasobu OData. | Tak |

**Przykład**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiowanie właściwości działania

Ta sekcja zawiera listę właściwości, które obsługuje źródło OData.

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania działań, zobacz [Potoki](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>OData jako źródło

Aby skopiować dane z odata, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** źródła działania kopiowania musi być ustawiona na **ODataSource**. | Tak |
| query | Opcje kwerendy OData do filtrowania danych. Przykład: `"$select=Name,Description&$top=5"`.<br/><br/>**Uwaga:** Łącznik OData kopiuje dane `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]`z połączonego adresu URL: . Aby uzyskać więcej informacji, zobacz [Składniki adresu URL OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nie |

**Przykład**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Jeśli używasz `RelationalSource` wpisanego źródła, jest ono nadal obsługiwane w stanie as-is, podczas gdy zaleca się użycie nowego w przyszłości.

## <a name="data-type-mapping-for-odata"></a>Mapowanie typu danych dla OData

Podczas kopiowania danych z OData, następujące mapowania są używane między typami danych OData i usługi Azure Data Factory tymczasowych typów danych. Aby dowiedzieć się, jak aktywność kopiowania mapuje schemat źródłowy i typ danych do ujścia, zobacz [Mapowanie schematu i typów danych](copy-activity-schema-and-type-mapping.md).

| Typ danych OData | Tymczasowy typ danych data factory |
|:--- |:--- |
| Edm.Binary (Edm.Binary) | Bajt[] |
| Edm.Boolean | Wartość logiczna |
| Edm.Byte (Edm.Byte) | Bajt[] |
| Edm.DateTime | DateTime |
| Edm.Decimal (Edm.Decimal) | Wartość dziesiętna |
| Edm.Double | Double |
| Edm.Single (Edm.Pojedyncze) | Single |
| Edm.Guid (Edm.Guid) | Guid (identyfikator GUID) |
| Edm.Int16 (Edm.Int16) | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte (Edm.SByte) | Int16 |
| Edm.String | Ciąg |
| Edm.Time (Edm.Time) | przedział_czasu |
| Edm.DateTimeOffset | Datetimeoffset |

> [!NOTE]
> OData złożonych typów danych (takich jak **Object**) nie są obsługiwane.


## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych, które działanie kopiowania obsługuje jako źródła i pochłaniacze w usłudze Azure Data Factory, zobacz [Obsługiwane magazyny danych i formaty](copy-activity-overview.md#supported-data-stores-and-formats).