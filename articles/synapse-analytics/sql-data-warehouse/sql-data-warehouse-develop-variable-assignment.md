---
title: Przypisz zmienne
description: W tym artykule znajdziesz istotne porady dotyczące przypisywania zmiennych T-SQL dla dedykowanych pul SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 87448ea737c11af13a52632e5bf4f67dc54d9ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96459231"
---
# <a name="assign-variables-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Przypisywanie zmiennych dla dedykowanych pul SQL w usłudze Azure Synapse Analytics

W tym artykule znajdziesz istotne porady dotyczące przypisywania zmiennych T-SQL w dedykowanej puli SQL.

## <a name="set-variables-with-declare"></a>Ustaw zmienne z deklaracją DECLARE

Zmienne w dedykowanej puli SQL są ustawiane przy użyciu `DECLARE` instrukcji lub `SET` instrukcji. Inicjowanie zmiennych przy użyciu deklaracji jest jednym z najbardziej elastycznych sposobów ustawiania wartości zmiennej w puli SQL.

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
