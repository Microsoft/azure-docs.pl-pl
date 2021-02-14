---
title: Wypychanie danych do indeksu wyszukiwania przy użyciu Data Factory
description: Dowiedz się więcej o sposobie wypychania danych do indeksu usługi Azure Wyszukiwanie poznawcze przy użyciu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0226ab75d53733b94a9ae5734b42b7340998759c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379271"
---
# <a name="push-data-to-an-azure-cognitive-search-index-by-using-azure-data-factory"></a>Wypychanie danych do indeksu Wyszukiwanie poznawcze platformy Azure przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-azure-search-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-search.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Azure wyszukiwanie poznawcze Connector w wersji 2](../connector-azure-search.md).

W tym artykule opisano sposób użycia działania kopiowania w celu wypychania danych z obsługiwanego źródłowego magazynu danych do indeksu Wyszukiwanie poznawcze platformy Azure. Obsługiwane źródłowe magazyny danych są wymienione w kolumnie Source w tabeli [obsługiwane źródła i ujścia](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . W tym artykule opisano informacje o [działaniach związanych z przenoszeniem danych](data-factory-data-movement-activities.md) , które przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania i obsługiwanych kombinacji magazynu danych.

## <a name="enabling-connectivity"></a>Włączanie łączności
Aby umożliwić usłudze Data Factory łączenie się z lokalnym magazynem danych, należy zainstalować bramę Zarządzanie danymi w środowisku lokalnym. Bramę można zainstalować na tym samym komputerze, na którym znajduje się źródłowy magazyn danych lub na oddzielnej maszynie, aby uniknąć konkurowania zasobów z magazynem danych.

Usługa Zarządzanie danymi Gateway łączy lokalne źródła danych z usługami w chmurze w bezpieczny i zarządzany sposób. Szczegółowe informacje na temat bramy Zarządzanie danymi można znaleźć w artykule [przenoszenie danych między środowiskiem lokalnym i chmurą](data-factory-move-data-between-onprem-and-cloud.md) .

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok za pomocą działania kopiowania, które wypycha dane ze źródłowego magazynu danych do indeksu wyszukiwania przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) na potrzeby szybkiego instruktażu dotyczącego tworzenia potoku przy użyciu Kreatora kopiowania danych.

Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon Azure Resource Manager**, interfejs API **platformy .NET** i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych.
2. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON.  Aby uzyskać przykład z definicjami JSON dla Data Factory jednostek, które są używane do kopiowania danych do indeksu wyszukiwania, zobacz [przykład JSON: kopiowanie danych z SQL Server do sekcji indeksu wyszukiwanie poznawcze platformy Azure](#json-example-copy-data-from-sql-server-to-azure-cognitive-search-index) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania jednostek Data Factory specyficznych dla indeksu wyszukiwania:

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Poniższa tabela zawiera opisy elementów JSON, które są specyficzne dla połączonej usługi Wyszukiwanie poznawcze platformy Azure.

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| typ | Właściwość Type musi mieć wartość: **AzureSearch**. | Tak |
| url | Adres URL usługi wyszukiwania. | Tak |
| key | Klucz administratora dla usługi wyszukiwania. | Tak |

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych, są podobne dla wszystkich typów zestawów danych. Sekcja **typeProperties** jest inna dla każdego typu zestawu danych. Sekcja typeProperties zestawu danych typu **AzureSearchIndex** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| -------- | ----------- | -------- |
| typ | Właściwość Type musi być ustawiona na wartość **AzureSearchIndex**.| Tak |
| indexName | Nazwa indeksu wyszukiwania. Data Factory nie tworzy indeksu. Indeks musi istnieć na platformie Azure Wyszukiwanie poznawcze. | Tak |


## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości takie jak nazwa, opis, tabele wejściowe i wyjściowe oraz różne zasady są dostępne dla wszystkich typów działań. Natomiast właściwości dostępne w sekcji typeProperties różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się w zależności od typów źródeł i ujścia.

W przypadku działania kopiowania, gdy ujścia ma typ **AzureSearchIndexSink**, w sekcji typeProperties są dostępne następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Określa, czy należy scalić lub zamienić, gdy dokument już istnieje w indeksie. Zobacz [Właściwość WriteBehavior](#writebehavior-property).| Scal (domyślnie)<br/>Przekazywanie| Nie |
| WriteBatchSize | Przekazuje dane do indeksu wyszukiwania, gdy rozmiar buforu osiągnie writeBatchSize. Aby uzyskać szczegółowe informacje, zobacz [Właściwość WriteBatchSize](#writebatchsize-property) . | od 1 do 1 000. Wartość domyślna to 1000. | Nie |

### <a name="writebehavior-property"></a>Właściwość WriteBehavior
AzureSearchSink upserts podczas zapisywania danych. Innymi słowy podczas pisania dokumentu, jeśli klucz dokumentu istnieje już w indeksie wyszukiwania, usługa Azure Wyszukiwanie poznawcze aktualizuje istniejący dokument zamiast zgłaszania wyjątku konfliktu.

AzureSearchSink udostępnia następujące dwa zachowania upsert (przy użyciu zestawu SDK AzureSearch):

- **Scalanie**: Połącz wszystkie kolumny w nowym dokumencie z istniejącym elementem. W przypadku kolumn o wartości null w nowym dokumencie jest zachowywana wartość w istniejącej.
- **Przekazywanie**: nowy dokument zastępuje istniejący. W przypadku kolumn nieokreślonych w nowym dokumencie wartość jest ustawiana na wartość null niezależnie od tego, czy w istniejącym dokumencie istnieje wartość inna niż null.

Zachowanie domyślne jest **scalane**.

### <a name="writebatchsize-property"></a>Właściwość WriteBatchSize
Usługa Azure Wyszukiwanie poznawcze obsługuje pisanie dokumentów jako partii. Zadanie wsadowe może zawierać od 1 do 1 000 akcji. Akcja obsługuje jeden dokument, aby wykonać operację przekazywania/scalania.

### <a name="data-type-support"></a>Obsługa typu danych
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

## <a name="json-example-copy-data-from-sql-server-to-azure-cognitive-search-index"></a>Przykład JSON: kopiowanie danych z SQL Server do usługi Azure Wyszukiwanie poznawcze index

Poniższy przykład pokazuje:

1. Połączona usługa typu [AzureSearch](#linked-service-properties).
2. Połączona usługa typu [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [SqlServer](data-factory-sqlserver-connector.md#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureSearchIndex](#dataset-properties).
4. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa elementu [sqlsource](data-factory-sqlserver-connector.md#copy-activity-properties) i [AzureSearchIndexSink](#copy-activity-properties).

Przykład kopiuje dane szeregów czasowych z bazy danych SQL Server, aby wyszukiwać indeks co godzinę. Właściwości JSON używane w tym przykładzie są opisane w sekcjach poniżej przykładów.

Pierwszym krokiem jest skonfigurowanie bramy zarządzania danymi na maszynie lokalnej. Instrukcje dotyczą [przemieszczania danych między lokalizacjami lokalnymi i artykułami w chmurze](data-factory-move-data-between-onprem-and-cloud.md) .

**Połączona usługa Azure Wyszukiwanie poznawcze:**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**SQL Server połączona usługa**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**SQL Server wejściowy zestaw danych**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w SQL Server i zawiera kolumnę o nazwie "timestampcolumn" dla danych szeregów czasowych. Można wykonywać zapytania dotyczące wielu tabel w tej samej bazie danych przy użyciu jednego zestawu danych, ale dla tabeli dataset typeProperty należy użyć pojedynczej tabeli.

Ustawienie "External": "true" informuje Data Factory usługę, że zestaw danych jest zewnętrzny względem fabryki danych i nie jest tworzony przez działanie w fabryce danych.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Zestaw danych wyjściowych usługi Azure Wyszukiwanie poznawcze:**

Przykład kopiuje dane do indeksu Wyszukiwanie poznawcze platformy Azure o nazwie **Products**. Data Factory nie tworzy indeksu. Aby przetestować przykład, Utwórz indeks o tej nazwie. Utwórz indeks wyszukiwania z taką samą liczbą kolumn jak w zestawie danych wejściowych. Nowe wpisy są dodawane do indeksu wyszukiwania co godzinę.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

**Działanie kopiowania w potoku za pomocą źródła SQL i ujścia indeksu Wyszukiwanie poznawcze Azure:**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma ustawioną wartość **sqlsource** , a typ **ujścia** to **AzureSearchIndexSink**. Zapytanie SQL określone dla właściwości **SqlReaderQuery** wybiera dane w ciągu ostatniej godziny do skopiowania.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

W przypadku kopiowania danych z magazynu danych w chmurze do usługi Azure Wyszukiwanie poznawcze `executionLocation` Właściwość jest wymagana. Poniższy fragment kodu JSON pokazuje zmianę potrzebną w ramach działania kopiowania `typeProperties` jako przykładu. Sprawdź opcję [Kopiuj dane między magazynami danych w chmurze](data-factory-data-movement-activities.md#global) , aby poznać obsługiwane wartości i więcej szczegółów.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>Kopiowanie ze źródła w chmurze
W przypadku kopiowania danych z magazynu danych w chmurze do usługi Azure Wyszukiwanie poznawcze `executionLocation` Właściwość jest wymagana. Poniższy fragment kodu JSON pokazuje zmianę potrzebną w ramach działania kopiowania `typeProperties` jako przykładu. Sprawdź opcję [Kopiuj dane między magazynami danych w chmurze](data-factory-data-movement-activities.md#global) , aby poznać obsługiwane wartości i więcej szczegółów.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

Można również mapować kolumny ze źródłowego zestawu danych do kolumn z zestawu danych ujścia w definicji działania kopiowania. Aby uzyskać szczegółowe informacje, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Zapoznaj się z [przewodnikiem dotyczącym wydajności i dostrajania działania kopiowania](data-factory-copy-activity-performance.md) , aby poznać kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) i różne sposoby jego optymalizacji.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

* [Samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) zawiera instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania.
