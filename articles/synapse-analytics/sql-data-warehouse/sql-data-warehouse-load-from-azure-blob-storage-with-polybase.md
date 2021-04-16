---
title: Ładowanie danych sprzedaży detalicznej firmy Contoso do dedykowanych pul SQL
description: Użyj poleceń PolyBase i T-SQL, aby załadować dwie tabele z danych sprzedaży detalicznej firmy Contoso do dedykowanych pul SQL.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b1afcdfa74245eb566663d5dec6ce2e2276fbdc8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568237"
---
# <a name="load-contoso-retail-data-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Ładowanie danych sprzedaży detalicznej firmy Contoso do dedykowanych pul SQL w Azure Synapse Analytics

Z tego samouczka dowiesz się, jak za pomocą poleceń PolyBase i T-SQL załadować dwie tabele z danych sprzedaży detalicznej firmy Contoso do dedykowanych pul SQL.

Ten samouczek zawiera informacje na temat:

1. Konfigurowanie programu PolyBase do ładowania z usługi Azure Blob Storage
2. Ładowanie danych publicznych do bazy danych
3. Po zakończeniu ładowania należy przeprowadzić optymalizacje.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Do uruchomienia tego samouczka potrzebne jest konto platformy Azure, które ma już dedykowaną pulę SQL. Jeśli nie masz aprowizowanych magazynów danych, zobacz [Create a data warehouse and set server-level firewall rule](create-data-warehouse-portal.md)(Tworzenie magazynu danych i ustawianie reguły zapory na poziomie serwera).

## <a name="configure-the-data-source"></a>Konfigurowanie źródła danych

Program PolyBase używa obiektów zewnętrznych języka T-SQL do definiowania lokalizacji i atrybutów danych zewnętrznych. Definicje obiektów zewnętrznych są przechowywane w dedykowanych pulach SQL. Dane są przechowywane zewnętrznie.

## <a name="create-a-credential"></a>Tworzenie poświadczeń

**Pomiń ten** krok, jeśli ładujesz publiczne dane firmy Contoso. Nie potrzebujesz bezpiecznego dostępu do danych publicznych, ponieważ są już dostępne dla wszystkich użytkowników.

**Nie pomijaj tego kroku,** jeśli używasz tego samouczka jako szablonu do ładowania własnych danych. Aby uzyskać dostęp do danych za pomocą poświadczeń, użyj następującego skryptu, aby utworzyć poświadczenia w zakresie bazy danych. Następnie użyj go podczas definiowania lokalizacji źródła danych.

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

