---
title: Przenoszenie danych do i z SQL Server
description: Dowiedz się więcej na temat przenoszenia danych do/z bazy danych SQL Server, która jest lokalna lub na maszynie wirtualnej platformy Azure przy użyciu Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: fbd1e1d652db3bbd91344ea828278d057baeb060
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100368816"
---
# <a name="move-data-to-and-from-sql-server-using-azure-data-factory"></a>Przenoszenie danych do i z SQL Server przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-sqlserver-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-sql-server.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zapoznaj się z tematem [SQL Server Connector w wersji 2](../connector-sql-server.md).

W tym artykule wyjaśniono, jak za pomocą działania kopiowania w Azure Data Factory przenieść dane do/z bazy danych SQL Server. Jest on używany w artykule dotyczącym [przenoszenia danych](data-factory-data-movement-activities.md) , który przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane **z bazy danych SQL Server** można kopiować do następujących magazynów danych:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Dane z następujących magazynów danych można kopiować **do bazy danych SQL Server**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Obsługiwane wersje programu SQL Server
Ten SQL Server łącznika obsługuje kopiowanie danych z/do następujących wersji hostowanego lokalnie lub w usłudze Azure IaaS przy użyciu uwierzytelniania SQL i uwierzytelniania systemu Windows: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 SQL Server 2005

## <a name="enabling-connectivity"></a>Włączanie łączności
Te same koncepcje i kroki niezbędne do nawiązania połączenia z usługą SQL Server hostowaną lokalnie lub na maszynach wirtualnych usługi Azure IaaS (infrastruktura jako usługa) są takie same. W obu przypadkach należy użyć bramy Zarządzanie danymi do łączności.

Zobacz temat [przeniesienie danych między lokalizacjami lokalnymi i artykułem w chmurze](data-factory-move-data-between-onprem-and-cloud.md) , aby dowiedzieć się więcej na temat bramy zarządzanie danymi i instrukcje krok po kroku dotyczące konfigurowania bramy. Skonfigurowanie wystąpienia bramy jest wymagane przed połączeniem z SQL Server.

