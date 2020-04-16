---
title: Przypisywanie zmiennych za pomocą synapsy SQL
description: W tym artykule znajdziesz wskazówki dotyczące przypisywania zmiennych T-SQL za pomocą synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: b2a596b71ee7e5f58e01d5bc10b330f6f54a69d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428669"
---
# <a name="assigning-variables-with-synapse-sql"></a>Przypisywanie zmiennych za pomocą synapsy SQL

W tym artykule znajdziesz wskazówki dotyczące przypisywania zmiennych T-SQL za pomocą synapse SQL.

## <a name="setting-variables-with-declare"></a>Ustawianie zmiennych za pomocą DECLARE

Zmienne w synapse SQL `DECLARE` są ustawiane przy użyciu instrukcji lub `SET` instrukcji. Inicjowanie zmiennych za pomocą DECLARE jest jednym z najbardziej elastycznych sposobów ustawiania wartości zmiennej w synapse SQL.

```sql
DECLARE @v  int = 0
;
```

Można również użyć DECLARE, aby ustawić więcej niż jedną zmienną naraz. Nie można użyć SELECT lub UPDATE, aby wykonać następujące czynności:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Nie można zainicjować i użyć zmiennej w tej samej instrukcji DECLARE. Aby zilustrować, poniższy przykład nie *@p1* jest dozwolone, ponieważ jest inicjowane i używane w tej samej instrukcji DECLARE. Poniższy przykład zawiera błąd.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Ustawianie wartości za pomocą SET

SET jest powszechną metodą ustawiania pojedynczej zmiennej.

Następujące instrukcje są wszystkie prawidłowe sposoby, aby ustawić zmienną z SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Za pomocą SET można ustawić tylko jedną zmienną naraz. Jednakże podmioty podmiotów łączenia są dopuszczalne.

## <a name="limitations"></a>Ograniczenia

Nie można użyć UPDATE do przypisania zmiennych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej wskazówek dotyczących programowania, zobacz [Synapse SQL development overview](develop-overview.md) article.