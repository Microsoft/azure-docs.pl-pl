---
title: Ładowanie danych detalicznych contoso do magazynu danych Synapse SQL
description: Polecenia PolyBase i T-SQL umożliwia załadowanie dwóch tabel z danych detalicznych contoso do programu Synapse SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 118653efc8829ac5ef6287bb36fb5595cff1147b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416123"
---
# <a name="load-contoso-retail-data-to-synapse-sql"></a>Ładowanie danych detalicznych contoso do synapsy SQL 

W tym samouczku nauczysz się używać poleceń PolyBase i T-SQL do ładowania dwóch tabel z danych detalicznych Contoso do magazynu danych Synapse SQL.

W tym poradniku będziesz:

1. Konfigurowanie bazy danych PolyBase do ładowania z magazynu obiektów obiektów blob platformy Azure
2. Ładowanie danych publicznych do bazy danych
3. Wykonaj optymalizacje po zakończeniu ładowania.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby uruchomić ten samouczek, potrzebujesz konta platformy Azure, które ma już magazyn danych Synapse SQL. Jeśli nie masz aprowizowanego magazynu danych, zobacz [Tworzenie magazynu danych i ustawianie reguły zapory na poziomie serwera](create-data-warehouse-portal.md).

## <a name="configure-the-data-source"></a>Konfigurowanie źródła danych

PolyBase używa obiektów zewnętrznych T-SQL do definiowania lokalizacji i atrybutów danych zewnętrznych. Definicje obiektów zewnętrznych są przechowywane w magazynie danych Synapse SQL. Dane są przechowywane zewnętrznie.

## <a name="create-a-credential"></a>Tworzenie poświadczenia

**Pomiń ten krok,** jeśli ładujesz dane publiczne Contoso. Nie potrzebujesz bezpiecznego dostępu do danych publicznych, ponieważ są one już dostępne dla każdego.

**Nie pomijaj tego kroku,** jeśli używasz tego samouczka jako szablonu do ładowania własnych danych. Aby uzyskać dostęp do danych za pośrednictwem poświadczeń, użyj następującego skryptu, aby utworzyć poświadczenia o zakresie bazy danych. Następnie użyj go podczas definiowania lokalizacji źródła danych.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

## <a name="create-the-external-data-source"></a>Tworzenie zewnętrznego źródła danych

Użyj tego polecenia [UTWÓRZ ZEWNĘTRZNE ŹRÓDŁO DANYCH,](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) aby zapisać lokalizację danych i typ danych.

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH
(  
    TYPE = Hadoop
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
);
```

> [!IMPORTANT]
> Jeśli zdecydujesz się udostępnić kontenery magazynu obiektów blob azure publicznych, należy pamiętać, że jako właściciel danych będzie naliczana opłata za transfer danych wychodzących, gdy dane opuszcza centrum danych.

## <a name="configure-the-data-format"></a>Konfigurowanie formatu danych

Dane są przechowywane w plikach tekstowych w magazynie obiektów blob platformy Azure, a każde pole jest oddzielone ogranicznikiem. W systemie SSMS uruchom następujące polecenie CREATE EXTERNAL FILE FORMAT, aby określić format danych w plikach tekstowych. Dane Contoso są nieskompresowane i rozdzielane potokami.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-schema-for-the-external-tables"></a>Tworzenie schematu dla tabel zewnętrznych

Po określeniu źródła danych i formatu pliku można przystąpić do tworzenia schematu dla tabel zewnętrznych.

Aby utworzyć miejsce do przechowywania danych Contoso w bazie danych, należy utworzyć schemat.

```sql
CREATE SCHEMA [asb]
GO
```

## <a name="create-the-external-tables"></a>Tworzenie tabel zewnętrznych

Uruchom następujący skrypt, aby utworzyć tabele zewnętrzne DimProduct i FactOnlineSales. Wszystko, co robisz w tym miejscu, to definiowanie nazw kolumn i typów danych i powiązanie ich z lokalizacją i formatem plików magazynu obiektów blob platformy Azure. Definicja jest przechowywana w magazynie danych, a dane są nadal w obiekcie blob usługi Azure Storage.

Parametr **LOKALIZACJA** jest folderem w folderze głównym w obiekcie Blob usługi Azure Storage. Każda tabela znajduje się w innym folderze.

```sql
--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/'
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="load-the-data"></a>Ładowanie danych

