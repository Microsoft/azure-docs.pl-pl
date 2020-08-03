---
title: Przypisywanie zmiennych przy użyciu języka SQL Synapse
description: W tym artykule znajdziesz wskazówki dotyczące przypisywania zmiennych T-SQL za pomocą języka SQL Synapse.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: c3bd04d037f7852bd7d9ea51719abf27f09093b0
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495679"
---
# <a name="assign-variables-with-synapse-sql"></a>Przypisywanie zmiennych przy użyciu języka SQL Synapse

W tym artykule znajdziesz wskazówki dotyczące przypisywania zmiennych T-SQL za pomocą języka SQL Synapse.

## <a name="set-variables-with-declare"></a>Ustaw zmienne z deklaracją DECLARE

Zmienne w Synapse SQL są ustawiane przy użyciu `DECLARE` instrukcji lub `SET` instrukcji. Inicjowanie zmiennych przy użyciu deklaracji jest jednym z najbardziej elastycznych sposobów ustawiania wartości zmiennej w Synapse SQL.

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

Nie można zainicjować i użyć zmiennej w tej samej instrukcji DECLARE. Do zilustrowania Poniższy przykład nie jest dozwolony, ponieważ * \@ P1* jest zarówno zainicjowany, jak i używany w tej samej instrukcji DECLARE. Poniższy przykład zawiera błąd.

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

Aby uzyskać więcej porad programistycznych, zobacz artykuł [Omówienie programowania w programie Synapse dla języka SQL](develop-overview.md) .
