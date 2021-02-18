---
title: Renderowanie złożonego paska Azure Monitor skoroszytu
description: Dowiedz się więcej na temat wizualizacji modułu renderowania słupków złożonych w skoroszycie Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 9/04/2020
ms.author: lagayhar
ms.openlocfilehash: 3bcbd2a58764003c174c02ec637050b57632048a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100618873"
---
# <a name="composite-bar-renderer"></a>Moduł renderowania złożonego paska

Skoroszyt umożliwia renderowanie danych przy użyciu paska złożonego, który składa się z wielu słupków.

Poniższy obraz pokazuje pasek złożony dla stanu bazy danych reprezentujący liczbę serwerów w trybie online, offline i w stanie odzyskiwania.

![Zrzut ekranu przedstawiający pasek złożony dla stanu bazy danych.](./media/workbooks-composite-bar/database-status.png)

Program renderowania słupków złożonych jest obsługiwany dla wizualizacji, kafelków i grafów.

## <a name="adding-composite-bar-renderer"></a>Dodawanie modułu renderowania słupków złożonych

1. Przełącz skoroszyt do trybu edycji, wybierając pozycję *Edytuj* element paska narzędzi.
2. Wybierz pozycję *Dodaj* , a następnie *Dodaj zapytanie*.
3. Ustaw *Źródło danych* na "JSON" i *wizualizację* do "Grid".
4. Dodaj następujące dane JSON.

```json
[
    {"sub":"X", "server": "A", "online": 20, "recovering": 3, "offline": 4, "total": 27},
    {"sub":"X", "server": "B", "online": 15, "recovering": 8, "offline": 5, "total": 28},
    {"sub":"Y", "server": "C", "online": 25, "recovering": 4, "offline": 5, "total": 34},
    {"sub":"Y", "server": "D", "online": 18, "recovering": 6, "offline": 9, "total": 33}
]
```

5. Uruchom zapytanie.
6. Wybierz pozycję **Ustawienia kolumny** , aby otworzyć ustawienia.
7. Wybierz pozycję "łącznie" z *kolumn* i wybierz pozycję "pasek złożony" dla *modułu renderowania kolumn*.
8. Ustaw następujące ustawienia w obszarze *Ustawienia paska złożonego*.

| Nazwa kolumny | Kolor        |
|-------------|--------------|
| online      | Green (Zielony)        |
| odzyskiwania  | Yellow       |
| tryb offline     | Czerwony (jasny) |

9. Dodaj etykietę:`["online"] of ["total"] are healthy`
10. W ustawieniach kolumny dla trybu online, offline i odzyskiwania można ustawić moduł renderowania kolumn na wartość "Hidden" (opcjonalnie).
11. Wybierz pozycję *etykiety* u góry i zaktualizuj etykietę kolumny suma jako "stan bazy danych" (opcjonalnie).
12. Wybierz pozycję **Zastosuj**

Ustawienia słupków złożonych będą wyglądać podobnie jak na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający Ustawienia kolumny słupków złożonych z ustawieniami opisanymi powyżej.](./media/workbooks-composite-bar/composite-bar-settings.png)

Pasek złożony z powyższymi ustawieniami:

![Zrzut ekranu przedstawiający pasek złożony.](./media/workbooks-composite-bar/composite-bar.png)

## <a name="composite-bar-settings"></a>Ustawienia paska złożonego

Wybierz pozycję Nazwa kolumny i odpowiedni kolor, aby renderować tę kolumnę w tym kolorze jako część paska złożonego. Można wstawiać, usuwać i przenosić wiersze w górę i w dół.

### <a name="label"></a>Etykieta

Etykieta paska złożonego jest wyświetlana w górnej części paska złożonego. Można użyć mieszanki tekstu statycznego, kolumn i parametru.  Jeśli etykieta jest pusta, wartość bieżących kolumn jest wyświetlana jako etykieta. W poprzednim przykładzie po lewej stronie pola Etykieta zostanie wyświetlona wartość całkowita liczba kolumn.

