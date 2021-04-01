---
title: Klucze podstawowe, obce i unikatowe
description: Ograniczenia tabeli obsługują używanie dedykowanej puli SQL w usłudze Azure Synapse Analytics
services: synapse-analytics
author: mstehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: emtehran
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 88b63ce30000340a70811e9f623e4273ccbb272a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98117286"
---
# <a name="primary-key-foreign-key-and-unique-key-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Klucz podstawowy, klucz obcy i unikatowy klucz przy użyciu dedykowanej puli SQL w usłudze Azure Synapse Analytics

Dowiedz się więcej o ograniczeniach tabel w dedykowanej puli SQL, w tym kluczu podstawowym, kluczu obcym i unikatowym kluczu.

## <a name="table-constraints"></a>Ograniczenia tabeli

Dedykowana Pula SQL obsługuje następujące ograniczenia tabeli: 
- KLUCZ podstawowy jest obsługiwany tylko w przypadku, gdy są używane obiekty nieklastrowane i niewymuszone.    
- Ograniczenie UNIQUE jest obsługiwane tylko przez niewymuszone użycie.

W obszarze Składnia Sprawdź pozycję [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) i [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse). 

Ograniczenie klucza obcego nie jest obsługiwane w dedykowanej puli SQL.  


## <a name="remarks"></a>Uwagi

Posiadanie klucza podstawowego i/lub unikatowego klucza umożliwia dedykowanemu aparatowi puli SQL generowanie optymalnego planu wykonywania zapytania.  Wszystkie wartości w kolumnie klucza podstawowego lub unikatowej kolumnie ograniczenia powinny być unikatowe.

Po utworzeniu tabeli z kluczem podstawowym lub ograniczeniem Unique w dedykowanej puli SQL użytkownicy muszą upewnić się, że wszystkie wartości w tych kolumnach są unikatowe.  Naruszenie, które może spowodować zwrócenie nieprawidłowego wyniku zapytania.  Ten przykład pokazuje, jak zapytanie może zwracać niedokładne wyniki, jeśli klucz podstawowy lub kolumna ograniczenia UNIQUE zawiera zduplikowane wartości.  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>Przykłady

Utwórz dedykowaną tabelę puli SQL z kluczem podstawowym: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```

Utwórz dedykowaną tabelę puli SQL z ograniczeniem Unique:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu tabel dla dedykowanej puli SQL następnym krokiem jest załadowanie danych do tabeli. Aby zapoznać się z samouczkiem ładowania, zobacz [ładowanie danych do dedykowanej puli SQL](load-data-wideworldimportersdw.md).