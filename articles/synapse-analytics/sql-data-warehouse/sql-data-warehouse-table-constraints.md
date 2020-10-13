---
title: Klucze podstawowe, obce i unikatowe
description: Obsługa ograniczeń tabeli w Synapse puli SQL w usłudze Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 562e2cce317d8774ecf72971d53be4f66f9c3da4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85212772"
---
# <a name="primary-key-foreign-key-and-unique-key-in-synapse-sql-pool"></a>Klucz podstawowy, klucz obcy i unikatowy klucz w puli SQL Synapse

Dowiedz się więcej o ograniczeniach tabeli w puli Synapse SQL, w tym klucz podstawowy, klucz obcy i unikatowy klucz.

## <a name="table-constraints"></a>Ograniczenia tabeli

Synapse Pula SQL obsługuje następujące ograniczenia tabeli: 
- KLUCZ podstawowy jest obsługiwany tylko w przypadku, gdy są używane obiekty nieklastrowane i niewymuszone.    
- Ograniczenie UNIQUE jest obsługiwane tylko przez niewymuszone użycie.

W obszarze Składnia Sprawdź pozycję [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) i [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse). 

Ograniczenie klucza obcego nie jest obsługiwane w puli SQL Synapse.  


## <a name="remarks"></a>Uwagi

Posiadanie klucza podstawowego i/lub unikatowego klucza umożliwia aparatowi puli SQL Synapse generowanie optymalnego planu wykonywania zapytania.  Wszystkie wartości w kolumnie klucza podstawowego lub unikatowej kolumnie ograniczenia powinny być unikatowe.

Po utworzeniu tabeli przy użyciu klucza podstawowego lub ograniczenia UNIQUE w Synapse puli SQL użytkownicy muszą upewnić się, że wszystkie wartości w tych kolumnach są unikatowe.  Naruszenie, które może spowodować zwrócenie nieprawidłowego wyniku zapytania.  Ten przykład pokazuje, jak zapytanie może zwracać niedokładne wyniki, jeśli klucz podstawowy lub kolumna ograniczenia UNIQUE zawiera zduplikowane wartości.  

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

Utwórz tabelę puli SQL Synapse z kluczem podstawowym: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Utwórz tabelę puli Synapse SQL z unikatowym ograniczeniem:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu tabel dla puli SQL Synapse, następnym krokiem jest załadowanie danych do tabeli. Aby zapoznać się z samouczkiem ładowania, zobacz [ładowanie danych do puli SQL Synapse](load-data-wideworldimportersdw.md).
