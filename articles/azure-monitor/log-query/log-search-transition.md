---
title: Wyświetlanie i analizowanie danych na platformie Azure Log Analytics | Microsoft Docs
description: Pomoc dla użytkowników przeszukiwania dzienników Log Analytics w celu Azure Monitor środowiska zapytania dziennika.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2019
ms.openlocfilehash: 8abd9d7f33a07141418ad67cc2128af40ad0bd51
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91607380"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Przejście z przeszukiwania dzienników Log Analytics do dzienników Azure Monitor
Przeszukiwanie dzienników w Log Analytics zostało ostatnio zastąpione nowym doświadczeniem do analizowania dzienników Azure Monitor. Strona przeszukiwania dzienników jest obecnie nadal dostępna za pomocą elementu menu **dzienniki (klasyczne)** na stronie **obszary robocze log Analytics** w Azure Portal, ale zostanie usunięta 15 lutego 2019. W tym artykule opisano różnice między tymi dwoma środowiskami, które ułatwiają przejście z przeszukiwania dzienników. 

## <a name="filter-results-of-a-query"></a>Filtruj wyniki zapytania
W przeszukiwaniu dzienników Lista filtrów jest wyświetlana w miarę dostarczania wyników wyszukiwania. Wybierz filtr, a następnie kliknij przycisk **Zastosuj** , aby uruchomić zapytanie z wybranym filtrem.

![Filtr przeszukiwania dzienników](media/log-search-transition/filter-log-search.png)

W obszarze Dzienniki Azure Monitor wybierz opcję *Filtr (wersja zapoznawcza)* , aby wyświetlić filtry. Kliknij ikonę filtru, aby wyświetlić filtry dodawania. Wybierz filtr, a następnie kliknij przycisk **zastosuj & Uruchom** , aby uruchomić zapytanie z wybranym filtrem.

![Filtr dzienników](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Wyodrębnij pola niestandardowe 
W przeszukiwaniu dzienników można wyodrębnić [pola niestandardowe](../platform/custom-fields.md) z widoku listy, gdzie menu pola zawiera akcję _Wyodrębnij pola z tabeli_.

![Przeszukiwanie dzienników — pola wyodrębniania](media/log-search-transition/extract-fields-log-search.png)

W dziennikach Azure Monitor można wyodrębnić pola niestandardowe z widoku tabeli. Rozwiń rekord, klikając strzałkę po lewej stronie, a następnie kliknij wielokropek, aby uzyskać dostęp do akcji _Wyodrębnij pola_ .

![Dzienniki wyodrębniania pól](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Funkcje i grupy komputerów
Aby zapisać wyszukiwanie w przeszukiwaniu dzienników, wybierz pozycję **zapisane wyszukiwania** i **Dodaj** , aby podać nazwę, kategorię i tekst zapytania. Utwórz [grupę komputerów](../platform/computer-groups.md) przez dodanie aliasu funkcji.

![Zapisz przeszukiwanie dzienników](media/log-search-transition/save-search-log-search.png)

Aby zapisać bieżące zapytanie w dziennikach Azure Monitor, wybierz pozycję **Zapisz**. Zmień wartość _funkcji_ **Zapisz jako** na i podaj **alias funkcji** , aby utworzyć [funkcję](functions.md). Wybierz opcję _Zapisz to zapytanie jako grupę komputerów_ , aby użyć aliasu funkcji dla [grupy komputerów](../platform/computer-groups.md).

![Zapisz zapytanie dziennika](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Zapisane zapytania
W przeszukiwaniu dzienników zapisane zapytania są dostępne przez **zapisane wyszukiwania**elementów paska akcji. W dziennikach Azure Monitor dostęp do zapisanych zapytań z [Eksploratora zapytań](./get-started-portal.md#save-queries).

![Eksplorator zapytań](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Przechodzenie do szczegółów w wierszach podsumowania
W obszarze Wyszukiwanie w dzienniku można kliknąć wiersz w podsumowaniu zapytania, aby uruchomić kolejne zapytanie zawierające szczegółowe rekordy w tym wierszu.

![Przechodzenie do szczegółów przeszukiwania dzienników](media/log-search-transition/drilldown-search.png)

W dziennikach Azure Monitor należy zmodyfikować zapytanie, aby zwracało te rekordy. Rozwiń jeden z wierszy w wynikach i kliknij **+** obok wartości, aby dodać go do zapytania. Następnie Dodaj komentarz do polecenia **podsumowywania** i ponownie uruchom zapytanie.

![Azure Monitor przechodzenie do szczegółów](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Wykonywanie akcji
W przeszukiwaniu dzienników można [uruchomić element Runbook](../platform/action-groups.md) z wyniku wyszukiwania, wybierając pozycję **Wykonaj akcję**.

![Wykonywanie akcji](media/log-search-transition/take-action-log-search.png)

W dziennikach Azure Monitor [Utwórz alert z zapytania dziennika](../platform/alerts-log.md). Skonfiguruj grupę akcji z co najmniej jedną akcją, która będzie uruchamiana w odpowiedzi na alert.

![Grupa akcji](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat nowego [środowiska Azure monitor dzienników](get-started-portal.md).

