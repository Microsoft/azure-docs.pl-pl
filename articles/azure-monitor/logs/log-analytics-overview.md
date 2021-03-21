---
title: Omówienie Log Analytics w Azure Monitor
description: Opisuje Log Analytics, który jest narzędziem w Azure Portal używanym do edytowania i uruchamiania zapytań dzienników na potrzeby analizowania danych w dziennikach Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/04/2020
ms.openlocfilehash: d83cc4045f09ed0f704a0d61c6b303406835eb44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052300"
---
# <a name="overview-of-log-analytics-in-azure-monitor"></a>Omówienie Log Analytics w Azure Monitor
Log Analytics jest narzędziem w Azure Portal używanym do edytowania i uruchamiania zapytań dzienników z danymi w dziennikach Azure Monitor. Można napisać proste zapytanie, które zwraca zestaw rekordów, a następnie użyć funkcji Log Analytics do sortowania, filtrowania i analizowania. Możesz też napisać bardziej zaawansowane zapytanie w celu przeprowadzenia analizy statystycznej i wizualizować wyniki na wykresie, aby zidentyfikować konkretny trend. Niezależnie od tego, czy pracujesz z wynikami zapytań interaktywnie, czy używasz ich z innymi Azure Monitormi funkcjami, takimi jak alerty lub skoroszyty zapytań, Log Analytics to narzędzie, za pomocą którego można pisać i testować. 


> [!TIP]
> Ten artykuł zawiera opis Log Analytics i każdej z jej funkcji. Jeśli chcesz przejść bezpośrednio do samouczka, zobacz [samouczek log Analytics](./log-analytics-tutorial.md).



## <a name="starting-log-analytics"></a>Uruchamianie Log Analytics
Rozpocznij Log Analytics od **dzienników** w menu **Azure monitor** w Azure Portal. Ta opcja zostanie również wyświetlona w menu dla większości zasobów platformy Azure. Niezależnie od tego, gdzie zaczynasz od, będzie to samo narzędzie Log Analytics. Menu używane do uruchamiania Log Analytics określa dane, które będą dostępne w tym przypadku. Jeśli uruchomisz go z poziomu menu **Azure monitor** lub z menu **log Analytics obszary robocze** , będziesz mieć dostęp do wszystkich rekordów w obszarze roboczym. W przypadku wybrania **dzienników** z innego typu zasobów dane będą ograniczone do danych dziennika dla tego zasobu. Aby uzyskać szczegółowe informacje [, zobacz zakres zapytań dzienników i zakres czasu w Azure Monitor Log Analytics](./scope.md) .

