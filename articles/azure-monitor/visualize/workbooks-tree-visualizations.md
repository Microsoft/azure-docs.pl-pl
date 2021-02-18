---
title: Azure Monitor wizualizacje drzewa skoroszytu
description: Poznaj wszystkie Azure Monitor wizualizacje drzewa skoroszytu.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a815f4dcf7c76d4c11b4f2e227802b15f9c92a54
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619963"
---
# <a name="tree-visualizations"></a>Wizualizacje drzewa

Skoroszyty obsługują widoki hierarchiczne za pośrednictwem siatek drzewa. Drzewa pozwalają na rozwijanie niektórych wierszy do następnego poziomu dla środowiska przechodzenia do szczegółów.

W poniższym przykładzie przedstawiono wizualizacje metryk (rozmiar zestawu roboczego) jako siatkę drzewa. Poniżej znajdują się węzły najwyższego poziomu, które są węzłami usługi Azure Kubernetes Service (AKS), następnym poziomem są zasobniki, a końcowym poziomem są kontenery. Należy zauważyć, że nadal można sformatować kolumny tak jak w siatce (mapę cieplną, ikony, link). Bazowe źródło danych w tym przypadku jest obszarem roboczym Log Analytics z dziennikami AKS.

[![Zrzut ekranu przedstawiający Widok podsumowania kafelków](./media/workbooks-tree-visualizations/trees.png)](./media/workbooks-tree-visualizations/trees.png#lightbox)

## <a name="adding-a-tree-grid"></a>Dodawanie siatki drzewa
1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edytowanie_ .
2. Wybierz pozycję **Dodaj** , a następnie *Dodaj zapytanie* , aby dodać kontrolkę zapytania dziennika do skoroszytu.
3. Wybierz typ zapytania jako **Dziennik**, typ zasobu (na przykład Application Insights) i zasoby, które mają być docelowe.
4. Użyj edytora zapytań, aby wprowadzić KQL do analizy
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Ustaw wizualizację na **siatkę**
6. Wybierz przycisk **Ustawienia kolumny** , aby otworzyć okienko ustawienia
7. W sekcji **drzewo/grupowanie według ustawień** na dole Ustaw:
    * Typ drzewa: `Parent/Child`
    * Pole identyfikatora: `Id`
    * Pole identyfikatora nadrzędnego: `ParentId`
    * Pokaż Ekspander na: `Name`
    * Zaznacz pole wyboru *Rozwiń najwyższy poziom drzewa* .
8. W sekcji _kolumny_ w górnej części Ustaw:
    * Renderowanie kolumny _identyfikatora_ :`Hidden`
    * Moduł renderowania kolumn _nadrzędnych_ :`Hidden`
    * _Żądania_ -Column renderujące: `Bar` , Color: `Blue` , wartość minimalna: `0`
9. Wybierz przycisk **Zapisz i Zamknij** w dolnej części okienka.

[![Zrzut ekranu przedstawiający Widok podsumowania kafelków z powyższym zapytaniem i ustawieniami.](./media/workbooks-tree-visualizations/tree-settings.png)](./media/workbooks-tree-visualizations/tree-settings.png#lightbox)

## <a name="tree-settings"></a>Ustawienia drzewa

| Ustawienie | Wyjaśnienie |
|:------------- |:-------------|
| `Id Field` | Unikatowy identyfikator każdego wiersza w siatce. |
| `Parent Id Field` | Identyfikator elementu nadrzędnego bieżącego wiersza. |
| `Show the expander on` | Kolumna, w której ma zostać wyświetlony Ekspander drzewa. Siatka drzewa jest często ukryta, aby ukryć swoje identyfikatory i pola identyfikatorów nadrzędnych, ponieważ nie są one bardzo czytelne. Zamiast tego Ekspander pojawia się w polu z bardziej czytelną wartością, taką jak nazwa jednostki. |
| `Expand the top level of the tree` | Jeśli ta opcja jest zaznaczona, Siatka drzewa zostanie rozwinięta na najwyższego poziomu. Przydatne, jeśli chcesz domyślnie wyświetlić więcej informacji. |

## <a name="grouping-in-a-grid"></a>Grupowanie w siatce

Grupowanie pozwala tworzyć widoki hierarchiczne podobne do powyższych w przypadku znacznie prostszego wykonywania zapytań. W węzłach wewnętrznych drzewa utracisz agregację, ale będzie to możliwe do zastosowania w niektórych scenariuszach. Użyj opcji *Grupuj według* w celu utworzenia widoków drzewa, gdy podstawowy zestaw wyników nie może zostać przekształcony w odpowiedni, bezpłatny formularz, na przykład: alert, kondycja i dane metryki.

## <a name="adding-a-tree-using-grouping"></a>Dodawanie drzewa przy użyciu grupowania

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edytowanie_ .
2. Wybierz pozycję **Dodaj** , a następnie *Dodaj zapytanie* , aby dodać kontrolkę zapytania dziennika do skoroszytu.
3. Wybierz typ zapytania jako **Dziennik**, typ zasobu (na przykład Application Insights) i zasoby, które mają być docelowe.
4. Użyj edytora zapytań, aby wprowadzić KQL do analizy
    ```kusto
    requests
    | summarize Requests = count() by App = appName, RequestName = name
    | order by Requests desc
    ```
1. Ustaw wizualizację na *siatkę*.
2. Wybierz **przycisk Ustawienia kolumny** , aby otworzyć okienko ustawienia.
3. W sekcji *drzewo/grupowanie według ustawień* na dole Ustaw:
    * Typ drzewa: `Group By`
    * Grupuj według: `App`
    * Następnie według: `None`
    * Zaznacz pole wyboru *Rozwiń najwyższy poziom drzewa* .
4. W sekcji *kolumny* w górnej części Ustaw:
    * Renderowanie w kolumnie *App* -Column:`Hidden`
    * *Żądania* -Column renderujące: `Bar` , Color: `Blue` , wartość minimalna: `0`
5. Wybierz przycisk **Zapisz i Zamknij** w dolnej części okienka.

[![Zrzut ekranu przedstawiający tworzenie wizualizacji drzewa w skoroszytach](./media/workbooks-tree-visualizations/tree-group-create.png)](./media/workbooks-tree-visualizations/tree-group-create.png#lightbox)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć [Wykres w skoroszytach](workbooks-graph-visualizations.md).
* Dowiedz się, jak utworzyć [kafelek w skoroszytach](workbooks-tile-visualizations.md).
