---
title: Sprzężenia w kwerendach dziennika Azure Monitor | Microsoft Docs
description: W tym artykule opisano sposób korzystania z sprzężeń w zapytaniach dziennika Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 2dace6968fbbe69f806c27fb7a46e60c63f78b4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77670206"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Sprzężenia w kwerendach dziennika Azure Monitor

> [!NOTE]
> Przed ukończeniem tej lekcji należy ukończyć pracę [z Azure Monitor Log Analytics](get-started-portal.md) i [Azure monitor zapytania dziennika](get-started-queries.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Sprzężenia umożliwiają analizowanie danych z wielu tabel w tym samym zapytaniu. Scalanie wierszy dwóch zestawów danych przez dopasowanie wartości określonych kolumn.


```Kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

W tym przykładzie pierwszy zestaw danych filtruje wszystkie zdarzenia logowania. Jest to przyłączone do drugiego zestawu danych, który filtruje wszystkie zdarzenia wylogowania. Rzutowane kolumny to _Computer_, _Account_, _TargetLogonId_i _TimeGenerated_. Zestawy danych są skorelowane przez wspólną kolumnę _TargetLogonId_. Dane wyjściowe są pojedynczym rekordem na korelację, która ma zarówno czas logowania, jak i wylogowania.

Jeśli oba zestawy danych mają kolumny o tych samych nazwach, kolumny zestawu danych po prawej stronie będą mieć numer indeksu, więc w tym przykładzie wyniki będą wyświetlane jako _TargetLogonId_ z wartościami z tabeli po lewej stronie i _TargetLogonId1_  z wartościami z tabeli po prawej stronie. W takim przypadku druga kolumna _TargetLogonId1_ została usunięta przy użyciu `project-away` operatora.

> [!NOTE]
> Aby zwiększyć wydajność, należy zachować tylko odpowiednie kolumny połączonych zestawów danych przy użyciu `project` operatora.


Użyj następującej składni, aby dołączyć dwa zestawy danych, a przyłączony klucz ma inną nazwę między dwiema tabelami:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Tabele odnośników
Typowym zastosowaniem sprzężeń jest użycie statycznego mapowania wartości przy użyciu `datatable` , które mogą pomóc w przekształcaniu wyników na bardziej niemożliwe do wysłania. Na przykład, aby wzbogacić dane zdarzenia zabezpieczeń z nazwą zdarzenia dla każdego identyfikatora zdarzenia.

```Kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

![Dołącz do elementu DataTable](media/joins/dim-table.png)

## <a name="join-kinds"></a>Rodzaje sprzężeń
Określ typ sprzężenia z argumentem _rodzaju_ . Każdy typ wykonuje różne dopasowania między rekordami podanych tabel, zgodnie z opisem w poniższej tabeli.

| Typ sprzężenia | Opis |
|:---|:---|
| innerunique | Jest to domyślny tryb sprzężenia. Najpierw zostaną znalezione wartości dopasowanej kolumny z lewej tabeli, a zduplikowane wartości są usuwane.  Następnie zestaw unikatowych wartości jest dopasowywany do prawej tabeli. |
| fabryk | Tylko pasujące rekordy w obu tabelach są uwzględniane w wynikach. |
| leftouter | Wszystkie rekordy w tabeli po lewej i pasujące rekordy w prawej tabeli są zawarte w wynikach. Niedopasowane właściwości wyjściowe zawierają wartości null.  |
| leftanti | Rekordy z lewej strony, które nie mają dopasowań z prawej strony, są zawarte w wynikach. Tabela wyników zawiera tylko kolumny z tabeli po lewej. |
| leftsemi | Rekordy z lewej strony, które mają dopasowania od prawej strony, są zawarte w wynikach. Tabela wyników zawiera tylko kolumny z tabeli po lewej. |


## <a name="best-practices"></a>Najlepsze rozwiązania

W celu uzyskania optymalnej wydajności należy rozważyć następujące kwestie:

- Użyj filtru czasu dla każdej tabeli, aby zmniejszyć liczbę rekordów, które muszą zostać ocenione dla sprzężenia.
- Użyj `where` i, `project` Aby zmniejszyć liczbę wierszy i kolumn w tabelach wejściowych przed sprzężeniem.
- Jeśli jedna tabela jest zawsze mniejsza niż druga, użyj jej jako lewej strony sprzężenia.


## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi lekcjami dotyczącymi używania zapytań dzienników Azure Monitor:

- [Operacje dotyczące ciągów](string-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Agregacje zaawansowane](advanced-aggregations.md)
- [Notacja JSON i struktury danych](json-data-structures.md)
- [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
- [Wykresy](charts.md)
