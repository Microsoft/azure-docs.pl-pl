---
title: Samouczek dotyczący ładowania danych z Azure Data Lake Storage
description: Użyj instrukcji COPY, aby załadować dane z Azure Data Lake Storage dla dedykowanych pul SQL.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 16f95a86169be04eba202b311fc4437b204ec8b3
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566532"
---
# <a name="load-data-from-azure-data-lake-storage-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Ładowanie danych z usługi Azure Data Lake Storage do dedykowanych pul SQL w programie Azure Synapse Analytics

W tym przewodniku opisano sposób używania [instrukcji COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) do ładowania danych z Azure Data Lake Storage. Aby uzyskać krótkie przykłady użycia instrukcji COPY we wszystkich metodach uwierzytelniania, odwiedź następującą dokumentację: Bezpieczne ładowanie danych przy [użyciu dedykowanych pul SQL.](./quickstart-bulk-load-copy-tsql-examples.md)

> [!NOTE]  
> Aby przekazać opinię lub zgłosić problemy dotyczące instrukcji COPY, wyślij wiadomość e-mail na następującą listę dystrybucyjną: sqldwcopypreview@service.microsoft.com .
>
> [!div class="checklist"]
>
> * Utwórz tabelę docelową, aby załadować dane z Azure Data Lake Storage.
> * Utwórz instrukcje COPY, aby załadować dane do magazynu danych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Zanim rozpoczniesz ten samouczek, pobierz i zainstaluj najnowszą wersję programu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS).

Do uruchomienia tego samouczka potrzebne są:

* Dedykowana pula SQL. Zobacz [Tworzenie dedykowanej puli SQL i wykonywanie zapytań dotyczących danych](create-data-warehouse-portal.md).
* Konto Data Lake Storage użytkownika. Zobacz [Wprowadzenie do Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). W przypadku tego konta magazynu należy skonfigurować lub określić jedno z następujących poświadczeń do załadowania: klucz konta magazynu, klucz sygnatury dostępu współdzielonego (SAS), użytkownik aplikacji usługi Azure Directory lub użytkownik usługi AAD, który ma odpowiednią rolę platformy Azure dla konta magazynu.

## <a name="create-the-target-table"></a>Tworzenie tabeli docelowej

Połącz się z dedykowaną pulą SQL i utwórz tabelę docelową do załadowania. W tym przykładzie tworzymy tabelę wymiarów produktu.

```sql
-- A: Create the target table
-- DimProduct
CREATE TABLE [dbo].[DimProduct]
(
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    DISTRIBUTION = HASH([ProductKey]),
    CLUSTERED COLUMNSTORE INDEX
    --HEAP
);
```


## <a name="create-the-copy-statement"></a>Tworzenie instrukcji COPY

Połącz się z dedykowaną pulą SQL i uruchom instrukcję COPY. Pełną listę przykładów można znaleźć w następującej dokumentacji: [Bezpieczne ładowanie danych przy użyciu dedykowanych pul SQL.](./quickstart-bulk-load-copy-tsql-examples.md)

```sql
-- B: Create and execute the COPY statement

COPY INTO [dbo].[DimProduct] 
--The column list allows you map, omit, or reorder input file columns to target table columns. 
--You can also specify the default value when there is a NULL value in the file.
--When the column list is not specified, columns will be mapped based on source and target ordinality
(
    ProductKey default -1 1,
    ProductLabel default 'myStringDefaultWhenNull' 2,
    ProductName default 'myStringDefaultWhenNull' 3
)
--The storage account location where you data is staged
FROM 'https://storageaccount.blob.core.windows.net/container/directory/'
WITH 
(
   --CREDENTIAL: Specifies the authentication method and credential access your storage account
   CREDENTIAL = (IDENTITY = '', SECRET = ''),
   --FILE_TYPE: Specifies the file type in your storage account location
   FILE_TYPE = 'CSV',
   --FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text (CSV) file
   FIELDTERMINATOR = '|',
   --ROWTERMINATOR: Marks the end of a record in the file
   ROWTERMINATOR = '0x0A',
   --FIELDQUOTE: Specifies the delimiter for data of type string in a delimited text (CSV) file
   FIELDQUOTE = '',
   ENCODING = 'UTF8',
   DATEFORMAT = 'ymd',
   --MAXERRORS: Maximum number of reject rows allowed in the load before the COPY operation is canceled
   MAXERRORS = 10,
   --ERRORFILE: Specifies the directory where the rejected rows and the corresponding error reason should be written
   ERRORFILE = '/errorsfolder',
) OPTION (LABEL = 'COPY: ADLS tutorial');
```

## <a name="optimize-columnstore-compression"></a>Optymalizowanie kompresji magazynu kolumn

Domyślnie tabele są definiowane jako indeks klastrowanego magazynu kolumn. Po zakończeniu ładowania niektóre wiersze danych mogą nie zostać skompresowane do magazynu kolumn.  Może się to zdarzyć z różnych powodów. Aby dowiedzieć się więcej, zobacz [Zarządzanie indeksami magazynu kolumn](sql-data-warehouse-tables-index.md).

Aby zoptymalizować wydajność zapytań i kompresję magazynu kolumn po załadowaniu, ponownie skompiluj tabelę, aby wymusić kompresowanie wszystkich wierszy przez indeks magazynu kolumn.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optymalizowanie statystyk

Najlepiej jest utworzyć statystyki z jedną kolumną natychmiast po załadowaniu. Istnieją pewne opcje dotyczące statystyk. Jeśli na przykład utworzysz statystyki z jedną kolumną dla każdej kolumny, ponowne skompilowanie wszystkich statystyk może zająć dużo czasu. Jeśli wiesz, że niektóre kolumny nie będą w predykatach zapytań, możesz pominąć tworzenie statystyk dla tych kolumn.

Jeśli zdecydujesz się utworzyć statystyki z jedną kolumną dla każdej kolumny w każdej tabeli, możesz użyć przykładowego kodu procedury składowanej `prc_sqldw_create_stats` w artykule [statystyk.](sql-data-warehouse-tables-statistics.md)

Poniższy przykład jest dobrym punktem wyjścia do tworzenia statystyk. Tworzy statystyki z jedną kolumną dla każdej kolumny w tabeli wymiarów i dla każdej kolumny łączącej w tabelach faktów. Statystyki z jedną lub wieloma kolumnami można zawsze dodawać później do innych kolumn tabeli fakt.

## <a name="achievement-unlocked"></a>Odblokowane osiągnięcie!

Pomyślnie załadowano dane do magazynu danych. Dobra robota!

## <a name="next-steps"></a>Następne kroki
Ładowanie danych to pierwszy krok do opracowania rozwiązania magazynu danych przy użyciu Azure Synapse Analytics. Zapoznaj się z naszymi zasobami programistyki.

> [!div class="nextstepaction"]
> [Dowiedz się, jak opracowywać tabele do magazynowania danych](sql-data-warehouse-tables-overview.md)

Aby uzyskać więcej przykładów i odwołań do ładowania, zobacz następującą dokumentację:
- [Dokumentacja referencyjna instrukcji COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)
- [Przykłady copy dla każdej metody uwierzytelniania](./quickstart-bulk-load-copy-tsql-examples.md)
- [PRZEWODNIK Szybki start copy dla pojedynczej tabeli](./quickstart-bulk-load-copy-tsql.md)