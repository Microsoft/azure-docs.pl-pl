---
title: Przenoszenie danych do/z tabeli platformy Azure
description: Dowiedz się, jak przenosić dane do/z usługi Azure Table Storage przy użyciu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: cc8e0272dc690ed541883ae943c118cbbe2f1854
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392123"
---
# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Przenoszenie danych do i z tabeli platformy Azure przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-azure-table-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-table-storage.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Azure Table Storage Connector w wersji 2](../connector-azure-table-storage.md).

W tym artykule wyjaśniono, jak za pomocą działania kopiowania w Azure Data Factory przenieść dane do/z usługi Azure Table Storage. Jest on używany w artykule dotyczącym [przenoszenia danych](data-factory-data-movement-activities.md) , który przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania. 

Dane można kopiować z dowolnego obsługiwanego źródłowego magazynu danych na platformę Azure Table Storage lub z usługi Azure Table Storage do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła lub ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok za pomocą działania kopiowania, które przenosi dane do/z Table Storage platformy Azure przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) na potrzeby szybkiego instruktażu dotyczącego tworzenia potoku przy użyciu Kreatora kopiowania danych.

Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon Azure Resource Manager**, interfejs API **platformy .NET** i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia: 

1. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych.
2. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania. 
3. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. 

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON. Przykłady zawierające definicje JSON dla Data Factory jednostek, które są używane do kopiowania danych do/z Table Storage platformy Azure, zobacz sekcję [przykłady JSON](#json-examples) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania Data Factory jednostek specyficznych dla platformy Azure Table Storage: 

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Istnieją dwa typy połączonych usług, których można użyć do łączenia magazynu obiektów blob platformy Azure z fabryką danych Azure. Są to: **AzureStorage** połączonej usługi i **AzureStorageSas** połączonej usługi. Połączona usługa Azure Storage udostępnia fabrykę danych z globalnym dostępem do usługi Azure Storage. W związku z tym połączona usługa Azure Storage SAS (Sygnatura dostępu współdzielonego) udostępnia fabrykę danych z ograniczonym dostępem do usługi Azure Storage. Nie istnieją inne różnice między tymi dwoma połączonymi usługami. Wybierz połączoną usługę, która odpowiada Twoim potrzebom. Poniższe sekcje zawierają więcej informacji na temat tych dwóch połączonych usług.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych są podobne dla wszystkich typów zestawu danych (Azure SQL, Azure Blob, Azure Table itp.).

Sekcja typeProperties jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w magazynie danych. Sekcja **typeProperties** zestawu danych typu **Azure** ma następujące właściwości.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych tabel platformy Azure, do której odwołuje się połączona usługa. |Tak. Gdy tabelaname jest określona bez azureTableSourceQuery, wszystkie rekordy z tabeli są kopiowane do lokalizacji docelowej. Jeśli określono również azureTableSourceQuery, rekordy z tabeli, która spełnia zapytanie, są kopiowane do miejsca docelowego. |

### <a name="schema-by-data-factory"></a>Schemat według Data Factory
W przypadku magazynów danych bez schematu, takich jak Azure Table, Usługa Data Factoryą wnioskuje schemat w jeden z następujących sposobów:

1. Jeśli określisz strukturę danych przy użyciu właściwości **Structure** w definicji zestawu danych, Usługa Data Factory uznaje tę strukturę za schemat. W takim przypadku, jeśli wiersz nie zawiera wartości dla kolumny, zostanie dla niego podana wartość null.
2. Jeśli nie określisz struktury danych przy użyciu właściwości **Structure** w definicji zestawu danych, Data Factory zawnioskuje schemat przy użyciu pierwszego wiersza w danych. W takim przypadku, jeśli pierwszy wiersz nie zawiera pełnego schematu, niektóre kolumny zostaną pominięte w wyniku operacji kopiowania.

W związku z tym najlepszym rozwiązaniem dla źródeł danych bez schematu jest określenie struktury danych przy użyciu właściwości **Structure** .

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości, takie jak nazwa, opis, wejściowe i wyjściowe zestawy danych, oraz zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w sekcji typeProperties działania z drugiej strony różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się w zależności od typów źródeł i ujścia.

**AzureTableSource** obsługuje następujące właściwości w sekcji typeProperties:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| azureTableSourceQuery |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania w tabeli platformy Azure. Zobacz przykłady w następnej sekcji. |Nie. Gdy tabelaname jest określona bez azureTableSourceQuery, wszystkie rekordy z tabeli są kopiowane do lokalizacji docelowej. Jeśli określono również azureTableSourceQuery, rekordy z tabeli, która spełnia zapytanie, są kopiowane do miejsca docelowego. |
| azureTableSourceIgnoreTableNotFound |Wskazuje, czy w trakcie połknięcia wyjątek tabeli nie istnieje. |Prawda<br/>Fałsz |Nie |

### <a name="azuretablesourcequery-examples"></a>Przykłady azureTableSourceQuery
Jeśli kolumna tabeli platformy Azure jest typu String:

```JSON
azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"
```

Jeśli kolumna tabeli platformy Azure jest typu DateTime:

```JSON
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"
```

**AzureTableSink** obsługuje następujące właściwości w sekcji typeProperties:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Domyślna wartość klucza partycji, która może być używana przez ujścia. |Wartość ciągu. |Nie |
| azureTablePartitionKeyName |Określ nazwę kolumny, której wartości są używane jako klucze partycji. Jeśli nie zostanie określony, AzureTableDefaultPartitionKeyValue jest używany jako klucz partycji. |Nazwa kolumny. |Nie |
| azureTableRowKeyName |Określ nazwę kolumny, której wartości kolumn są używane jako klucz wiersza. Jeśli nie zostanie określony, użyj identyfikatora GUID dla każdego wiersza. |Nazwa kolumny. |Nie |
| azureTableInsertType |Tryb wstawiania danych do tabeli platformy Azure.<br/><br/>Ta właściwość określa, czy istniejące wiersze w tabeli wyjściowej ze zgodnymi partycjami i kluczami wierszy mają zamienione lub scalone wartości. <br/><br/>Aby dowiedzieć się, jak działają te ustawienia (Scalanie i zamienianie), zobacz sekcję [Wstawianie lub scalanie jednostek](/rest/api/storageservices/Insert-Or-Merge-Entity) oraz [Wstawianie lub zastępowanie](/rest/api/storageservices/Insert-Or-Replace-Entity) tematów. <br/><br> To ustawienie jest stosowane na poziomie wiersza, a nie na poziomie tabeli, a żadna opcja usuwa wiersze w tabeli wyjściowej, które nie istnieją w danych wejściowych. |Scal (domyślnie)<br/>Zastąp |Nie |
| writeBatchSize |Wstawia dane do tabeli platformy Azure po trafieniu writeBatchSize lub writeBatchTimeout. |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 10000) |
| writeBatchTimeout |Wstawia dane do tabeli platformy Azure po trafieniu writeBatchSize lub writeBatchTimeout |zakres czasu<br/><br/>Przykład: "00:20:00" (20 minut) |Nie (Domyślnie wartość domyślna limitu czasu klienta magazynu 90 s) |

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Mapowanie kolumny źródłowej do kolumny docelowej przy użyciu właściwości JSON usługi translator, aby można było użyć kolumny docelowej jako azureTablePartitionKeyName.

