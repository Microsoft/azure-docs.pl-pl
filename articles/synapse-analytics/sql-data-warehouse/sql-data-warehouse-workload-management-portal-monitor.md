---
title: Monitorowanie portalu zarządzania obciążeniami
description: Wskazówki dotyczące monitorowania w portalu zarządzania obciążeniami w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 3ea6318ff4a1f99218caea83c2d83ee1f62ef697
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679639"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring"></a>Azure Synapse Analytics — Monitorowanie portal zarządzania obciążeń

W tym artykule wyjaśniono, jak monitorować wykorzystanie zasobów [grupy obciążeń](sql-data-warehouse-workload-isolation.md#workload-groups) i działania zapytań.
Aby uzyskać szczegółowe informacje na temat konfigurowania Eksplorator metryk platformy Azure, zobacz artykuł [wprowadzenie do usługi azure Eksplorator metryk](../../azure-monitor/platform/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .  Szczegóły dotyczące monitorowania zużycia zasobów systemowych można znaleźć w sekcji [użycie zasobów](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) w dokumentacji monitorowania usługi Azure Synapse Analytics.
Dostępne są dwie różne kategorie metryk grup obciążenia do monitorowania zarządzania obciążeniami: aktywność przydziału zasobów i zapytań.  Te metryki można podzielić i filtrować według grupy obciążeń.  Metryki mogą być podzielone i filtrowane w zależności od tego, czy są zdefiniowane przez system (grupy obciążenia klasy zasobów) czy zdefiniowane przez użytkownika (utworzone przez użytkownika ze składnią [tworzenia grupy obciążeń](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ).

## <a name="workload-management-metric-definitions"></a>Definicje metryk zarządzania obciążeniami

|Nazwa metryki                    |Opis  |Typ agregacji |
|-------------------------------|-------------|-----------------|
|Procent zasobów obowiązujących dla limitu | *Procent zasobów obowiązujących* dla limitu jest stałym limitem procentu zasobów dostępnych dla grupy obciążeń, uwzględniając *efektywną minimalną stawkę procentową zasobów* przydzieloną dla innych grup obciążeń. Metryka *procentu zasobów efektywnych* jest konfigurowana przy użyciu `CAP_PERCENTAGE_RESOURCE` parametru w SKŁADNI [tworzenia grupy obciążeń](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Wartość efektywna jest opisana tutaj.<br><br>Jeśli na przykład grupa obciążeń `DataLoads` jest tworzona z użyciem `CAP_PERCENTAGE_RESOURCE` wartości = 100, a druga grupa obciążeń jest tworzona z efektywną wartością procentową zasobu wynoszącą 25%, *procent efektywnego zasobu* dla `DataLoads` grupy obciążeń to 75%.<br><br>*Procent zasobu efektywnego limitu* określa górną granicę współbieżności (i w ten sposób potencjalną przepływność), którą może osiągnąć Grupa obciążeń.  Jeśli wymagana jest dodatkowa przepływność poza tym, co jest aktualnie zgłaszane przez metrykę *procentową zasobów obowiązujących* , zwiększ wartość `CAP_PERCENTAGE_RESOURCE` , Zmniejsz `MIN_PERCENTAGE_RESOURCE` Pozostałe grupy obciążeń lub Skaluj wystąpienie w górę, aby dodać więcej zasobów.  Zmniejszenie `REQUEST_MIN_RESOURCE_GRANT_PERCENT` możliwości może zwiększyć współbieżność, ale nie może zwiększyć ogólnej przepływności.| Min, średnia, maksimum |
|Efektywny minimalny procent zasobów |*Efektywny procent zasobów* jest minimalnym procentem zasobów zarezerwowanych i izolowanych dla grupy obciążeń biorąc pod uwagę minimalny poziom usług.  Metryka efektywnego procentowego zasobu jest konfigurowana przy użyciu `MIN_PERCENTAGE_RESOURCE` parametru w składni [tworzenia grupy obciążeń](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Wartość efektywna jest opisana [tutaj](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#effective-values).<br><br>Użyj sumy typu agregacji, jeśli ta Metryka jest odfiltrowana i nie można jej podzielić, aby monitorować łączną izolację obciążeń skonfigurowaną w systemie.<br><br>*Efektywny minimalny procent zasobów* określa dolną granicę gwarantowanej współbieżności (i w ten sposób gwarantowaną przepływność), którą może osiągnąć Grupa obciążeń.  Jeśli potrzebujesz dodatkowych zasobów gwarantowanych poza tym, co jest aktualnie zgłaszane przez metrykę *procent efektywnego zasobu* , zwiększ `MIN_PERCENTAGE_RESOURCE` parametr skonfigurowany dla grupy obciążenia.  Zmniejszenie `REQUEST_MIN_RESOURCE_GRANT_PERCENT` możliwości może zwiększyć współbieżność, ale nie może zwiększyć ogólnej przepływności. |Min, średnia, maksimum|
|Aktywne zapytania grupy obciążenia  |Ta Metryka służy do raportowania aktywnych zapytań w grupie obciążenia.  Użycie tej metryki nie zostało odfiltrowane i rozdzielenie spowoduje wyświetlenie wszystkich aktywnych zapytań uruchomionych w systemie.|Sum         |
|Alokacja grupy obciążeń według maksymalnego procentu zasobów |Ta Metryka przedstawia procentową alokację zasobów względem *procentu zasobów obowiązujących* dla grupy obciążenia.  Ta Metryka zapewnia efektywne wykorzystanie grupy obciążeń.<br><br>Weź pod uwagę grupę obciążeń `DataLoads` z wartością *procentową zasobu limitu* wynoszącą 75% i `REQUEST_MIN_RESOURCE_GRANT_PERCENT` skonfigurowaną o 25%.  Wartość *procentowa przydziału grup obciążeń według maksymalnej wartości procentowej zasobu* przefiltrowana na `DataLoads` 33% (25%/75%) Jeśli jedno zapytanie zostało uruchomione w tej grupie obciążenia.<br><br>Ta Metryka służy do identyfikowania wykorzystania grupy obciążeń.  Wartość bliska 100% wskazuje, że wszystkie zasoby dostępne dla grupy obciążeń są używane.  Ponadto *Metryka zakolejkowanej grupy obciążenia* dla tej samej grupy obciążeń, która przedstawia wartość większą od zera, będzie wskazywać, że grupa obciążenia będzie używać dodatkowych zasobów, jeśli są przydzieleni.  Odwrotnie, jeśli ta Metryka jest stale niska i *aktywne zapytania grupy obciążenia* są niskie, Grupa obciążeń nie jest używana.  Ta sytuacja jest szczególnie problematyczna w przypadku, gdy *procent efektywnego zasobu* jest większy niż zero, co oznacza [nieużywaną izolację obciążenia](#underutilized-workload-isolation).|Min, średnia, maksimum |
|Alokacja grupy obciążeń według wartości procentowej systemu | Ta Metryka przedstawia procentową alokację zasobów względem całego systemu.<br><br>Weź pod uwagę grupę obciążeń `DataLoads` z `REQUEST_MIN_RESOURCE_GRANT_PERCENT` konfiguracją o 25%.  *Przydziały przydziału grup obciążeń według wartości procentowej systemu* wyfiltrowanej do `DataLoads` byłyby 25% (25%/100%) Jeśli jedno zapytanie zostało uruchomione w tej grupie obciążenia.|Min, średnia, maksimum |
|Limity czasu zapytania grupy obciążeń |Zapytania dotyczące grupy obciążeń, które przekroczyły limit czasu.  Limity czasu zapytania zgłoszone przez tę metrykę są wykonywane dopiero po rozpoczęciu wykonywania zapytania (nie obejmuje to czasu oczekiwania z powodu blokady lub oczekiwania na zasób).<br><br>Limit czasu zapytania jest konfigurowany przy użyciu `QUERY_EXECUTION_TIMEOUT_SEC` parametru w składni [tworzenia grupy obciążeń](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Zwiększenie wartości może zmniejszyć liczbę przekroczeń limitu czasu zapytania.<br><br>Rozważ zwiększenie wartości `REQUEST_MIN_RESOURCE_GRANT_PERCENT` parametru grupy obciążenia, aby zmniejszyć liczbę limitów czasu i przydzielić więcej zasobów na zapytanie.  Należy pamiętać, `REQUEST_MIN_RESOURCE_GRANT_PERCENT` że zwiększenie zmniejsza liczbę współbieżności dla grupy obciążenia. |Sum |
|Zakolejkowane zapytania grupy obciążenia | Zapytania dla grupy obciążeń, które znajdują się obecnie w kolejce oczekujące na rozpoczęcie wykonywania.  Zapytania mogą być kolejkami, ponieważ oczekują na zasoby lub blokady.<br><br>Zapytania mogą oczekiwać z wielu powodów.  Jeśli system jest przeciążony, a żądanie współbieżności jest większe niż to, co jest dostępne, kwerendy będą kierowane do kolejki.<br><br>Rozważ dodanie większej liczby zasobów do grupy obciążeń przez zwiększenie `CAP_PERCENTAGE_RESOURCE` parametru w instrukcji [tworzenia grupy obciążeń](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Jeśli `CAP_PERCENTAGE_RESOURCE` jest większa niż Metryka *procentu efektywnego zasobu* , skonfigurowana izolacja obciążenia dla innej grupy obciążeń ma wpływ na zasoby przydzielono do tej grupy obciążeń.  Rozważ obniżenie poziomu `MIN_PERCENTAGE_RESOURCE` innych grup obciążeń lub przeskalowanie w górę wystąpienia, aby dodać więcej zasobów. |Sum |

## <a name="monitoring-scenarios-and-actions"></a>Scenariusze monitorowania i akcje

Poniżej przedstawiono serię konfiguracji wykresu, aby wyróżnić użycie metryki zarządzania obciążeniem na potrzeby rozwiązywania problemów wraz ze skojarzonymi akcjami w celu rozwiązania problemu.

### <a name="underutilized-workload-isolation"></a>Izolacja niewykorzystywanego obciążenia

Należy wziąć pod uwagę następującą konfigurację grupy obciążeń i klasyfikatora, w której `wgPriority` jest tworzona Grupa obciążeń, a *TheCEO* `membername` jest zamapowana na nią przy użyciu `wcCEOPriority` klasyfikatora obciążenia.  `wgPriority`Grupa obciążeń ma skonfigurowaną dla niej izolację obciążenia o wartości 25% ( `MIN_PERCENTAGE_RESOURCE` = 25).  Każde zapytanie przesłane przez *TheCEO* ma 5% zasobów systemowych ( `REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5).

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

Poniższy wykres jest skonfigurowany w następujący sposób:<br>
Metric 1: *efektywny minimalny procent zasobów* (średnia agregacji, `blue line` )<br>
Metryka 2: *alokacja grupy obciążeń według wartości procentowej systemu* (średnia agregacja, `purple line` )<br>
Filtr: [Grupa obciążeń] = `wgPriority`<br>
![Zrzut ekranu przedstawia wykres z dwiema metrykami i filtrem.](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png)
Wykres pokazuje, że za pomocą 25% obciążania obciążenia jest używana średnio 10%.  W takim przypadku `MIN_PERCENTAGE_RESOURCE` wartość parametru może być obniżona do zakresu od 10 do 15, a inne obciążenia w systemie zużywają zasoby.

### <a name="workload-group-bottleneck"></a>Wąskie gardła grupy obciążeń

Należy wziąć pod uwagę następującą konfigurację grupy obciążeń i klasyfikatora, w której utworzono grupę obciążeń `wgDataAnalyst` , a element *dataanalizatorze* `membername` jest mapowany do niego przy użyciu `wcDataAnalyst` klasyfikatora obciążenia.  `wgDataAnalyst`Grupa obciążeń ma dla niej ustawioną 6% izolację obciążenia ( `MIN_PERCENTAGE_RESOURCE` = 6) i limit zasobów równy 9% ( `CAP_PERCENTAGE_RESOURCE` = 9).  Każde zapytanie przesłane przez *dataanalizować* ma 3% zasobów systemowych ( `REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

Poniższy wykres jest skonfigurowany w następujący sposób:<br>
Metryka 1: *procent efektywnego zasobu zakończenia* (średnia agregacji, `blue line` )<br>
Metryka 2: *alokacja grupy obciążeń według maksymalnego procentu zasobów* (średnia agregacja, `purple line` )<br>
Metryka 3: *grupowanie zadań w kolejce* (agregacja SUM, `turquoise line` )<br>
Filtr: [Grupa obciążeń] = `wgDataAnalyst`<br>
![Zrzut ekranu przedstawia wykres z trzema metrykami i filtrem.](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png)
Na wykresie widać, że dla zasobów zostanie wyświetlona wartość 9%, grupa obciążenia jest 90% + wykorzystana (z *metryki przydziału grupy obciążeń według maksymalnej wartości procentowej zasobu*).  Istnieje stała kolejkowanie zapytań, jak pokazano w *kolejce metryki zapytań grupy obciążenia*.  W takim przypadku zwiększenie `CAP_PERCENTAGE_RESOURCE` do wartości większej niż 9% umożliwi wykonywanie większej liczby zapytań jednocześnie.  Zwiększenie `CAP_PERCENTAGE_RESOURCE` zakłada, że dostępna jest wystarczająca ilość zasobów i nie jest izolowany przez inne grupy obciążeń.  Sprawdź, czy limit wzrostu został zwiększony przez sprawdzenie *metryki procentowej zasobu limitu*.  Jeśli jest wymagana większa przepływność, należy również rozważyć zwiększenie `REQUEST_MIN_RESOURCE_GRANT_PERCENT` do wartości większej niż 3.  Zwiększenie możliwego `REQUEST_MIN_RESOURCE_GRANT_PERCENT` do szybszego działania zapytań.

## <a name="next-steps"></a>Następne kroki

- [Szybki Start: Konfigurowanie izolacji obciążenia przy użyciu języka T-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
- [Utwórz grupę obciążeń (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [Utwórz klasyfikator obciążeń (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)<br>
- [Monitorowanie wykorzystania zasobów](sql-data-warehouse-concept-resource-utilization-query-activity.md)
