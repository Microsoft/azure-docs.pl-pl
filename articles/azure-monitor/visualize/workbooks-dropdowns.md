---
title: Azure Monitor parametry rozwijane skoroszytu
description: Uprość złożone raportowanie ze wstępnie skompilowanymi i niestandardowymi skoroszytami zawierającymi parametry listy rozwijanej
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: d6fb4954ff616bccd9b237aedb3001b0a8d592dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717200"
---
# <a name="workbook-drop-down-parameters"></a>Parametry listy rozwijanej skoroszytu

Lista rozwijana Zezwalaj użytkownikowi na zbieranie co najmniej jednej wartości wejściowej z znanego zestawu (na przykład wybierz jedną z żądań aplikacji). Lista rozwijana zapewnia przyjazny dla użytkownika sposób zbierania dowolnych danych wejściowych od użytkowników. Lista rozwijana jest szczególnie przydatna podczas włączania filtrowania raportów interaktywnych. 

Najprostszym sposobem określenia listy rozwijanej jest podawanie statycznej listy w ustawieniu parametru. Bardziej interesujący jest sposób, aby uzyskać listę dynamicznie za pośrednictwem zapytania KQL. Ustawienia parametrów umożliwiają również określenie, czy jest to pojedynczy, czy wybór wieloznaczny, a jeśli jest to wybór wieloznaczny, w jaki sposób zestaw wyników powinien być sformatowany (ogranicznik, oferta itp.).

## <a name="creating-a-static-drop-down-parameter"></a>Tworzenie statycznego parametru listy rozwijanej

1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz pozycję _Dodaj parametry_ z linków w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr_ .
4. W okienku Nowy parametr, który jest podręczny ENTER:
    1. Nazwa parametru: `Environment`
    2. Typ parametru: `Drop down`
    3. Wymagane: `checked`
    4. Zezwalaj `multiple selection` : `unchecked`
    5. Pobierz dane z: `JSON`
5. W bloku tekstu wejściowego JSON Wstaw ten fragment kodu JSON:
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. Naciśnij niebieski `Update` przycisk.
7. Wybierz pozycję "Zapisz" na pasku narzędzi, aby utworzyć parametr.
8. Parametr Environment będzie listą rozwijaną z trzema wartościami.

    ![Obraz przedstawiający tworzenie statycznej Drown w dół](./media/workbooks-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>Tworzenie statycznego listy rozwijanej z grupami elementów

Jeśli wynik zapytania/JSON zawiera pole "Grupa", na liście rozwijanej zostaną wyświetlone grupy wartości. Postępuj zgodnie z powyższym przykładem, ale zamiast tego użyj następującego kodu JSON:

```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```

![Obraz przedstawiający przykład zgrupowanego listy rozwijanej](./media/workbooks-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>Tworzenie dynamicznego parametru rozwijanego
1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz pozycję _Dodaj parametry_ z linków w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr_ .
4. W okienku Nowy parametr, który jest podręczny ENTER:
    1. Nazwa parametru: `RequestName`
    2. Typ parametru: `Drop down`
    3. Wymagane: `checked`
    4. Zezwalaj `multiple selection` : `unchecked`
    5. Pobierz dane z: `Query`
5. W bloku tekstu wejściowego JSON Wstaw ten fragment kodu JSON:

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. Naciśnij niebieski `Run Query` przycisk.
2. Wybierz pozycję "Zapisz" na pasku narzędzi, aby utworzyć parametr.
3. Parametr RequestName będzie listą rozwijaną nazw wszystkich żądań w aplikacji.

    ![Obraz przedstawiający tworzenie dynamicznego listy rozwijanej](./media/workbooks-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>Parametr listy rozwijanej odwołującego się

### <a name="in-kql"></a>W KQL
1. Dodaj kontrolkę zapytania do skoroszytu i wybierz zasób Application Insights.
2. W edytorze KQL wprowadź ten fragment kodu

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. Spowoduje to rozwinięcie czasu oceny zapytania, aby:

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. Uruchom zapytanie, aby zobaczyć wyniki. Opcjonalnie Renderuj ją jako wykres.

    ![Obraz przedstawiający listę rozwijaną, do której odwołuje się element KQL](./media/workbooks-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>Wartość parametru, etykieta, wybór i Grupa
Zapytanie użyte w dynamicznym parametrze rozwijanym powyżej tylko zwraca listę wartości, które są renderowane w postaci wiernej na liście rozwijanej. Ale co zrobić, jeśli chcesz wybrać inną nazwę wyświetlaną lub jedną z nich do wybrania? Parametry rozwijane umożliwiają to za pośrednictwem kolumn Value, Label, Selection i Group.

Poniższy przykład pokazuje, jak uzyskać listę Application Insights zależności, których nazwy wyświetlane są stylami z emoji, ma pierwszy wybrany i jest pogrupowane według nazw operacji.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```

![Obraz przedstawiający parametr listy rozwijanej przy użyciu opcji Value, Label, Selection i Group](./media/workbooks-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>Opcje parametru listy rozwijanej
| Parametr | Wyjaśnienie | Przykład |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | Wybrana wartość | Pobierz fabrikamaccount |
| `{DependencyName:label}` | Zaznaczona etykieta | 🌐 GET fabrikamaccount |
| `{DependencyName:value}` | Wybrana wartość | Pobierz fabrikamaccount |

## <a name="multiple-selection"></a>Wybór wielokrotny
Przykładowo jawnie ustaw parametr, aby wybrać tylko jedną wartość z listy rozwijanej. Parametry listy rozwijanej obsługują również obsługę `multiple selection` — włączenie tej opcji jest proste, ponieważ jest to możliwe `Allow multiple selection` . 

Użytkownik ma także opcję określania formatu zestawu wyników za pośrednictwem `delimiter` `quote with` ustawień i. Wartość domyślna po prostu zwraca wartości jako kolekcję w tej formie: "a", "b", "c". Mogą także ograniczyć liczbę wybranych opcji.

KQL odwoływania się do parametru musi się zmienić, aby działał z formatem wyniku. Najbardziej typowym sposobem na włączenie go jest za pośrednictwem `in` operatora.

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

Oto przykład listy rozwijanej z obsługą wielodostępną w pracy:

![Obraz przedstawiający parametr listy rozwijanej z pojedynczym wybieraniem](./media/workbooks-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij](./workbooks-overview.md#visualizations) naukę więcej o skoroszytach wiele opcji rozbudowanych wizualizacji.
* [Kontroluj](./workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.