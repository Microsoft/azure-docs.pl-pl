---
title: Funkcje w kwerendach dzienników Azure Monitor | Microsoft Docs
description: W tym artykule opisano, jak używać funkcji do wywoływania zapytania z innego zapytania dziennika w Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/31/2020
ms.openlocfilehash: 678a6f0dc19d966f3d15e713008c19c8fbb96f5e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024368"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Używanie funkcji w kwerendach dzienników Azure Monitor

Aby użyć zapytania dziennika z innym zapytaniem, można je zapisać jako funkcję. Pozwala to uprościć złożone zapytania, dzieląc je na części i umożliwia ponowne użycie wspólnego kodu z wieloma zapytaniami.

## <a name="create-a-function"></a>Tworzenie funkcji

Utwórz funkcję z Log Analytics w Azure Portal, klikając przycisk **Zapisz** , a następnie podając informacje w poniższej tabeli.

| Ustawienie | Opis |
|:---|:---|
| Nazwa           | Nazwa wyświetlana zapytania w **Eksploratorze zapytań**. |
| Zapisz jako        | Funkcja |
| Alias funkcji | Krótka nazwa służąca do korzystania z funkcji w innych zapytaniach. Nie może zawierać spacji i musi być unikatowa. |
| Kategoria       | Kategoria do organizowania zapisanych zapytań i funkcji w **Eksploratorze zapytań**. |




## <a name="use-a-function"></a>Korzystanie z funkcji
Użyj funkcji, dołączając jej alias w innym zapytaniu. Może być używana tak jak jakakolwiek inna tabela.

## <a name="function-parameters"></a>Parametry funkcji 
Można dodać parametry do funkcji, aby można było podać wartości dla niektórych zmiennych podczas ich wywoływania. Jedynym sposobem na obecnie utworzenie funkcji z parametrami jest użycie szablonu Menedżer zasobów. Zapoznaj się z przykładami [Menedżer zasobów szablonów dla zapytań dzienników w Azure monitor](../samples/resource-manager-log-queries.md#parameterized-function) .

## <a name="example"></a>Przykład
Następujące przykładowe zapytanie zwraca wszystkie brakujące aktualizacje zabezpieczeń zgłoszone w ciągu ostatniego dnia. Zapisz to zapytanie jako funkcję z aliasem _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Utwórz kolejne zapytanie i odwołujące się do funkcji _security_updates_last_day_ , aby wyszukać odpowiednie aktualizacje zabezpieczeń dotyczące języka SQL.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi lekcjami dotyczącymi pisania Azure Monitor zapytań dziennika:

- [Operacje dotyczące ciągów](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)
- [Operacje dotyczące daty i godziny](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#date-and-time-operations)
- [Funkcje agregacji](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations)
- [Agregacje zaawansowane](/azure/data-explorer/write-queries#advanced-aggregations)
- [Notacja JSON i struktury danych](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#json-and-data-structures)
- [Sprzężenia](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins)
- [Schematy](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#charts)