Aby zainstalować bramę na tym samym komputerze lokalnym lub w chmurze jako SQL Server w celu uzyskania lepszej wydajności, zalecamy zainstalowanie ich na oddzielnych komputerach. Posiadanie bramy i SQL Server na oddzielnych komputerach zmniejsza rywalizację o zasoby.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok za pomocą działania kopiowania, które przenosi dane do/z bazy danych SQL Server przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania**. Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) na potrzeby szybkiego instruktażu dotyczącego tworzenia potoku przy użyciu Kreatora kopiowania danych.

Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon Azure Resource Manager**, interfejs API **platformy .NET** i **interfejs API REST**. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **fabrykę danych**. Fabryka danych może zawierać jeden lub więcej potoków.
2. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych. Na przykład jeśli kopiujesz dane z bazy danych SQL Server do magazynu obiektów blob platformy Azure, utworzysz dwie połączone usługi, aby połączyć bazę danych SQL Server i konto usługi Azure Storage z fabryką danych. Aby uzyskać właściwości połączonej usługi, które są specyficzne dla SQL Server Database, zobacz sekcję [Właściwości połączonej usługi](#linked-service-properties) .
3. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania. W przykładzie opisanym w ostatnim kroku utworzysz zestaw danych, aby określić tabelę SQL w bazie danych SQL Server, która zawiera dane wejściowe. Ponadto utworzysz kolejny zestaw danych, aby określić kontener obiektów blob i folder, który zawiera dane skopiowane z bazy danych SQL Server. Dla właściwości zestawu danych, które są specyficzne dla bazy danych SQL Server, zobacz sekcję [Właściwości zestawu danych](#dataset-properties) .
4. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. W powyższym przykładzie użyto elementu sqlsource jako źródła i wartość blobsink jako ujścia dla działania kopiowania. Podobnie, jeśli kopiujesz z usługi Azure Blob Storage do SQL Server Database, używasz BlobSource i sqlsink w działaniu kopiowania. Dla właściwości działania kopiowania, które są specyficzne dla SQL Server bazy danych, zobacz sekcję [właściwości działania kopiowania](#copy-activity-properties) . Aby uzyskać szczegółowe informacje na temat używania magazynu danych jako źródła lub ujścia, kliknij link w poprzedniej sekcji dla magazynu danych.

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON. Przykłady zawierające definicje JSON dla Data Factory jednostek, które są używane do kopiowania danych do/z bazy danych SQL Server, zobacz sekcję [przykłady JSON](#json-examples-for-copying-data-from-and-to-sql-server) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania Data Factory jednostek specyficznych dla SQL Server:

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Utworzysz połączoną usługę typu **OnPremisesSqlServer** , aby połączyć bazę danych SQL Server z fabryką danych. Poniższa tabela zawiera opis elementów JSON specyficznych dla SQL Server połączonej usługi.

Poniższa tabela zawiera opis elementów JSON specyficznych dla SQL Server połączonej usługi.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość Type powinna mieć wartość: **OnPremisesSqlServer**. |Tak |
| Parametry połączenia |Określ informacje o connectionString, które są konieczne do nawiązania połączenia z bazą danych SQL Server przy użyciu uwierzytelniania SQL lub uwierzytelniania systemu Windows. |Tak |
| gatewayName |Nazwa bramy, która ma być używana przez usługę Data Factory do łączenia się z bazą danych SQL Server. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, jeśli używasz uwierzytelniania systemu Windows. Przykład: **nazwa_domeny \\ nazwa_użytkownika**. |Nie |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. |Nie |

Poświadczenia można szyfrować za pomocą polecenia cmdlet **New-AzDataFactoryEncryptValue** i używać ich w parametrach połączenia, jak pokazano w poniższym przykładzie (Właściwość **EncryptedCredential** ):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Samples
**KOD JSON do użycia uwierzytelniania SQL**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**KOD JSON do użycia uwierzytelniania systemu Windows**

Brama Zarządzanie danymi będzie personifikować określone konto użytkownika w celu nawiązania połączenia z bazą danych SQL Server Database.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
W przykładach użyto zestawu danych typu **SqlServer** , aby reprezentować tabelę w bazie danych SQL Server.

Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych, są podobne dla wszystkich typów zestawu danych (SQL Server, Azure Blob, Azure Table itp.).

Sekcja typeProperties jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w magazynie danych. Sekcja **typeProperties** zestawu danych typu **SqlServerName** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w wystąpieniu bazy danych SQL Server, do której odwołuje się połączona usługa. |Tak |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Jeśli przenosisz dane z bazy danych SQL Server, wpisz typ źródła w działaniu Kopiuj do elementu **sqlsource**. Podobnie, Jeśli przenosisz dane do bazy danych SQL Server, należy ustawić typ ujścia w działaniu kopiowania na **sqlsink**. Ta sekcja zawiera listę właściwości obsługiwanych przez element sqlsource i sqlsink.

Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, oraz zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania przyjmuje tylko jedno wejście i generuje tylko jedno wyjście.

Natomiast właściwości dostępne w sekcji typeProperties działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się w zależności od typów źródeł i ujścia.

### <a name="sqlsource"></a>Wartość sqlsource
Gdy źródło w działaniu kopiowania jest typu **sqlsource**, w sekcji **typeProperties** są dostępne następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL. Na przykład: select * from MyTable. Może odwoływać się do wielu tabel z bazy danych, do której odwołuje się wejściowy zestaw danych. Jeśli nie zostanie określony, instrukcja SQL, która jest wykonywana: select from MyTable. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. Ostatnia instrukcja SQL musi być instrukcją SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Pary nazwa/wartość. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |

Jeśli **sqlReaderQuery** jest określony dla elementu sqlsource, działanie Copy uruchamia to zapytanie względem źródła bazy danych SQL Server, aby uzyskać dane.

Alternatywnie można określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (Jeśli procedura składowana pobiera parametry).

Jeśli nie określisz opcji sqlReaderQuery ani sqlReaderStoredProcedureName, kolumny zdefiniowane w sekcji Structure są używane do tworzenia zapytania select do uruchamiania w bazie danych SQL Server. Jeśli definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

> [!NOTE]
> W przypadku korzystania z **sqlReaderStoredProcedureName**, nadal trzeba określić wartość właściwości **TableName** w kodzie JSON zestawu danych. Nie przeprowadzono żadnych operacji sprawdzania poprawności dla tej tabeli, chociaż.

### <a name="sqlsink"></a>Ujściem element sqlsink
**Obiekt sqlsink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed upływem limitu czasu. |zakres czasu<br/><br/> Przykład: "00:30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize. |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 10000) |
| sqlWriterCleanupScript |Określ zapytanie dla działania kopiowania, które ma zostać wykonane, aby dane określonego wycinka zostały oczyszczone. Aby uzyskać więcej informacji, zobacz sekcję [powtarzania kopiowania](#repeatable-copy) . |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny dla działania kopiowania, która ma zostać wypełniona automatycznie wygenerowaną identyfikatorem wycinka, która jest używana do czyszczenia danych określonego wycinka po ponownym uruchomieniu. Aby uzyskać więcej informacji, zobacz sekcję [powtarzania kopiowania](#repeatable-copy) . |Nazwa kolumny kolumny zawierającej dane typu binary (32). |Nie |
| sqlWriterStoredProcedureName |Nazwa procedury składowanej, która definiuje sposób zastosowania danych źródłowych do tabeli docelowej, np. w celu przeprowadzenia upserts lub przekształcenia przy użyciu własnej logiki biznesowej. <br/><br/>Należy zauważyć, że ta procedura składowana zostanie **wywołana na partię**. Jeśli chcesz wykonać operację, która jest uruchamiana tylko raz i nie ma nic do zrobienia z danymi źródłowymi, np. Delete/Truncate, USE `sqlWriterCleanupScript` Property. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Pary nazwa/wartość. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |
| sqlWriterTableType |Określ nazwę typu tabeli, która ma zostać użyta w procedurze składowanej. Działanie kopiowania sprawia, że dane są dostępne w tabeli tymczasowej z tym typem tabeli. Kod procedury składowanej może następnie scalić dane kopiowane z istniejącymi danymi. |Nazwa typu tabeli. |Nie |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>Przykłady JSON kopiowania danych z i do SQL Server
W poniższych przykładach przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). W poniższych przykładach pokazano, jak skopiować dane do i z SQL Server i Blob Storage platformy Azure. Można jednak skopiować dane **bezpośrednio** z dowolnego źródła do dowolnego ujścia w [tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w Azure Data Factory.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Przykład: kopiowanie danych z SQL Server do obiektu blob platformy Azure
Poniższy przykład pokazuje:

1. Połączona usługa typu [OnPremisesSqlServer](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [SqlServer](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa elementów [sqlsource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane szeregów czasowych z tabeli SQL Server do obiektu blob platformy Azure co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

Pierwszym krokiem jest skonfigurowanie bramy zarządzania danymi. Instrukcje dotyczą [przemieszczania danych między lokalizacjami lokalnymi i artykułami w chmurze](data-factory-move-data-between-onprem-and-cloud.md) .

**SQL Server połączona usługa**
```json
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
**Połączona usługa Azure Blob Storage**

```json
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
**SQL Server wejściowy zestaw danych**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w SQL Server i zawiera kolumnę o nazwie "timestampcolumn" dla danych szeregów czasowych. Można wykonywać zapytania dotyczące wielu tabel w tej samej bazie danych przy użyciu jednego zestawu danych, ale dla tabeli dataset typeProperty należy użyć pojedynczej tabeli.

Ustawienie "External": "true" informuje Data Factory usługę, że zestaw danych jest zewnętrzny względem fabryki danych i nie jest tworzony przez działanie w fabryce danych.

```json
{
  "name": "SqlServerInput",
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
**Wyjściowy zestaw danych obiektów blob platformy Azure**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu dla obiektu BLOB jest obliczana dynamicznie na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny rozpoczęcia.

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

Potok zawiera działanie kopiowania, które jest skonfigurowane do używania tych wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma ustawioną wartość **sqlsource** , a typ **ujścia** to **wartość blobsink**. Zapytanie SQL określone dla właściwości **SqlReaderQuery** wybiera dane w ciągu ostatniej godziny do skopiowania.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
W tym przykładzie **sqlReaderQuery** jest określony dla elementu sqlsource. Działanie kopiowania uruchamia to zapytanie względem źródła danych SQL Server, aby uzyskać dane. Alternatywnie można określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (Jeśli procedura składowana pobiera parametry). SqlReaderQuery może odwoływać się do wielu tabel w bazie danych, do których odwołuje się wejściowy zestaw danych. Nie jest ograniczony tylko do zestawu tabel jako tabeli typeProperty.

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName, kolumny zdefiniowane w sekcji struktury są używane do tworzenia zapytania select do uruchomienia względem bazy danych SQL Server. Jeśli definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

Zobacz sekcję [Źródło SQL](#sqlsource) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties) , aby uzyskać listę właściwości obsługiwanych przez sqlsource i wartość blobsink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Przykład: Kopiuj dane z obiektu blob platformy Azure do SQL Server
Poniższy przykład pokazuje:

1. Połączona usługa typu [OnPremisesSqlServer](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [SqlServer](data-factory-sqlserver-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i sqlsink.

Przykład kopiuje dane szeregów czasowych z obiektu blob platformy Azure do tabeli SQL Server co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

**SQL Server połączona usługa**

```json
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
**Połączona usługa Azure Blob Storage**

```json
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
**Zestaw danych wejściowych obiektów blob platformy Azure**

Dane są pobierane z nowego obiektu BLOB co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu i nazwa pliku obiektu BLOB są dynamicznie oceniane na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części Year, month i Day czasu rozpoczęcia i nazwy pliku używa części godziny godziny rozpoczęcia. ustawienie "External": "true" informuje usługę Data Factory, że zestaw danych jest zewnętrzny względem fabryki danych i nie jest tworzony przez działanie w fabryce danych.

```json
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
**SQL Server wyjściowy zestaw danych**

Przykład kopiuje dane do tabeli o nazwie "MyTable" w SQL Server. Utwórz tabelę w SQL Server z taką samą liczbą kolumn jak oczekiwany plik CSV. Nowe wiersze są dodawane do tabeli co godzinę.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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
**Potok z działaniem kopiowania**

Potok zawiera działanie kopiowania, które jest skonfigurowane do używania tych wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **BlobSource** , a typ **ujścia** to **sqlsink**.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": " SqlServerOutput "
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
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

## <a name="troubleshooting-connection-issues"></a>Rozwiązywanie problemów z połączeniem
1. Skonfiguruj SQL Server tak, aby akceptowały połączenia zdalne. Uruchom **SQL Server Management Studio**, kliknij prawym przyciskiem myszy pozycję **serwer**, a następnie kliknij polecenie **Właściwości**. Wybierz z listy pozycję **połączenia** i zaznacz pole wyboru **Zezwalaj na połączenia zdalne z serwerem**.

    ![Włącz połączenia zdalne](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Szczegółowe instrukcje można znaleźć w temacie [Konfigurowanie serwera dostępu zdalnego](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option) .
2. Uruchom **Configuration Manager SQL Server**. Rozwiń **SQL Server konfigurację sieci** dla żądanego wystąpienia, a następnie wybierz pozycję **Protokoły dla MSSQLSERVER**. Protokoły powinny być widoczne w okienku po prawej stronie. Aby włączyć protokół TCP/IP, kliknij prawym przyciskiem myszy pozycję **TCP/IP** , a następnie kliknij pozycję **Włącz**.

    ![Włącz protokół TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Zobacz [Włączanie lub wyłączanie protokołu sieciowego serwera](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol) , aby uzyskać szczegółowe informacje i alternatywne sposoby włączania protokołu TCP/IP.
3. W tym samym oknie kliknij dwukrotnie pozycję **TCP/IP** , aby uruchomić okno **właściwości protokołu TCP/IP** .
4. Przejdź do karty **adresy IP** . Przewiń w dół, aby wyświetlić sekcję **IPAll** . Zanotuj **port TCP**(domyślnie **1433**).
5. Utwórz **regułę dla zapory systemu Windows** na komputerze, aby zezwolić na ruch przychodzący przez ten port.
6. **Sprawdź połączenie**: Aby nawiązać połączenie z SQL Server przy użyciu w pełni kwalifikowanej nazwy, użyj SQL Server Management Studio z innego komputera. Na przykład: " \<machine\> . \<domain\> . Corp. \<company\> . com, 1433. "

   > [!IMPORTANT]
   > 
   > Aby uzyskać szczegółowe informacje [, zobacz Przenoszenie danych między źródłami lokalnymi i chmurą z bramą zarządzanie danymi](data-factory-move-data-between-onprem-and-cloud.md) .
   > 
   > Zobacz [Rozwiązywanie problemów z bramą](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) , aby uzyskać porady dotyczące rozwiązywania problemów związanych z połączeniem/bramą.


## <a name="identity-columns-in-the-target-database"></a>Kolumny tożsamości w docelowej bazie danych
Ta sekcja zawiera przykład, który kopiuje dane z tabeli źródłowej bez kolumny tożsamości do tabeli docelowej z kolumną tożsamości.

**Tabela źródłowa:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Tabela docelowa:**

```sql
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```

Zwróć uwagę, że tabela docelowa ma kolumnę tożsamości.

**Definicja JSON zestawu danych źródłowych**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Definicja JSON zestawu danych docelowych**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Zwróć uwagę, że jako tabela źródłowa i docelowa mają różne schematy (element docelowy ma dodatkową kolumnę z tożsamością). W tym scenariuszu należy określić właściwość **Structure** w definicji docelowego zestawu danych, która nie zawiera kolumny tożsamości.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Wywołaj procedurę składowaną z ujścia SQL
Zobacz [wywoływanie procedury składowanej dla UJŚCIA SQL w artykule działanie kopiowania](data-factory-invoke-stored-procedure-from-copy-activity.md) , aby zapoznać się z przykładem wywoływania procedury składowanej ze ujścia bazy danych SQL w działaniu kopiowania potoku.

## <a name="type-mapping-for-sql-server"></a>Mapowanie typu dla programu SQL Server
Jak wspomniano w artykule [działania związane z przenoszeniem danych](data-factory-data-movement-activities.md) , działanie kopiowania wykonuje konwersje typu automatycznego z typów źródłowych na typy obiektów ujścia o następujących dwuetapowych podejściach:

1. Konwertuj z natywnych typów źródła na typ .NET
2. Konwertuj z typu .NET na natywny typ ujścia

Podczas przesuwania danych do & z programu SQL Server następujące mapowania są używane z typu SQL do typu .NET i na odwrót.

Mapowanie jest takie samo jak mapowanie typu danych SQL Server ADO.NET.

| Typ aparatu bazy danych SQL Server | Typ programu .NET Framework |
| --- | --- |
| bigint |Int64 |
| binarny |Byte [] |
| bit |Wartość logiczna |
| char |String, Char [] |
| data |DateTime |
| Datetime (data/godzina) |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Liczba dziesiętna |Liczba dziesiętna |
| FILESTREAM — atrybut (varbinary (max)) |Byte [] |
| Float |Double |
| image (obraz) |Byte [] |
| int |Int32 |
| pieniędzy |Liczba dziesiętna |
| nchar |String, Char [] |
| ntext |String, Char [] |
| numeryczne |Liczba dziesiętna |
| nvarchar |String, Char [] |
| liczba rzeczywista |Pojedynczy |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Liczba dziesiętna |
| sql_variant |Stream |
| tekst |String, Char [] |
| time |przedział_czasu |
| sygnatura czasowa |Byte [] |
| tinyint |Byte |
| uniqueidentifier |Guid (identyfikator GUID) |
| varbinary |Byte [] |
| varchar |String, Char [] |
| xml |Xml |

## <a name="mapping-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby zmapować kolumny ze źródłowego zestawu danych do kolumn z obiektu ujścia danych, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Powtórzona kopia
Podczas kopiowania danych do bazy danych SQL Server działanie Copy domyślnie dołącza dane do tabeli sink. Aby wykonać UPSERT zamiast tego, zobacz [powtarzalny zapis w artykule sqlsink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) .

Podczas kopiowania danych z magazynów danych relacyjnych należy mieć na uwadze powtarzalność, aby uniknąć niezamierzonych wyników. W Azure Data Factory można ręcznie uruchomić ponownie wycinka. Możesz również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek był uruchamiany ponownie w przypadku wystąpienia błędu. Gdy wycinek jest uruchamiany ponownie w dowolny sposób, należy się upewnić, że te same dane są odczytywane niezależnie od tego, ile razy jest uruchomiony plasterek. Zobacz [powtarzanie odczytu ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [Przewodnik dostrajania wydajności & działania kopiowania](data-factory-copy-activity-performance.md) , aby poznać kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji.