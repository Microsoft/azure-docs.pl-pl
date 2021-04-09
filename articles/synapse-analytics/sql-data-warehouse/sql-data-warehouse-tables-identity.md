---
title: Używanie tożsamości do tworzenia kluczy zastępczych
description: Zalecenia i przykłady dotyczące używania właściwości IDENTITY do tworzenia kluczy zastępczych w tabelach w dedykowanej puli SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/20/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 5c620aa60e134379614a905226caa4a66d179fae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98673539"
---
# <a name="using-identity-to-create-surrogate-keys-using-dedicated-sql-pool-in-azuresynapse-analytics"></a>Używanie tożsamości do tworzenia kluczy zastępczych przy użyciu dedykowanej puli SQL w usłudze AzureSynapse Analytics

W tym artykule znajdziesz zalecenia i przykłady dotyczące używania właściwości IDENTITY do tworzenia kluczy zastępczych w tabelach w dedykowanej puli SQL.

## <a name="what-is-a-surrogate-key"></a>Co to jest klucz zastępczy

Klucz zastępczy w tabeli jest kolumną o unikatowym identyfikatorze dla każdego wiersza. Klucz nie jest generowany na podstawie danych tabeli. Modele danych, takie jak tworzenie kluczy zastępczych w tabelach, podczas projektowania modeli magazynu danych. Można użyć właściwości IDENTITY do osiągnięcia tego celu po prostu i efektywnie bez wpływu na wydajność ładowania.
> [!NOTE]
> W usłudze Azure Synapse Analytics wartość tożsamości jest zwiększana we własnym rozmieszczeniu i nie nakłada się na wartości tożsamości w innych dystrybucjach.  Wartość tożsamości w Synapse nie gwarantuje, że jest ona unikatowa, jeśli użytkownik jawnie wstawi zduplikowaną wartość z "SET IDENTITY_INSERT ON" lub "renasiona" tożsamości. Aby uzyskać szczegółowe informacje, zobacz [CREATE TABLE (Właściwość Transact-SQL)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest&preserve-view=true). 


## <a name="creating-a-table-with-an-identity-column"></a>Tworzenie tabeli z kolumną tożsamości

Właściwość IDENTITY została zaprojektowana w celu skalowania w poziomie między wszystkimi dystrybucjami w dedykowanej puli SQL bez wpływu na wydajność ładowania. W związku z tym implementacja tożsamości jest ukierunkowana na osiągnięcie tych celów.

Można zdefiniować tabelę jako mającą właściwość IDENTITY podczas pierwszej tworzenia tabeli przy użyciu składni podobnej do następującej:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1) NOT NULL
,    C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Można następnie użyć, `INSERT..SELECT` Aby wypełnić tabelę.

W tym pozostałej części tej sekcji przedstawiono wszystkie szczegóły wdrożenia, aby ułatwić zrozumienie ich w pełni.  

### <a name="allocation-of-values"></a>Alokacja wartości

Właściwość IDENTITY nie gwarantuje kolejności, w której wartości zastępcze są przydzielane ze względu na rozproszoną architekturę hurtowni danych. Właściwość IDENTITY została zaprojektowana w celu skalowania w poziomie między wszystkimi dystrybucjami w dedykowanej puli SQL bez wpływu na wydajność ładowania. 

Poniższy przykład jest ilustracją:

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)    NOT NULL
,    C2 VARCHAR(30)                NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

W poprzednim przykładzie dwa wiersze wyładowywane w dystrybucji 1. Pierwszy wiersz ma wartość zastępczą 1 w kolumnie `C1` , a drugi wiersz ma wartość zastępczą 61. Obie te wartości zostały wygenerowane przez właściwość IDENTITY. Jednak alokacja wartości nie jest ciągła. To zachowanie jest celowe.

### <a name="skewed-data"></a>Skośne dane

Zakres wartości dla typu danych jest równomiernie rozłożony przez dystrybucje. Jeśli tabela rozproszona pogorszy się od pochylonych danych, zakres wartości dostępnych dla tego elementu DataType może zostać przedwcześnie wyczerpany. Na przykład, jeśli wszystkie dane kończą się w ramach jednej dystrybucji, efektywnie tabela ma dostęp tylko do jednej sixtieth wartości typu danych. Z tego powodu właściwość IDENTITY jest ograniczona `INT` `BIGINT` tylko do typów danych.

### <a name="selectinto"></a>Wybierz pozycję.. PRZEKSZTAŁCA

