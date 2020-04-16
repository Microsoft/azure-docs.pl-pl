---
title: Kopiowanie danych do indeksu wyszukiwania
description: Dowiedz się, jak wypychać lub kopiować dane do indeksu wyszukiwania platformy Azure przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/13/2019
ms.openlocfilehash: dfa1ad318ccc9e891b646ec050f6a0776e108206
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418239"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Kopiowanie danych do indeksu usługi Azure Cognitive Search przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-azure-search-connector.md)
> * [Bieżąca wersja](connector-azure-search.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych do indeksu usługi Azure Cognitive Search. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Dane z dowolnego obsługiwanego magazynu danych źródłowych można skopiować do indeksu wyszukiwania. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek usługi Data Factory specyficznych dla łącznika usługi Azure Cognitive Search.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej usługi Azure Cognitive Search:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **AzureSearch** | Tak |
| url | adres URL usługi wyszukiwania. | Tak |
| key | Klucz administratora usługi wyszukiwania. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie (jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

> [!IMPORTANT]
> Podczas kopiowania danych z magazynu danych w chmurze do indeksu wyszukiwania w usłudze połączonej usługi Azure Cognitive Search należy odwołać się do środowiska wykonawczego integracji platformy Azure z jawnym regionem w connactVia. Ustaw region jako region, w którym znajduje się usługa wyszukiwania. Dowiedz się więcej z [środowiska wykonawczego integracji platformy Azure](concepts-integration-runtime.md#azure-integration-runtime).

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych usługi Azure Cognitive Search.

Aby skopiować dane do usługi Azure Cognitive Search, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **AzureSearchIndex** | Tak |
| nazwa indeksu | Nazwa indeksu wyszukiwania. Fabryka danych nie tworzy indeksu. Indeks musi istnieć w usłudze Azure Cognitive Search. | Tak |

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło usługi Azure Cognitive Search.

### <a name="azure-cognitive-search-as-sink"></a>Usługa Azure Cognitive Search jako zlew

Aby skopiować dane do usługi Azure Cognitive Search, ustaw typ źródła w działaniu kopiowania na **AzureSearchIndexSink**. Następujące właściwości są obsługiwane w sekcji **ujście** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **AzureSearchIndexSink** | Tak |
| writeZachody | Określa, czy dokument ma być scalany, czy zamieniany, gdy dokument już istnieje w indeksie. Zobacz [WriteBehavior właściwości](#writebehavior-property).<br/><br/>Dozwolone wartości to: **Scalanie** (domyślnie) i **Przekazywanie**. | Nie |
| writeBatchSize | Przekazuje dane do indeksu wyszukiwania, gdy rozmiar buforu osiągnie writeBatchSize. Zobacz [WriteBatchSize właściwość,](#writebatchsize-property) aby uzyskać szczegółowe informacje.<br/><br/>Dozwolone wartości to: całkowita 1 do 1000; domyślnie wynosi 1000. | Nie |

### <a name="writebehavior-property"></a>WriteBehavior właściwość

Usługa AzureSearchSink upserts podczas zapisywania danych. Innymi słowy podczas pisania dokumentu, jeśli klucz dokumentu już istnieje w indeksie wyszukiwania, usługa Azure Cognitive Search aktualizuje istniejący dokument, zamiast zgłaszać wyjątek konfliktu.

Usługa AzureSearchSink udostępnia następujące dwa zachowania upsert (przy użyciu narzędzia AzureSearch SDK):

- **Scalanie**: połącz wszystkie kolumny w nowym dokumencie z istniejącą. W przypadku kolumn o wartości null w nowym dokumencie wartość w istniejącym jest zachowywana.
- **Przekaż**: Nowy dokument zastępuje istniejący. W przypadku kolumn niewymienione w nowym dokumencie wartość jest ustawiona na wartość null, niezależnie od tego, czy w istniejącym dokumencie jest wartość inną niż null.

Domyślnym zachowaniem jest **Scalanie**.

### <a name="writebatchsize-property"></a>WriteBatchSize Właściwość

Usługa Azure Cognitive Search obsługuje zapisywanie dokumentów jako partii. Partia może zawierać od 1 do 1000 akcji. Akcja obsługuje jeden dokument do wykonania operacji przekazywania/scalania.

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

## <a name="data-type-support"></a>Obsługa typów danych

W poniższej tabeli określono, czy typ danych usługi Azure Cognitive Search jest obsługiwany, czy nie.

| Typ danych usługi Azure Cognitive Search | Obsługiwane w usłudze Azure Cognitive Search Sink |
| ---------------------- | ------------------------------ |
| Ciąg | Tak |
| Int32 | Tak |
| Int64 | Tak |
| Double | Tak |
| Wartość logiczna | Tak |
| DataTimeOffset (Zestaw danych) | Tak |
| Tablica ciągów | Nie |
| GeografiaPunkt | Nie |

Obecnie inne typy danych, np. Aby uzyskać pełną listę typów danych obsługiwanych przez usługę Azure Cognitive Search, zobacz [Obsługiwane typy danych (Usługa Azure Cognitive Search).](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
