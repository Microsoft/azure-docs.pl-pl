---
title: Arkusz Ściągawka zapytania dziennika SQL do Azure Monitor | Microsoft Docs
description: Pomoc dla użytkowników znających program SQL podczas pisania zapytań dzienników w Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: e9f937323f4e14b5c8c3f30c94bf9d2daef0baea
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606695"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>Arkusz Ściągawka zapytania dziennika SQL do Azure Monitor 

Poniższa tabela ułatwia użytkownikom znającym język SQL uzyskanie informacji o języku zapytań Kusto w celu pisania zapytań dzienników w Azure Monitor. Zapoznaj się z poleceniem T-SQL w celu rozwiązywania typowych scenariuszy i równoważnej w zapytaniu dziennika Azure Monitor.

## <a name="sql-to-azure-monitor"></a>SQL do Azure Monitor

Opis |Zapytanie SQL | Azure Monitor zapytanie dziennika
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Zaznacz wszystkie dane z tabeli |`SELECT * FROM dependencies` |<code>dependencies</code>
Wybieranie określonych kolumn z tabeli |`SELECT name, resultCode FROM dependencies` |<code>dependencies <br>&#124; project name, resultCode</code>
Wybieranie 100 rekordów z tabeli |`SELECT TOP 100 * FROM dependencies` |<code>dependencies <br>&#124; take 100</code>
Obliczanie wartości null |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL` |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Porównanie ciągów: równość |`SELECT * FROM dependencies WHERE name = "abcde"` |<code>dependencies <br>&#124; where name == "abcde"</code>
Porównanie ciągów: podciąg |`SELECT * FROM dependencies WHERE name like "%bcd%"` |<code>dependencies <br>&#124; where name contains "bcd"</code>
Porównanie ciągów: symbol wieloznaczny |`SELECT * FROM dependencies WHERE name like "abc%"` |<code>dependencies <br>&#124; where name startswith "abc"</code>
Porównanie dat: ostatni 1 dzień |`SELECT * FROM dependencies WHERE timestamp > getdate()-1` |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Porównanie dat: zakres dat |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'` |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Porównanie wartości logicznych |`SELECT * FROM dependencies WHERE !(success)` |<code>dependencies <br>&#124; where success == "False" </code>
Sortowanie |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc` |<code>dependencies <br>&#124; order by timestamp asc </code>
Distinct |`SELECT DISTINCT name, type  FROM dependencies` |<code>dependencies <br>&#124; summarize by name, type </code>
Grupowanie, agregacja |`SELECT name, AVG(duration) FROM dependencies GROUP BY name` |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Aliasy kolumn, rozszerzone |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name` |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
Pierwsze n rekordów według miary |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc` |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Unia |`SELECT * FROM dependencies UNION SELECT * FROM exceptions` |<code>union dependencies, exceptions</code>
Unia: warunki |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions WHERE value < 5` |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Join |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z lekcjami dotyczącymi [pisania zapytań dzienników w Azure monitor](get-started-queries.md).