Użyj tego [polecenia CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) do przechowywania lokalizacji danych i typu danych.

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH
(  
    TYPE = Hadoop
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
);
```

> [!IMPORTANT]
> Jeśli zdecydujesz się publicznie udostępnić kontenery usługi Azure Blob Storage, pamiętaj, że jako właściciel danych opłaty za ruch wychodzący danych zostaną naliczone, gdy dane opuścią centrum danych.

## <a name="configure-the-data-format"></a>Konfigurowanie formatu danych

Dane są przechowywane w plikach tekstowych w usłudze Azure Blob Storage, a każde pole jest oddzielone ogranicznikiem. W programie SSMS uruchom następujące polecenie CREATE EXTERNAL FILE FORMAT, aby określić format danych w plikach tekstowych. Dane firmy Contoso są nieskompresowane i rozdzielane potokami.

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

Teraz, po podano źródło danych i format pliku, możesz utworzyć schemat dla tabel zewnętrznych.

Aby utworzyć miejsce do przechowywania danych firmy Contoso w bazie danych, utwórz schemat.

```sql
CREATE SCHEMA [asb]
GO
```

## <a name="create-the-external-tables"></a>Tworzenie tabel zewnętrznych

Uruchom następujący skrypt, aby utworzyć tabele zewnętrzne DimProduct i FactOnlineSales. W tym miejscu definiujesz nazwy kolumn i typy danych oraz wiążesz je z lokalizacją i formatem plików usługi Azure Blob Storage. Definicja jest przechowywana w magazynie danych, a dane są nadal w Azure Storage Blob.

Parametr  **LOCATION** to folder w folderze głównym w Azure Storage Blob. Każda tabela znajduje się w innym folderze.

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

Istnieją różne sposoby uzyskiwania dostępu do danych zewnętrznych.  Możesz odpytywania danych bezpośrednio z tabel zewnętrznych, ładować je do nowych tabel w magazynie danych lub dodawać dane zewnętrzne do istniejących tabel magazynu danych.  

### <a name="create-a-new-schema"></a>Tworzenie nowego schematu

CtAS tworzy nową tabelę zawierającą dane.  Najpierw utwórz schemat dla danych firmy Contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="load-the-data-into-new-tables"></a>Ładowanie danych do nowych tabel

Aby załadować dane z usługi Azure Blob Storage do tabeli magazynu danych, użyj CREATE TABLE [AS SELECT (Transact-SQL).](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) Ładowanie za [pomocą ctaS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) wykorzystuje silnie typowane tabele zewnętrzne, które zostały utworzone. Aby załadować dane do nowych tabel, użyj jednej instrukcji CTAS na tabelę.

Instrukcja CTAS tworzy nową tabelę i wypełnia ją wynikami instrukcji select. Instrukcja CTAS definiuje nową tabelę tak, aby zawierała te same kolumny i typy danych co wyniki instrukcji select. Jeśli wybierzesz wszystkie kolumny z tabeli zewnętrznej, nowa tabela będzie repliką kolumn i typów danych w tabeli zewnętrznej.

W tym przykładzie tworzymy zarówno wymiar, jak i tabelę faktów jako tabele rozproszone przy wyznaczaniu wartości skrótu.

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="track-the-load-progress"></a>Śledzenie postępu ładowania

Postęp ładowania można śledzić przy użyciu dynamicznych widoków zarządzania (DMV).

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

## <a name="optimize-columnstore-compression"></a>Optymalizowanie kompresji magazynu kolumn

Domyślnie dedykowane pule SQL przechowują tabelę jako indeks klastrowanego magazynu kolumn. Po zakończeniu ładowania niektóre wiersze danych mogą nie zostać skompresowane do magazynu kolumn.  Może się to zdarzyć z różnych powodów. Aby dowiedzieć się więcej, zobacz [Zarządzanie indeksami magazynu kolumn.](sql-data-warehouse-tables-index.md)

Aby zoptymalizować wydajność zapytań i kompresję magazynu kolumn po załadowaniu, ponownie skompiluj tabelę, aby wymusić kompresowanie wszystkich wierszy przez indeks magazynu kolumn.

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Aby uzyskać więcej informacji na temat obsługi indeksów magazynu kolumn, zobacz [artykuł Zarządzanie indeksami magazynu](sql-data-warehouse-tables-index.md) kolumn.

## <a name="optimize-statistics"></a>Optymalizowanie statystyk

Najlepiej utworzyć statystyki z jedną kolumną natychmiast po załadowaniu. Jeśli wiesz, że niektóre kolumny nie będą w predykatach zapytań, możesz pominąć tworzenie statystyk dla tych kolumn. Jeśli utworzysz statystyki z jedną kolumną dla każdej kolumny, ponowne skompilowanie wszystkich statystyk może zająć dużo czasu.

Jeśli zdecydujesz się utworzyć statystyki z jedną kolumną dla każdej kolumny w każdej tabeli, możesz użyć przykładowego kodu procedury składowanej `prc_sqldw_create_stats` w [artykule statystyk.](sql-data-warehouse-tables-statistics.md)

Poniższy przykład jest dobrym punktem wyjścia do tworzenia statystyk. Tworzy statystyki z jedną kolumną dla każdej kolumny w tabeli wymiarów i dla każdej kolumny łączącej w tabelach faktów. Statystyki z jedną lub wieloma kolumnami można zawsze dodawać później do innych kolumn tabeli fakt.

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

## <a name="achievement-unlocked"></a>Odblokowane osiągnięcie!

Pomyślnie załadowano dane publiczne do magazynu danych. Dobra robota!

Teraz możesz rozpocząć wykonywanie zapytań dotyczących tabel w celu eksplorowania danych. Uruchom następujące zapytanie, aby dowiedzieć się, jaka jest łączna sprzedaż na markę:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Następne kroki

Aby załadować pełny zestaw danych, uruchom przykład ładowania pełnego magazynu danych detalicznych [firmy Contoso](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md) z Microsoft SQL Server przykładów.
Aby uzyskać więcej wskazówek dotyczących programowania, zobacz Design decisions and coding techniques for data warehouses (Decyzje projektowe i techniki [kodowania dla magazynów danych).](sql-data-warehouse-overview-develop.md)
