---
title: Kopiowanie danych do/z usługi Azure SQL Data Warehouse
description: Dowiedz się, jak kopiować dane do/z usługi Azure SQL Data Warehouse przy użyciu usługi Azure Data Factory
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
ms.openlocfilehash: 4335763269f4a39b4893d9022f4789296b178e92
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419327"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Kopiowanie danych do i z usługi Azure SQL Data Warehouse przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [łącznik usługi Azure SQL Data Warehouse w wersji 2](../connector-azure-sql-data-warehouse.md).

W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych do/z usługi Azure SQL Data Warehouse. Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) który przedstawia ogólny przegląd przenoszenia danych z działaniem kopiowania.

> [!TIP]
> Aby osiągnąć najlepszą wydajność, użyj PolyBase, aby załadować dane do usługi Azure SQL Data Warehouse. [Sekcja Użyj polybase do ładowania danych do](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) usługi Azure SQL Data Warehouse sekcja zawiera szczegółowe informacje. Aby uzyskać instruktaż z przypadkiem użycia, zobacz [Ładowanie 1 TB do usługi Azure SQL Data Warehouse w ramach 15 minut z usługi Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Można skopiować dane **z usługi Azure SQL Data Warehouse** do następujących magazynów danych:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Dane z następujących magazynów danych można **skopiować do usługi Azure SQL Data Warehouse:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Podczas kopiowania danych z programu SQL Server lub usługi Azure SQL Database do usługi Azure SQL Data Warehouse, jeśli tabela nie istnieje w magazynie docelowym, usługa Data Factory może automatycznie utworzyć tabelę w magazynie danych SQL przy użyciu schematu tabeli w źródłowym magazynie danych. Szczegółowe informacje można znaleźć w części [Automatyczne tworzenie tabeli.](#auto-table-creation)

## <a name="supported-authentication-type"></a>Typ obsługiwanego uwierzytelniania
Łącznik usługi Azure SQL Data Warehouse obsługuje uwierzytelnianie podstawowe.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania, który przenosi dane do/z usługi Azure SQL Data Warehouse przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku, który kopiuje dane do/z usługi Azure SQL Data Warehouse jest użycie kreatora kopiowania danych. Zobacz [Samouczek: Ładowanie danych do magazynu danych SQL z fabryką danych](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) w celu szybkiego instruktażu w sprawie tworzenia potoku przy użyciu kreatora kopiowania danych.

Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania.

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **fabryki danych**. Fabryka danych może zawierać jeden lub więcej potoków. 
2. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych. Na przykład w przypadku kopiowania danych z magazynu obiektów blob platformy Azure do magazynu danych SQL platformy Azure utworzysz dwie połączone usługi, aby połączyć konto usługi Azure storage i magazyn danych SQL platformy Azure z fabryką danych. Właściwości usługi połączone, które są specyficzne dla usługi Azure SQL Data Warehouse, zobacz [sekcję właściwości usługi połączone.](#linked-service-properties) 
3. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania. W przykładzie wymienionym w ostatnim kroku utworzysz zestaw danych, aby określić kontener obiektu blob i folder zawierający dane wejściowe. I utworzyć inny zestaw danych, aby określić tabelę w magazynie danych SQL platformy Azure, który przechowuje dane skopiowane z magazynu obiektów blob. Właściwości zestawu danych, które są specyficzne dla usługi Azure SQL Data Warehouse, zobacz [sekcję właściwości zestawu danych.](#dataset-properties)
4. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. W przykładzie wspomniano wcześniej, należy użyć BlobSource jako źródło i SqlDWSink jako ujście dla działania kopiowania. Podobnie, jeśli kopiujesz z usługi Azure SQL Data Warehouse do usługi Azure Blob Storage, używasz SqlDWSource i BlobSink w działania kopiowania. Aby uzyskać właściwości działania kopiowania, które są specyficzne dla usługi Azure SQL Data Warehouse, zobacz [sekcję właściwości działania kopiowania.](#copy-activity-properties) Aby uzyskać szczegółowe informacje na temat używania magazynu danych jako źródła lub ujścia, kliknij łącze w poprzedniej sekcji dla magazynu danych.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON. W przypadku przykładów z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych do/z usługi Azure SQL Data Warehouse, zobacz [sekcję przykładów JSON](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) w tym artykule.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory specyficznych dla usługi Azure SQL Data Warehouse:

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Poniższa tabela zawiera opis elementów JSON specyficznych dla usługi połączonej usługi Azure SQL Data Warehouse.

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| type |Właściwość typu musi być ustawiona na: **AzureSqlDW** |Tak |
| Parametry połączenia |Określ informacje potrzebne do nawiązania połączenia z wystąpieniem usługi Azure SQL Data Warehouse dla właściwości connectionString. Obsługiwane jest tylko uwierzytelnianie podstawowe. |Tak |

> [!IMPORTANT]
> Skonfiguruj [Zaporę bazy danych SQL platformy Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) i serwer bazy danych, aby umożliwić [usługom platformy Azure dostęp do serwera](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Ponadto w przypadku kopiowania danych do usługi Azure SQL Data Warehouse spoza platformy Azure, w tym z lokalnych źródeł danych z bramą fabryki danych, skonfiguruj odpowiedni zakres adresów IP dla komputera, który wysyła dane do usługi Azure SQL Data Warehouse.

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcji & właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Tworzenie zestawów danych.](data-factory-create-datasets.md) Sekcje, takie jak struktura, dostępność i zasady zestawu danych JSON są podobne dla wszystkich typów zestawów danych (Azure SQL, Azure blob, tabela platformy Azure itp.).

Sekcja typeProperties jest inna dla każdego typu zestawu danych i zawiera informacje o lokalizacji danych w magazynie danych. Sekcja **typeProperties** dla zestawu danych typu **AzureSqlDWTable** ma następujące właściwości:

| Właściwość | Opis | Wymagany |
| --- | --- | --- |
| tableName |Nazwa tabeli lub widoku w bazie danych usługi Azure SQL Data Warehouse, do których odwołuje się usługa połączona. |Tak |

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań.

> [!NOTE]
> Działanie kopiowania zajmuje tylko jedno dane wejściowe i generuje tylko jedno dane wyjściowe.

Mając na uwadze, właściwości dostępne w sekcji typeProperties działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze.

### <a name="sqldwsource"></a>Źródło sqldw
Gdy źródło jest typu **SqlDWSource,** następujące właściwości są dostępne w sekcji **typeProperties:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| sqlReaderQuery |Użyj kwerendy niestandardowej, aby odczytać dane. |Ciąg zapytania SQL. Na przykład: wybierz * z MyTable. |Nie |
| nazwa sqlReaderStoredProcedureName |Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. |Nazwa procedury składowanej. Ostatnia instrukcja SQL musi być instrukcją SELECT w procedurze składowanej. |Nie |
| przechowywaneParametryprocedure |Parametry procedury składowanej. |Pary nazwy/wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |

Jeśli **sqlReaderQuery** jest określony dla SqlDWSource, copy activity uruchamia tę kwerendę względem źródła usługi Azure SQL Data Warehouse, aby uzyskać dane.

Alternatywnie można określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli procedura składowana przyjmuje parametry).

Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName, kolumny zdefiniowane w sekcji struktury zestawu danych JSON są używane do tworzenia kwerendy do uruchomienia względem usługi Azure SQL Data Warehouse. Przykład: `select column1, column2 from mytable`. Jeśli definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.

#### <a name="sqldwsource-example"></a>Przykład usługi SqlDWSource

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

### <a name="sqldwsink"></a>SqlDWSink (100)
**SqlDWSink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Określ kwerendę dla działania kopiowania, aby wykonać takie, że dane określonego plasterka są czyszczone. Aby uzyskać szczegółowe informacje, zobacz [sekcję powtarzalność](#repeatability-during-copy). |Instrukcja kwerendy. |Nie |
| allowPolyBase |Wskazuje, czy ma być używany PolyBase (w stosownych przypadkach) zamiast mechanizmu BULKINSERT. <br/><br/> **Za pomocą PolyBase jest zalecanym sposobem ładowania danych do magazynu danych SQL.** Aby uzyskać ograniczenia i szczegóły, zobacz [Korzystanie z bazy danych PolyBase w](#use-polybase-to-load-data-into-azure-sql-data-warehouse) celu załadowania danych do sekcji usługi Azure SQL Data Warehouse. |True <br/>False (domyślnie) |Nie |
| lyBaseSettings (190) |Grupa właściwości, które można określić, gdy właściwość **allowPolybase** jest ustawiona na **true**. |&nbsp; |Nie |
| odrzucanieValue |Określa liczbę lub procent wierszy, które można odrzucić, zanim kwerenda zakończy się niepowodzeniem. <br/><br/>Dowiedz się więcej o opcjach odrzucania bazy PolyBase w sekcji **Argumenty** w temacie [TWORZENIE TABELI ZEWNĘTRZNEJ (Transact-SQL).](https://msdn.microsoft.com/library/dn935021.aspx) |0 (domyślnie), 1, 2, ... |Nie |
| rejectType |Określa, czy opcja rejectValue jest określona jako wartość literału czy wartość procentowa. |Wartość (domyślna), Wartość procentowa |Nie |
| odrzucanawłaksza wartość |Określa liczbę wierszy do pobrania, zanim PolyBase ponownie obliczy procent odrzuconych wierszy. |1, 2, ... |Tak, jeśli **rejectType** jest **wartością procentową** |
| użyjTypeDefault |Określa sposób obsługi brakujących wartości w rozdzielonych plikach tekstowych, gdy PolyBase pobiera dane z pliku tekstowego.<br/><br/>Dowiedz się więcej o tej właściwości z sekcji Argumenty w [programie CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |Prawda, fałsz (domyślnie) |Nie |
| writeBatchSize |Wstawia dane do tabeli SQL, gdy rozmiar buforu osiągnie writeBatchSize |Liczba całkowita (liczba wierszy) |Nie (domyślnie: 10000) |
| writeBatchTimeout |Czas oczekiwania na zakończenie operacji wstawiania partii przed jej limitem czasu. |Timespan<br/><br/> Przykład: "00:30:00" (30 minut). |Nie |

#### <a name="sqldwsink-example"></a>Przykład SqlDWSink

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Ładowanie danych do usługi Azure SQL Data Warehouse za pomocą bazy danych PolyBase
Korzystanie z **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** jest skutecznym sposobem ładowania dużej ilości danych do usługi Azure SQL Data Warehouse o wysokiej przepływności. Można zobaczyć duży zysk w przepływności przy użyciu PolyBase zamiast domyślnego mechanizmu BULKINSERT. Zobacz [numer referencyjny wydajności kopiowania](data-factory-copy-activity-performance.md#performance-reference) ze szczegółowym porównaniem. Aby uzyskać instruktaż z przypadkiem użycia, zobacz [Ładowanie 1 TB do usługi Azure SQL Data Warehouse w ramach 15 minut z usługi Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Jeśli dane źródłowe znajdują się w **usłudze Azure Blob lub Azure Data Lake Store**, a format jest zgodny z PolyBase, można bezpośrednio skopiować do usługi Azure SQL Data Warehouse przy użyciu polybase. Zobacz **[Kopiowanie bezpośrednie przy użyciu PolyBase](#direct-copy-using-polybase)** ze szczegółami.
* Jeśli źródłowy magazyn danych i format nie są pierwotnie obsługiwane przez PolyBase, można użyć funkcji Kopia etapowa przy użyciu funkcji **[PolyBase.](#staged-copy-using-polybase)** Zapewnia również lepszą przepływność, automatycznie konwertując dane do formatu zgodnego z PolyBase i przechowując dane w magazynie obiektów Blob platformy Azure. Następnie ładuje dane do magazynu danych SQL.

Ustaw `allowPolyBase` właściwość na **true,** jak pokazano w poniższym przykładzie dla usługi Azure Data Factory do kopiowania danych do usługi Azure SQL Data Warehouse. Po ustawieniu allowPolyBase do true, można określić PolyBase określonych właściwości przy użyciu grupy `polyBaseSettings` właściwości. Zobacz [sekcję SqlDWSink, aby](#sqldwsink) uzyskać szczegółowe informacje na temat właściwości, których można używać z polyBaseSettings.

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

### <a name="direct-copy-using-polybase"></a>Kopiowanie bezpośrednie przy użyciu polybase
PolyBase magazynu danych SQL bezpośrednio obsługuje usługi Azure Blob i Azure Data Lake Store (przy użyciu jednostki usługi) jako źródło i z określonymi wymaganiami dotyczącymi formatu pliku. Jeśli dane źródłowe spełniają kryteria opisane w tej sekcji, można bezpośrednio skopiować ze źródłowego magazynu danych do usługi Azure SQL Data Warehouse przy użyciu polybase. W przeciwnym razie można użyć [kopii etapowej przy użyciu polybase](#staged-copy-using-polybase).

> [!TIP]
> Aby wydajnie kopiować dane z magazynu Data Lake Store do magazynu danych SQL, dowiedz się więcej z [usługi Azure Data Factory, dzięki czemu jeszcze łatwiej i wygodnie jest odkryć informacje z danych podczas korzystania z usługi Data Lake Store z magazynem danych SQL.](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)

Jeśli wymagania nie są spełnione, usługa Azure Data Factory sprawdza ustawienia i automatycznie przechodzi z powrotem do mechanizmu BULKINSERT dla przenoszenia danych.

1. **Usługa połączona ze źródłem** jest typu: **AzureStorage** lub **AzureDataLakeStore z uwierzytelnianiem jednostkowym usługi.**
2. **Wejściowy zestaw danych** jest typu: **AzureBlob** lub **AzureDataLakeStore**, a typ formatu w właściwościach `type` jest **OrcFormat**, **ParquetFormat**lub **TextFormat** z następującymi konfiguracjami:

   1. `rowDelimiter`musi być **\n**.
   2. `nullValue`jest **ustawiona** na pusty `treatEmptyAsNull` ciąg (""), lub jest ustawiona na **true**.
   3. `encodingName`jest ustawiona na **utf-8**, co jest **wartością domyślną.**
   4. `escapeChar`, `quoteChar` `firstRowAsHeader`, `skipLineCount` i nie są określone.
   5. `compression`może być **bez kompresji,** **GZip**lub **Deflate**.

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

3. Nie ma `skipHeaderLineCount` żadnych ustawień w obszarze **BlobSource** lub **AzureDataLakeStore** dla działania kopiowania w potoku.
4. W obszarze `sliceIdentifierColumnName` **SqlDWSink** nie ma żadnych ustawień dla działania kopiowania w potoku. (PolyBase gwarantuje, że wszystkie dane są aktualizowane lub nic nie jest aktualizowany w jednym uruchomieniu. Aby osiągnąć **powtarzalność,** można użyć `sqlWriterCleanupScript`).
5. Nie jest `columnMapping` używany w skojarzone w Copy działania.

### <a name="staged-copy-using-polybase"></a>Kopia etapowa przy użyciu bazy PolyBase
Jeśli dane źródłowe nie spełniają kryteriów wprowadzonych w poprzedniej sekcji, można włączyć kopiowanie danych za pośrednictwem tymczasowej tymczasowej usługi Azure Blob Storage (nie może być magazynem w wersji Premium). W takim przypadku usługa Azure Data Factory automatycznie wykonuje przekształcenia danych w celu spełnienia wymagań dotyczących formatu danych polybase, a następnie użyj PolyBase do załadowania danych do magazynu SQL Data, a w końcu czyści dane tymczasowe z magazynu obiektów Blob. Zobacz [kopia etapowa, aby](data-factory-copy-activity-performance.md#staged-copy) uzyskać szczegółowe informacje na temat kopiowania danych za pośrednictwem przemieszczania usługi Azure Blob działa w ogóle.

> [!NOTE]
> Podczas kopiowania danych z lokalnego magazynu danych do usługi Azure SQL Data Warehouse przy użyciu polybase i przemieszczania, jeśli wersja bramy zarządzania danymi jest poniżej 2.4, środowisko JRE (Java Runtime Environment) jest wymagane na komputerze bramy, który jest używany do przekształcania danych źródłowych do odpowiedniego formatu. Zasugeruj uaktualnienie bramy do najnowszej, aby uniknąć takiej zależności.
>

Aby użyć tej funkcji, należy utworzyć [usługę połączone usługi Azure Storage,](data-factory-azure-blob-connector.md#azure-storage-linked-service) która odwołuje `enableStaging` się `stagingSettings` do konta usługi Azure Storage, która ma tymczasowy magazyn obiektów blob, a następnie określ właściwości i właściwości działania kopiowania, jak pokazano w poniższym kodzie:

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
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

## <a name="best-practices-when-using-polybase"></a>Najważniejsze wskazówki dotyczące korzystania z bazy PolyBase
W poniższych sekcjach przedstawiono dodatkowe najlepsze rozwiązania dotyczące tych, które są wymienione w [najlepszych rozwiązaniach dotyczących usługi Azure SQL Data Warehouse.](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md)

### <a name="required-database-permission"></a>Wymagane uprawnienie do bazy danych
Aby użyć PolyBase, wymaga użytkownika używanego do ładowania danych do usługi SQL Data Warehouse ma [uprawnienie "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) w docelowej bazie danych. Jednym ze sposobów osiągnięcia tego celu jest dodanie tego użytkownika jako członka roli "db_owner". Dowiedz się, jak to zrobić, wykonując [tę sekcję](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Ograniczenie rozmiaru wiersza i typu danych
Obciążenia polybase są ograniczone do wierszy ładowania mniejszych niż **1 MB** i nie można załadować do VARCHR(MAX), NVARCHAR(MAX) lub VARBINARY(MAX). Patrz [tutaj](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Jeśli masz dane źródłowe o rozmiarze większym niż 1 MB, możesz podzielić tabele źródłowe pionowo na kilka małych, gdzie największy rozmiar wiersza każdego z nich nie przekracza limitu. Mniejsze tabele można następnie załadować przy użyciu PolyBase i scalone razem w usłudze Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Klasa zasobów magazynu danych SQL
Aby osiągnąć najlepszą możliwą przepływność, należy rozważyć przypisanie większej klasy zasobów do użytkownika używanego do ładowania danych do magazynu danych SQL za pośrednictwem PolyBase. Dowiedz się, jak to zrobić, wykonując następujące [zmiany przykładu klasy zasobów użytkownika](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).

### <a name="tablename-in-azure-sql-data-warehouse"></a>nazwa tabeli w magazynie danych SQL usługi Azure
W poniższej tabeli przedstawiono przykłady określania właściwości **tableName** w zestawie danych JSON dla różnych kombinacji schematu i nazwy tabeli.

| Schemat bazy danych | Nazwa tabeli | tableName JSON właściwość |
| --- | --- | --- |
| Dbo |Mytable |MyTable lub dbo. MyTable lub [dbo]. [MyTable] |
| dbo1 |Mytable |dbo1. MyTable lub [dbo1]. [MyTable] |
| Dbo |My.Tabela |[My.Table] lub [dbo]. [Moja.Tabela] |
| dbo1 |My.Tabela |[dbo1]. [Moja.Tabela] |

Jeśli zostanie wyświetlony następujący błąd, może to być problem z wartością określoną dla właściwości tableName. Zobacz tabelę, aby uzyskać poprawny sposób określania wartości właściwości tableName JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Kolumny z wartościami domyślnymi
Obecnie funkcja PolyBase w fabryce danych akceptuje tylko taką samą liczbę kolumn, jak w tabeli docelowej. Załóżmy, że masz tabelę z czterema kolumnami, a jedna z nich jest zdefiniowana z wartością domyślną. Dane wejściowe powinny nadal zawierać cztery kolumny. Podanie 3-kolumnowego zestawu danych wejściowych spowoduje błąd podobny do następującego komunikatu:

```
All columns of the table must be specified in the INSERT BULK statement.
```
Wartość NULL jest specjalną formą wartości domyślnej. Jeśli kolumna jest nullable, dane wejściowe (w obiekcie blob) dla tej kolumny może być pusty (nie można zabraknąć w zestawie danych wejściowych). PolyBase wstawia null dla nich w usłudze Azure SQL Data Warehouse.

## <a name="auto-table-creation"></a>Automatyczne tworzenie tabeli
Jeśli używasz Kreatora kopiowania do kopiowania danych z programu SQL Server lub usługi Azure SQL Database do usługi Azure SQL Data Warehouse, a tabela odpowiadająca tabeli źródłowej nie istnieje w magazynie docelowym, usługa Data Factory może automatycznie utworzyć tabelę w magazynie danych przy użyciu schematu tabeli źródłowej.

Usługa Data Factory tworzy tabelę w magazynie docelowym o tej samej nazwie tabeli w źródłowym magazynie danych. Typy danych dla kolumn są wybierane na podstawie następującego mapowania typów. W razie potrzeby wykonuje konwersje typu, aby naprawić wszelkie niezgodności między magazynami źródłowymi i docelowymi. Używa również dystrybucji tabeli okrężnych.

| Typ kolumny Źródłowa baza danych SQL | Docelowy typ kolumny SQL DW (ograniczenie rozmiaru) |
| --- | --- |
| int | int |
| Bigint | Bigint |
| Smallint | Smallint |
| Tinyint | Tinyint |
| Bitowych | Bitowych |
| Wartość dziesiętna | Wartość dziesiętna |
| Liczbowe | Wartość dziesiętna |
| Liczba zmiennoprzecinkowa | Liczba zmiennoprzecinkowa |
| Pieniądze | Pieniądze |
| Rzeczywiste | Rzeczywiste |
| Smallmoney | Smallmoney |
| plików binarnych | plików binarnych |
| Varbinary | Varbinary (do 8000) |
| Date | Date |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Time | Time |
| Datetimeoffset | Datetimeoffset |
| Smalldatetime | Smalldatetime |
| Tekst | Varchar (do 8000) |
| Ntext | NVarChar (do 4000) |
| Image (Obraz) | VarBinary (do 8000) |
| Uniqueidentifier | Uniqueidentifier |
| Char | Char |
| Nchar | Nchar |
| Varchar | VarChar (do 8000) |
| Nvarchar | NVarChar (do 4000) |
| Xml | Varchar (do 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Mapowanie typów dla usługi Azure SQL Data Warehouse
Jak wspomniano w artykule działania związane z [przenoszeniem danych,](data-factory-data-movement-activities.md) działanie kopiowania wykonuje automatyczne konwersje typów z typów źródłowych do typów ujścia z następującym podejściem dwuetapowym:

1. Konwertowanie z natywnych typów źródeł na typ .NET
2. Konwertowanie z typu .NET na natywny typ ujścia

Podczas przenoszenia danych do & z usługi Azure SQL Data Warehouse, następujące mapowania są używane z typu SQL do typu .NET i odwrotnie.

Mapowanie jest takie samo jak [mapowanie typów danych programu SQL Server dla ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Typ aparatu bazy danych programu SQL Server | Typ programu .NET Framework |
| --- | --- |
| bigint |Int64 |
| binarny |Bajt[] |
| bit |Wartość logiczna |
| char |Ciąg, Char[] |
| date |DateTime |
| Datetime (data/godzina) |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Wartość dziesiętna |Wartość dziesiętna |
| Atrybut FILESTREAM (varbinary(max)) |Bajt[] |
| Liczba zmiennoprzecinkowa |Double |
| image |Bajt[] |
| int |Int32 |
| pieniędzy |Wartość dziesiętna |
| nchar |Ciąg, Char[] |
| Ntext |Ciąg, Char[] |
| numeryczne |Wartość dziesiętna |
| nvarchar |Ciąg, Char[] |
| rzeczywiste |Single |
| Rowversion |Bajt[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Wartość dziesiętna |
| Sql_variant |Obiekt * |
| tekst |Ciąg, Char[] |
| time |przedział_czasu |
| sygnatura czasowa |Bajt[] |
| tinyint |Byte |
| uniqueidentifier |Guid (identyfikator GUID) |
| varbinary |Bajt[] |
| varchar |Ciąg, Char[] |
| xml |Xml |

Można również mapować kolumny z źródłowego zestawu danych na kolumny z zestawu danych ujścia w definicji działania kopiowania. Aby uzyskać szczegółowe informacje, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>Przykłady JSON do kopiowania danych do i z magazynu SQL Data Warehouse
Poniższe przykłady zawierają przykładowe definicje JSON, których można użyć do utworzenia potoku przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Pokazują one, jak skopiować dane do i z usługi Azure SQL Data Warehouse i usługi Azure Blob Storage. Jednak dane mogą być kopiowane **bezpośrednio** z dowolnego źródła do dowolnego ujścia podane [w tym miejscu](data-factory-data-movement-activities.md#supported-data-stores-and-formats) przy użyciu działania kopiowania w usłudze Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Przykład: Kopiowanie danych z usługi Azure SQL Data Warehouse do obiektu Blob platformy Azure
Przykład definiuje następujące jednostki fabryki danych:

1. Połączona usługa typu [AzureSqlDW](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureSqlDWTable](#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [sqldwsource](#copy-activity-properties) i [blobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Przykładowy kopiuje szeregi czasowe (co godzinę, codziennie itp.) dane z tabeli w bazie danych usługi Azure SQL Data Warehouse do obiektu blob co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

**Usługa połączona z usługą Azure SQL Data Warehouse:**

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
**Usługa połączona z magazynem obiektów Blob platformy Azure:**

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
**Zestaw danych wejściowych usługi Azure SQL Data Warehouse:**

W przykładzie przyjęto założenie, że utworzono tabelę "MyTable" w usłudze Azure SQL Data Warehouse i zawiera kolumnę o nazwie "timestampcolumn" dla danych szeregów czasowych.

Ustawienie "zewnętrzne": "true" informuje usługę Data Factory, że zestaw danych jest zewnętrzny dla fabryki danych i nie jest produkowany przez działanie w fabryce danych.

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
**Zestaw danych wyjściowych obiektów blob platformy Azure:**

Dane są zapisywane w nowym obiekcie blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu obiektu blob jest dynamicznie oceniana na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części czasu rozpoczęcia z roku, miesiąca, dnia i godziny.

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

**Kopiowanie działania w potoku za pomocą sqldwsource i blobSink:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **SqlDWSource** i typ **ujścia** jest ustawiony na **BlobSink**. Kwerenda SQL określona dla właściwości **SqlReaderQuery** wybiera dane w ciągu ostatniej godziny do skopiowania.

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
> W przykładzie **sqlReaderQuery** jest określony dla SqlDWSource. Działanie kopiowania uruchamia tę kwerendę względem źródła usługi Azure SQL Data Warehouse, aby uzyskać dane.
>
> Alternatywnie można określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** (jeśli procedura składowana przyjmuje parametry).
>
> Jeśli nie określisz sqlReaderQuery lub sqlReaderStoredProcedureName, kolumny zdefiniowane w sekcji struktury zestawu danych JSON są używane do tworzenia kwerendy (wybierz kolumnę1, kolumna2 z mytable) do uruchomienia względem usługi Azure SQL Data Warehouse. Jeśli definicja zestawu danych nie ma struktury, wszystkie kolumny są wybierane z tabeli.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Przykład: Kopiowanie danych z obiektu Blob platformy Azure do usługi Azure SQL Data Warehouse
Przykład definiuje następujące jednostki fabryki danych:

1. Połączona usługa typu [AzureSqlDW](#linked-service-properties).
2. Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureSqlDWTable](#dataset-properties).
5. [Potok](data-factory-create-pipelines.md) z działaniem Kopiowania, który używa [obiektów BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [SqlDWSink](#copy-activity-properties).

Przykładowy kopiuje dane szeregów czasowych (co godzinę, codziennie itp.) z obiektu blob platformy Azure do tabeli w bazie danych usługi Azure SQL Data Warehouse co godzinę. Właściwości JSON używane w tych próbkach są opisane w sekcjach następujących po próbkach.

**Usługa połączona z usługą Azure SQL Data Warehouse:**

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
**Usługa połączona z magazynem obiektów Blob platformy Azure:**

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
**Zestaw danych wejściowych obiektów Blob platformy Azure:**

Dane są pobierane z nowego obiektu blob co godzinę (częstotliwość: godzina, interwał: 1). Ścieżka folderu i nazwa pliku obiektu blob są dynamicznie oceniane na podstawie czasu rozpoczęcia wycinka, który jest przetwarzany. Ścieżka folderu używa części roku, miesiąca i dnia czasu rozpoczęcia i nazwy pliku, która używa części godzinowej godziny rozpoczęcia. "external": ustawienie "true" informuje usługę Data Factory, że ta tabela jest zewnętrzna dla fabryki danych i nie jest wytwarzana przez działanie w fabryce danych.

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
**Zestaw danych wyjściowych usługi Azure SQL Data Warehouse:**

Przykładowy kopie dane do tabeli o nazwie "MyTable" w usłudze Azure SQL Data Warehouse. Utwórz tabelę w usłudze Azure SQL Data Warehouse z taką samą liczbą kolumn, jak oczekujesz, że plik CSV obiektów blob będzie zawierać. Nowe wiersze są dodawane do tabeli co godzinę.

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
**Kopiowanie działania w potoku za pomocą obiektów BlobSource i SqlDWSink:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do korzystania z wejściowych i wyjściowych zestawów danych i jest zaplanowane do uruchomienia co godzinę. W definicji JSON potoku typ **źródła** jest ustawiony na **BlobSource** i typ **ujścia** jest ustawiony na **SqlDWSink**.

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
Aby uzyskać przewodnik, zobacz [ładowanie 1 TB do usługi Azure SQL Data Warehouse w ramach 15 minut z usługi Azure Data Factory](data-factory-load-sql-data-warehouse.md) i [załadować dane za pomocą usługi Azure Data Factory](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) artykuł w dokumentacji usługi Azure SQL Data Warehouse.

## <a name="performance-and-tuning"></a>Wydajność i strojenie
Zobacz [Kopiowanie wydajności działania & dostrajania przewodnika,](data-factory-copy-activity-performance.md) aby dowiedzieć się więcej o kluczowych czynnikach, które wpływają na wydajność przenoszenia danych (Copy Activity) w usłudze Azure Data Factory i na różne sposoby optymalizacji.
