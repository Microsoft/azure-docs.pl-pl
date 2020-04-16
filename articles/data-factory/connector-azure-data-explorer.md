---
title: Kopiowanie danych do lub z Eksploratora danych platformy Azure
description: Dowiedz się, jak kopiować dane do lub z Usługi Azure Data Explorer przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
ms.author: orspodek
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 12bf807f5866567508b644105f377cfde1494250
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410781"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Kopiowanie danych do lub z usługi Azure Data Explorer przy użyciu usługi Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób kopiowania danych w usłudze Azure Data Factory do kopiowania danych do lub z [Eksploratora danych platformy Azure.](/azure/data-explorer/data-explorer-overview) Opiera się na artykule [omówienie działania kopiowania,](copy-activity-overview.md) który oferuje ogólny przegląd działania kopiowania.

>[!TIP]
>W przypadku ogólnie integracji usługi Azure Data Factory i Azure Data Explorer dowiedz się więcej z [integruj Eksploratora danych platformy Azure z usługą Azure Data Factory.](/azure/data-explorer/data-factory-integration)

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik usługi Azure Data Explorer jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Do Eksploratora danych platformy Azure można skopiować dane z dowolnego obsługiwanego magazynu danych źródłowych. Można również skopiować dane z usługi Azure Data Explorer do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które działanie kopiowania obsługuje jako źródła lub pochłaniacze, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

>[!NOTE]
>Kopiowanie danych do lub z usługi Azure Data Explorer za pośrednictwem lokalnego magazynu danych przy użyciu środowiska wykonawczego integracji hostowanego samodzielnie jest obsługiwane w wersji 3.14 i nowszych.

Za pomocą łącznika usługi Azure Data Explorer można wykonać następujące czynności:

* Kopiowanie danych przy użyciu uwierzytelniania tokenu aplikacji usługi Azure Active Directory (Azure AD) z **jednostką usługi.**
* Jako źródło, pobrać dane przy użyciu kwerendy KQL (Kusto).
* Jako zlewnik dołącz dane do tabeli docelowej.

## <a name="getting-started"></a>Wprowadzenie

>[!TIP]
>Aby zapoznać się z instruktażem łącznika Usługi Azure Data Explorer, zobacz [Kopiowanie danych do/z Eksploratora danych platformy Azure przy użyciu usługi Azure Data Factory](/azure/data-explorer/data-factory-load-data) i [kopiowania zbiorczego z bazy danych do Eksploratora danych platformy Azure.](/azure/data-explorer/data-factory-template)

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek usługi Data Factory specyficznych dla łącznika usługi Azure Data Explorer.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Łącznik Usługi Azure Data Explorer używa uwierzytelniania głównego usługi. Wykonaj następujące kroki, aby uzyskać jednostkę usługi i udzielić uprawnień:

1. Zarejestruj jednostkę aplikacji w usłudze Azure Active Directory, wykonując kroki opisane w [Rejestrze aplikacji u dzierżawy usługi Azure AD.](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) Zanotuj następujące wartości, których używasz do definiowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Udziel podmiotowi usługi poprawnych uprawnień w Eksploratorze danych platformy Azure. Zobacz [Zarządzanie uprawnieniami bazy danych Usługi Azure Data Explorer,](/azure/data-explorer/manage-database-permissions) aby uzyskać szczegółowe informacje o rolach i uprawnieniach oraz o zarządzaniu uprawnieniami. Ogólnie rzecz biorąc, należy:

    - **Jako źródło**, przyznać co najmniej **rolę przeglądarki bazy danych** do bazy danych
    - **Jako zlew**, przyznać co najmniej **rolę ingestor bazy danych** do bazy danych

>[!NOTE]
>Podczas tworzenia interfejsu użytkownika fabryki danych konto użytkownika logowania jest używane do listy klastrów, baz danych i tabel usługi Azure Data Explorer. Ręcznie wprowadź nazwę, jeśli nie masz uprawnień do tych operacji.