W poniższym przykładzie kolumna źródłowa DivisionID jest mapowana do kolumny docelowej: DivisionID.  

```JSON
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```
DivisionID jest określony jako klucz partycji.

```JSON
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID",
    "writeBatchSize": 100,
    "writeBatchTimeout": "01:00:00"
}
```
## <a name="json-examples"></a>Przykłady JSON
W poniższych przykładach przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują one sposób kopiowania danych do i z usługi Azure Table Storage i usługi Azure Blob Database. Dane można jednak skopiować **bezpośrednio** z dowolnego źródła do dowolnego z obsługiwanych ujścia. Aby uzyskać więcej informacji, zobacz sekcję "obsługiwane magazyny i formaty danych" w temacie [przenoszenie danych za pomocą działania kopiowania](data-factory-data-movement-activities.md).

## <a name="example-copy-data-from-azure-table-to-azure-blob"></a>Przykład: kopiowanie danych z tabeli platformy Azure do obiektu blob platformy Azure
Poniższy przykład pokazuje:

1. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (używana dla obu tabel & obiektów BLOB).
2. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [Azure](#dataset-properties).
3. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z AzureTableSource i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane należące do domyślnej partycji w tabeli platformy Azure do obiektu BLOB co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

**Połączona usługa Azure Storage:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory obsługuje dwa typy połączonych usług Azure Storage: **AzureStorage** i **AzureStorageSas**. W pierwszej kolejności należy określić parametry połączenia, które zawierają klucz konta i dla późniejszej, należy określić identyfikator URI sygnatury dostępu współdzielonego (SAS). Szczegóły można znaleźć w sekcji [połączone usługi](#linked-service-properties) .  

**Zestaw danych wejściowych tabeli platformy Azure:**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w tabeli platformy Azure.

Ustawienie "External": "true" informuje usługę Data Factory, że zestaw danych jest zewnętrzny względem fabryki danych i nie jest tworzony przez działanie w fabryce danych.

```JSON
{
  "name": "AzureTableInput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
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

**Wyjściowy zestaw danych obiektów blob platformy Azure:**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu dla obiektu BLOB jest obliczana dynamicznie na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny rozpoczęcia.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Działanie Copy w potoku z AzureTableSource i wartość blobsink:**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **AzureTableSource** , a typ **ujścia** to **wartość blobsink**. Zapytanie SQL określone za pomocą właściwości **AzureTableSourceQuery** wybiera dane z partycji domyślnej co godzinę do skopiowania.

```JSON
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureTabletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureTableInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureTableSource",
                        "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                    },
                    "sink": {
                        "type": "BlobSink"
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

## <a name="example-copy-data-from-azure-blob-to-azure-table"></a>Przykład: kopiowanie danych z obiektu blob platformy Azure do tabeli platformy Azure
Poniższy przykład pokazuje:

1. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) (używana dla obu tabel & obiektów BLOB)
2. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
3. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [Azure](#dataset-properties).
4. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [AzureTableSink](#copy-activity-properties).

Przykład kopiuje dane szeregów czasowych z obiektu blob platformy Azure do tabeli platformy Azure co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

**Usługa Azure Storage (dla połączonej usługi Azure Table & BLOB):**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

Azure Data Factory obsługuje dwa typy połączonych usług Azure Storage: **AzureStorage** i **AzureStorageSas**. W pierwszej kolejności należy określić parametry połączenia, które zawierają klucz konta i dla późniejszej, należy określić identyfikator URI sygnatury dostępu współdzielonego (SAS). Szczegóły można znaleźć w sekcji [połączone usługi](#linked-service-properties) .

**Zestaw danych wejściowych obiektów blob platformy Azure:**

Dane są pobierane z nowego obiektu BLOB co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu i nazwa pliku obiektu BLOB są dynamicznie oceniane na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części Year, month i Day czasu rozpoczęcia i nazwy pliku używa części godziny godziny rozpoczęcia. ustawienie "External": "true" informuje usługę Data Factory, że zestaw danych jest zewnętrzny względem fabryki danych i nie jest tworzony przez działanie w fabryce danych.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
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

**Wyjściowy zestaw danych tabeli platformy Azure:**

Przykład kopiuje dane do tabeli o nazwie "MyTable" w tabeli platformy Azure. Utwórz tabelę platformy Azure o tej samej liczbie kolumn, która oczekuje, że plik CSV obiektu BLOB będzie zawierać. Nowe wiersze są dodawane do tabeli co godzinę.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Działanie Copy w potoku z BlobSource i AzureTableSink:**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **BlobSource** , a typ **ujścia** to **AzureTableSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoTable",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureTableOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "AzureTableSink",
            "writeBatchSize": 100,
            "writeBatchTimeout": "01:00:00"
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
## <a name="type-mapping-for-azure-table"></a>Mapowanie typu dla tabeli platformy Azure
Jak wspomniano w artykule [działania związane z przenoszeniem danych](data-factory-data-movement-activities.md) , działanie kopiowania wykonuje konwersje typów automatycznych z typów źródłowych na typy obiektów ujścia z poniższym podejściem dwuetapowym.

1. Konwertuj z natywnych typów źródła na typ .NET
2. Konwertuj z typu .NET na natywny typ ujścia

Podczas przeniesienia danych do & z tabeli platformy Azure następujące [mapowania zdefiniowane przez usługę azure Table Service](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) są używane w ramach typów OData w tabeli platformy Azure do typu .NET i na odwrót.

| Typ danych protokołu OData | Typ .NET | Szczegóły |
| --- | --- | --- |
| EDM. Binary |Byte [] |Tablica bajtów do 64 KB. |
| Edm.Boolean |bool |Wartość logiczna. |
| EDM. DateTime |DateTime |Wartość 64-bitowa wyrażona jako uniwersalny czas koordynowany (UTC). Obsługiwany zakres DateTime rozpoczyna się od 12:00 północy, 1 stycznia 1601 N.E. (0001), UTC. Zakres zostaje zakończony 31 grudnia 9999. |
| Edm.Double |double |64-bitowa wartość zmiennoprzecinkowa. |
| EDM. GUID |Guid (identyfikator GUID) |128-bitowy unikatowy identyfikator globalny. |
| Edm.Int32 |Int32 |32-bitowa liczba całkowita. |
| Edm.Int64 |Int64 |64-bitowa liczba całkowita. |
| Edm.String |Ciąg |Wartość zakodowana w formacie UTF-16. Wartości ciągu mogą należeć do 64 KB. |

### <a name="type-conversion-sample"></a>Przykład konwersji typu
Poniższy przykład służy do kopiowania danych z obiektu blob platformy Azure do tabeli platformy Azure z konwersjemi typów.

Załóżmy, że zestaw danych obiektu BLOB jest w formacie CSV i zawiera trzy kolumny. Jednym z nich jest kolumna DateTime z niestandardowym formatem DateTime używającą skróconych nazw francuskich dla dnia tygodnia.

Zdefiniuj źródłowy zestaw danych obiektu BLOB w następujący sposób oraz definicje typów kolumn.

```JSON
{
    "name": " AzureBlobInput",
    "properties":
    {
        "structure":
        [
            { "name": "userid", "type": "Int64"},
            { "name": "name", "type": "String"},
            { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1,
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
Uwzględniając mapowanie typu z tabeli platformy Azure typu OData na typ .NET, należy zdefiniować tabelę w tabeli platformy Azure z następującym schematem.

**Schemat tabeli platformy Azure:**

| Nazwa kolumny | Typ |
| --- | --- |
| userid |Edm.Int64 |
| name |Edm.String |
| lastlogindate |EDM. DateTime |

Następnie zdefiniuj zestaw danych tabeli platformy Azure w następujący sposób. Nie trzeba określać sekcji "Structure" z informacjami o typie, ponieważ informacje o typie zostały już określone w źródłowym magazynie danych.

```JSON
{
  "name": "AzureTableOutput",
  "properties": {
    "type": "AzureTable",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

W takim przypadku Data Factory automatycznie przeprowadza konwersje typów, w tym pole DateTime z niestandardowym formatem DateTime przy użyciu kultury "fr-fr" podczas przesuwania danych z obiektów BLOB do tabeli platformy Azure.

> [!NOTE]
> Aby zmapować kolumny ze źródłowego zestawu danych do kolumn z obiektu ujścia danych, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Aby dowiedzieć się więcej na temat kluczowych czynników wpływających na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji, zobacz temat [wydajność działania kopiowania & Przewodnik po dostrajania](data-factory-copy-activity-performance.md).