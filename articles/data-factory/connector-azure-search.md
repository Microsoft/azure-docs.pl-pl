---
title: Kopiuj dane do indeksu wyszukiwania
description: Informacje o sposobie wypychania lub kopiowania danych do indeksu usługi Azure Search przy użyciu działania kopiowania w potoku Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 82f5d4f4c3f12b6e14e260fa004bb031247e1096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597508"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Kopiowanie danych do indeksu Wyszukiwanie poznawcze platformy Azure przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-search-connector.md)
> * [Bieżąca wersja](connector-azure-search.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych do usługi Azure Wyszukiwanie poznawcze index. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Dane można kopiować z dowolnego obsługiwanego źródłowego magazynu danych do indeksu wyszukiwania. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania Data Factory jednostek specyficznych dla łącznika usługi Azure Wyszukiwanie poznawcze.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi Azure Wyszukiwanie poznawcze są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **AzureSearch** | Tak |
| url | Adres URL usługi wyszukiwania. | Tak |
| key | Klucz administratora dla usługi wyszukiwania. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

> [!IMPORTANT]
> Podczas kopiowania danych z magazynu danych w chmurze do indeksu wyszukiwania w usłudze Azure Wyszukiwanie poznawcze połączonej usłudze należy odwołać się do Azure Integration Runtime z jawnym regionem w connactVia. Ustaw region jako lokalizację, w której znajduje się Twoja usługa wyszukiwania. Dowiedz się więcej z [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

**Przykład:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych Wyszukiwanie poznawcze platformy Azure.

Aby skopiować dane do usługi Azure Wyszukiwanie poznawcze, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość: **AzureSearchIndex** | Tak |
| indexName | Nazwa indeksu wyszukiwania. Data Factory nie tworzy indeksu. Indeks musi istnieć na platformie Azure Wyszukiwanie poznawcze. | Tak |

**Przykład:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Wyszukiwanie poznawcze platformy Azure.

### <a name="azure-cognitive-search-as-sink"></a>Wyszukiwanie poznawcze platformy Azure jako ujścia

Aby skopiować dane do usługi Azure Wyszukiwanie poznawcze, ustaw typ źródła w działaniu Copy na **AzureSearchIndexSink**. W sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **AzureSearchIndexSink** | Tak |
| writeBehavior | Określa, czy należy scalić lub zamienić, gdy dokument już istnieje w indeksie. Zobacz [Właściwość WriteBehavior](#writebehavior-property).<br/><br/>Dozwolone wartości to: **Scal** (wartość domyślna) i **Przekaż**. | Nie |
| writeBatchSize | Przekazuje dane do indeksu wyszukiwania, gdy rozmiar buforu osiągnie writeBatchSize. Aby uzyskać szczegółowe informacje, zobacz [Właściwość WriteBatchSize](#writebatchsize-property) .<br/><br/>Dozwolone wartości to: integer od 1 do 1 000; wartość domyślna to 1000. | Nie |
| maxConcurrentConnections |Górny limit równoczesnych połączeń ustanowiony dla magazynu danych podczas uruchamiania działania. Określ wartość tylko wtedy, gdy chcesz ograniczyć połączenia współbieżne.| Nie |

### <a name="writebehavior-property"></a>Właściwość WriteBehavior

AzureSearchSink upserts podczas zapisywania danych. Innymi słowy podczas pisania dokumentu, jeśli klucz dokumentu istnieje już w indeksie wyszukiwania, usługa Azure Wyszukiwanie poznawcze aktualizuje istniejący dokument zamiast zgłaszania wyjątku konfliktu.

AzureSearchSink udostępnia następujące dwa zachowania upsert (przy użyciu zestawu SDK AzureSearch):

- **Scalanie**: Połącz wszystkie kolumny w nowym dokumencie z istniejącym elementem. W przypadku kolumn o wartości null w nowym dokumencie jest zachowywana wartość w istniejącej.
- **Przekazywanie**: nowy dokument zastępuje istniejący. W przypadku kolumn nieokreślonych w nowym dokumencie wartość jest ustawiana na wartość null niezależnie od tego, czy w istniejącym dokumencie istnieje wartość inna niż null.

Zachowanie domyślne jest **scalane**.

### <a name="writebatchsize-property"></a>Właściwość WriteBatchSize

Usługa Azure Wyszukiwanie poznawcze obsługuje pisanie dokumentów jako partii. Zadanie wsadowe może zawierać od 1 do 1 000 akcji. Akcja obsługuje jeden dokument, aby wykonać operację przekazywania/scalania.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Cognitive Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

## <a name="data-type-support"></a>Obsługa typu danych

W poniższej tabeli określono, czy typ danych Wyszukiwanie poznawcze platformy Azure jest obsługiwany.

| Typ danych Wyszukiwanie poznawcze platformy Azure | Obsługiwane w usłudze Azure Wyszukiwanie poznawcze sink |
| ---------------------- | ------------------------------ |
| Ciąg | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| Wartość logiczna | Y |
| DataTimeOffset | Y |
| Tablica ciągów | N |
| GeographyPoint względem | N |

Obecnie inne typy danych (np. ComplexType) nie są obsługiwane. Aby uzyskać pełną listę obsługiwanych typów danych Wyszukiwanie poznawcze platformy Azure, zobacz [obsługiwane typy danych (Azure wyszukiwanie poznawcze)](/rest/api/searchservice/supported-data-types).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).