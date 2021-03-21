---
title: Przenoszenie danych z programu MySQL przy użyciu Azure Data Factory
description: Dowiedz się więcej na temat przenoszenia danych z bazy danych MySQL przy użyciu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 83c39435d2249981a45798ffe0717054fa7b0717
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387329"
---
# <a name="move-data-from-mysql-using-azure-data-factory"></a>Przenoszenie danych z programu MySQL przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-onprem-mysql-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-mysql.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Łącznik MySQL w wersji 2](../connector-mysql.md).


W tym artykule wyjaśniono, jak za pomocą działania kopiowania w Azure Data Factory przenieść dane z lokalnej bazy danych MySQL. Jest on używany w artykule dotyczącym [przenoszenia danych](data-factory-data-movement-activities.md) , który przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania.

Dane z lokalnego magazynu danych MySQL można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Fabryka danych obsługuje obecnie tylko przeniesienie danych z magazynu danych MySQL do innych magazynów danych, ale nie do przeniesienia danych z innych magazynów danych do magazynu danych MySQL. 

## <a name="prerequisites"></a>Wymagania wstępne
Usługa Data Factory obsługuje łączenie się z lokalnymi źródłami MySQL przy użyciu bramy Zarządzanie danymi. Zobacz temat [przeniesienie danych między lokalizacjami lokalnymi i artykułem w chmurze](data-factory-move-data-between-onprem-and-cloud.md) , aby dowiedzieć się więcej na temat bramy zarządzanie danymi i instrukcje krok po kroku dotyczące konfigurowania bramy.

Brama jest wymagana, nawet jeśli baza danych MySQL jest hostowana na maszynie wirtualnej Azure IaaS (VM). Możesz zainstalować bramę na tej samej maszynie wirtualnej, w której znajduje się magazyn danych lub na innej maszynie wirtualnej, o ile Brama może połączyć się z bazą danych.

> [!NOTE]
> Zobacz [Rozwiązywanie problemów z bramą](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) , aby uzyskać porady dotyczące rozwiązywania problemów związanych z połączeniem/bramą.

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacja
Aby można było nawiązać połączenie z bazą danych MySQL przy użyciu bramy Zarządzanie danymi, należy zainstalować [łącznik programu MySQL/NET dla systemu Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) (wersja między 6.6.5 i 6.10.7) w tym samym systemie co Brama zarządzanie danymi. Ten sterownik 32-bitowy jest zgodny z 64 bitowym Zarządzanie danymią bramą. Obsługiwane są wersje MySQL 5,1 i nowsze.

> [!TIP]
> Jeśli wystąpi błąd na "uwierzytelnianie nie powiodło się, ponieważ strona zdalna zamknęła strumień transportowy". Rozważ uaktualnienie łącznika MySQL/sieć do nowszej wersji.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok za pomocą działania kopiowania, które przenosi dane z lokalnego magazynu danych Cassandra przy użyciu różnych narzędzi/interfejsów API. 

- Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) na potrzeby szybkiego instruktażu dotyczącego tworzenia potoku przy użyciu Kreatora kopiowania danych. 
- Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon Azure Resource Manager**, interfejs API **platformy .NET** i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych.
2. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania. 
3. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. 

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON.  Aby uzyskać przykład z definicjami JSON dla Data Factory jednostek, które są używane do kopiowania danych z lokalnego magazynu danych MySQL, zobacz [przykład JSON: Kopiuj dane z bazy danych MySQL do usługi Azure Blob](#json-example-copy-data-from-mysql-to-azure-blob) w tym artykule. 

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania jednostek Data Factory specyficznych dla magazynu danych MySQL:

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla połączonej usługi MySQL.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość Type musi mieć wartość: **OnPremisesMySql** |Tak |
| serwer |Nazwa serwera MySQL. |Tak |
| database |Nazwa bazy danych MySQL. |Tak |
| schema |Nazwa schematu w bazie danych programu. |Nie |
| authenticationType |Typ uwierzytelniania używany do łączenia się z bazą danych MySQL. Możliwe wartości to: `Basic` . |Tak |
| userName |Określ nazwę użytkownika w celu nawiązania połączenia z bazą danych MySQL. |Tak |
| hasło |Określ hasło dla podanego konta użytkownika. |Tak |
| gatewayName |Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z lokalną bazą danych MySQL. |Tak |

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych są podobne dla wszystkich typów zestawu danych (Azure SQL, Azure Blob, Azure Table itp.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w magazynie danych. Sekcja typeProperties dla zestawu danych typu **relacyjnego** (który zawiera zestaw danych MySQL) ma następujące właściwości

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w wystąpieniu bazy danych MySQL, do której odwołuje się połączona usługa. |Nie (Jeśli **kwerenda** **RelationalSource** jest określona) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, są dostępne dla wszystkich typów działań.

Natomiast właściwości dostępne w sekcji **typeProperties** działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się w zależności od typów źródeł i ujścia.

Gdy źródło w działaniu kopiowania jest typu **RelationalSource** (co obejmuje MySQL), w sekcji typeProperties są dostępne następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL. Na przykład: select * from MyTable. |Nie (Jeśli określono element **TableName** **zestawu danych** ) |


## <a name="json-example-copy-data-from-mysql-to-azure-blob"></a>Przykład JSON: kopiowanie danych z programu MySQL do obiektu blob platformy Azure
Ten przykład zawiera przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazuje, jak skopiować dane z lokalnej bazy danych MySQL do Blob Storage platformy Azure. Dane można jednak kopiować do dowolnego ujścia w [tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w Azure Data Factory.

> [!IMPORTANT]
> Ten przykład zawiera fragmenty kodu JSON. Zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych. Instrukcje krok po kroku znajdują się [w sekcji przeniesienie danych między lokalizacjami lokalnymi i artykułem w chmurze](data-factory-move-data-between-onprem-and-cloud.md) .

Przykład zawiera następujące jednostki fabryki danych:

1. Połączona usługa typu [OnPremisesMySql](data-factory-onprem-mysql-connector.md#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [relacyjnego](data-factory-onprem-mysql-connector.md#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [RelationalSource](data-factory-onprem-mysql-connector.md#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z wyniku zapytania w bazie danych MySQL do obiektu BLOB co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

Pierwszym krokiem jest skonfigurowanie bramy zarządzania danymi. Instrukcje dotyczą [przemieszczania danych między lokalizacjami lokalnymi i artykułami w chmurze](data-factory-move-data-between-onprem-and-cloud.md) .

**Połączona usługa MySQL:**

```JSON
    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
        }
      }
    }
```

**Połączona usługa Azure Storage:**

```JSON
    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }
```

**Zestaw danych wejściowych MySQL:**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w programie MySQL i zawiera ona kolumnę o nazwie "timestampcolumn" dla danych szeregów czasowych.

Ustawienie "External": "true" informuje usługę Data Factory, że tabela jest zewnętrzna z fabryką danych i nie jest generowana przez działanie w fabryce danych.

```JSON
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
            "typeProperties": {},
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

```JSON
    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
```

**Potok z działaniem kopiowania:**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **RelationalSource** , a typ **ujścia** to **wartość blobsink**. Zapytanie SQL określone dla właściwości **zapytania** wybiera dane w ciągu ostatniej godziny do skopiowania.

```JSON
    {
        "name": "CopyMySqlToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
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
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }
```


### <a name="type-mapping-for-mysql"></a>Mapowanie typu dla programu MySQL
Jak wspomniano w artykule [działania związane z przenoszeniem danych](data-factory-data-movement-activities.md) , działanie kopiowania wykonuje konwersje typów automatycznych z typów źródłowych na typy obiektów ujścia z następującymi krokami:

1. Konwertuj z natywnych typów źródła na typ .NET
2. Konwertuj z typu .NET na natywny typ ujścia

Podczas przeniesienia danych do programu MySQL następujące mapowania są używane z typów MySQL do typów .NET.

| Typ bazy danych MySQL | Typ programu .NET Framework |
| --- | --- |
| bigint bez znaku |Liczba dziesiętna |
| bigint |Int64 |
| bit |Liczba dziesiętna |
| blob |Byte [] |
| bool |Wartość logiczna |
| char |Ciąg |
| data |Datetime (data/godzina) |
| datetime |Datetime (data/godzina) |
| decimal |Liczba dziesiętna |
| Podwójna precyzja |Double |
| double |Double |
| enum |Ciąg |
| float |Pojedynczy |
| int bez znaku |Int64 |
| int |Int32 |
| Liczba całkowita bez znaku |Int64 |
| liczba całkowita |Int32 |
| Long varbinary |Byte [] |
| Long varchar |Ciąg |
| longblob |Byte [] |
| longtext |Ciąg |
| mediumblob |Byte [] |
| MEDIUMINT bez znaku |Int64 |
| mediumint |Int32 |
| mediumtext |Ciąg |
| numeryczne |Liczba dziesiętna |
| liczba rzeczywista |Double |
| set |Ciąg |
| smallint bez znaku |Int32 |
| smallint |Int16 |
| tekst |Ciąg |
| time |przedział_czasu |
| sygnatura czasowa |Datetime (data/godzina) |
| tinyblob |Byte [] |
| tinyint unsigned |Int16 |
| tinyint |Int16 |
| tinytext |Ciąg |
| varchar |Ciąg |
| rok |int |

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się więcej na temat mapowania kolumn w źródłowym zestawie danych na kolumny w datadataset, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Powtarzanie odczytu ze źródeł relacyjnych
Podczas kopiowania danych z magazynów danych relacyjnych należy mieć na uwadze powtarzalność, aby uniknąć niezamierzonych wyników. W Azure Data Factory można ręcznie uruchomić ponownie wycinka. Możesz również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek był uruchamiany ponownie w przypadku wystąpienia błędu. Gdy wycinek jest uruchamiany ponownie w dowolny sposób, należy się upewnić, że te same dane są odczytywane niezależnie od tego, ile razy jest uruchomiony plasterek. Zobacz [powtarzanie odczytu ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [Przewodnik dostrajania wydajności & działania kopiowania](data-factory-copy-activity-performance.md) , aby poznać kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji.
