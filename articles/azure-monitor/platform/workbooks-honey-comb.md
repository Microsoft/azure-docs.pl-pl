---
title: Wizualizacje plastra miodu Azure Monitor skoroszyty
description: Dowiedz się więcej o Azure Monitor wizualizacji plastrów miodu w skoroszycie.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: lagayhar
ms.openlocfilehash: 859240623c5dd3009890cd2633ffb9cccf230080
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91347430"
---
# <a name="honey-comb-visualizations"></a>Wizualizacje plastrów miodu

Plastry miodu umożliwiają wyświetlanie widoków o wysokiej gęstości w metrykach lub kategoriach, które można opcjonalnie grupować jako klastry. Są one przydatne w wizualnie identyfikujących się punktach i drążeniu.

Poniższy obraz przedstawia użycie procesora CPU przez maszyny wirtualne w dwóch subskrypcjach. Każda komórka reprezentuje maszynę wirtualną, a kolor/etykieta reprezentuje średnie użycie procesora (Reds są gorącą maszyną). Maszyny wirtualne są klastrowane przez subskrypcję.

[![Zrzut ekranu przedstawia użycie procesora CPU przez maszyny wirtualne w ramach dwóch subskrypcji](.\media\workbooks-honey-comb\cpu-example.png)](.\media\workbooks-honey-comb\cpu-example.png#lightbox)

## <a name="adding-a-honey-comb"></a>Dodawanie plastra miodu

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi Edytowanie.
2. Użyj **Dodaj**  na dole, a następnie *Dodaj zapytanie* , aby dodać kontrolkę zapytania dziennika do skoroszytu.
3. Wybierz "Logs" jako *Źródło danych*"log Analytics" jako *Typ zasobu*, a dla punktu *zasobów* do obszaru roboczego zawierającego dziennik wydajności maszyny wirtualnej.
4. Użyj edytora zapytań, aby wprowadzić KQL do analizy.

```kusto
    Perf
| where CounterName == 'Available MBytes'
| summarize CounterValue = avg(CounterValue) by Computer, _ResourceId
| extend ResourceGroup = extract(@'/subscriptions/.+/resourcegroups/(.+)/providers/microsoft.compute/virtualmachines/.+', 1, _ResourceId)
| extend ResourceGroup = iff(ResourceGroup == '', 'On-premise computers', ResourceGroup), Id = strcat(_ResourceId, '::', Computer)
```

5. Uruchom zapytanie.
6. Ustaw *wizualizację* na "Graph".
7. Wybierz pozycję **Ustawienia grafu**.
    1. W obszarze *pola układu* u dołu ustaw:
        1. Typ grafu: **klastry Hive**.
        2. Identyfikator węzła: `Id` .
        3. Grupuj według: `None` .
        4. Rozmiar węzła: 100.
        5. Margines między sześciokątami: `5` .
        6. Typ kolorowania: **mapę cieplną**.
        7. Pole koloru węzła: `CouterValue` .
        8. Paleta kolorów: `Red to Green` .
        9. Wartość minimalna: `100` .
        10. Wartość maksymalna: `2000` .
    2. W obszarze *Ustawienia formatu węzła* u góry Ustaw:
        1. Górna zawartość:
            1. Użyj kolumny: `Computer` .
            2. Renderowanie kolumn " `Text` .
        9. Wyśrodkuj zawartość:
            1. Użyj kolumny: `CounterValue` .
            2. Renderowanie kolumn: `Big Number` .
            3. Paleta kolorów: `None` .
            4. Sprawdź pole *formatowania liczby niestandardowej* .
            5. Jednostki: `Megabytes` .
            6. Maksymalna liczba znaków ułamkowych: `1` .
8. Wybierz przycisk **Zapisz i Zamknij** w dolnej części okienka.

[![Zrzut ekranu przedstawiający kontrolkę zapytania, Ustawienia wykresu i plastra miodu z powyższym zapytaniem i ustawieniami](.\media\workbooks-honey-comb\available-memory.png)](.\media\workbooks-honey-comb\available-memory.png#lightbox)

## <a name="honey-comb-layout-settings"></a>Ustawienia układu plastra miodu

| Ustawienie | Objaśnienie |
|:------------- |:-------------|
| `Node Id` | Wybiera kolumnę, która dostarcza unikatowy identyfikator węzłów. Wartością kolumny może być ciąg lub liczba. |
| `Group By Field` | Wybierz kolumnę, w której mają być klastrowane węzły. |
| `Node Size` | Ustawia rozmiar komórek sześciokątnych. Użyj właściwości, `Margin between hexagons` Aby dostosować wygląd wykresu plastra miodu. |
| `Margin between hexagons` | Ustawia przerwy między komórkami sześciokątnymi. Użyj właściwości, `Node size` Aby dostosować wygląd wykresu plastra miodu. |
| `Coloring type` | Wybiera schemat, który ma być używany do kolorowania węzła. |
| `Node Color Field` | Wybiera kolumnę, która dostarcza metrykę, na której będą oparte obszary węzła. |

## <a name="node-coloring-types"></a>Typy kolorowania węzła

| Typ kolorowania | Objaśnienie |
|:------------- |:-------------|
| `None` | Wszystkie węzły mają ten sam kolor. |
| `Categorical` | Do węzłów są przypisywane kolory oparte na wartości lub kategorii z kolumny w zestawie wyników. W powyższym przykładzie kolorowanie jest oparte na _rodzaju_ kolumn zestawu wyników. Obsługiwane palety to `Default` , `Pastel` i `Cool tone` .  |
| `Heatmap` | W tym typie komórki są kolorowe w oparciu o kolumnę metryki i paletę kolorów. Zapewnia to prosty sposób wyróżniania metryk rozłożonych na komórki. |
| `Thresholds` | W tym typie kolory komórek są ustawiane przez reguły progu (na przykład _procesor cpu > 90% => Red, 60% > procesora > 90% => żółty, procesor cpu < 60% => zielony_) |
| `Field Based` | W tym typie kolumna zawiera określone wartości RGB, które mają być używane dla węzła. Zapewnia największą elastyczność, ale zazwyczaj wymaga więcej pracy do włączenia.  |
      
## <a name="node-format-settings"></a>Ustawienia formatu węzła

Autorzy plastrów miodu mogą określać zawartość przechodzącą do różnych części węzła: Top, Left, Center, Right i Bottom. Autorzy mogą korzystać z dowolnych skoroszytów programu renderowania (tekst, wielkie liczby, linie Spark, ikona itp.).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak utworzyć [złożony moduł renderowania słupkowego w skoroszytach](workbooks-composite-bar.md).
- Dowiedz się, jak [importować Azure monitor dane dziennika do Power BI](powerbi.md).
