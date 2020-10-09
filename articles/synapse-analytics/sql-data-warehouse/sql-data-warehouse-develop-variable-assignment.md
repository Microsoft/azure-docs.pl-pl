---
title: Przypisz zmienne
description: W tym artykule znajdziesz istotne porady dotyczące przypisywania zmiennych T-SQL w puli SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0cbadc10fb817c70bd259397c840aae68abc2d54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85213333"
---
# <a name="assign-variables-in-synapse-sql-pool"></a>Przypisywanie zmiennych w puli SQL Synapse

W tym artykule znajdziesz istotne porady dotyczące przypisywania zmiennych T-SQL w puli SQL.

## <a name="set-variables-with-declare"></a>Ustaw zmienne z deklaracją DECLARE

Zmienne w puli SQL są ustawiane przy użyciu `DECLARE` instrukcji lub `SET` instrukcji. Inicjowanie zmiennych przy użyciu deklaracji jest jednym z najbardziej elastycznych sposobów ustawiania wartości zmiennej w puli SQL.

```sql
DECLARE @v  int = 0
;
```

Można również użyć DECLARE, aby ustawić więcej niż jedną zmienną jednocześnie. Nie można użyć opcji SELECT ani UPDATE w celu wykonania następujących czynności:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Nie można zainicjować i użyć zmiennej w tej samej instrukcji DECLARE. W celu zilustrowania punktu Poniższy przykład jest **niedozwolony,** ponieważ @p1 jest zainicjowany i używany w tej samej instrukcji DECLARE. W związku z tym Poniższy przykład zawiera błąd:

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Ustaw wartości za pomocą zestawu

SET to wspólna metoda ustawiania pojedynczej zmiennej.

Następujące instrukcje są prawidłowymi sposobami ustawiania zmiennej przy użyciu zestawu:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Można ustawić tylko jedną zmienną naraz z ZESTAWem. Jednak operatory złożone są dozwolone.

## <a name="limitations"></a>Ograniczenia

Nie można używać aktualizacji do przypisywania zmiennych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](sql-data-warehouse-overview-develop.md).
