---
title: Filtry w widokach Azure Monitor | Microsoft Docs
description: Filtr w widoku Azure Monitor umożliwia użytkownikom filtrowanie danych w widoku przez wartość określonej właściwości bez modyfikowania widoku.  W tym artykule opisano sposób używania filtra i dodawania go do widoku niestandardowego.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: b4840ed30eb1f6dc8d6e6cef47da17807f9644d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658578"
---
# <a name="filters-in-azure-monitor-views"></a>Filtry w widokach Azure Monitor
**Filtr** w [widoku Azure monitor](view-designer.md) umożliwia użytkownikom filtrowanie danych w widoku przez wartość określonej właściwości bez modyfikowania widoku.  Można na przykład umożliwić użytkownikom widoku filtrowanie widoku danych tylko z określonego komputera lub zestawu komputerów.  Można utworzyć wiele filtrów w jednym widoku, aby umożliwić użytkownikom filtrowanie według wielu właściwości.  W tym artykule opisano sposób używania filtra i dodawania go do widoku niestandardowego.

## <a name="using-a-filter"></a>Korzystanie z filtru
Kliknij zakres dat w górnej części widoku, aby otworzyć listę rozwijaną, w której można zmienić zakres dat dla widoku.

![Przykład filtru](media/view-designer-filters/filters-example-time.png)

Kliknij, **+** aby dodać filtr przy użyciu filtrów niestandardowych, które są zdefiniowane dla tego widoku. Wybierz wartość filtru z listy rozwijanej lub wpisz wartość. Kontynuuj Dodawanie filtrów, klikając przycisk **+**. 


![Przykład filtru](media/view-designer-filters/filters-example-custom.png)

Jeśli usuniesz wszystkie wartości filtru, ten filtr nie zostanie już zastosowany.


## <a name="creating-a-filter"></a>Tworzenie filtru

Utwórz filtr na karcie **filtry** podczas [edytowania widoku](view-designer.md).  Filtr jest globalny dla widoku i ma zastosowanie do wszystkich części w widoku.  

![Ustawienia filtru](media/view-designer-filters/filters-settings.png)

W poniższej tabeli opisano ustawienia filtru.

| Ustawienie | Opis |
|:---|:---|
| Nazwa pola | Nazwa pola używanego do filtrowania.  To pole musi być zgodne z polem podsumowania w **zapytaniu o wartości**. |
| Zapytanie o wartości | Zapytanie do uruchomienia, aby wypełnić listę rozwijaną filtru dla użytkownika.  To zapytanie musi używać wartości [podsumowujących](/azure/kusto/query/summarizeoperator) lub [odrębnych](/azure/kusto/query/distinctoperator) w celu zapewnienia unikatowości dla określonego pola i musi odpowiadać **nazwie pola**.  Możesz użyć [sortowania](/azure/kusto/query/sortoperator) , aby posortować wartości, które są wyświetlane użytkownikowi. |
| Tag | Nazwa pola, które jest używane w zapytaniach obsługujących filtr, i jest również wyświetlana użytkownikowi. |

### <a name="examples"></a>Przykłady

Poniższa tabela zawiera kilka przykładów typowych filtrów.  

| Nazwa pola | Zapytanie o wartości | Tag |
|:--|:--|:--|
| Computer (Komputer)   | Puls &#124; różne komputery &#124; Sortuj według komputera ASC | Komputery |
| EventLevelName | Zdarzenie &#124; DISTINCT EventLevelName | Ważność |
| SeverityLevel | Dziennik systemowy &#124; DISTINCT SeverityLevel | Ważność |
| SvcChangeType | Zmianakonfiguracji &#124; DISTINCT svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Modyfikuj zapytania widoku

Aby filtr miał efekt, należy zmodyfikować wszystkie zapytania w widoku w celu odfiltrowania wybranych wartości.  Jeśli nie zmodyfikujesz żadnych zapytań w widoku, wszelkie wartości wybrane przez użytkownika nie będą miały żadnego efektu.

Składnia używana przez wartość filtru w zapytaniu to: 

    where ${filter name}  

Na przykład jeśli widok zawiera zapytanie zwracające zdarzenia i korzysta z filtru o nazwie _Computers_, można użyć poniższego zapytania.

    Event | where ${Computers} | summarize count() by EventLevelName

Jeśli dodano inny filtr o nazwie ważność, można użyć następującego zapytania, aby użyć obu filtrów.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [częściach wizualizacji](view-designer-parts.md) , które można dodać do widoku niestandardowego.