Po wybraniu istniejącej kolumny tożsamości w nowej tabeli, Nowa kolumna dziedziczy właściwość IDENTITY, chyba że jest spełniony jeden z następujących warunków:

- Instrukcja SELECT zawiera sprzężenie.
- Wielokrotne instrukcje SELECT są sprzężone przy użyciu UNION.
- Kolumna tożsamości jest wymieniona więcej niż jeden raz na liście wyboru.
- Kolumna tożsamości jest częścią wyrażenia.

Jeśli którykolwiek z tych warunków ma wartość true, kolumna zostanie utworzona nie NULL zamiast dziedziczyć właściwość IDENTITY.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

CREATE TABLE jako SELECT (CTAS) ma takie samo zachowanie SQL Server, które zostało udokumentowane w przypadku WYBRANia.. Przekształca. Nie można jednak określić właściwości IDENTITY w definicji kolumny `CREATE TABLE` części instrukcji. Nie można również użyć funkcji IDENTITY w `SELECT` części CTAs. Aby wypełnić tabelę, należy użyć `CREATE TABLE` do zdefiniowania tabeli, a następnie jej `INSERT..SELECT` wypełnienia.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Jawne wstawianie wartości do kolumny tożsamości

Dedykowana Pula SQL obsługuje `SET IDENTITY_INSERT <your table> ON|OFF` składnię. Możesz użyć tej składni, aby jawnie wstawić wartości do kolumny tożsamość.

Wiele modeli danych, takich jak użycie wstępnie zdefiniowanych wartości ujemnych dla niektórych wierszy w ich wymiarach. Przykładem jest wiersz-1 lub "nieznany element członkowski".

Następny skrypt pokazuje, jak jawnie dodać ten wiersz przy użyciu SET IDENTITY_INSERT:

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT     *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>Ładowanie danych

Obecność właściwości IDENTITY ma pewne konsekwencje związane z kodem ładowania danych. W tej sekcji przedstawiono podstawowe wzorce dotyczące ładowania danych do tabel przy użyciu tożsamości.

Aby załadować dane do tabeli i wygenerować klucz zastępczy przy użyciu tożsamości, należy utworzyć tabelę, a następnie użyć instrukcji INSERT.. Wybierz lub Wstaw.. WARTOŚCI do wykonania ładowania.

Poniższy przykład wyróżnia wzorzec podstawowy:

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)
,    C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT     C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> Obecnie nie jest możliwe użycie `CREATE TABLE AS SELECT` podczas ładowania danych do tabeli z kolumną tożsamości.
>

Aby uzyskać więcej informacji na temat ładowania danych, zobacz [projektowanie wyodrębniania, ładowania i przekształcania (ELT) dla dedykowanej puli SQL](design-elt-data-loading.md) i  [ładowanie najlepszych](guidance-for-loading-data.md)rozwiązań.

## <a name="system-views"></a>Widoki systemu

Widok wykazu [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) służy do identyfikowania kolumny zawierającej właściwość Identity.

Aby lepiej zrozumieć schemat bazy danych, w tym przykładzie pokazano, jak zintegrować sys.identity_column z innymi widokami wykazu systemu:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Ograniczenia

Nie można użyć właściwości IDENTITY:

- Gdy typem danych kolumny nie jest INT lub BIGINT
- Gdy kolumna jest również kluczem dystrybucji
- Gdy tabela jest tabelą zewnętrzną

Następujące powiązane funkcje nie są obsługiwane w dedykowanej puli SQL:

- [TOŻSAMOŚĆ ()](/sql/t-sql/functions/identity-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="common-tasks"></a>Typowe zadania

Ta sekcja zawiera przykładowy kod, którego można użyć do wykonywania typowych zadań podczas pracy z kolumnami tożsamości.

Kolumna C1 jest TOŻSAMOŚCIą we wszystkich następujących zadaniach.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Znajdź najwyższą przydzieloną wartość dla tabeli

Użyj `MAX()` funkcji, aby określić największą wartość przydzieloną dla rozproszonej tabeli:

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Znajdź inicjator i przyrost dla właściwości IDENTITY

Widoków wykazu można użyć do odnajdywania wartości konfiguracji przyrostu i inicjatora tożsamości dla tabeli przy użyciu następującej kwerendy:

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Następne kroki

- [Przegląd tabeli](sql-data-warehouse-tables-overview.md)
- [TOŻSAMOŚĆ CREATE TABLE (Transact-SQL) (Właściwość)](/sql/t-sql/statements/create-table-transact-sql-identity-property?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [POLECENIE DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
