---
title: Kopiowanie danych do lub z programu Oracle przy użyciu Data Factory
description: Informacje o kopiowaniu danych do lub z lokalnej bazy danych Oracle przy użyciu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1aa8708701af37834ae3b6cdc42de9c691ccacec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86084294"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Kopiowanie danych do lokalnego lub z programu Oracle w środowisku lokalnym przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-onprem-oracle-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-oracle.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Azure Data Factory. Jeśli używasz bieżącej wersji usługi Azure Data Factory, zobacz [Łącznik Oracle w wersji 2](../connector-oracle.md).


W tym artykule opisano sposób używania działania kopiowania w programie Azure Data Factory do przenoszenia danych do lub z lokalnej bazy danych programu Oracle. Artykuł jest oparty na [działaniach związanych z przenoszeniem danych](data-factory-data-movement-activities.md), które przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Dane *z bazy danych programu Oracle* można kopiować do następujących magazynów danych:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Dane z następujących magazynów danych można kopiować *do bazy danych programu Oracle*:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Data Factory obsługuje łączenie się z lokalnymi źródłami programu Oracle przy użyciu bramy Zarządzanie danymi Gateway. Aby dowiedzieć się więcej o bramie Zarządzanie danymi, zobacz [Zarządzanie danymi Gateway](data-factory-data-management-gateway.md) . Instrukcje krok po kroku dotyczące sposobu konfigurowania bramy w potoku danych do przenoszenia danych można znaleźć [w temacie Przenoszenie danych z lokalizacji lokalnej do chmury](data-factory-move-data-between-onprem-and-cloud.md).

Brama jest wymagana nawet wtedy, gdy Oracle jest hostowana na maszynie wirtualnej infrastruktury jako usługi (IaaS) platformy Azure. Możesz zainstalować bramę na tej samej maszynie wirtualnej IaaS, w której znajduje się magazyn danych lub na innej maszynie wirtualnej, o ile Brama może połączyć się z bazą danych.