Istnieją różne sposoby uzyskiwania dostępu do danych zewnętrznych.  Można wyszukiwać dane bezpośrednio z tabel zewnętrznych, ładować dane do nowych tabel w magazynie danych lub dodawać dane zewnętrzne do istniejących tabel magazynu danych.  

### <a name="create-a-new-schema"></a>Tworzenie nowego schematu

CTAS tworzy nową tabelę, która zawiera dane.  Najpierw utwórz schemat dla contoso danych.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="load-the-data-into-new-tables"></a>Ładowanie danych do nowych tabel

Aby załadować dane z magazynu obiektów blob platformy Azure do tabeli magazynu danych, należy użyć instrukcji [CREATE TABLE AS SELECT (Transact-SQL).](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) Ładowanie za pomocą [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) wykorzystuje silnie wpisane tabele zewnętrzne, które zostały utworzone. Aby załadować dane do nowych tabel, należy użyć jednej instrukcji CTAS na tabelę.

CTAS tworzy nową tabelę i wypełnia ją wynikami instrukcji select. CTAS definiuje nową tabelę, aby mieć te same kolumny i typy danych, jak wyniki select instrukcji. Jeśli wybierzesz wszystkie kolumny z tabeli zewnętrznej, nowa tabela będzie repliką kolumn i typów danych w tabeli zewnętrznej.

W tym przykładzie tworzymy zarówno wymiar, jak i tabelę faktów jako tabele rozproszone skrótu.

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="track-the-load-progress"></a>Śledzenie postępu obciążenia

Postęp obciążenia można śledzić za pomocą dynamicznych widoków zarządzania (DMV).

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="optimize-columnstore-compression"></a>Optymalizacja kompresji magazynu kolumn

Domyślnie magazyn danych Synapse SQL przechowuje tabelę jako indeks klastrowanego magazynu kolumn. Po zakończeniu ładowania niektóre wiersze danych mogą nie zostać skompresowane do magazynu kolumn.  Istnieją różne powody, dla których może się to zdarzyć. Aby dowiedzieć się więcej, zobacz [zarządzanie indeksami magazynu kolumn](sql-data-warehouse-tables-index.md).

Aby zoptymalizować wydajność kwerend i kompresji magazynu kolumn po obciążeniu, odbuduj tabelę, aby wymusić indeks magazynu kolumn do kompresji wszystkich wierszy.

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Aby uzyskać więcej informacji na temat obsługi indeksów magazynu kolumn, zobacz zarządzanie [indeksami magazynu kolumn](sql-data-warehouse-tables-index.md) artykułu.

## <a name="optimize-statistics"></a>Optymalizacja statystyk

Najlepiej jest utworzyć statystyki jednokolumnowe natychmiast po załadowaniu. Jeśli wiesz, że niektóre kolumny nie będą w predykatach kwerendy, możesz pominąć tworzenie statystyk dla tych kolumn. Jeśli utworzysz statystyki jednokolumnowe dla każdej kolumny, odbudowanie wszystkich statystyk może zająć dużo czasu.

Jeśli zdecydujesz się utworzyć statystyki jednokolumnowe dla każdej kolumny każdej tabeli, możesz użyć przykładu `prc_sqldw_create_stats` kodu procedury składowanej w [artykule statystyk.](sql-data-warehouse-tables-statistics.md)

Poniższy przykład jest dobrym punktem wyjścia do tworzenia statystyk. Tworzy jednokolumnowe statystyki dla każdej kolumny w tabeli wymiarów i dla każdej kolumny łączącej w tabelach faktów. Zawsze możesz później dodać statystyki jedno- lub wielokolumnowe do innych kolumn tabeli faktów.

```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Osiągnięcie odblokowane!

Pomyślnie załadowano dane publiczne do magazynu danych. Świetna robota!

Teraz możesz rozpocząć wykonywanie zapytań do tabel w celu eksplorowania danych. Uruchom następującą kwerendę, aby dowiedzieć się, czy łączna sprzedaż na markę:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Następne kroki

Aby załadować pełny zestaw danych, uruchom [przykładowy moduł ładowania pełnego magazynu danych sieci sprzedaży Contoso](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) z repozytorium przykładów programu Microsoft SQL Server.
Aby uzyskać więcej wskazówek dotyczących programowania, zobacz [Projektowanie decyzji i technik kodowania dla magazynów danych.](sql-data-warehouse-overview-develop.md)
