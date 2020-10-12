---
title: Funkcje w kwerendach dzienników Azure Monitor | Microsoft Docs
description: W tym artykule opisano, jak używać funkcji do wywoływania zapytania z innego zapytania dziennika w Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/31/2020
ms.openlocfilehash: 155f27b9f04db74955640aad8f582fdf05539368
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87511942"
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

- [Operacje dotyczące ciągów](string-operations.md)
- [Operacje dotyczące daty i godziny](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Agregacje zaawansowane](advanced-aggregations.md)
- [Notacja JSON i struktury danych](json-data-structures.md)
- [Sprzężenia](joins.md)
- [Wykresy](charts.md)
