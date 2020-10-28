---
title: Kopiuj dane do/z Azure SQL Database
description: Informacje o kopiowaniu danych do/z Azure SQL Database przy użyciu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 83ab9e212e71ad53007a84ad8c10979bfea4516b
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637398"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Kopiowanie danych do i z Azure SQL Database przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-azure-sql-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-sql-database.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zapoznaj się z tematem [Azure SQL Database Connector w wersji 2](../connector-azure-sql-database.md).

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do przenoszenia danych do i z Azure SQL Database. Jest on używany w artykule dotyczącym [przenoszenia danych](data-factory-data-movement-activities.md) , który przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane **z Azure SQL Database** można kopiować do następujących magazynów danych:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Dane z następujących magazynów danych można kopiować **do Azure SQL Database** :

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Obsługiwany typ uwierzytelniania
Łącznik Azure SQL Database obsługuje uwierzytelnianie podstawowe.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, które przenosi dane do/z Azure SQL Database przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku jest użycie **Kreatora kopiowania** . Zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) na potrzeby szybkiego instruktażu dotyczącego tworzenia potoku przy użyciu Kreatora kopiowania danych.

Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio** , **Azure PowerShell** , **szablon Azure Resource Manager** , interfejs API **platformy .NET** i **interfejs API REST** . Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **fabrykę danych** . Fabryka danych może zawierać jeden lub więcej potoków.
2. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych. Na przykład jeśli kopiujesz dane z usługi Azure Blob Storage do Azure SQL Database, utworzysz dwie połączone usługi, aby połączyć konto usługi Azure Storage i Azure SQL Database z fabryką danych. Aby uzyskać właściwości połączonej usługi, które są specyficzne dla Azure SQL Database, zobacz sekcję [Właściwości połączonej usługi](#linked-service-properties) .
3. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania. W przykładzie opisanym w ostatnim kroku utworzysz zestaw danych, aby określić kontener obiektów blob i folder zawierający dane wejściowe. Ponadto utworzysz kolejny zestaw danych, aby określić tabelę SQL w Azure SQL Database, która zawiera dane skopiowane z magazynu obiektów BLOB. Dla właściwości zestawu danych, które są specyficzne dla Azure Data Lake Store, zobacz sekcję [Właściwości zestawu danych](#dataset-properties) .
4. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. W powyższym przykładzie użyto BlobSource jako źródła i obiektu sqlsink jako ujścia dla działania kopiowania. Podobnie, jeśli kopiujesz z Azure SQL Database do Blob Storage platformy Azure, użyjesz sqlsource i wartość blobsink w działaniu kopiowania. Aby uzyskać właściwości działania kopiowania, które są specyficzne dla Azure SQL Database, zobacz sekcję [właściwości działania kopiowania](#copy-activity-properties) . Aby uzyskać szczegółowe informacje na temat używania magazynu danych jako źródła lub ujścia, kliknij link w poprzedniej sekcji dla magazynu danych.

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON. Przykłady zawierające definicje JSON dla Data Factory jednostek, które są używane do kopiowania danych do/z Azure SQL Database, zobacz sekcję [przykłady JSON](#json-examples-for-copying-data-to-and-from-sql-database) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania Data Factory jednostek specyficznych dla Azure SQL Database:

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Linki połączonej usługi Azure SQL Azure SQL Database do fabryki danych. Poniższa tabela zawiera opis elementów JSON specyficznych dla połączonej usługi Azure SQL.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość Type musi mieć wartość: **AzureSqlDatabase** |Tak |
| Parametry połączenia |Określ informacje, które są konieczne do nawiązania połączenia z wystąpieniem Azure SQL Database dla właściwości connectionString. Obsługiwane jest tylko uwierzytelnianie podstawowe. |Tak |

> [!IMPORTANT]
> Skonfiguruj [Azure SQL Database zaporą](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure) serwera bazy danych, aby [umożliwić usługom platformy Azure dostęp do serwera](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure). Ponadto, jeśli kopiujesz dane do Azure SQL Database spoza platformy Azure, w tym z lokalnych źródeł danych za pomocą bramy usługi Data Factory, skonfiguruj odpowiedni zakres adresów IP dla maszyny wysyłającej dane do Azure SQL Database.

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby określić zestaw danych do reprezentowania danych wejściowych lub wyjściowych w Azure SQL Database, należy ustawić Właściwość Type zestawu danych na: **wartość azuresqltable** . Ustaw właściwość **linkedServiceName** zestawu danych na nazwę połączonej usługi Azure SQL.

Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych są podobne dla wszystkich typów zestawu danych (Azure SQL, Azure Blob, Azure Table itp.).

Sekcja typeProperties jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w magazynie danych. Sekcja **typeProperties** zestawu danych typu **wartość azuresqltable** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w wystąpieniu Azure SQL Database, do którego odwołuje się połączona usługa. |Tak |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, oraz zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania przyjmuje tylko jedno wejście i generuje tylko jedno wyjście.

Natomiast właściwości dostępne w sekcji **typeProperties** działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się w zależności od typów źródeł i ujścia.

Jeśli przenosisz dane z Azure SQL Database, musisz ustawić typ źródła w działaniu Kopiuj do **sqlsource** . Podobnie, Jeśli przenosisz dane do Azure SQL Database, należy ustawić typ ujścia w działaniu kopiowania na **sqlsink** . Ta sekcja zawiera listę właściwości obsługiwanych przez element sqlsource i sqlsink.

### <a name="sqlsource"></a>Wartość sqlsource
W działaniu kopiowania, gdy źródłem jest typ **sqlsource** , w sekcji **typeProperties** są dostępne następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL. Przykład: `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. Ostatnia instrukcja SQL musi być instrukcją SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Pary nazwa/wartość. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |

Jeśli **sqlReaderQuery** jest określony dla elementu sqlsource, działanie Copy uruchamia to zapytanie względem źródła Azure SQL Database, aby uzyskać dane. Alternatywnie można określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (Jeśli procedura składowana pobiera parametry).

Jeśli nie określisz opcji sqlReaderQuery ani sqlReaderStoredProcedureName, kolumny zdefiniowane w sekcji Struktura JSON zestawu danych są używane do tworzenia zapytania ( `select column1, column2 from mytable` ) w celu uruchomienia względem Azure SQL Database. Jeśli definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

> [!NOTE]
> W przypadku korzystania z **sqlReaderStoredProcedureName** , nadal trzeba określić wartość właściwości **TableName** w kodzie JSON zestawu danych. Nie przeprowadzono żadnych operacji sprawdzania poprawności dla tej tabeli, chociaż.
>
>

### <a name="sqlsource-example"></a>Przykład sqlsource

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```

**Definicja procedury składowanej:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqlsink"></a>Ujściem element sqlsink
**Obiekt sqlsink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed upływem limitu czasu. |zakres czasu<br/><br/> Przykład: "00:30:00" (30 minut). |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize. |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 10000) |
| sqlWriterCleanupScript |Określ zapytanie dla działania kopiowania, które ma zostać wykonane, aby dane określonego wycinka zostały oczyszczone. Aby uzyskać więcej informacji, zobacz [powtórzona kopia](#repeatable-copy). |Instrukcja zapytania. |Nie |
| sliceIdentifierColumnName |Określ nazwę kolumny dla działania kopiowania, która ma zostać wypełniona automatycznie wygenerowaną identyfikatorem wycinka, która jest używana do czyszczenia danych określonego wycinka po ponownym uruchomieniu. Aby uzyskać więcej informacji, zobacz [powtórzona kopia](#repeatable-copy). |Nazwa kolumny kolumny zawierającej dane typu binary (32). |Nie |
| sqlWriterStoredProcedureName |Nazwa procedury składowanej, która definiuje sposób zastosowania danych źródłowych do tabeli docelowej, np. w celu przeprowadzenia upserts lub przekształcenia przy użyciu własnej logiki biznesowej. <br/><br/>Należy zauważyć, że ta procedura składowana zostanie **wywołana na partię** . Jeśli chcesz wykonać operację, która jest uruchamiana tylko raz i nie ma nic do zrobienia z danymi źródłowymi, np. Delete/Truncate, USE `sqlWriterCleanupScript` Property. |Nazwa procedury składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Pary nazwa/wartość. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |
| sqlWriterTableType |Określ nazwę typu tabeli, która ma zostać użyta w procedurze składowanej. Działanie kopiowania sprawia, że dane są dostępne w tabeli tymczasowej z tym typem tabeli. Kod procedury składowanej może następnie scalić dane kopiowane z istniejącymi danymi. |Nazwa typu tabeli. |Nie |

#### <a name="sqlsink-example"></a>Przykład obiektu sqlsink

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>Przykłady JSON kopiowania danych do i z SQL Database
W poniższych przykładach przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują one sposób kopiowania danych do i z Azure SQL Database i Blob Storage platformy Azure. Można jednak skopiować dane **bezpośrednio** z dowolnego źródła do dowolnego ujścia w [tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Przykład: kopiowanie danych z Azure SQL Database do obiektu blob platformy Azure
Ten sam definiuje następujące jednostki Data Factory:

1. Połączona usługa typu [AzureSqlDatabase](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [wartość azuresqltable](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa elementu [sqlsource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane szeregów czasowych (co godzinę, codziennie itd.) z tabeli w Azure SQL Database do obiektu BLOB co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

**Azure SQL Database połączona usługa:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Zapoznaj się z sekcją połączonej usługi Azure SQL, aby uzyskać listę właściwości obsługiwanych przez tę połączoną usługę.

**Połączona usługa Azure Blob Storage:**

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
Listę właściwości obsługiwanych przez tę połączoną usługę znajdziesz w artykule dotyczącym [obiektów blob platformy Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) .


**Zestaw danych wejściowych usługi Azure SQL:**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w usłudze Azure SQL i zawiera kolumnę o nazwie "timestampcolumn" dla danych szeregów czasowych.

Ustawienie "External": "true" informuje usługę Azure Data Factory, że zestaw danych jest zewnętrzny względem fabryki danych i nie jest tworzony przez działanie w fabryce danych.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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

Zobacz sekcję właściwości typu zestawu danych usługi Azure SQL, aby zapoznać się z listą właściwości obsługiwanych przez ten typ zestawu danych.

**Wyjściowy zestaw danych obiektów blob platformy Azure:**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu dla obiektu BLOB jest obliczana dynamicznie na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części roku, miesiąca, dnia i godziny rozpoczęcia.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
Zobacz sekcję [właściwości typu zestawu danych obiektów blob platformy Azure](data-factory-azure-blob-connector.md#dataset-properties) , aby uzyskać listę właściwości obsługiwanych przez ten typ zestawu danych.

**Działanie kopiowania w potoku ze źródłem SQL i obiektem BLOB:**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma ustawioną wartość **sqlsource** , a typ **ujścia** to **wartość blobsink** . Zapytanie SQL określone dla właściwości **SqlReaderQuery** wybiera dane w ciągu ostatniej godziny do skopiowania.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
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
W przykładzie **sqlReaderQuery** jest określony dla elementu sqlsource. Działanie kopiowania uruchamia to zapytanie względem źródła Azure SQL Database, aby uzyskać dane. Alternatywnie można określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (Jeśli procedura składowana pobiera parametry).

Jeśli nie określisz opcji sqlReaderQuery ani sqlReaderStoredProcedureName, kolumny zdefiniowane w sekcji Struktura JSON elementu DataSet są używane do tworzenia zapytania do uruchomienia względem Azure SQL Database. Na przykład: `select column1, column2 from mytable`. Jeśli definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

Zobacz sekcję [Źródło SQL](#sqlsource) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties) , aby uzyskać listę właściwości obsługiwanych przez sqlsource i wartość blobsink.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Przykład: Kopiuj dane z obiektu blob platformy Azure do Azure SQL Database
Przykład definiuje następujące jednostki Data Factory:

1. Połączona usługa typu [AzureSqlDatabase](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [wartość azuresqltable](#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [sqlsink](#copy-activity-properties).

Przykład kopiuje dane szeregów czasowych (co godzinę, codziennie itd.) z obiektu blob platformy Azure do tabeli w Azure SQL Database co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

**Połączona usługa Azure SQL:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Zapoznaj się z sekcją połączonej usługi Azure SQL, aby uzyskać listę właściwości obsługiwanych przez tę połączoną usługę.

**Połączona usługa Azure Blob Storage:**

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
Listę właściwości obsługiwanych przez tę połączoną usługę znajdziesz w artykule dotyczącym [obiektów blob platformy Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) .


**Zestaw danych wejściowych obiektów blob platformy Azure:**

Dane są pobierane z nowego obiektu BLOB co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu i nazwa pliku obiektu BLOB są dynamicznie oceniane na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części Year, month i Day czasu rozpoczęcia i nazwy pliku używa części godziny godziny rozpoczęcia. ustawienie "External": "true" informuje usługę Data Factory, że ta tabela jest zewnętrzna z fabryką danych i nie jest generowana przez działanie w fabryce danych.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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
Zobacz sekcję [właściwości typu zestawu danych obiektów blob platformy Azure](data-factory-azure-blob-connector.md#dataset-properties) , aby uzyskać listę właściwości obsługiwanych przez ten typ zestawu danych.

**Azure SQL Database wyjściowy zestaw danych:**

Przykład kopiuje dane do tabeli o nazwie "MyTable" w usłudze Azure SQL. Utwórz tabelę w usłudze Azure SQL o tej samej liczbie kolumn, która będzie zawierała plik CSV. Nowe wiersze są dodawane do tabeli co godzinę.

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
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
Zobacz sekcję właściwości typu zestawu danych usługi Azure SQL, aby zapoznać się z listą właściwości obsługiwanych przez ten typ zestawu danych.

**Działanie kopiowania w potoku ze źródłem obiektów blob i obiektem SQL:**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **BlobSource** , a typ **ujścia** to **sqlsink** .

```JSON
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
            "name": "AzureSqlOutput"
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
Zobacz sekcję [ujścia SQL](#sqlsink) i [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) , aby uzyskać listę właściwości obsługiwanych przez obiekty sqlsink i BlobSource.

## <a name="identity-columns-in-the-target-database"></a>Kolumny tożsamości w docelowej bazie danych
Ta sekcja zawiera przykład kopiowania danych z tabeli źródłowej bez kolumny tożsamości do tabeli docelowej z kolumną tożsamości.

**Tabela źródłowa:**

```SQL
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Tabela docelowa:**

```SQL
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```
Zwróć uwagę, że tabela docelowa ma kolumnę tożsamości.

**Definicja JSON zestawu danych źródłowych**

```JSON
{
    "name": "SampleSource",
    "properties": {
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

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
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
Aby zapoznać się z przykładem wywoływania procedury składowanej ze ujścia bazy danych SQL w działaniu kopiowania potoku, zobacz [wywoływanie procedury składowanej dla UJŚCIA SQL w artykule działanie kopiowania](data-factory-invoke-stored-procedure-from-copy-activity.md) .

## <a name="type-mapping-for-azure-sql-database"></a>Mapowanie typu dla Azure SQL Database
Jak wspomniano w artykule [działania związane z przenoszeniem danych](data-factory-data-movement-activities.md) , działanie kopiowania wykonuje konwersje typów automatycznych z typów źródłowych do typów ujścia z następującymi krokami:

1. Konwertuj z natywnych typów źródła na typ .NET
2. Konwertuj z typu .NET na natywny typ ujścia

Podczas przemieszczania danych do i z Azure SQL Database następujące mapowania są używane z typu SQL do typu .NET i na odwrót. Mapowanie jest takie samo jak mapowanie typu danych SQL Server ADO.NET.

| Typ aparatu bazy danych SQL Server | Typ programu .NET Framework |
| --- | --- |
| bigint |Int64 |
| binarny |Byte [] |
| bit |Boolean |
| char |String, Char [] |
| date |DateTime |
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

## <a name="map-source-to-sink-columns"></a>Mapowanie źródła do kolumn ujścia
Aby dowiedzieć się więcej na temat mapowania kolumn w źródłowym zestawie danych na kolumny w datadataset, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Powtórzona kopia
Podczas kopiowania danych do bazy danych SQL Server działanie Copy domyślnie dołącza dane do tabeli sink. Aby wykonać UPSERT zamiast tego, zobacz [powtarzalny zapis w artykule sqlsink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) .

Podczas kopiowania danych z magazynów danych relacyjnych należy mieć na uwadze powtarzalność, aby uniknąć niezamierzonych wyników. W Azure Data Factory można ręcznie uruchomić ponownie wycinka. Możesz również skonfigurować zasady ponawiania dla zestawu danych, aby wycinek był uruchamiany ponownie w przypadku wystąpienia błędu. Gdy wycinek jest uruchamiany ponownie w dowolny sposób, należy się upewnić, że te same dane są odczytywane niezależnie od tego, ile razy jest uruchomiony plasterek. Zobacz [powtarzanie odczytu ze źródeł relacyjnych](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [Przewodnik dostrajania wydajności & działania kopiowania](data-factory-copy-activity-performance.md) , aby poznać kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji.