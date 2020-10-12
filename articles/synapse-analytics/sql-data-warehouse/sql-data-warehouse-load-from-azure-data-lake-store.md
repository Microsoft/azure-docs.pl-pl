---
title: Samouczek ładowania danych z Azure Data Lake Storage
description: Użyj instrukcji COPY, aby załadować dane z Azure Data Lake Storage for SQL Synapse.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 06/07/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: c634b4f7ac3aa1fe83e6ab3f863e998b8d25232c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533941"
---
# <a name="load-data-from-azure-data-lake-storage-for-synapse-sql"></a>Ładowanie danych z Azure Data Lake Storage dla Synapse SQL

W tym przewodniku opisano sposób używania [instrukcji Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) do ładowania danych z Azure Data Lake Storage. Aby zapoznać się z szybkimi przykładami dotyczącymi używania instrukcji COPY we wszystkich metodach uwierzytelniania, zapoznaj się z następującą dokumentacją: [bezpiecznie Ładuj dane przy użyciu języka SQL Synapse](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples).

> [!NOTE]  
> Aby przekazać opinię lub zgłosić problemy dotyczące instrukcji COPY, Wyślij wiadomość e-mail na następującą listę dystrybucyjną: sqldwcopypreview@service.microsoft.com .
>
> [!div class="checklist"]
>
> * Utwórz tabelę docelową, aby załadować dane z Azure Data Lake Storage.
> * Utwórz instrukcję COPY, aby załadować dane do magazynu danych.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Zanim rozpoczniesz ten samouczek, pobierz i zainstaluj najnowszą wersję programu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

Aby uruchomić ten samouczek, potrzebne są:

* Pula SQL. Zobacz [Tworzenie puli SQL i wykonywanie zapytań dotyczących danych](create-data-warehouse-portal.md).
* Konto Data Lake Storage. Zobacz [wprowadzenie do Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). W przypadku tego konta magazynu musisz skonfigurować lub określić jedno z następujących poświadczeń do załadowania: klucz konta magazynu, klucz sygnatury dostępu współdzielonego, użytkownika aplikacji usługi Azure Directory lub użytkownika AAD, który ma odpowiednią rolę platformy Azure dla konta magazynu.

## <a name="create-the-target-table"></a>Tworzenie tabeli docelowej

Połącz się z pulą SQL i Utwórz docelową tabelę, do której chcesz załadować. W tym przykładzie tworzymy tabelę wymiarów produktów.

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

Połącz się z pulą SQL i uruchom instrukcję COPY. Pełną listę przykładów można znaleźć w następującej dokumentacji: [bezpiecznie Ładuj dane przy użyciu języka SQL Synapse](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples).

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
   CREDENTIAL (IDENTITY = '', SECRET = '')
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

Domyślnie tabele są zdefiniowane jako klastrowany indeks magazynu kolumn. Po zakończeniu ładowania niektóre wiersze danych mogą nie zostać skompresowane do magazynu kolumn.  Istnieje wiele powodów, dla których może się to zdarzyć. Aby dowiedzieć się więcej, zobacz [Zarządzanie indeksami magazynu kolumn](sql-data-warehouse-tables-index.md).

Aby zoptymalizować wydajność zapytań i kompresję magazynu kolumn po załadowaniu, należy ponownie skompilować tabelę, aby wymusić, że indeks magazynu kolumn będzie kompresowany ze wszystkimi wierszami.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optymalizowanie statystyk

Najlepiej jest utworzyć statystyki pojedynczej kolumny bezpośrednio po załadowaniu. Istnieją pewne możliwości statystyczne. Na przykład w przypadku tworzenia statystyk z jedną kolumną dla każdej kolumny może upłynąć dużo czasu, aby ponownie skompilować wszystkie statystyki. Jeśli wiesz, że niektóre kolumny nie będą znajdować się w predykatach zapytań, możesz pominąć tworzenie statystyk dla tych kolumn.

Jeśli zdecydujesz się utworzyć statystykę jednokolumnową dla każdej kolumny każdej tabeli, możesz użyć przykładowego kodu procedury składowanej `prc_sqldw_create_stats` w artykule [Statystyka](sql-data-warehouse-tables-statistics.md) .

Poniższy przykład jest dobrym punktem wyjścia do tworzenia statystyk. Tworzy statystykę jednokolumnową dla każdej kolumny w tabeli wymiarów i dla każdej kolumny sprzężenia w tabelach faktów. Można zawsze dodawać pojedyncze lub wielokolumnowe statystyki do innych kolumn tabeli faktów w późniejszym czasie.

## <a name="achievement-unlocked"></a>Nieodblokowane osiągnięcie!

Dane zostały pomyślnie załadowane do magazynu danych. Dobra robota!

## <a name="next-steps"></a>Następne kroki
Ładowanie danych to pierwszy krok tworzenia rozwiązania magazynu danych przy użyciu usługi Azure Synapse Analytics. Zapoznaj się z naszymi zasobami programistycznymi.

> [!div class="nextstepaction"]
> [Dowiedz się, jak opracowywać tabele na potrzeby magazynowania danych](sql-data-warehouse-tables-overview.md)

Aby uzyskać więcej przykładów i odwołań, zapoznaj się z następującą dokumentacją:
- [Kopiuj dokumentację referencyjną instrukcji](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [Kopiuj przykłady dla każdej metody uwierzytelniania](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)
- [Kopiuj Przewodnik Szybki Start dla pojedynczej tabeli](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
