---
title: Kopiowanie danych z programu Dynamics AX
description: Informacje o kopiowaniu danych z programu Dynamics AX do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/12/2020
ms.openlocfilehash: 38ff77ad56f16fbd33b77021b18be77f6a153b3f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380988"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Kopiowanie danych z programu Dynamics AX przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych ze źródła Dynamics AX. Artykuł jest oparty na [działaniu kopiowania w Azure Data Factory](copy-activity-overview.md), co przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik programu Dynamics AX jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z programu Dynamics AX można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia, zobacz [obsługiwane magazyny i formaty danych](copy-activity-overview.md#supported-data-stores-and-formats).

Ten łącznik systemu Dynamics AX obsługuje kopiowanie danych z systemu Dynamics AX przy użyciu **protokołu OData** z **uwierzytelnianiem jednostki usługi**.

>[!TIP]
>Tego łącznika można również użyć do kopiowania danych z systemu **Dynamics 365 Finanse i operacje**. Zapoznaj się z metodą [obsługi](/dynamics365/unified-operations/dev-itpro/data-entities/odata) i [uwierzytelniania](/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication)OData w usłudze Dynamics 365.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, których można użyć do definiowania jednostek Data Factory, które są specyficzne dla łącznika programu Dynamics AX.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć uwierzytelniania jednostki usługi, wykonaj następujące kroki:

1. Zarejestruj jednostkę aplikacji w Azure Active Directory (Azure AD), wykonując czynności opisane w temacie [Rejestrowanie aplikacji w dzierżawie usługi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Należy zwrócić uwagę na następujące wartości, których można użyć do zdefiniowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Przejdź do systemu Dynamics AX i przyznaj tej jednostce usługi odpowiednie uprawnienia dostępu do systemu Dynamics AX.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Dynamics AX są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** musi być ustawiona na wartość **DynamicsAX**. |Tak |
| url | Punkt końcowy OData usługi Dynamics AX (lub Dynamics 365 — Finanse i operacje). |Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako element **SecureString** , aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| dzierżaw | Określ informacje o dzierżawie (nazwę domeny lub identyfikator dzierżawy), w których znajduje się Twoja aplikacja. Pobierz go, aktywując wskaźnik myszy w prawym górnym rogu Azure Portal. | Tak |
| aadResourceId | Określ zasób usługi AAD, którego żądasz do autoryzacji. Na przykład, jeśli adres URL systemu Dynamics to, jest to `https://sampledynamics.sandbox.operations.dynamics.com/data/` zwykle odpowiedni zasób usługi AAD `https://sampledynamics.sandbox.operations.dynamics.com` . | Tak |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Możesz wybrać Azure Integration Runtime lub własne Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

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

Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Dynamics AX.

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). 

Aby skopiować dane z systemu Dynamics AX, ustaw właściwość **Type** zestawu danych na **DynamicsAXResource**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** zestawu danych musi być ustawiona na wartość **DynamicsAXResource**. | Tak |
| path | Ścieżka do jednostki usługi Dynamics AX OData. | Tak |

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

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Dynamics AX.

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md). 

### <a name="dynamics-ax-as-source"></a>Dynamics AX jako źródło

Aby skopiować dane z systemu Dynamics AX, ustaw typ **źródła** w działaniu Kopiuj na **DynamicsAXSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** źródła działania Copy musi być ustawiona na wartość **DynamicsAXSource**. | Tak |
| query | Opcje zapytania OData dotyczące filtrowania danych. Przykład: `"?$select=Name,Description&$top=5"`.<br/><br/>**Uwaga**: Łącznik kopiuje dane ze połączonego adresu URL: `[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]` . Aby uzyskać więcej informacji, zobacz [składniki URL usługi OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Nie |
| httpRequestTimeout | Limit czasu (wartość **TimeSpan** ) żądania HTTP w celu uzyskania odpowiedzi. Ta wartość jest przekroczeniem limitu czasu w celu uzyskania odpowiedzi, a nie limitu czasu odczytu danych odpowiedzi. Jeśli nie zostanie określony, wartość domyślna to **00:30:00** (30 minut). | Nie |

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


## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia w Azure Data Factory, zobacz [obsługiwane magazyny i formaty danych](copy-activity-overview.md#supported-data-stores-and-formats).