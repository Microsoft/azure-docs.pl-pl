---
title: Kopiowanie danych z systemu Dynamics AX
description: Dowiedz się, jak kopiować dane z systemu Dynamics AX do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
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
ms.date: 08/01/2019
ms.openlocfilehash: 4dd82eea0a80ef81a0f972d1964a62e6c17a80c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417366"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Kopiowanie danych z systemu Dynamics AX przy użyciu usługi Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak kopiować dane ze źródła systemu Dynamics AX za pomocą funkcji Kopiowanie w usłudze Azure Data Factory. Artykuł opiera się na [copy activity w usłudze Azure Data Factory](copy-activity-overview.md), który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Dynamics AX jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Dane z systemu Dynamics AX można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i pochłaniacze, zobacz [Obsługiwane magazyny danych i formaty](copy-activity-overview.md#supported-data-stores-and-formats).

W szczególności ten łącznik Dynamics AX obsługuje kopiowanie danych z systemu Dynamics AX przy użyciu **protokołu OData** z **uwierzytelnianiem jednostki usługi.**

>[!TIP]
>Za pomocą tego łącznika można również kopiować dane z **programu Dynamics 365 Finance and Operations**. Zapoznaj się z metodą obsługi i [uwierzytelniania](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication) [OData](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) firmy Dynamics 365.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach podano szczegółowe informacje o właściwościach, których można użyć do zdefiniowania elementów fabryki danych specyficznych dla łącznika Dynamics AX.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć uwierzytelniania głównego usługi, wykonaj następujące kroki:

1. Zarejestruj jednostkę aplikacji w usłudze Azure Active Directory (Azure AD), wykonując [rejestrację aplikacji u dzierżawy usługi Azure AD.](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) Zanotuj następujące wartości, których używasz do definiowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Przejdź do dynamics AX i przyznaj tej jednostki usługi odpowiednie uprawnienia dostępu do systemu Dynamics AX.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

W przypadku usługi połączonej Dynamics AX obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **type** musi być ustawiona na **DynamicsAX**. |Tak |
| url | Dynamics AX (lub Dynamics 365 Finanse i operacje) punkt końcowy OData wystąpienia. |Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako **SecureString,** aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| Dzierżawy | Określ informacje o dzierżawie (nazwa domeny lub identyfikator dzierżawy), w którym znajduje się aplikacja. Pobierz go, najeżdżając myszą w prawym górnym rogu witryny Azure portal. | Tak |
| aadResourceId | Określ zasób AAD, którego żądasz do autoryzacji. Na przykład, jeśli adres `https://sampledynamics.sandbox.operations.dynamics.com/data/`URL Dynamics jest , `https://sampledynamics.sandbox.operations.dynamics.com`odpowiedni zasób usługi AAD jest zwykle . | Tak |
| connectVia | [Środowisko wykonawcze integracji](concepts-integration-runtime.md) do nawiązywania połączenia z magazynem danych. Można wybrać środowisko uruchomieniowe integracji platformy Azure lub własny hostowany środowisko uruchomieniowe integracji (jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używany jest domyślny środowiska wykonawczego integracji platformy Azure. |Nie |

**Przykład**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Ta sekcja zawiera listę właściwości, które obsługuje zestaw danych Dynamics AX.

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Zestawy danych i połączone usługi](concepts-datasets-linked-services.md). 

Aby skopiować dane z systemu Dynamics AX, ustaw właściwość **typu** zestawu danych na **DynamicsAXResource**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** zestawu danych musi być ustawiona na **DynamicsAXResource**. | Tak |
| ścieżka | Ścieżka do jednostki Dynamics AX OData. | Tak |

**Przykład**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiowanie właściwości działania

Ta sekcja zawiera listę właściwości, które obsługuje źródło Dynamics AX.

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania działań, zobacz [Potoki](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX jako źródło

Aby skopiować dane z systemu Dynamics AX, ustaw typ **źródła** w aktywności kopiowania na **DynamicsAXSource**. Następujące właściwości są obsługiwane w sekcji **Źródło** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** źródła działania kopiowania musi być ustawiona na **DynamicsAXSource**. | Tak |
| query | Opcje kwerendy OData do filtrowania danych. Przykład: `"?$select=Name,Description&$top=5"`.<br/><br/>**Uwaga:** Łącznik kopiuje dane z `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`połączonego adresu URL: . Aby uzyskać więcej informacji, zobacz [Składniki adresu URL OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nie |

**Przykład**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
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
                "type": "DynamicsAXSource",
                "query": "$top=10"
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

Aby uzyskać listę magazynów danych, które działanie kopiowania obsługuje jako źródła i pochłaniacze w usłudze Azure Data Factory, zobacz [Obsługiwane magazyny danych i formaty](copy-activity-overview.md#supported-data-stores-and-formats).
