---
title: Przenoszenie danych z SAP HANA przy użyciu Azure Data Factory
description: Dowiedz się więcej na temat przenoszenia danych z SAP HANA przy użyciu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 361b98a1cde8ee5dee99a370b46d8fc8e0f5af28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84707144"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Przenoszenie danych z SAP HANA przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-sap-hana-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-sap-hana.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zapoznaj się z tematem [SAP HANA Connector w wersji 2](../connector-sap-business-warehouse.md).

W tym artykule wyjaśniono, jak za pomocą działania kopiowania w Azure Data Factory przenieść dane z SAP HANA lokalnych. Jest on używany w artykule dotyczącym [przenoszenia danych](data-factory-data-movement-activities.md) , który przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania.

Dane z lokalnego magazynu danych SAP HANA można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Fabryka danych obsługuje obecnie tylko przeniesienie danych z SAP HANA do innych magazynów danych, ale nie do przeniesienia danych z innych magazynów danych do SAP HANA.

## <a name="supported-versions-and-installation"></a>Obsługiwane wersje i instalacja
Ten łącznik obsługuje dowolną wersję bazy danych SAP HANA. Obsługuje kopiowanie danych z modeli informacji HANA (takich jak widoki analityczne i obliczeniowe) oraz tabel wierszy/kolumn przy użyciu zapytań SQL.

