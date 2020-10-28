---
title: Kopiowanie danych do/z usługi Azure Synapse Analytics (dawniej SQL Data Warehouse)
description: Dowiedz się, jak kopiować dane do/z usługi Azure Synapse Analytics (dawniej SQL Data Warehouse) za pomocą Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 55582fb8c4fc80ab005a01ec015035963404e639
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637415"
---
# <a name="copy-data-to-and-from-azure-synapse-analytics-formerly-sql-data-warehouse-using-azure-data-factory"></a>Kopiowanie danych do i z usługi Azure Synapse Analytics (dawniej SQL Data Warehouse) przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Łącznik usługi Azure Synapse Analytics w wersji 2](../connector-azure-sql-data-warehouse.md).

W tym artykule wyjaśniono, jak za pomocą działania kopiowania w Azure Data Factory przenieść dane do/z usługi Azure Synapse Analytics. Jest on używany w artykule dotyczącym [przenoszenia danych](data-factory-data-movement-activities.md) , który przedstawia ogólne omówienie przenoszenia danych za pomocą działania kopiowania.

> [!TIP]
> Aby uzyskać najlepszą wydajność, należy użyć bazy danych na platformie Azure Synapse Analytics. [Aby załadować dane do usługi Azure Synapse Analytics, należy wykonać](#use-polybase-to-load-data-into-azure-synapse-analytics) szczegóły. Aby zapoznać się z przewodnikiem dotyczącym przypadku użycia, zobacz [ładowanie 1 TB do usługi Azure Synapse Analytics na 15 minut z Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane **z usługi Azure Synapse Analytics** można kopiować do następujących magazynów danych:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Dane z następujących magazynów danych można kopiować **do usługi Azure Synapse Analytics** :

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Podczas kopiowania danych z SQL Server lub Azure SQL Database do usługi Azure Synapse Analytics, jeśli tabela nie istnieje w magazynie docelowym, Data Factory może automatycznie utworzyć tabelę w Synapse Analytics przy użyciu schematu tabeli w źródłowym magazynie danych. Aby uzyskać szczegółowe informacje, zobacz sekcję [Tworzenie tabeli](#auto-table-creation) .

## <a name="supported-authentication-type"></a>Obsługiwany typ uwierzytelniania
Łącznik usługi Azure Synapse Analytics obsługuje uwierzytelnianie podstawowe.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, które przenosi dane do/z usługi Azure Synapse Analytics przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku, który kopiuje dane do/z usługi Azure Synapse Analytics, jest użycie Kreatora kopiowania danych. Zapoznaj [się z samouczkiem: Załaduj dane do analizy Synapse z Data Factory](../load-azure-sql-data-warehouse.md) , aby uzyskać szybki Przewodnik tworzenia potoku za pomocą Kreatora kopiowania danych.

Do utworzenia potoku można także użyć następujących narzędzi: **Visual Studio** , **Azure PowerShell** , **szablon Azure Resource Manager** , interfejs API **platformy .NET** i **interfejs API REST** . Aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoku za pomocą działania kopiowania, zobacz [Samouczek dotyczący działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Niezależnie od tego, czy używasz narzędzi, czy interfejsów API, wykonaj następujące kroki, aby utworzyć potok służący do przenoszenia danych ze źródłowego magazynu danych do magazynu danych ujścia:

1. Utwórz **fabrykę danych** . Fabryka danych może zawierać jeden lub więcej potoków. 
2. Utwórz **połączone usługi** , aby połączyć magazyny danych wejściowych i wyjściowych z fabryką danych. Na przykład w przypadku kopiowania danych z usługi Azure Blob Storage do usługi Azure Synapse Analytics można utworzyć dwie połączone usługi, aby połączyć konto usługi Azure Storage i usługę Azure Synapse Analytics z fabryką danych. Aby uzyskać właściwości połączonej usługi, które są specyficzne dla usługi Azure Synapse Analytics, zobacz sekcję [Właściwości połączonej usługi](#linked-service-properties) . 
3. Utwórz **zestawy** danych, aby reprezentować dane wejściowe i wyjściowe dla operacji kopiowania. W przykładzie opisanym w ostatnim kroku utworzysz zestaw danych, aby określić kontener obiektów blob i folder zawierający dane wejściowe. Ponadto utworzysz kolejny zestaw danych, aby określić tabelę w usłudze Azure Synapse Analytics, która przechowuje dane skopiowane z magazynu obiektów BLOB. Dla właściwości zestawu danych, które są specyficzne dla usługi Azure Synapse Analytics, zobacz sekcję [Właściwości zestawu danych](#dataset-properties) .
4. Utwórz **potok** z działaniem kopiowania, które pobiera zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. W powyższym przykładzie użyto BlobSource jako źródła i SqlDWSink jako ujścia dla działania kopiowania. Podobnie, jeśli kopiujesz z usługi Azure Synapse Analytics do usługi Azure Blob Storage, używasz funkcji SqlDWSource i wartość blobsink w działaniu kopiowania. Aby uzyskać właściwości działania kopiowania specyficzne dla usługi Azure Synapse Analytics, zobacz sekcję [właściwości działania kopiowania](#copy-activity-properties) . Aby uzyskać szczegółowe informacje na temat używania magazynu danych jako źródła lub ujścia, kliknij link w poprzedniej sekcji dla magazynu danych.

Gdy używasz Kreatora, definicje JSON dla tych Data Factory jednostek (połączone usługi, zestawy danych i potok) są automatycznie tworzone. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API .NET), należy zdefiniować te Data Factory jednostki przy użyciu formatu JSON. Przykłady zawierające definicje JSON dla Data Factory jednostek, które są używane do kopiowania danych do/z usługi Azure Synapse Analytics, zobacz sekcję [przykłady JSON](#json-examples-for-copying-data-to-and-from-azure-synapse-analytics) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje na temat właściwości JSON, które są używane do definiowania jednostek Data Factory specyficznych dla usługi Azure Synapse Analytics:

## <a name="linked-service-properties"></a>Właściwości połączonej usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla połączonej usługi Azure Synapse Analytics.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| typ |Właściwość Type musi mieć wartość: **AzureSqlDW** |Tak |
| Parametry połączenia |Określ informacje, które są konieczne do nawiązania połączenia z wystąpieniem usługi Azure Synapse Analytics dla właściwości connectionString. Obsługiwane jest tylko uwierzytelnianie podstawowe. |Tak |

> [!IMPORTANT]
> Skonfiguruj [zaporę Azure SQL Database](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure) i serwer baz danych, aby [umożliwić usługom platformy Azure dostęp do serwera](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure). Ponadto w przypadku kopiowania danych do usługi Azure Synapse Analytics z zewnątrz platformy Azure, w tym z lokalnych źródeł danych za pomocą bramy usługi Data Factory, należy skonfigurować odpowiedni zakres adresów IP dla maszyny wysyłającej dane do analizy Synapse Azure.

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych](data-factory-create-datasets.md) . Sekcje, takie jak struktura, dostępność i zasady JSON zestawu danych są podobne dla wszystkich typów zestawu danych (Azure SQL, Azure Blob, Azure Table itp.).

Sekcja typeProperties jest inna dla każdego typu zestawu danych i zawiera informacje dotyczące lokalizacji danych w magazynie danych. Sekcja **typeProperties** zestawu danych typu **AzureSqlDWTable** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w bazie danych usługi Azure Synapse Analytics, do której odwołuje się połączona usługa. |Tak |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz artykuł [Tworzenie potoków](data-factory-create-pipelines.md) . Właściwości, takie jak nazwa, opis, tabele wejściowe i wyjściowe, oraz zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania przyjmuje tylko jedno wejście i generuje tylko jedno wyjście.

Natomiast właściwości dostępne w sekcji typeProperties działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się w zależności od typów źródeł i ujścia.

### <a name="sqldwsource"></a>SqlDWSource
Jeśli źródło jest typu **SqlDWSource** , w sekcji **typeProperties** są dostępne następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj zapytania niestandardowego do odczytywania danych. |Ciąg zapytania SQL. Na przykład: select * from MyTable. |Nie |
| sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. Ostatnia instrukcja SQL musi być instrukcją SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Parametry procedury składowanej. |Pary nazwa/wartość. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |

Jeśli **sqlReaderQuery** jest określony dla SqlDWSource, działanie Copy uruchamia to zapytanie względem źródła analizy usługi Azure Synapse, aby uzyskać dane.

Alternatywnie można określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (Jeśli procedura składowana pobiera parametry).

Jeśli nie określisz opcji sqlReaderQuery ani sqlReaderStoredProcedureName, kolumny zdefiniowane w sekcji Struktura JSON zestawu danych są używane do tworzenia zapytania do uruchamiania w usłudze Azure Synapse Analytics. Przykład: `select column1, column2 from mytable`. Jeśli definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

#### <a name="sqldwsource-example"></a>Przykład SqlDWSource

```JSON
"source": {
    "type": "SqlDWSource",
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

### <a name="sqldwsink"></a>SqlDWSink
**SqlDWSink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Określ zapytanie dla działania kopiowania, które ma zostać wykonane, aby dane określonego wycinka zostały oczyszczone. Aby uzyskać szczegółowe informacje, zobacz [sekcję powtarzalność](#repeatability-during-copy). |Instrukcja zapytania. |Nie |
| allowPolyBase |Wskazuje, czy należy używać elementu Base (jeśli ma zastosowanie) zamiast mechanizmu BULKINSERT. <br/><br/> **Korzystanie z bazy danych Base jest zalecanym sposobem ładowania do usługi Azure Synapse Analytics.** Aby uzyskać informacje o ograniczeniach i szczegółach, zobacz temat Tworzenie [bazy danych w sekcji Analiza usługi Azure Synapse](#use-polybase-to-load-data-into-azure-synapse-analytics) . |Prawda <br/>False (domyślnie) |Nie |
| polyBaseSettings |Grupa właściwości, które można określić, gdy właściwość **allowPolybase** ma **wartość true** . |&nbsp; |Nie |
| rejectValue |Określa liczbę lub procent wierszy, które można odrzucić przed zakończeniem wykonywania zapytania. <br/><br/>Dowiedz się więcej o opcjach odrzucenia bazy danych w sekcji **argumenty** w temacie [Create External Table (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql) . |0 (wartość domyślna), 1, 2,... |Nie |
| rejectType |Określa, czy opcja rejectValue jest określona jako wartość literału, czy wartość procentowa. |Wartość (domyślnie), wartość procentowa |Nie |
| rejectSampleValue |Określa liczbę wierszy do pobrania przed ponownym obliczeniem procentu odrzuconych wierszy. |1, 2,... |Tak, w przypadku **odrzucenia** **wartości procentowej** |
| useTypeDefault |Określa, jak obsługiwać brakujące wartości w rozdzielanych plikach tekstowych, gdy baza danych pobiera dane z pliku tekstowego.<br/><br/>Więcej informacji na temat tej właściwości znajduje się w sekcji argumentów w temacie [Create External File Format (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql). |Prawda, FAŁSZ (wartość domyślna) |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 10000) |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed upływem limitu czasu. |zakres czasu<br/><br/> Przykład: "00:30:00" (30 minut). |Nie |

#### <a name="sqldwsink-example"></a>Przykład SqlDWSink

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>Ładowanie danych do usługi Azure Synapse Analytics przy użyciu wielobazowego
Korzystanie z **[bazy danych Base](/sql/relational-databases/polybase/polybase-guide)** jest efektywnym sposobem ładowania dużych ilości dane do usługi Azure Synapse Analytics z wysoką przepływność. Duży wzrost przepływności można zobaczyć przy użyciu bazy zamiast domyślnego mechanizmu BULKINSERT. Zobacz [Kopiuj numer referencyjny wydajności](data-factory-copy-activity-performance.md#performance-reference) ze szczegółowym porównaniem. Aby zapoznać się z przewodnikiem dotyczącym przypadku użycia, zobacz [ładowanie 1 TB do usługi Azure Synapse Analytics na 15 minut z Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Jeśli dane źródłowe są w **obiekcie blob platformy Azure lub Azure Data Lake Store** , a format jest zgodny z bazą danych, możesz bezpośrednio skopiować do usługi Azure Synapse Analytics przy użyciu bazy danych Base. Aby uzyskać szczegółowe informacje, zobacz **[bezpośrednia kopia przy użyciu bazy Base](#direct-copy-using-polybase)** .
* Jeśli źródłowy magazyn danych i format nie są pierwotnie obsługiwane przez bazę kodu, można użyć zamiast tego funkcji **[kopiowania etapowego](#staged-copy-using-polybase)** . Zapewnia również lepszą przepływność przez automatyczne Konwertowanie danych do formatu zgodnego z podstawą i przechowywanie danych w usłudze Azure Blob Storage. Następnie ładuje dane do usługi Azure Synapse Analytics.

Ustaw `allowPolyBase` Właściwość na **wartość true** , jak pokazano w poniższym przykładzie, aby Azure Data Factory do kopiowania danych do usługi Azure Synapse Analytics. Po ustawieniu allowPolyBase na true, można określić właściwości specyficzne dla bazy za pomocą `polyBaseSettings` grupy właściwości. Zobacz sekcję [SqlDWSink](#sqldwsink) , aby uzyskać szczegółowe informacje na temat właściwości, których można użyć z polyBaseSettings.

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

### <a name="direct-copy-using-polybase"></a>Bezpośrednie kopiowanie przy użyciu bazy
Baza danych Azure Synapse Analytics obsługuje bezpośrednio obsługę obiektów blob platformy Azure i Azure Data Lake Store (przy użyciu nazwy głównej usługi) jako źródła i z określonymi wymaganiami dotyczącymi formatu pliku. Jeśli dane źródłowe spełniają kryteria opisane w tej sekcji, można bezpośrednio skopiować ze źródłowego magazynu danych do usługi Azure Synapse Analytics przy użyciu bazy. W przeciwnym razie można użyć [kopii etapowej przy użyciu bazy](#staged-copy-using-polybase).

> [!TIP]
> Aby wydajnie kopiować dane z Data Lake Store do usługi Azure Synapse Analytics, Dowiedz się więcej z [Azure Data Factory ułatwiają one wgląd w dane przy użyciu Data Lake Store z usługą Azure Synapse Analytics](/archive/blogs/azuredatalake/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse).

Jeśli wymagania nie są spełnione, Azure Data Factory sprawdza ustawienia i automatycznie powraca do mechanizmu BULKINSERT na potrzeby przenoszenia danych.

1. **Źródłowa połączona usługa** jest typu: **AzureStorage** lub **AzureDataLakeStore z uwierzytelnianiem głównym usługi** .
2. **Wejściowy zestaw danych** jest typu: **AzureBlob** lub **AzureDataLakeStore** , a typ formatu w obszarze `type` Właściwości to **OrcFormat** , **ParquetFormat** lub **TextFormat** z następującymi konfiguracjami:

   1. `rowDelimiter` musi być **\n** .
   2. `nullValue` jest ustawiony na **pusty ciąg** ("") lub `treatEmptyAsNull` jest ustawiony na **wartość true** .
   3. `encodingName` jest ustawiony na **UTF-8** , która jest wartością **domyślną** .
   4. `escapeChar`, `quoteChar` , `firstRowAsHeader` i `skipLineCount` nie są określone.
   5. `compression` nie może to być **kompresja** , **gzip** ani **Wklęśnięcie** .

      ```JSON
      "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
      },
      ```

3. Brak ustawienia w `skipHeaderLineCount` obszarze **BlobSource** lub **AzureDataLakeStore** dla działania kopiowania w potoku.
4. Brak ustawienia w `sliceIdentifierColumnName` obszarze **SqlDWSink** dla działania kopiowania w potoku. (Podstawowe gwarancje, że wszystkie dane są aktualizowane lub nic nie jest aktualizowane w jednym przebiegu. Aby osiągnąć **powtarzalność** , można użyć `sqlWriterCleanupScript` .
5. Nie jest `columnMapping` używany w działaniu związanym z kopiowaniem.

### <a name="staged-copy-using-polybase"></a>Przemieszczona kopia przy użyciu bazy
Jeśli dane źródłowe nie spełniają kryteriów wprowadzonych w poprzedniej sekcji, można włączyć kopiowanie danych za pośrednictwem tymczasowego przemieszczania Blob Storage platformy Azure (nie można Premium Storage). W takim przypadku Azure Data Factory automatycznie wykonuje przekształcenia danych w celu spełnienia wymagań dotyczących formatu danych podstawowych, a następnie użyć podstawy do ładowania danych do usługi Azure Synapse Analytics oraz przy ostatnim czyszczeniu danych tymczasowych z magazynu obiektów BLOB. Zobacz [przygotowane kopie](data-factory-copy-activity-performance.md#staged-copy) , aby uzyskać szczegółowe informacje na temat kopiowania danych za pośrednictwem tymczasowego obiektu blob platformy Azure.

> [!NOTE]
> W przypadku kopiowania danych z lokalnego magazynu danych do usługi Azure Synapse Analytics przy użyciu platformy Base i przemieszczania, jeśli wersja bramy Zarządzanie danymi jest niższa niż 2,4, środowisko JRE (Java Runtime Environment) jest wymagane na maszynie bramy, która jest używana do przekształcania danych źródłowych w prawidłowy format. Zasugeruj, aby uaktualnić bramę do najnowszej wersji, aby uniknąć takiej zależności.
>

Aby użyć tej funkcji, Utwórz [połączoną usługę Azure Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service) , która odwołuje się do konta usługi Azure Storage, które ma pośredni magazyn obiektów blob, a następnie określ `enableStaging` `stagingSettings` właściwości i dla działania kopiowania, jak pokazano w poniższym kodzie:

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to Azure Synapse Analytics via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlDwSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob"
        }
    }
}
]
```

## <a name="best-practices-when-using-polybase"></a>Najlepsze rozwiązania w przypadku korzystania z bazy
Poniższe sekcje zawierają dodatkowe najlepsze rozwiązania dotyczące tych, które zostały wymienione w [najlepszych rozwiązaniach dotyczących usługi Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Wymagane uprawnienie do bazy danych
Aby można było korzystać z bazy danych bazowych, [wymaga to,](/sql/relational-databases/security/permissions-database-engine) aby użytkownik użył do załadowania danych do usługi Azure Synapse Analytics. Jednym ze sposobów osiągnięcia tego celu jest dodanie tego użytkownika jako członka roli "db_owner". Dowiedz się, jak to zrobić, wykonując czynności opisane w [tej sekcji](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Ograniczenie rozmiaru wiersza i typu danych
Obciążenia wielopodstawowe są ograniczone do ładowania wierszy mniejszych niż **1 MB** i nie można ładować do VARCHR (max), nvarchar (max) ani varbinary (max). Zobacz [tutaj](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Jeśli masz dane źródłowe z wierszami o rozmiarze większym niż 1 MB, możesz chcieć podzielić tabele źródłowe w pionie na kilka małych, w których największym rozmiarze wierszy każdego z nich nie przekracza limit. Mniejsze tabele mogą następnie zostać załadowane za pomocą bazy Base i scalone w usłudze Azure Synapse Analytics.

### <a name="azure-synapse-analytics-resource-class"></a>Klasa zasobów analizy usługi Azure Synapse
Aby osiągnąć najlepszą możliwą przepływność, należy rozważyć przypisanie większej klasy zasobów do użytkownika używanego do ładowania danych do usługi Azure Synapse Analytics za pośrednictwem bazy. Dowiedz się, jak to zrobić, wykonując następujące czynności: [Zmień przykład klasy zasobów użytkownika](../../synapse-analytics/sql-data-warehouse/resource-classes-for-workload-management.md).

### <a name="tablename-in-azure-synapse-analytics"></a>TableName w usłudze Azure Synapse Analytics
W poniższej tabeli przedstawiono przykłady, jak określić właściwość **TableName** w formacie JSON zestawu danych dla różnych kombinacji schematu i nazwy tabeli.

| Schemat bazy danych | Nazwa tabeli | TableName JSON — Właściwość |
| --- | --- | --- |
| dbo |MyTable |MyTable lub dbo. MyTable lub [dbo]. MyTable |
| dbo1 |MyTable |dbo1. MyTable lub [dbo1]. MyTable |
| dbo |My. Table |[My. Table] lub [dbo]. [My. Table] |
| dbo1 |My. Table |[dbo1]. [My. Table] |

Jeśli zostanie wyświetlony następujący błąd, może to być problem z wartością określoną dla właściwości TableName. W tabeli podano prawidłowy sposób określania wartości właściwości TableName JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Kolumny z wartościami domyślnymi
Obecnie funkcja klasy bazowej w Data Factory akceptuje tylko taką samą liczbę kolumn jak w tabeli docelowej. Załóżmy, że masz tabelę z czterema kolumnami, a jedna z nich jest zdefiniowana z wartością domyślną. Dane wejściowe powinny nadal zawierać cztery kolumny. Dostarczanie zestawu danych wejściowych 3-kolumnowego spowoduje zwrócenie błędu podobnego do następującego komunikatu:

```
All columns of the table must be specified in the INSERT BULK statement.
```
Wartość NULL jest specjalną formą wartości domyślnej. Jeśli kolumna dopuszcza wartość null, dane wejściowe (w obiekcie BLOB) dla tej kolumny mogą być puste (nie można ich odnaleźć w wejściowym zestawie danych). Baza bazowych wstawia wartość NULL dla nich w usłudze Azure Synapse Analytics.

## <a name="auto-table-creation"></a>Tworzenie tabeli Autotekstu
Jeśli używasz Kreatora kopiowania do kopiowania danych z programu SQL Server lub Azure SQL Database do usługi Azure Synapse Analytics, a tabela odpowiadająca tabeli źródłowej nie istnieje w magazynie docelowym, Data Factory może automatycznie utworzyć tabelę w magazynie danych przy użyciu schematu tabeli źródłowej.

Data Factory tworzy tabelę w magazynie docelowym z tą samą nazwą tabeli w źródłowym magazynie danych. Typy danych dla kolumn są wybierane na podstawie następującego mapowania typu. W razie potrzeby wykonuje konwersje typów, aby naprawić wszelkie niezgodności między magazynami źródłowymi i docelowymi. Używa również rozkładu tabeli "Round Robin".

| Typ kolumny SQL Database źródłowej | Docelowy typ kolumny analizy usługi Azure Synapse (ograniczenie rozmiaru) |
| --- | --- |
| int | int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| Bit | Bit |
| Liczba dziesiętna | Liczba dziesiętna |
| Numeryczne | Liczba dziesiętna |
| Float | Float |
| Pieniądze | Pieniądze |
| Rzeczywiste | Rzeczywiste |
| SmallMoney | SmallMoney |
| Binarne | Binarne |
| Liczby | Varbinary (do 8000) |
| Date | Date |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Godzina | Godzina |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Tekst | Varchar (do 8000) |
| NText | NVarChar (do 4000) |
| Image (Obraz) | VarBinary (do 8000) |
| UniqueIdentifier | UniqueIdentifier |
| Char | Char |
| NChar | NChar |
| VarChar | VarChar (do 8000) |
| NVarChar | NVarChar (do 4000) |
| Xml | Varchar (do 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-synapse-analytics"></a>Mapowanie typu dla usługi Azure Synapse Analytics
Jak wspomniano w artykule [działania związane z przenoszeniem danych](data-factory-data-movement-activities.md) , działanie kopiowania wykonuje konwersje typów automatycznych z typów źródłowych na typy obiektów ujścia o następujących dwuetapowych podejściach:

1. Konwertuj z natywnych typów źródła na typ .NET
2. Konwertuj z typu .NET na natywny typ ujścia

Podczas przeniesienia danych do & z usługi Azure Synapse Analytics następujące mapowania są używane z typu SQL do typu .NET i na odwrót.

Mapowanie jest takie samo jak [Mapowanie typu danych SQL Server ADO.NET](/dotnet/framework/data/adonet/sql-server-data-type-mappings).

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

Można również mapować kolumny ze źródłowego zestawu danych do kolumn z zestawu danych ujścia w definicji działania kopiowania. Aby uzyskać szczegółowe informacje, zobacz [Mapowanie kolumn zestawu danych w Azure Data Factory](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-azure-synapse-analytics"></a>Przykłady JSON kopiowania danych do i z usługi Azure Synapse Analytics
W poniższych przykładach przedstawiono przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Pokazują one sposób kopiowania danych do i z usługi Azure Synapse Analytics i platformy Azure Blob Storage. Można jednak skopiować dane **bezpośrednio** z dowolnego źródła do dowolnego ujścia w [tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w Azure Data Factory.

### <a name="example-copy-data-from-azure-synapse-analytics-to-azure-blob"></a>Przykład: kopiowanie danych z usługi Azure Synapse Analytics do obiektu blob platformy Azure
Przykład definiuje następujące jednostki Data Factory:

1. Połączona usługa typu [AzureSqlDW](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureSqlDWTable](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [SqlDWSource](#copy-activity-properties) i [wartość blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykład kopiuje dane w postaci szeregów czasowych (co godzinę, codziennie itd.) z tabeli w usłudze Azure Synapse Analytics Database do obiektu BLOB co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

**Połączona usługa Azure Synapse Analytics:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
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
**Zestaw danych wejściowych analizy usługi Azure Synapse:**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w usłudze Azure Synapse Analytics i zawiera kolumnę o nazwie "timestampcolumn" dla danych szeregów czasowych.

Ustawienie "External": "true" informuje usługę Data Factory, że zestaw danych jest zewnętrzny względem fabryki danych i nie jest tworzony przez działanie w fabryce danych.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
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

**Działanie Copy w potoku z SqlDWSource i wartość blobsink:**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **SqlDWSource** , a typ **ujścia** to **wartość blobsink** . Zapytanie SQL określone dla właściwości **SqlReaderQuery** wybiera dane w ciągu ostatniej godziny do skopiowania.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
> [!NOTE]
> W przykładzie **sqlReaderQuery** jest określony dla SqlDWSource. Działanie kopiowania uruchamia to zapytanie względem źródła analizy usługi Azure Synapse, aby uzyskać dane.
>
> Alternatywnie można określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (Jeśli procedura składowana pobiera parametry).
>
> Jeśli nie określisz opcji sqlReaderQuery ani sqlReaderStoredProcedureName, kolumny zdefiniowane w sekcji Struktura JSON zestawu danych są używane do tworzenia zapytania (wybierz Kolumna1, Kolumna2 z MyTable) do uruchamiania w usłudze Azure Synapse Analytics. Jeśli definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-synapse-analytics"></a>Przykład: kopiowanie danych z usługi Azure BLOB do usługi Azure Synapse Analytics
Przykład definiuje następujące jednostki Data Factory:

1. Połączona usługa typu [AzureSqlDW](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureSqlDWTable](#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania korzystającym z [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [SqlDWSink](#copy-activity-properties).

Przykład kopiuje dane szeregów czasowych (co godzinę, codziennie itd.) z usługi Azure BLOB do tabeli w bazie danych usługi Azure Synapse Analytics co godzinę. Właściwości JSON używane w tych przykładach są opisane w sekcjach poniżej przykładów.

**Połączona usługa Azure Synapse Analytics:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
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
**Zestaw danych wejściowych obiektów blob platformy Azure:**

Dane są pobierane z nowego obiektu BLOB co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu i nazwa pliku obiektu BLOB są dynamicznie oceniane na podstawie czasu rozpoczęcia przetwarzanego wycinka. Ścieżka folderu używa części Year, month i Day czasu rozpoczęcia i nazwy pliku używa części godziny godziny rozpoczęcia. ustawienie "External": "true" informuje usługę Data Factory, że ta tabela jest zewnętrzna z fabryką danych i nie jest generowana przez działanie w fabryce danych.

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
**Wyjściowy zestaw danych usługi Azure Synapse Analytics:**

Przykład kopiuje dane do tabeli o nazwie "MyTable" w usłudze Azure Synapse Analytics. Utwórz tabelę w usłudze Azure Synapse Analytics przy użyciu takiej samej liczby kolumn, jak oczekuje się, że plik CSV obiektów BLOB zawiera. Nowe wiersze są dodawane do tabeli co godzinę.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
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
**Działanie Copy w potoku z BlobSource i SqlDWSink:**

Potok zawiera działanie kopiowania, które jest skonfigurowane do korzystania z wejściowych i wyjściowych zestawów danych i zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** ma wartość **BlobSource** , a typ **ujścia** to **SqlDWSink** .

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
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
Aby zapoznać się z przewodnikiem, zobacz artykuł [Załaduj 1 TB do usługi Azure Synapse Analytics na 15 minut z Azure Data Factory](data-factory-load-sql-data-warehouse.md) i [Załaduj dane przy użyciu Azure Data Factory](../load-azure-sql-data-warehouse.md) artykułu w dokumentacji usługi Azure Synapse Analytics.

## <a name="performance-and-tuning"></a>Wydajność i dostrajanie
Zobacz [Przewodnik dostrajania wydajności & działania kopiowania](data-factory-copy-activity-performance.md) , aby poznać kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w Azure Data Factory i różne sposoby jego optymalizacji.