---
title: Kopiowanie danych do lub z usługi Azure Eksplorator danych
description: Dowiedz się, jak kopiować dane do lub z usługi Azure Eksplorator danych przy użyciu działania kopiowania w potoku Azure Data Factory.
ms.author: orspodek
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 16126e8b9e5c34529016018273edcf65a31e2280
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379985"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Kopiowanie danych do lub z usługi Azure Eksplorator danych przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych do lub z [usługi Azure Eksplorator danych](/azure/data-explorer/data-explorer-overview). Jest on kompilowany w artykule [przegląd działania kopiowania](copy-activity-overview.md) , który oferuje ogólne omówienie działania kopiowania.

>[!TIP]
>Aby uzyskać ogólne informacje na temat Azure Data Factory i integracji Eksplorator danych platformy Azure, Dowiedz się więcej od [integracji Eksplorator danych platformy Azure z usługą Azure Data Factory](/azure/data-explorer/data-factory-integration).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik usługi Azure Eksplorator danych jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane można kopiować z dowolnego obsługiwanego źródłowego magazynu danych na platformę Azure Eksplorator danych. Możesz również skopiować dane z usługi Azure Eksplorator danych do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła lub ujścia można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

>[!NOTE]
>Kopiowanie danych do i z usługi Azure Eksplorator danych za pośrednictwem lokalnego magazynu danych przy użyciu własnego środowiska Integration Runtime jest obsługiwane w wersji 3,14 i nowszych.

Za pomocą łącznika usługi Azure Eksplorator danych można wykonać następujące czynności:

* Kopiowanie danych za pomocą usługi Azure Active Directory (Azure AD) uwierzytelnianie tokenu aplikacji z jednostką **usług**.
* Jako źródło Pobierz dane przy użyciu zapytania KQL (Kusto).
* Jako ujścia Dodaj dane do tabeli docelowej.

## <a name="getting-started"></a>Wprowadzenie

>[!TIP]
>Aby zapoznać się z przewodnikiem dotyczącym łącznika usługi Azure Eksplorator danych, zobacz [Kopiowanie danych do/z platformy azure Eksplorator danych przy użyciu Azure Data Factory](/azure/data-explorer/data-factory-load-data) i [kopiowania zbiorczego z bazy danych na platformę Azure Eksplorator danych](/azure/data-explorer/data-factory-template).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania Data Factory jednostek specyficznych dla łącznika usługi Azure Eksplorator danych.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Łącznik usługi Azure Eksplorator danych używa uwierzytelniania jednostki usługi. Wykonaj następujące kroki, aby uzyskać nazwę główną usługi i udzielić uprawnień:

1. Zarejestruj jednostkę aplikacji w Azure Active Directory, wykonując czynności opisane w temacie [Rejestrowanie aplikacji za pomocą dzierżawy usługi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Należy zwrócić uwagę na następujące wartości, których można użyć do zdefiniowania połączonej usługi:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. Przyznaj jednostce usługi odpowiednie uprawnienia w usłudze Azure Eksplorator danych. Aby uzyskać szczegółowe informacje na temat ról i uprawnień oraz zarządzania uprawnieniami, zobacz [Zarządzanie uprawnieniami usługi Azure Eksplorator danych Database](/azure/data-explorer/manage-database-permissions) . Ogólnie rzecz biorąc, należy:

    - **Jako źródło** Udziel co najmniej roli **przeglądarki bazy** danych do bazy danych
    - **Jako ujścia**, Przydziel co najmniej rolę pozyskiwania **bazy danych** do bazy danych

>[!NOTE]
>Korzystając z interfejsu użytkownika Data Factory do tworzenia, konto użytkownika logowania służy do wyświetlania listy klastrów Eksplorator danych, baz danych i tabel platformy Azure. Ręcznie wprowadź nazwę, jeśli nie masz uprawnień do tych operacji.

Dla połączonej usługi Azure Eksplorator danych są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** musi być ustawiona na wartość **AzureDataExplorer**. | Tak |
| endpoint | Adres URL punktu końcowego klastra Eksplorator danych platformy Azure z formatem jako `https://<clusterName>.<regionName>.kusto.windows.net` . | Tak |
| database | Nazwa bazy danych. | Tak |
| dzierżaw | Określ informacje o dzierżawie (nazwę domeny lub identyfikator dzierżawy), w których znajduje się Twoja aplikacja. Jest on znany jako "Identyfikator urzędu" w [parametrach połączenia Kusto](/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Pobierz go, aktywując wskaźnik myszy w prawym górnym rogu Azure Portal. | Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. Jest to nazywane "IDENTYFIKATORem klienta aplikacji usługi AAD" w [parametrach połączenia Kusto](/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Tak |
| servicePrincipalKey | Określ klucz aplikacji. Jest to tzw. "klucz aplikacji usługi AAD" w [parametrach połączenia Kusto](/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Oznacz to pole jako element **SecureString** , aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do zabezpieczonych danych przechowywanych w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |

**Przykład właściwości połączonej usługi:**

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [zestawy danych w Azure Data Factory](concepts-datasets-linked-services.md). Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Eksplorator danych platformy Azure.

Aby skopiować dane do usługi Azure Eksplorator danych, ustaw właściwość Type zestawu danych na **AzureDataExplorerTable**.

Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** musi być ustawiona na wartość **AzureDataExplorerTable**. | Tak |
| tabela | Nazwa tabeli, do której odwołuje się połączona usługa. | Tak dla ujścia; Nie dla źródła |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [potoki i działania w Azure Data Factory](concepts-pipelines-activities.md). Ta sekcja zawiera listę właściwości, które obsługuje usługa Azure Eksplorator danych źródła i ujścia.

### <a name="azure-data-explorer-as-source"></a>Eksplorator danych platformy Azure jako źródło

Aby skopiować dane z usługi Azure Eksplorator danych, ustaw właściwość **Type** w źródle działania Copy na **AzureDataExplorerSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** źródła działania Copy musi być ustawiona na wartość: **AzureDataExplorerSource** | Tak |
| query | Żądanie tylko do odczytu podawane w [formacie KQL](/azure/kusto/query/). Użyj niestandardowego zapytania KQL jako odwołania. | Tak |
| queryTimeout | Czas oczekiwania przed upływem limitu czasu żądania zapytania. Wartość domyślna to 10 min (00:10:00); dozwolona maksymalna wartość to 1 godzina (01:00:00). | Nie |
| notruncateer | Wskazuje, czy ma zostać obcięta zwracany zestaw wyników. Domyślnie wynik jest obcinany po 500 000 rekordach lub 64 megabajtów (MB). Obcinanie jest zdecydowanie zalecane, aby zapewnić poprawne zachowanie działania. |Nie |

>[!NOTE]
>Domyślnie usługa Azure Eksplorator danych source ma limit rozmiaru wynoszący 500 000 rekordów lub 64 MB. Aby pobrać wszystkie rekordy bez obcinania, można określić `set notruncation;` na początku zapytania. Aby uzyskać więcej informacji, zobacz [limity zapytań](/azure/kusto/concepts/querylimits).

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

### <a name="azure-data-explorer-as-sink"></a>Eksplorator danych platformy Azure jako ujścia

Aby skopiować dane do usługi Azure Eksplorator danych, ustaw właściwość Type w ujścia działania Copy na **AzureDataExplorerSink**. W sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** ujścia działania Copy musi być ustawiona na wartość: **AzureDataExplorerSink**. | Tak |
| ingestionMappingName | Nazwa wstępnie utworzonego [mapowania](/azure/kusto/management/mappings#csv-mapping) dla tabeli Kusto. Aby zmapować kolumny ze źródła do Eksplorator danych platformy Azure (które mają zastosowanie do [wszystkich obsługiwanych magazynów i formatów źródłowych](copy-activity-overview.md#supported-data-stores-and-formats), w tym formatów CSV/JSON/Avro), można użyć [mapowania kolumny](copy-activity-schema-and-type-mapping.md) działania kopiowania (niejawnie według nazwy lub jawnie skonfigurowane) i/lub mapowań Eksplorator danych platformy Azure. | Nie |
| additionalProperties | Zbiór właściwości, który może służyć do określania wszelkich właściwości pozyskiwania, które nie są ustawiane jeszcze przez ujścia Eksplorator danych platformy Azure. W odniesieniu do nich można określić Tagi pozyskiwania. Dowiedz się więcej z dokumentacji pozyskiwania [danych na platformie Azure](/azure/data-explorer/ingestion-properties). | Nie |

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

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby uzyskać więcej informacji o właściwościach, zobacz [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać listę magazynów danych, które działanie kopiowania w Azure Data Factory obsługuje jako źródła i ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).

* Dowiedz się więcej na temat sposobu [kopiowania danych z Azure Data Factory do usługi Azure Eksplorator danych](/azure/data-explorer/data-factory-load-data).