Zapoznaj się z kolumnami z `["columnName"]` .

Zapoznaj się z parametrami `{paramName}` .

W obu nazwach kolumn i nazwach parametrów jest rozróżniana wielkość liter. Możesz również utworzyć łącze do etykiet, zaznaczając opcję "Uczyń ten element jako link", a następnie dodając ustawienia łącza.

### <a name="aggregation"></a>Agregacja

Agregacje są przydatne dla drzewa/grupowania według wizualizacji. Dane dla kolumny dla wiersza grupy są określane przez zestaw agregacji dla tej kolumny. Istnieją trzy typy agregacji, które mają zastosowanie w przypadku słupków złożonych: None, sum i Inherit.

Aby dodać grupę według ustawień:

1. W obszarze Ustawienia kolumny przejdź do kolumny, do której chcesz dodać ustawienia.
2. W obszarze *drzewo/grupowanie według ustawień* w obszarze *Typ drzewa* wybierz pozycję **Grupuj według**
3. Wybierz pole, według którego chcesz grupować.

![Zrzut ekranu przedstawiający ustawienia Grupuj według.](./media/workbooks-composite-bar/group-by-settings.png)

#### <a name="none"></a>Brak

Agregacja brak oznacza, że nie są wyświetlane żadne wyniki dla tej kolumny dla wierszy grupy.

![Zrzut ekranu przedstawiający pasek złożony z agregacją none.](./media/workbooks-composite-bar/none.png)

#### <a name="sum"></a>Sum

Jeśli agregacja jest ustawiona jako sum, kolumna w wierszu grupy będzie wyświetlać pasek złożony przy użyciu sumy kolumn używanych do ich renderowania. Etykieta będzie również używać sumy kolumn, do których się odwołuje.

W przykładzie poniżej trybu online, offline i odzyskiwania wszystkie mają ustawioną maksymalną agregację, a agregacją dla kolumny sum jest suma.

![Zrzut ekranu przedstawiający pasek złożony z agregacją sum.](./media/workbooks-composite-bar/sum.png)

#### <a name="inherit"></a>Odziedziczony

Jeśli agregacja jest ustawiona jako inherit, kolumna w wierszu grupy będzie wyświetlać pasek złożony przy użyciu agregacji ustawionej przez użytkowników dla kolumn używanych do renderowania. Kolumny używane w etykiecie również używają agregacji ustawionej przez użytkownika. Jeśli bieżący pasek renderowania kolumn jest paskiem złożonym i jest on uznawany za etykietę (taką jak "łącznie" w powyższym przykładzie), suma jest używana jako agregacja dla tej kolumny.

W poniższym przykładzie w trybie online, offline i odzyskiwania wszystkie mają ustawioną maksymalną agregację, a agregacja kolumny sum jest dziedziczona.

![Zrzut ekranu przedstawiający pasek złożony z dziedziczeniem agregacji.](./media/workbooks-composite-bar/inherit.png)

## <a name="sorting"></a>Sortowanie

W przypadku wizualizacji siatki Sortowanie wierszy kolumny przy użyciu modułu renderowania słupków złożonych działa w oparciu o wartość będącą sumą kolumn używanych do dynamicznego renderowania zbiorczego komputera słupkowego. W poprzednich przykładach wartość użyta do sortowania to suma wartości online, odzyskiwania i kolumn offline dla danego wiersza.

## <a name="tiles-visualization"></a>Wizualizacja kafelków