Aby włączyć łączność z wystąpieniem SAP HANA, Zainstaluj następujące składniki:
- **Zarządzanie danymi Gateway**: Usługa Data Factory obsługuje łączenie się z lokalnymi magazynami danych (w tym SAP HANA) przy użyciu składnika o nazwie Brama zarządzanie danymi. Aby dowiedzieć się więcej na temat bramy Zarządzanie danymi i instrukcje krok po kroku dotyczące konfigurowania bramy, zobacz temat [przeniesienie danych między lokalnym magazynem danych do magazynu danych w chmurze](data-factory-move-data-between-onprem-and-cloud.md) . Brama jest wymagana, nawet jeśli SAP HANA jest hostowana na maszynie wirtualnej Azure IaaS (VM). Możesz zainstalować bramę na tej samej maszynie wirtualnej, w której znajduje się magazyn danych lub na innej maszynie wirtualnej, o ile Brama może połączyć się z bazą danych.
- **SAP HANA sterownika ODBC** na maszynie bramy. Sterownik ODBC platformy SAP HANA możesz pobrać z witryny [SAP Software Download Center (Centrum pobierania oprogramowania SAP)](https://support.sap.com/swdc). Wyszukaj za pomocą słowa kluczowego **SAP HANA Client dla systemu Windows**. 

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok za pomocą działania kopiowania, które przenosi dane z lokalnego magazynu danych SAP HANA przy użyciu różnych narzędzi/interfejsów API. 

- Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) na potrzeby szybkiego instruktażu dotyczącego tworzenia potoku przy użyciu Kreatora kopiowania danych. 
- Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon Azure Resource Manager**, interfejs API **platformy .NET**i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych.
2. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania. 
3. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. 

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON.  Aby uzyskać przykład z definicjami JSON dla Data Factory jednostek, które są używane do kopiowania danych z SAP HANA lokalnych, zobacz [przykład JSON: Kopiuj dane z SAP HANA do usługi Azure Blob](#json-example-copy-data-from-sap-hana-to-azure-blob) w tym artykule. 

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania Data Factory jednostek specyficznych dla SAP HANA magazynu danych:

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla SAP HANA połączonej usługi.

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
serwer | Nazwa serwera, na którym znajduje się wystąpienie SAP HANA. Jeśli serwer używa niestandardowego portu, określ `server:port` . | ciąg | Tak
authenticationType | Typ uwierzytelniania. | . "Podstawowa" lub "Windows" | Tak 
nazwa użytkownika | Nazwa użytkownika, który ma dostęp do serwera SAP | ciąg | Tak
hasło | Hasło użytkownika. | ciąg | Tak
gatewayName | Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z lokalnym wystąpieniem SAP HANA. | ciąg | Tak
encryptedCredential | Zaszyfrowany ciąg poświadczeń. | ciąg | Nie

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych są podobne dla wszystkich typów zestawu danych (Azure SQL, Azure Blob, Azure Table itp.).

Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w magazynie danych. Brak właściwości specyficznych dla typu, które są obsługiwane dla SAP HANAgo zestawu danych typu **relacyjnego**. 


## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, są dostępne dla wszystkich typów działań.

Natomiast właściwości dostępne w sekcji **typeProperties** działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się w zależności od typów źródeł i ujścia.

Gdy źródło w działaniu kopiowania jest typu **RelationalSource** (w tym SAP HANA), w sekcji typeProperties są dostępne następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| query | Określa zapytanie SQL do odczytu danych z wystąpienia SAP HANA. | Zapytanie SQL. | Tak |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>Przykład JSON: kopiowanie danych z SAP HANA do obiektu blob platformy Azure
Poniższy przykład zawiera przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ten przykład pokazuje, jak skopiować dane z SAP HANA lokalnego do Blob Storage platformy Azure. Dane można jednak skopiować **bezpośrednio** do dowolnego ujścia wymienionego w [tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w Azure Data Factory.  

> [!IMPORTANT]
> Ten przykład zawiera fragmenty kodu JSON. Zawiera instrukcje krok po kroku dotyczące tworzenia fabryki danych. Instrukcje krok po kroku znajdują się [w sekcji przeniesienie danych między lokalizacjami lokalnymi i artykułem w chmurze](data-factory-move-data-between-onprem-and-cloud.md) .

Przykład zawiera następujące jednostki fabryki danych:

1. Połączona usługa typu [SapHana](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [relacyjnego](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [RelationalSource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane z wystąpienia SAP HANA do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

Pierwszym krokiem jest skonfigurowanie bramy zarządzania danymi. Instrukcje dotyczą [przemieszczania danych między lokalizacjami lokalnymi i artykułami w chmurze](data-factory-move-data-between-onprem-and-cloud.md) .

### <a name="sap-hana-linked-service"></a>SAP HANA połączona usługa
Ta połączona usługa łączy wystąpienie SAP HANA z fabryką danych. Właściwość Type jest ustawiona na wartość **SapHana**. Sekcja typeProperties zawiera informacje o połączeniu dla wystąpienia SAP HANA.

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```

### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
Ta połączona usługa łączy Twoje konto usługi Azure Storage z fabryką danych. Właściwość Type jest ustawiona na wartość **AzureStorage**. Sekcja typeProperties zawiera informacje o połączeniu dla konta usługi Azure Storage.

```json
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

### <a name="sap-hana-input-dataset"></a>SAP HANA wejściowy zestaw danych

Ten zestaw danych definiuje zestaw danych SAP HANA. Należy ustawić typ Data Factory zestawu **danych.** Obecnie nie określono żadnych właściwości specyficznych dla typu dla SAP HANA zestawu danych. Zapytanie w definicji działania kopiowania określa, jakie dane mają być odczytywane z wystąpienia SAP HANA. 

Ustawienie właściwości zewnętrznej na wartość true informuje usługę Data Factory, że tabela jest zewnętrzna z fabryką danych i nie jest generowana przez działanie w fabryce danych.

Właściwości częstotliwości i interwału definiują harmonogram. W takim przypadku dane są odczytywane z wystąpienia SAP HANA co godzinę. 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="azure-blob-output-dataset"></a>Wyjściowy zestaw danych obiektów blob platformy Azure
Ten zestaw danych definiuje wyjściowy zestaw danych obiektów blob platformy Azure. Właściwość Type jest ustawiona na wartość AzureBlob. Sekcja typeProperties zawiera informacje o tym, gdzie są przechowywane dane skopiowane z wystąpienia SAP HANA. Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu dla obiektu BLOB jest obliczana dynamicznie na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny rozpoczęcia.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Potok z działaniem kopiowania

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **RelationalSource** (dla źródła SAP HANA), a typ **ujścia** to **wartość blobsink**. Zapytanie SQL określone dla właściwości **zapytania** wybiera dane w ciągu ostatniej godziny do skopiowania.

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


### <a name="type-mapping-for-sap-hana"></a>Mapowanie typu dla SAP HANA
Jak wspomniano w artykule [działania związane z przenoszeniem danych](data-factory-data-movement-activities.md) , działanie kopiowania wykonuje konwersje typów automatycznych z typów źródłowych na typy obiektów ujścia z następującymi krokami:

1. Konwertuj z natywnych typów źródła na typ .NET
2. Konwertuj z typu .NET na natywny typ ujścia

Podczas przemieszczania danych z SAP HANA następujące mapowania są używane z typów SAP HANA do typów .NET.

Typ SAP HANA | Typ oparty na platformie .NET
------------- | ---------------
TINYINT | Byte
SMALLINT | Int16
INT | Int32
BIGINT | Int64
LICZBA RZECZYWISTA | Pojedynczy
DOUBLE | Pojedynczy
DOKŁADNOŚCI | Liczba dziesiętna
TYPU | Byte
VARCHAR | Ciąg
NVARCHAR | Ciąg
OBIEKTÓW CLOB | Byte []
ALPHANUM | Ciąg
TWORZENIA | Byte []
DATE | DateTime
TIME | przedział_czasu
ZNACZNIK czasu | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>Znane ograniczenia
Podczas kopiowania danych z SAP HANA istnieje kilka znanych ograniczeń:

- Ciągi NVARCHAR są obcinane do maksymalnej długości wynoszącej 4000 znaków Unicode
- Typ danych SMALLDECIMAL nie jest obsługiwany
- Typ danych VARBINARY nie jest obsługiwany
- Prawidłowe daty są z zakresu od 1899-12-30 do 9999-12-31

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się więcej na temat mapowania kolumn w źródłowym zestawie danych na kolumny w datadataset, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Powtarzanie odczytu ze źródeł relacyjnych
Podczas kopiowania danych z magazynów danych relacyjnych należy mieć na uwadze powtarzalność, aby uniknąć niezamierzonych wyników. W Azure Data Factory można ręcznie uruchomić ponownie wycinka. Możesz również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek był uruchamiany ponownie w przypadku wystąpienia błędu. Gdy wycinek jest uruchamiany ponownie w dowolny sposób, należy się upewnić, że te same dane są odczytywane niezależnie od tego, ile razy jest uruchomiony plasterek. Zobacz [powtarzanie odczytu ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [Przewodnik dostrajania wydajności & działania kopiowania](data-factory-copy-activity-performance.md) , aby poznać kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji.