[![Rozpocznij Log Analytics](media/log-analytics-overview/start-log-analytics.png)](media/log-analytics-overview/start-log-analytics.png#lightbox)

Po rozpoczęciu Log Analytics w pierwszej kolejności zobaczysz okno dialogowe z [przykładowymi zapytaniami](../logs/example-queries.md). Są one podzielone na kategorie według rozwiązania i można przeglądać lub wyszukiwać zapytania, które pasują do określonych wymagań. Może być możliwe znalezienie tego, co jest potrzebne, lub załadowanie go do edytora i zmodyfikowanie go zgodnie z potrzebami. Przeglądanie za pomocą przykładowych zapytań jest w rzeczywistości doskonałym sposobem, aby dowiedzieć się, jak pisać własne zapytania. Oczywiście, jeśli chcesz zacząć od pustego skryptu i napisać go samodzielnie, możesz zamknąć przykładowe zapytania. Po prostu kliknij **zapytania** w górnej części ekranu, jeśli chcesz je odzyskać.

## <a name="log-analytics-interface"></a>Interfejs Log Analytics
Na poniższej ilustracji przedstawiono różne składniki Log Analytics.

[![Log Analytics](media/log-analytics-overview/log-analytics.png)](media/log-analytics-overview/log-analytics.png#lightbox)

### <a name="1-top-action-bar"></a>1. Górny pasek akcji
Kontrolki do pracy z kwerendą w oknie zapytania.

| Opcja | Opis |
|:---|:---|
| Zakres | Określa zakres danych używanych w zapytaniu. Może to być wszystkie dane w obszarze roboczym Log Analytics lub dane dla określonego zasobu w wielu obszarach roboczych. Zobacz [zakres zapytań](./scope.md). |
| Przycisk Uruchom | Kliknij, aby uruchomić wybrane zapytanie w oknie zapytania. Możesz również nacisnąć klawisze SHIFT + ENTER, aby uruchomić zapytanie. |
| Wybór godziny | Wybierz zakres czasu dla danych dostępnych dla zapytania. Ta wartość zostanie zastąpiona, jeśli w zapytaniu zostanie uwzględniony filtr czasu. Zobacz [zakres zapytań dzienników i zakres czasu w Azure Monitor Log Analytics](./scope.md). |
| Przycisk Zapisz | Zapisz zapytanie do Eksploratora zapytań dla obszaru roboczego. |
 Kopiuj | Skopiuj link do zapytania, tekstu zapytania lub wyników zapytania do Schowka. |
| Przycisk reguły nowego alertu | Utwórz nową kartę z pustym zapytaniem. |
| Przycisk Eksportuj | Wyeksportuj wyniki zapytania do pliku CSV lub zapytania, aby Power Query format języka formuł do użycia z usługą Power BI. |
| Przycisk Przypnij do pulpitu nawigacyjnego | Dodaj wyniki zapytania do pulpitu nawigacyjnego platformy Azure. |
| Przycisk zapytania formatowania | Rozmieść zaznaczony tekst pod kątem czytelności. |
| Przycisk przykładowych zapytań | Otwórz okno dialogowe przykładowe zapytania, które jest wyświetlane podczas pierwszego otwierania Log Analytics. |
| Przycisk Eksplorator zapytań | Otwórz **Eksploratora zapytań** , który zapewnia dostęp do zapisanych zapytań w obszarze roboczym. |


### <a name="2-sidebar"></a>2. pasek boczny
Listy tabel w obszarze roboczym, przykładowe zapytania i opcje filtru dla bieżącego zapytania.

| Tab | Opis |
|:---|:---|
| tabelami | Wyświetla listę tabel, które są częścią wybranego zakresu. Wybierz pozycję **Grupuj według** , aby zmienić grupowanie tabel. Umieść kursor nad nazwą tabeli, aby wyświetlić okno dialogowe z opisem tabeli i opcji, aby wyświetlić jej dokumentację i wyświetlić jej dane. Rozwiń tabelę, aby wyświetlić jej kolumny. Kliknij dwukrotnie nazwę tabeli lub kolumny, aby dodać ją do zapytania. |
| Zapytania | Lista przykładowych zapytań, które można otworzyć w oknie zapytania. Jest to ta sama lista, która jest wyświetlana po otwarciu Log Analytics. Wybierz pozycję **Grupuj według** , aby zmienić grupowanie zapytań. Kliknij dwukrotnie zapytanie, aby dodać je do okna zapytania, lub umieść wskaźnik myszy na nim, aby wyświetlić inne opcje. |
| Filtr | Tworzy opcje filtru na podstawie wyników zapytania. Po uruchomieniu zapytania kolumny będą wyświetlane z różnymi wartościami z wyników. Wybierz co najmniej jedną wartość, a następnie kliknij przycisk **zastosuj & Uruchom** , aby dodać polecenie **WHERE** do zapytania i uruchomić je ponownie. |

### <a name="3-query-window"></a>3. okno zapytania
Okno zapytania służy do edytowania zapytania. Obejmuje to funkcję IntelliSense dla poleceń KQL i kodowania kolorów w celu zwiększenia czytelności. Kliknij w **+** górnej części okna, aby otworzyć kolejną kartę.

Jak pojedyncze okno może zawierać wiele zapytań. Zapytanie nie może zawierać pustych wierszy, dlatego można rozdzielić wiele zapytań w oknie z co najmniej jednym pustym wierszem. Bieżące zapytanie jest jednym z kursorem umieszczonym w dowolnym miejscu.

Aby uruchomić bieżące zapytanie, kliknij przycisk **Run (Uruchom** ) lub naciśnij klawisze SHIFT + ENTER.

### <a name="4-results-window"></a>4. Okno wyników
Wyniki zapytania są wyświetlane w oknie wyników. Domyślnie wyniki są wyświetlane jako tabela. Aby wyświetlić jako wykres, wybierz pozycję **Wykres** w oknie wyników lub Dodaj polecenie **renderowania** do zapytania.

#### <a name="results-view"></a>widok wyników
Wyświetla wyniki zapytania w tabeli zorganizowanej według kolumn i wierszy. Kliknij z lewej strony wiersza, aby rozwinąć jego wartości. Kliknij przycisk listy rozwijanej **kolumny** , aby zmienić listę kolumn. Aby posortować wyniki, kliknij nazwę kolumny. Aby filtrować wyniki, kliknij lejek obok nazwy kolumny. Wyczyść filtry i zresetuj sortowanie, uruchamiając zapytanie ponownie.

Wybierz pozycję **Grupuj kolumny** , aby wyświetlić pasek grupowania powyżej wyników zapytania. Grupuj wyniki według dowolnej kolumny, przeciągając ją na pasek. Utwórz grupy zagnieżdżone w wynikach przez dodanie dodatkowych kolumn. 

#### <a name="chart-view"></a>Widok wykresu
Wyświetla wyniki jako jeden z wielu dostępnych typów wykresów. Możesz określić typ wykresu w poleceniu **renderowania** w zapytaniu lub wybrać go z listy rozwijanej **typ wizualizacji** .

| Opcja | Opis |
|:---|:---|
| **Typ wizualizacji** | Typ wykresu do wyświetlenia. |
| **Oś X** | Kolumna w wynikach do użycia dla osi X 
| **Oś Y** | Kolumna w wynikach, która ma być używana dla osi Y. Zwykle będzie to kolumna numeryczna. |
| **Podział według** | Kolumna w wynikach, która definiuje serię na wykresie. Dla każdej wartości w kolumnie jest tworzona seria. |
| **Agregacja** | Typ agregacji do wykonania na wartościach liczbowych na osi Y. |

## <a name="relationship-to-azure-data-explorer"></a>Relacja do Eksplorator danych platformy Azure
Jeśli znasz już interfejs użytkownika usługi Azure Eksplorator danych Web, Log Analytics powinien wyglądać znajomo. Dzieje się tak, ponieważ jest ona oparta na usłudze Azure Eksplorator danych i używa tego samego języka zapytań Kusto (KQL). Log Analytics dodaje funkcje specyficzne dla Azure Monitor takich jak filtrowanie według zakresu czasu i możliwość tworzenia reguły alertu na podstawie zapytania. Oba narzędzia obejmują Eksploratora, który umożliwia przechodzenie przez strukturę dostępnych tabel, ale interfejs użytkownika usługi Azure Eksplorator danych Web działa przede wszystkim z tabelami w usłudze Azure Eksplorator danych Databases, podczas gdy Log Analytics działa z tabelami w obszarze roboczym Log Analytics. 

## <a name="next-steps"></a>Następne kroki
- Zapoznaj się z [samouczkiem dotyczącym korzystania z log Analytics w Azure Portal](./log-analytics-tutorial.md).
- Zapoznaj się z [samouczkiem dotyczącym pisania zapytań](./get-started-queries.md).