1. Wybierz pozycję **Dodaj** i *Dodaj zapytanie*.
2. Zmień źródło danych na JSON, wprowadzając dane z [poprzedniego przykładu](#adding-composite-bar-renderer).
3. Zmień wizualizację na *kafelki*.
4. Uruchom zapytanie.
5. Wybierz pozycję **Ustawienia kafelka**.
6. Zaznacz *pole* na kafelkach.
7. Wprowadź poniższe ustawienia w obszarze *Ustawienia pola*.
    1. Użyj kolumny: "serwer".
    2. Renderowanie kolumn: "text".
8. Wybierz pozycję *dolny* w polach kafelków.
9. Wprowadź poniższe ustawienia w obszarze *Ustawienia pola*.
    1. Użyj kolumny: "Total".
    2. Renderowanie kolumn: "pasek złożony".
    3. Wprowadź ustawienia następujących ustawień w obszarze "Ustawienia paska złożonego".

    | Nazwa kolumny | Kolor        |
    |-------------|--------------|
    | online      | Green (Zielony)        |
    | odzyskiwania  | Yellow       |
    | tryb offline     | Czerwony (jasny) |

    4. Dodaj etykietę: `["online"] of ["total"] are healthy` .
10. Wybierz przycisk **Zastosuj**.

Ustawienia słupków złożonych dla kafelków:

![Zrzut ekranu przedstawiający ustawienia kafelków złożonego paska z ustawieniami opisanymi powyżej.](./media/workbooks-composite-bar/tiles-settings.png)

Widok paska złożonego dla kafelków z powyższymi ustawieniami będzie wyglądać następująco:

![Zrzut ekranu przedstawiający złożone kafelki słupkowe.](./media/workbooks-composite-bar/composite-bar-tiles.png)

## <a name="graphs-visualization"></a>Wizualizacja grafów

Aby wykonać renderowanie złożonego paska dla wizualizacji wykresów (wpisz klastry Hive), postępuj zgodnie z poniższymi instrukcjami.

1. Wybierz pozycję **Dodaj** i *Dodaj zapytanie*.
2. Zmień źródło danych na JSON, wprowadzając dane z [poprzedniego przykładu](#adding-composite-bar-renderer).
3. Zmień wizualizację na *grafy*.
4. Uruchom zapytanie.
5. Wybierz pozycję **Ustawienia grafu**.
6. Wybierz pozycję *Wyśrodkuj zawartość* w ustawieniach formatu węzła.
7. Wprowadź poniższe ustawienia w obszarze *Ustawienia pola*.
    1. Użyj kolumny: "Total".
    2. Renderowanie kolumn: "pasek złożony".
    3. Wprowadź następujące ustawienia w obszarze *Ustawienia paska złożonego*.

    |Nazwa kolumny  |     Kolor    |
    |-------------|--------------|
    | online      | Green (Zielony)        |
    | odzyskiwania  | Yellow       |
    | tryb offline     | Czerwony (jasny) |

   4. Dodaj etykietę: `["online"] of ["total"] are healthy` .
9. Wprowadź poniższe ustawienia w obszarze *Ustawienia układu*.
    1. Typ grafu: **klastry Hive**.
    2. IDENTYFIKATOR węzła: "serwer".
    3. Pole Grupuj według: "Brak".
    4. Rozmiar węzła: 100.
    5. Margines między sześciokątami: 5.
    6. Typ kolorowania typ: **Brak**.
1. Wybierz przycisk **Zastosuj**.
    
Ustawienia słupków złożonych dla wykresów:

![Zrzut ekranu przedstawiający ustawienia grafu złożonego słupkowego z ustawieniami opisanymi powyżej.](./media/workbooks-composite-bar/graphs-settings.png)

Widok paska złożonego dla programu Graph z powyższymi ustawieniami będzie wyglądać następująco:

![Zrzut ekranu przedstawiający wykresy słupkowe złożone z klastrami Hive.](./media/workbooks-composite-bar/composite-bar-graphs.png)

## <a name="next-steps"></a>Następne kroki

* [Wdróż](../visualize/workbooks-automate.md) skoroszyty przy użyciu Azure Resource Manager.
* [Kontroluj](../platform/workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.