> [!NOTE]
> Aby uzyskać porady dotyczące rozwiązywania problemów związanych z połączeniem i bramą, zobacz [Rozwiązywanie problemów z bramą](data-factory-data-management-gateway.md#troubleshooting-gateway-issues).

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacja

Ten łącznik Oracle obsługuje dwie wersje sterowników:

- **Microsoft Driver for Oracle (zalecane)**: począwszy od bramy zarządzanie danymi w wersji 2,7, zostanie automatycznie zainstalowany sterownik firmy Microsoft dla programu Oracle z bramą. Nie musisz instalować ani aktualizować sterownika w celu nawiązania połączenia z bazą danych Oracle. Możesz również zwiększyć wydajność kopiowania przy użyciu tego sterownika. Obsługiwane są następujące wersje baz danych Oracle:
  - Oracle 12c R1 (12,1)
  - Oracle 11g R1, R2 (11,1, 11,2)
  - Oracle 10g R1, R2 (10,1, 10,2)
  - Oracle 9i R1, R2 (9.0.1, 9,2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Serwer proxy Oracle nie jest obsługiwany.

    > [!IMPORTANT]
    > Obecnie sterownik firmy Microsoft dla programu Oracle obsługuje tylko kopiowanie danych z programu Oracle. Sterownik nie obsługuje zapisywania do bazy danych Oracle. Możliwość połączenia testowego na karcie **diagnostyka** zarządzanie danymi Gateway nie obsługuje tego sterownika. Alternatywnie możesz użyć Kreatora kopiowania, aby sprawdzić poprawność łączności.
    >

- **Oracle dostawca danych dla platformy .NET**: możesz użyć programu Oracle dostawca danych do kopiowania danych z programu lub do programu Oracle. Ten składnik jest dostępny w [składnikach programu Oracle Data Access dla systemu Windows](https://www.oracle.com/technetwork/topics/dotnet/downloads/). Zainstaluj odpowiednią wersję (32-bitową lub 64-bitową) na komputerze, na którym zainstalowano bramę. [Oracle dostawca danych .net 12,1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) ma dostęp do Oracle Database 10 GB w wersji 2 i nowszych.

    W przypadku wybrania opcji **Instalacja xcopy**wykonaj kroki opisane w pliku readme.htm. Zalecamy wybranie Instalatora z interfejsem użytkownika (nie Instalatora XCopy).

    Po zainstalowaniu dostawcy należy ponownie uruchomić usługę hosta bramy Zarządzanie danymi na maszynie za pomocą apletu usługi lub Zarządzanie danymi bramy Configuration Manager.

W przypadku tworzenia potoku kopiowania przy użyciu Kreatora kopiowania typ sterownika jest określany jako autoustalany. Sterownik firmy Microsoft jest używany domyślnie, chyba że wersja bramy jest wcześniejsza niż wersja 2,7 lub jako ujścia jest wybierana baza danych Oracle.

## <a name="get-started"></a>Wprowadzenie

Można utworzyć potok z działaniem kopiowania. Potok przenosi dane do lub z lokalnej bazy danych Oracle przy użyciu różnych narzędzi lub interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie Kreatora kopiowania. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) , aby zapoznać się z krótkim przewodnikiem tworzenia potoku przy użyciu kreatora kopiowanie danych.

Do utworzenia potoku można także użyć jednego z następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablonu Azure Resource Manager**, **interfejsu API platformy .NET**lub **interfejsu API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku z działaniem kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **fabrykę danych**. Fabryka danych może zawierać jeden lub więcej potoków.
2. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych. Na przykład w przypadku kopiowania danych z bazy danych programu Oracle do usługi Azure Blob Storage Utwórz dwie połączone usługi, aby połączyć bazę danych Oracle i konto usługi Azure Storage z fabryką danych. Aby uzyskać właściwości połączonej usługi, które są specyficzne dla platformy Oracle, zobacz [Właściwości połączonej usługi](#linked-service-properties).
3. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania. W przykładzie w poprzednim kroku utworzysz zestaw danych, aby określić tabelę w bazie danych Oracle, która zawiera dane wejściowe. Tworzysz kolejny zestaw danych, aby określić kontener obiektów blob i folder, w którym są przechowywane dane skopiowane z bazy danych programu Oracle. Dla właściwości zestawu danych, które są specyficzne dla platformy Oracle, zobacz [Właściwości zestawu danych](#dataset-properties).
4. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. W poprzednim przykładzie użyto **OracleSource** jako źródła i **wartość blobsink** jako ujścia dla działania kopiowania. Podobnie, jeśli kopiujesz z usługi Azure Blob Storage do bazy danych Oracle, używasz funkcji **BlobSource** i **OracleSink** w działaniu kopiowania. Dla właściwości działania kopiowania, które są specyficzne dla bazy danych Oracle, zobacz [właściwości działania kopiowania](#copy-activity-properties). Aby uzyskać szczegółowe informacje na temat używania magazynu danych jako źródła lub ujścia, wybierz link do magazynu danych w poprzedniej sekcji.

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek są tworzone automatycznie dla Ciebie: połączone usługi, zestawy danych i potok. Korzystając z narzędzi lub interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON. Przykłady, które mają definicje JSON dla jednostek Data Factory używanych do kopiowania danych do lub z lokalnej bazy danych programu Oracle, można znaleźć w przykładach JSON.

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON używanych do definiowania jednostek Data Factory.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

W poniższej tabeli opisano elementy JSON, które są specyficzne dla połączonej usługi Oracle:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość **Type** musi być ustawiona na wartość **OnPremisesOracle**. |Tak |
| drivertype | Określ, który sterownik ma być używany do kopiowania danych z lub do bazy danych Oracle. Dozwolone wartości to **Microsoft** i **ODP** (domyślnie). Zobacz [obsługiwaną wersję i instalację,](#supported-versions-and-installation) Aby uzyskać szczegółowe informacje o sterowniku. | Nie |
| Parametry połączenia | Określ informacje, które są konieczne do nawiązania połączenia z wystąpieniem bazy danych Oracle dla właściwości **ConnectionString** . | Tak |
| gatewayName | Nazwa bramy, która jest używana do nawiązywania połączenia z lokalnym serwerem Oracle. |Tak |

**Przykład: korzystanie z sterownika firmy Microsoft**

> [!TIP]
> Jeśli zostanie wyświetlony komunikat o błędzie "ORA-01025: UPI Parameter spoza zakresu" i Oracle to wersja 8i, Dodaj `WireProtocolMode=1` do parametrów połączenia i spróbuj ponownie:

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Przykład: Użycie sterownika ODP**

Aby dowiedzieć się więcej na temat dozwolonych formatów, zobacz artykuł [dostawca danych programu Oracle dla programu .NET Odp](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>))); User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Tworzenie zestawów danych](data-factory-create-datasets.md).

Sekcje pliku JSON zestawu danych, takie jak struktura, dostępność i zasady, są podobne do wszystkich typów zestawu danych (na przykład dla Oracle, Azure Blob Storage i Azure Table Storage).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w magazynie danych. Sekcja **typeProperties** zestawu danych typu **Oracle** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli w bazie danych Oracle, do której odwołuje się połączona usługa. |Nie (Jeśli określono **oracleReaderQuery** lub **OracleSource** ) |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania działań, zobacz [Tworzenie potoków](data-factory-create-pipelines.md).

Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, a zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania przyjmuje tylko jedną dane wejściowe i generuje tylko jedno wyjście.

Właściwości, które są dostępne w sekcji **typeProperties** działania, różnią się w zależności od typu działania. Właściwości działania kopiowania różnią się w zależności od typu źródła i ujścia.

### <a name="oraclesource"></a>OracleSource

W działaniu kopiowania, gdy źródłem jest typ **OracleSource** , w sekcji **typeProperties** są dostępne następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| oracleReaderQuery |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL. Na przykład "Select \* from **MyTable**". <br/><br/>Jeśli nie zostanie określony, ta instrukcja SQL jest wykonywana: "Select \* from **MyTable**" |Nie<br />(Jeśli określono element **TableName** **zestawu danych** ) |

### <a name="oraclesink"></a>OracleSink

**OracleSink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed przekroczeniem limitu czasu. |**zakres czasu**<br/><br/> Przykład: 00:30:00 (30 minut) |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie wartość **writeBatchSize**. |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 100) |
| sqlWriterCleanupScript |Określa zapytanie dla działania kopiowania, które ma zostać wykonane, aby dane określonego wycinka zostały oczyszczone. |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określa nazwę kolumny dla działania kopiowania, która ma zostać wypełniona automatycznie wygenerowany identyfikator wycinka. Wartość dla **sliceIdentifierColumnName** służy do czyszczenia danych określonego wycinka podczas ponownego uruchomienia. |Nazwa kolumny zawierającej dane typu **Binary (32)**. |Nie |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>Przykłady JSON kopiowania danych do i z bazy danych programu Oracle

W poniższych przykładach przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). W przykładach pokazano, jak skopiować dane z lub do bazy danych Oracle oraz z usługi Azure Blob Storage lub z niej. Dane można jednak kopiować do dowolnego ujścia z listy [obsługiwanych magazynów danych i formatów](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w Azure Data Factory.

**Przykład: kopiowanie danych z programu Oracle do magazynu obiektów blob platformy Azure**

Przykład zawiera następujące jednostki Data Factory:

* Połączona usługa typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [Oracle](data-factory-onprem-oracle-connector.md#dataset-properties).
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako źródła i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties) jako ujścia.

Przykład kopiuje dane z tabeli w lokalnej bazie danych Oracle do obiektu BLOB co godzinę. Aby uzyskać więcej informacji na temat różnych właściwości, które są używane w przykładzie, zobacz sekcje, które są zgodne z przykładami.

**Połączona usługa Oracle**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Połączona usługa Azure Blob Storage**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Zestaw danych wejściowych Oracle**

W przykładzie przyjęto założenie, że utworzono tabelę o nazwie **MyTable** w bazie danych Oracle. Zawiera kolumnę o nazwie **timestampcolumn** dla danych szeregów czasowych.

Ustawienie **zewnętrzne**: **true** informuje usługę Data Factory, że zestaw danych znajduje się poza fabryką danych i że zestaw danych nie jest generowany przez działanie w fabryce danych.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
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

**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane w nowym obiekcie blob co godzinę (**częstotliwość**: **godzina**, **Interwał**: **1**). Ścieżka folderu i nazwa pliku obiektu BLOB są dynamicznie oceniane na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części Year, month, Day i Hour czasu rozpoczęcia.

```json
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

**Potok z działaniem kopiowania**

Potok zawiera działanie kopiowania, które jest skonfigurowane do używania wejściowych i wyjściowych zestawów danych i zaplanowane do uruchamiania co godzinę. W definicji JSON potoku typ **źródła** ma wartość **OracleSource** , a typ **ujścia** to **wartość blobsink**. Zapytanie SQL określone za pomocą właściwości **oracleReaderQuery** wybiera dane w ciągu ostatniej godziny do skopiowania.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for a copy activity",
        "activities":[
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

**Przykład: kopiowanie danych z usługi Azure Blob Storage do programu Oracle**

Ten przykład pokazuje, jak skopiować dane z konta usługi Azure Blob Storage do lokalnej bazy danych programu Oracle. Można jednak skopiować dane *bezpośrednio* z dowolnego źródła wymienionego w [obsługiwanych magazynach danych i formatach](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w Azure Data Factory.

Przykład zawiera następujące jednostki Data Factory:

* Połączona usługa typu [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties).
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [Oracle](data-factory-onprem-oracle-connector.md#dataset-properties).
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) jako źródła [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) jako ujścia.

Przykład kopiuje dane z obiektu BLOB do tabeli w lokalnej bazie danych Oracle co godzinę. Aby uzyskać więcej informacji na temat różnych właściwości, które są używane w przykładzie, zobacz sekcje, które są zgodne z przykładami.

**Połączona usługa Oracle**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Połączona usługa Azure Blob Storage**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Wejściowy zestaw danych obiektów blob platformy Azure**

Dane są pobierane z nowego obiektu BLOB co godzinę (**częstotliwość**: **godzina**, **Interwał**: **1**). Ścieżka folderu i nazwa pliku obiektu BLOB są dynamicznie oceniane na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części Year, month i Day czasu rozpoczęcia. Nazwa pliku używa części godzinowej godziny rozpoczęcia. Ustawienie **External**: **true** informuje usługę Data Factory, że ta tabela jest zewnętrzna z fabryką danych i nie jest generowana przez działanie w fabryce danych.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
            "frequency": "Day",
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

**Wyjściowy zestaw danych Oracle**

W przykładzie przyjęto założenie, że utworzono tabelę o nazwie **MyTable** w bazie danych Oracle. Utwórz tabelę w bazie danych Oracle z tą samą liczbą kolumn, która będzie zawierała plik CSV. Nowe wiersze są dodawane do tabeli co godzinę.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**Potok z działaniem kopiowania**

Potok zawiera działanie kopiowania, które jest skonfigurowane do używania wejściowych i wyjściowych zestawów danych i zaplanowane do uruchamiania co godzinę. W definicji JSON potoku typ **źródła** ma wartość **BlobSource** , a typ **ujścia** to **OracleSink**.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with a copy activity",
        "activities":[
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
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


## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

### <a name="problem-1-net-framework-data-provider"></a>Problem 1: .NET Framework Dostawca danych

**Komunikat o błędzie**

```text
Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.
```

**Możliwe przyczyny**

* Nie zainstalowano Dostawca danych .NET Framework dla programu Oracle.
* Dostawca danych .NET Framework dla programu Oracle został zainstalowany w .NET Framework 2,0 i nie można go znaleźć w folderach .NET Framework 4,0.

**Rozwiązanie**

* Jeśli nie zainstalowano dostawcy platformy .NET dla programu Oracle, [zainstaluj ją](https://www.oracle.com/technetwork/topics/dotnet/downloads/), a następnie ponów próbę wykonania tego scenariusza.
* Jeśli zobaczysz komunikat o błędzie nawet po zainstalowaniu dostawcy, wykonaj następujące czynności:
    1. Otwórz plik konfiguracji komputera dla programu .NET 2,0 z folderu <dysku systemowego \>:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Wyszukaj ciąg **Oracle dostawca danych dla platformy .NET**. Powinno być możliwe znalezienie wpisu, jak pokazano w poniższym przykładzie w obszarze **System. Data**  >  **DbProviderFactories**:`<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Skopiuj ten wpis do pliku machine.config w następującym folderze .NET 4,0: <dysku systemowego \>:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Następnie zmień wersję na 4. xxx. x.x.
* Zainstaluj ścieżkę <ODP.NET zainstalowana \>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll w globalnej pamięci podręcznej zestawów (GAC), uruchamiając **Gacutil/i [ścieżka dostawcy]**.

### <a name="problem-2-datetime-formatting"></a>Problem 2: Formatowanie daty i godziny

**Komunikat o błędzie**

```text
Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.
```

**Rozwiązanie**

Może być konieczne dostosowanie ciągu zapytania w działaniu kopiowania w zależności od sposobu skonfigurowania dat w bazie danych Oracle. Oto przykład (przy użyciu funkcji **TO_DATE** ):

```console   
"oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"
```


## <a name="type-mapping-for-oracle"></a>Mapowanie typu dla programu Oracle

Jak wspomniano w [działaniach związanych z przenoszeniem danych](data-factory-data-movement-activities.md), działanie kopiowania wykonuje konwersje typów automatycznych z typów źródłowych na typy obiektów ujścia przy użyciu następujących dwuetapowych podejścia:

1. Konwertuj z natywnych typów źródła na typ .NET.
2. Konwertuj z typu .NET na natywny typ ujścia.

Podczas przenoszenia danych z programu Oracle następujące mapowania są używane z typu danych Oracle do typu .NET i na odwrót:

| Typ danych Oracle | Typ danych .NET Framework |
| --- | --- |
| DOTYCZĄCE |Byte [] |
| TWORZENIA |Byte []<br/>(obsługiwane tylko w systemach Oracle 10g i nowszych w przypadku korzystania ze sterownika firmy Microsoft) |
| DELIKATN |Ciąg |
| OBIEKTÓW CLOB |Ciąg |
| DATE |DateTime |
| FLOAT |Decimal, String (jeśli precyzja > 28) |
| LICZBA CAŁKOWITA |Decimal, String (jeśli precyzja > 28) |
| INTERWAŁ OD ROKU DO MIESIĄCA |Int32 |
| INTERWAŁ OD DNIA DO SEKUNDY |przedział_czasu |
| DŁUGO |Ciąg |
| DŁUGI NIEPRZETWORZONY |Byte [] |
| NCHAR |Ciąg |
| NCLOB |Ciąg |
| Liczba |Decimal, String (jeśli precyzja > 28) |
| NVARCHAR2 |Ciąg |
| SUROWCÓW |Byte [] |
| Właściwość |Ciąg |
| ZNACZNIK czasu |DateTime |
| SYGNATURA CZASOWA Z LOKALNĄ STREFĄ CZASOWĄ |DateTime |
| SYGNATURA CZASOWA ZE STREFĄ CZASOWĄ |DateTime |
| LICZBA CAŁKOWITA BEZ ZNAKU |Liczba |
| VARCHAR2 |Ciąg |
| XML |Ciąg |

> [!NOTE]
> Typy danych **Interwał od roku do miesiąca** oraz **Interwał od dnia do sekundy** nie są obsługiwane w przypadku używania sterownika firmy Microsoft.

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia

Aby dowiedzieć się więcej o mapowaniu kolumn w źródłowym zestawie danych do kolumn w zestawie danych ujścia, zobacz [Mapowanie kolumn zestawu danych w Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Powtarzanie odczytu ze źródeł relacyjnych

Podczas kopiowania danych z magazynów danych relacyjnych należy mieć na uwadze powtarzalność, aby uniknąć niezamierzonych wyników. W Azure Data Factory można ręcznie ponownie uruchomić wycinek. Możesz również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek został ponownie uruchomiony po wystąpieniu błędu. Po ponownym uruchomieniu wycinka ręcznie lub przez zasady ponawiania, upewnij się, że te same dane są odczytywane niezależnie od tego, ile razy jest uruchomiony plasterek. Aby uzyskać więcej informacji, zobacz [powtarzalny odczyt ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie

Zapoznaj się z [przewodnikiem dotyczącym wydajności i dostrajania działania kopiowania](data-factory-copy-activity-performance.md) , aby poznać kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory. Możesz również poznać różne sposoby jego optymalizacji.
