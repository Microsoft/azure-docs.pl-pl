---
title: Konwertowanie klasy zasobów na grupę obciążeń
description: Dowiedz się, jak utworzyć grupę obciążeń podobną do klasy zasobów w Azure SQL Data Warehouse.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5d73ba8f21fe7731fb751d42a8497ff8e1ebba7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81383621"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Konwertowanie klas zasobów na grupy obciążeń

Grupy obciążeń zapewniają mechanizm izolowania i zawierania zasobów systemowych.  Ponadto grupy obciążeń umożliwiają ustawianie reguł wykonywania dla żądań w nich uruchomionych.  Reguła wykonywania limitu czasu zapytania umożliwia anulowanie zapytań dotyczących przemijających bez interwencji użytkownika.  W tym artykule wyjaśniono, jak zastosować istniejącą klasę zasobów i utworzyć grupę obciążeń z podobną konfiguracją.  Dodatkowo dodawana jest opcjonalna reguła limitu czasu zapytania.

> [!NOTE]
> Zobacz [mieszanie przypisań klasy zasobów z klasyfikatorami](sql-data-warehouse-workload-classification.md#mixing-resource-class-assignments-with-classifiers) w dokumencie koncepcji [klasyfikacji obciążenia](sql-data-warehouse-workload-classification.md) , aby uzyskać wskazówki dotyczące używania grup obciążeń i klas zasobów w tym samym czasie.

## <a name="understanding-the-existing-resource-class-configuration"></a>Zrozumienie istniejącej konfiguracji klasy zasobów

Grupy obciążeń wymagają parametru o nazwie `REQUEST_MIN_RESOURCE_GRANT_PERCENT` , który określa procent łącznych zasobów systemowych przyznanych na żądanie.  Alokacja zasobów jest wykonywana dla [klas zasobów](resource-classes-for-workload-management.md#what-are-resource-classes) przez alokowanie miejsc współbieżności.  Aby określić wartość do określenia `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, użyj wykazu sys. dm_workload_management_workload_groups_stats <link tbd> DMV.  Na przykład poniższe zapytanie zapytania zwraca wartość, która może być użyta dla `REQUEST_MIN_RESOURCE_GRANT_PERCENT` parametru w celu utworzenia grupy obciążeń podobnej do staticrc40.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Grupy obciążeń działają na podstawie wartości procentowej ogólnych zasobów systemowych.  

Ponieważ grupy obciążeń działają na podstawie wartości procentowej ogólnych zasobów systemowych, podczas skalowania w górę i w dół, procent zasobów przypisywanych do klas zasobów statycznych względem ogólnych zmian zasobów systemu.  Na przykład staticrc40 w DW1000c przydziela 9,6% ogólnych zasobów systemowych.  O godzinie DW2000c przydzielono 19,2%.  Ten model jest podobny, jeśli chcesz skalować w górę w celu zapewnienia współbieżności i przydzielania większej liczby zasobów na żądanie.

## <a name="create-workload-group"></a>Utwórz grupę obciążeń

Ze znanymi `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, można użyć SKŁADNI Utwórz grupę <link> obciążeń, aby utworzyć grupę obciążeń.  Opcjonalnie możesz określić `MIN_PERCENTAGE_RESOURCE` , że jest większy niż zero, aby izolować zasoby dla grupy obciążenia.  Ponadto można opcjonalnie określić `CAP_PERCENTAGE_RESOURCE` mniej niż 100, aby ograniczyć ilość zasobów, które może zużywać Grupa obciążeń.  

Poniższy przykład ustawia `MIN_PERCENTAGE_RESOURCE` do dedykowania 9,6% zasobów systemowych `wgDataLoads` i gwarantuje, że jedno zapytanie będzie mogło działać przez cały czas.  Ponadto `CAP_PERCENTAGE_RESOURCE` jest ustawiony na 38,4% i ogranicza tę grupę obciążeń do czterech współbieżnych żądań.  Ustawienie `QUERY_EXECUTION_TIMEOUT_SEC` parametru na 3600 spowoduje automatyczne anulowanie wszystkich zapytań, które są uruchamiane przez więcej niż 1 godzinę.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Tworzenie klasyfikatora

Poprzednio mapowanie zapytań do klas zasobów zostało wykonane z [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Aby osiągnąć te same funkcje i mapować żądania do grup obciążeń, użyj składni [KLASYFIKATORA obciążeń](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .  Za pomocą sp_addrolemember można mapować zasoby na żądanie na podstawie nazwy logowania.  Klasyfikator zapewnia dodatkowe opcje oprócz logowania, takie jak:
    - label
    - sesja
    - czas w poniższym przykładzie przypisuje zapytania z `AdfLogin` nazwy logowania, które również mają ustawioną `factloads` `wgDataLoads` [etykietę opcji](sql-data-warehouse-develop-label.md) na grupę obciążeń utworzoną powyżej.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>Testowanie za pomocą przykładowego zapytania

Poniżej znajduje się przykładowe zapytanie i zapytanie DMV, aby upewnić się, że grupa obciążeń i klasyfikator są prawidłowo skonfigurowane.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 *
  FROM nation
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command
  FROM sys.dm_pdw_exec_requests
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>Następne kroki

- [Izolacja obciążenia](sql-data-warehouse-workload-isolation.md)
- [Jak utworzyć grupę obciążeń](quickstart-configure-workload-isolation-tsql.md)
- [Utwórz klasyfikator obciążeń (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?&view=azure-sqldw-latest)
- [Utwórz grupę obciążeń (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
