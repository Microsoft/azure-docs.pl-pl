---
title: Przydatne operatory w kwerendach dzienników Azure Monitor | Microsoft Docs
description: Typowe funkcje do użycia dla różnych scenariuszy w kwerendach dzienników Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: ff63b9b7027e99c70971230936ed98186c2208e8
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91607364"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Przydatne operatory w kwerendach dzienników Azure Monitor

W poniższej tabeli przedstawiono niektóre typowe funkcje do użycia w różnych scenariuszach w zapytaniach dziennika Azure Monitor.

## <a name="useful-operators"></a>Przydatne operatory

Kategoria                                |Odpowiednia funkcja analizy
----------------------------------------|----------------------------------------
Wybór i aliasy kolumn            |`project`, `project-away`, `extend`
Tymczasowe tabele i stałe          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
Operatory porównania i ciągów         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Typowe funkcje ciągów                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Typowe funkcje matematyczne                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Analizowanie tekstu                            |`extract()`, `extractjson()`, `parse`, `split()`
Ograniczanie danych wyjściowych                         |`take`, `limit`, `top`, `sample`
Funkcje daty                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Grupowanie i agregacja                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Sprzężenia i związki                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Sortuj, Porządkuj                             |`sort`, `order` 
Obiekt dynamiczny (JSON i Array)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Operatory logiczne                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Uczenie maszynowe                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z lekcjami dotyczącymi [pisania zapytań dzienników w Azure monitor](get-started-queries.md).