Następujące właściwości są obsługiwane dla usługi połączonej usługi Azure Data Explorer:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** musi być ustawiona na **AzureDataExplorer**. | Tak |
| endpoint | Adres URL punktu końcowego klastra usługi `https://<clusterName>.<regionName>.kusto.windows.net`Azure Data Explorer z formatem jako . | Tak |
| database | Nazwa bazy danych. | Tak |
| Dzierżawy | Określ informacje o dzierżawie (nazwa domeny lub identyfikator dzierżawy), w którym znajduje się aplikacja. Jest to znane jako "Identyfikator urzędu" w [ciągu połączenia Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Pobierz go, najeżdżając wskaźnik myszy w prawym górnym rogu witryny Azure portal. | Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. Jest to znane jako "AAD application client ID" w [ciągu połączenia Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Tak |
| servicePrincipalKey | Określ klucz aplikacji. Jest to znane jako "klucz aplikacji AAD" w [ciągu połączenia Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Oznacz to pole jako **SecureString,** aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do bezpiecznych danych przechowywanych w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |

**Przykład połączonych właściwości usługi:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [Zestawy danych w usłudze Azure Data Factory](concepts-datasets-linked-services.md). W tej sekcji wymieniono właściwości, które obsługuje zestaw danych Usługi Azure Data Explorer.

Aby skopiować dane do Usługi Azure Data Explorer, ustaw właściwość typu zestawu danych na **AzureDataExplorerTable**.

Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** musi być ustawiona na **AzureDataExplorerTable**. | Tak |
| tabela | Nazwa tabeli, do których odwołuje się usługa połączona. | Tak dla zlewu; Nie dla źródła |

**Przykład właściwości zestawu danych:**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Potoki i działania w usłudze Azure Data Factory](concepts-pipelines-activities.md). Ta sekcja zawiera listę właściwości, które źródła usługi Azure Data Explorer źródeł i pochłania obsługi.

### <a name="azure-data-explorer-as-source"></a>Eksplorator danych platformy Azure jako źródło

Aby skopiować dane z Eksploratora danych platformy Azure, ustaw właściwość **typu** w źródle działania Kopiowanie na **AzureDataExplorerSource**. Następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** źródła działania kopiowania musi być ustawiona na: **AzureDataExplorerSource** | Tak |
| query | Żądanie tylko do odczytu podane w [formacie KQL](/azure/kusto/query/). Użyj niestandardowej kwerendy KQL jako odwołania. | Tak |
| Querytimeout | Czas oczekiwania przed przesądnieniem żądania kwerendy. Wartość domyślna to 10 min (00:10:00); dozwolona maksymalna wartość to 1 godzina (01:00:00). | Nie |
| noTruncation (nietrunacja) | Wskazuje, czy ma być obcinać zwrócony zestaw wyników. Domyślnie wynik jest obcinany po 500 000 rekordów lub 64 megabajtach (MB). Obcinania jest zdecydowanie zalecane, aby zapewnić prawidłowe zachowanie działania. |Nie |

>[!NOTE]
>Domyślnie źródło usługi Azure Data Explorer ma limit rozmiaru 500 000 rekordów lub 64 MB. Aby pobrać wszystkie rekordy bez obcinania, `set notruncation;` można określić na początku kwerendy. Aby uzyskać więcej informacji, zobacz [Limity kwerend](https://docs.microsoft.com/azure/kusto/concepts/querylimits).

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Eksplorator danych platformy Azure jako ujście

Aby skopiować dane do Usługi Azure Data Explorer, ustaw właściwość typu w ujściu działania kopiowania na **AzureDataExplorerSink**. Następujące właściwości są obsługiwane w sekcji **ujście** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** ujścia działania kopiowania musi być ustawiona na: **AzureDataExplorerSink**. | Tak |
| ingestionMappingName | Nazwa wstępnie utworzonego [mapowania](/azure/kusto/management/mappings#csv-mapping) na tabeli Kusto. Aby zamapować kolumny ze źródła do usługi Azure Data Explorer (która ma zastosowanie do [wszystkich obsługiwanych magazynów źródłowych i formatów](copy-activity-overview.md#supported-data-stores-and-formats), w tym formatów CSV/JSON/Avro), można użyć [mapowania kolumn](copy-activity-schema-and-type-mapping.md) aktywności kopiowania (niejawnie według nazwy lub jawnie skonfigurowanych) i/lub mapowania Usługi Azure Data Explorer. | Nie |
| additionalWłaściwości | Worek właściwości, który może służyć do określania dowolnej właściwości pozyskiwania, które nie są już ustawiane przez ujście usługi Azure Data Explorer. W szczególności może być przydatne do określania tagów pozyskiwania. Dowiedz się więcej z [usługi Azure Data Explore data ingestion doc](https://kusto.azurewebsites.net/docs/management/data-ingestion/index.html). | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby uzyskać więcej informacji o właściwościach, zobacz [Działanie odnośnika](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać listę magazynów danych, które działanie kopiowania w usłudze Azure Data Factory obsługuje jako źródła i pochłaniacze, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

* Dowiedz się więcej o [kopiowaniu danych z usługi Azure Data Factory do Usługi Azure Data Explorer](/azure/data-explorer/data-factory-load-data).
