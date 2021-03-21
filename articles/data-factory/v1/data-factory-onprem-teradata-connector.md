---
title: Przenoszenie danych z programu Teradata przy użyciu Azure Data Factory
description: Informacje na temat łącznika programu Teradata dla usługi Data Factory, która umożliwia przenoszenie danych z bazy danych programu Teradata
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ef992ed907bc070643f290e7fd536de05ebf9242
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387210"
---
# <a name="move-data-from-teradata-using-azure-data-factory"></a>Przenoszenie danych z programu Teradata przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-onprem-teradata-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-teradata.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [łącznik programu Teradata w wersji 2](../connector-teradata.md).

W tym artykule wyjaśniono, jak za pomocą działania kopiowania w Azure Data Factory przenieść dane z lokalnej bazy danych programu Teradata. Jest on używany w artykule dotyczącym [przenoszenia danych](data-factory-data-movement-activities.md) , który przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania.

Dane z lokalnego magazynu danych programu Teradata można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Fabryka danych obsługuje obecnie tylko przeniesienie danych z magazynu danych programu Teradata do innych magazynów danych, ale nie do przeniesienia danych z innych magazynów danych do magazynu danych programu Teradata.

## <a name="prerequisites"></a>Wymagania wstępne
Fabryka danych obsługuje łączenie z lokalnymi źródłami programu Teradata za pośrednictwem bramy Zarządzanie danymi. Zobacz temat [przeniesienie danych między lokalizacjami lokalnymi i artykułem w chmurze](data-factory-move-data-between-onprem-and-cloud.md) , aby dowiedzieć się więcej na temat bramy zarządzanie danymi i instrukcje krok po kroku dotyczące konfigurowania bramy.

Brama jest wymagana, nawet jeśli usługa Teradata jest hostowana na maszynie wirtualnej Azure IaaS. Możesz zainstalować bramę na tej samej maszynie wirtualnej IaaS, w której znajduje się magazyn danych lub na innej maszynie wirtualnej, o ile Brama może połączyć się z bazą danych.

> [!NOTE]
> Zobacz [Rozwiązywanie problemów z bramą](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) , aby uzyskać porady dotyczące rozwiązywania problemów związanych z połączeniem/bramą.

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacja
Aby można było nawiązać połączenie z bazą danych programu Teradata przy użyciu bramy Zarządzanie danymi, należy zainstalować program [.net dostawca danych dla programu Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) w wersji 14 lub nowszej w tym samym systemie, w którym znajduje się Brama zarządzanie danymi. Obsługiwana jest wersja 12 lub nowsza programu Teradata.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok za pomocą działania kopiowania, które przenosi dane z lokalnego magazynu danych Cassandra przy użyciu różnych narzędzi/interfejsów API.

- Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) na potrzeby szybkiego instruktażu dotyczącego tworzenia potoku przy użyciu Kreatora kopiowania danych.
- Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon Azure Resource Manager**, interfejs API **platformy .NET** i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych.
2. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania.
3. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe.

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON.  Aby uzyskać przykład z definicjami JSON dla Data Factory jednostek, które są używane do kopiowania danych z lokalnego magazynu danych programu Teradata, zobacz [przykład JSON: Kopiuj dane z programu Teradata do usługi Azure Blob](#json-example-copy-data-from-teradata-to-azure-blob) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania jednostek Data Factory specyficznych dla magazynu danych programu Teradata:

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla połączonej usługi programu Teradata.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość Type musi mieć wartość: **OnPremisesTeradata** |Tak |
| serwer |Nazwa serwera programu Teradata. |Tak |
| authenticationType |Typ uwierzytelniania używany do nawiązywania połączenia z bazą danych programu Teradata. Możliwe wartości to: Anonymous, Basic i Windows. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania podstawowego lub systemu Windows. |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |
| gatewayName |Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z lokalną bazą danych programu Teradata. |Tak |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych są podobne dla wszystkich typów zestawu danych (Azure SQL, Azure Blob, Azure Table itp.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w magazynie danych. Obecnie nie istnieją właściwości typu obsługiwane dla zestawu danych programu Teradata.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, oraz zasady są dostępne dla wszystkich typów działań.

Natomiast właściwości dostępne w sekcji typeProperties działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się w zależności od typów źródeł i ujścia.

Gdy źródłem jest typ **RelationalSource** (w tym program Teradata), w sekcji **typeProperties** dostępne są następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL. Na przykład: select * from MyTable. |Tak |

### <a name="json-example-copy-data-from-teradata-to-azure-blob"></a>Przykład JSON: kopiowanie danych z programu Teradata do obiektu blob platformy Azure
W poniższym przykładzie przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują one sposób kopiowania danych z programu Teradata do usługi Azure Blob Storage. Dane można jednak kopiować do dowolnego ujścia w [tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w Azure Data Factory.

Przykład zawiera następujące jednostki fabryki danych:

1. Połączona usługa typu [OnPremisesTeradata](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [relacyjnego](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [RelationalSource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z wyniku zapytania w bazie danych programu Teradata do obiektu BLOB co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

Pierwszym krokiem jest skonfigurowanie bramy zarządzania danymi. Instrukcje dotyczą [przemieszczania danych między lokalizacjami lokalnymi i artykułami w chmurze](data-factory-move-data-between-onprem-and-cloud.md) .

**Połączona usługa programu Teradata:**

```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Połączona usługa Azure Blob Storage:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Wejściowy zestaw danych programu Teradata:**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w programie Teradata i zawiera ona kolumnę o nazwie "timestamp" dla danych szeregów czasowych.

Ustawienie "External": true informuje usługę Data Factory, że tabela jest zewnętrzna z fabryką danych i nie jest generowana przez działanie w fabryce danych.

```json
{
    "name": "TeradataDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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

```json
{
    "name": "AzureBlobTeradataDataSet",
    "properties": {
        "published": false,
        "location": {
            "type": "AzureBlobLocation",
            "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
            "linkedServiceName": "AzureStorageLinkedService"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
**Potok z działaniem kopiowania:**

Potok zawiera działanie kopiowania, które jest skonfigurowane do używania wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **RelationalSource** , a typ **ujścia** to **wartość blobsink**. Zapytanie SQL określone dla właściwości **zapytania** wybiera dane w ciągu ostatniej godziny do skopiowania.

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "TeradataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobTeradataDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "TeradataToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z",
        "isPaused": false
    }
}
```
## <a name="type-mapping-for-teradata"></a>Mapowanie typu dla programu Teradata
Jak wspomniano w artykule [działania związane z przenoszeniem danych](data-factory-data-movement-activities.md) , działanie kopiowania wykonuje konwersje typu automatycznego z typów źródłowych na typy obiektów ujścia o następujących dwuetapowych podejściach:

1. Konwertuj z natywnych typów źródła na typ .NET
2. Konwertuj z typu .NET na natywny typ ujścia

Podczas przesuwania danych do programu Teradata następujące mapowania są używane z typu Teradata do typu .NET.

| Typ bazy danych programu Teradata | Typ programu .NET Framework |
| --- | --- |
| Char |Ciąg |
| Obiektów CLOB |Ciąg |
| Zdjęć |Ciąg |
| VarChar |Ciąg |
| VarGraphic |Ciąg |
| Obiekt blob |Byte [] |
| Byte |Byte [] |
| VarByte |Byte [] |
| BigInt |Int64 |
| ByteInt |Int16 |
| Liczba dziesiętna |Liczba dziesiętna |
| Double |Double |
| Liczba całkowita |Int32 |
| Liczba |Double |
| SmallInt |Int16 |
| Date (Data) |Data i godzina |
| Godzina |przedział_czasu |
| Czas ze strefą czasową |Ciąg |
| Znacznik czasu |DateTime |
| Sygnatura czasowa ze strefą czasową |DateTimeOffset |
| Dzień interwału |przedział_czasu |
| Interwał od dnia do godziny |przedział_czasu |
| Interwał od dnia do minuty |przedział_czasu |
| Interwał od dnia do sekundy |przedział_czasu |
| Interwał czasu |przedział_czasu |
| Interwał od godziny do minuty |przedział_czasu |
| Interwał od godziny do sekundy |przedział_czasu |
| Interwał minut |przedział_czasu |
| Interwał od minuty do sekundy |przedział_czasu |
| Interwał drugi |przedział_czasu |
| Rok interwału |Ciąg |
| Interwał od roku do miesiąca |Ciąg |
| Miesiąc interwału |Ciąg |
| Okres (Data) |Ciąg |
| Czas (Time) |Ciąg |
| Okres (czas ze strefą czasową) |Ciąg |
| Kropka (Sygnatura czasowa) |Ciąg |
| Okres (Sygnatura czasowa ze strefą czasową) |Ciąg |
| Xml |Ciąg |

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się więcej na temat mapowania kolumn w źródłowym zestawie danych na kolumny w datadataset, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Powtarzanie odczytu ze źródeł relacyjnych
Podczas kopiowania danych z magazynów danych relacyjnych należy mieć na uwadze powtarzalność, aby uniknąć niezamierzonych wyników. W Azure Data Factory można ręcznie uruchomić ponownie wycinka. Możesz również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek był uruchamiany ponownie w przypadku wystąpienia błędu. Gdy wycinek jest uruchamiany ponownie w dowolny sposób, należy się upewnić, że te same dane są odczytywane niezależnie od tego, ile razy jest uruchomiony plasterek. Zobacz [powtarzanie odczytu ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [Przewodnik dostrajania wydajności & działania kopiowania](data-factory-copy-activity-performance.md) , aby poznać kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji.
