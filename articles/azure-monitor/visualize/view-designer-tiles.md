---
title: Przewodnik odwołujący do kafelków projektanta widoków w Azure Monitor | Microsoft Docs
description: Za pomocą projektanta widoków w Azure Monitor można utworzyć niestandardowe widoki, które są wyświetlane w Azure Portal i zawierać różne wizualizacje danych w obszarze roboczym Log Analytics. Ten artykuł zawiera Przewodnik dotyczący ustawień kafelków, które są dostępne w niestandardowych widokach.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2018
ms.openlocfilehash: d1d0da70dc1e47d0a1ddb90abbed2eaea83919cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040179"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Przewodnik dotyczący wyświetlania kafelków projektanta w Azure Monitor
Za pomocą projektanta widoków w Azure Monitor można utworzyć różne widoki niestandardowe w Azure Portal, które mogą ułatwić wizualizację danych w obszarze roboczym Log Analytics. Ten artykuł zawiera Przewodnik dotyczący ustawień kafelków, które są dostępne w niestandardowych widokach.

Aby uzyskać więcej informacji na temat projektanta widoków, zobacz:

* [Projektant widoków](view-designer.md): zawiera omówienie projektanta widoków i procedur tworzenia i edytowania widoków niestandardowych.
* [Dokumentacja części wizualizacji](view-designer-parts.md): zawiera Przewodnik referencyjny dotyczący ustawień części wizualizacji, które są dostępne w niestandardowych widokach.


Dostępne kafelki projektanta widoku są opisane w poniższej tabeli:  

| Kafelek | Opis |
|:--- |:--- |
| [Liczba](#number-tile) |Liczba rekordów z zapytania. |
| [Dwie liczby](#two-numbers-tile) |Liczba rekordów z dwóch różnych zapytań. |
| [Pierścień](#donut-tile) | Wykres oparty na zapytaniu z wartością podsumowania w środku. |
| Wykres liniowy i objaśnienie | Wykres liniowy oparty na zapytaniu i objaśnienie z wartością podsumowania. |
| [Wykres liniowy](#line-chart-tile) |Wykres liniowy, który jest oparty na zapytaniu. |
| [Dwie osie czasu](#two-timelines-tile) | Wykres kolumnowy z dwiema seriami, każdy na podstawie oddzielnego zapytania. |

W następnych sekcjach szczegółowo opisano typy kafelków i ich właściwości.

> [!NOTE]
> Kafelki w widokach opierają się na [zapytaniach dzienników](../logs/log-query-overview.md) w obszarze roboczym log Analytics. Nie obsługują one obecnie [zapytań między zasobami](../logs/cross-workspace-query.md) w celu pobierania danych z Application Insights.

## <a name="number-tile"></a>Kafelek liczb
Kafelek **Liczba** wyświetla zarówno liczbę rekordów z kwerendy dziennika, jak i etykiety.

![Kafelek liczb](media/view-designer-tiles/tile-number.png)

| Ustawienie | Opis |
|:--- |:--- |
| Nazwa |Tekst wyświetlany w górnej części kafelka. |
| Opis |Tekst wyświetlany pod nazwą kafelka. |
| **Kafelek** | |
| Legenda |Tekst wyświetlany w polu wartość. |
| Zapytanie |Uruchomione zapytanie. Zostanie wyświetlona liczba rekordów zwracanych przez zapytanie. |
| **Zaawansowany** |**> weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz ten link, jeśli dla kafelka powinna być włączona weryfikacja przepływu danych. Takie podejście zapewnia alternatywny komunikat, jeśli dane są niedostępne. Zwykle używasz podejścia do dostarczania komunikatu w okresie tymczasowym, gdy widok zostanie zainstalowany, a dane staną się dostępne. |
| Zapytanie |Zapytanie, które jest uruchamiane w celu określenia, czy dane są dostępne dla widoku. Jeśli zapytanie nie zwraca żadnych wyników, zamiast wartości głównej kwerendy zostanie wyświetlony komunikat. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli zapytanie weryfikacyjne przepływu danych nie zwraca żadnych danych. Jeśli nie podano komunikatu, zostanie wyświetlony komunikat o stanie *wykonywanie oceny* . |


## <a name="two-numbers-tile"></a>Kafelek z dwoma liczbami
Ten kafelek przedstawia liczbę rekordów z dwóch różnych zapytań dzienników i etykietę dla każdej z nich.

![Kafelek z dwoma liczbami](media/view-designer-tiles/tile-two-numbers.png)

| Ustawienie | Opis |
|:--- |:--- |
| Nazwa |Tekst wyświetlany w górnej części kafelka. |
| Opis |Tekst wyświetlany pod nazwą kafelka. |
| **Pierwszy kafelek** | |
| Legenda |Tekst wyświetlany w polu wartość. |
| Zapytanie |Uruchomione zapytanie. Zostanie wyświetlona liczba rekordów zwracanych przez zapytanie. |
| **Drugi kafelek** | |
| Legenda |Tekst wyświetlany w polu wartość. |
| Zapytanie |Uruchomione zapytanie. Zostanie wyświetlona liczba rekordów zwracanych przez zapytanie. |
| **Zaawansowany** |**> weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz ten link, jeśli dla kafelka powinna być włączona weryfikacja przepływu danych. Takie podejście zapewnia alternatywny komunikat, jeśli dane są niedostępne. Zwykle używasz podejścia do dostarczania komunikatu w okresie tymczasowym, gdy widok zostanie zainstalowany, a dane staną się dostępne. |
| Zapytanie |Zapytanie, które jest uruchamiane w celu określenia, czy dane są dostępne dla widoku. Jeśli zapytanie nie zwraca żadnych wyników, zamiast wartości głównej kwerendy zostanie wyświetlony komunikat. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli zapytanie weryfikacyjne przepływu danych nie zwraca żadnych danych. Jeśli nie podano komunikatu, zostanie wyświetlony komunikat o stanie *wykonywanie oceny* . |


## <a name="donut-tile"></a>Kafelek pierścieniowy
Kafelek **pierścieniowy** Wyświetla pojedynczą liczbę, która podsumowuje kolumnę wartości w zapytaniu dziennika. Pierścień wyświetla graficznie wyniki trzech pierwszych rekordów.

![Kafelek pierścieniowy](media/view-designer-tiles/tile-donut.png)

| Ustawienie | Opis |
|:--- |:--- |
| Nazwa |Tekst wyświetlany w górnej części kafelka. |
| Opis |Tekst wyświetlany pod nazwą kafelka. |
| **Pierścień** | |
| Zapytanie |Zapytanie, które jest uruchamiane dla pierścienia. Pierwsza właściwość jest wartością tekstową, a druga właściwość jest wartością numeryczną. To zapytanie zwykle używa słowa kluczowego *Measure* do podsumowywania wyników. |
| **Pierścień** |**Centrum>** |
| Tekst |Tekst wyświetlany w obszarze wartości w pierścieniu. |
| Operacja |Operacja wykonywana na właściwości Value, która podsumowuje ją jako pojedynczą wartość.<ul><li>Sum: Dodaj wartości wszystkich rekordów z wartością właściwości.</li><li>Wartość procentowa: procent wartości zsumowanych z rekordów z wartością właściwości w porównaniu do wartości zsumowanych wszystkich rekordów.</li></ul> |
| Wartości wynikowe używane w środku operacji |Opcjonalnie wybierz znak plus (+), aby dodać jedną lub więcej wartości. Wyniki zapytania są ograniczone do rekordów o określonych wartościach właściwości. Jeśli nie zostaną dodane żadne wartości, wszystkie rekordy zostaną uwzględnione w zapytaniu. |
| **Pierścień** |**> dodatkowe opcje** |
| Kolory |Kolor wyświetlany dla każdej z trzech najważniejszych właściwości. Aby określić alternatywne kolory dla konkretnych wartości właściwości, użyj *zaawansowanego mapowania kolorów*. |
| Zaawansowane mapowanie kolorów |Wyświetla kolor reprezentujący określone wartości właściwości. Jeśli określona wartość znajduje się w pierwszych trzech, zamiast koloru standardowego zostanie wyświetlony kolor alternatywny. Jeśli właściwość nie znajduje się w trzech pierwszych, kolor nie jest wyświetlany. |
| **Zaawansowany** |**> weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz ten link, jeśli dla kafelka powinna być włączona weryfikacja przepływu danych. Takie podejście zapewnia alternatywny komunikat, jeśli dane są niedostępne. Zwykle używasz podejścia do dostarczania komunikatu w okresie tymczasowym, gdy widok zostanie zainstalowany, a dane staną się dostępne. |
| Zapytanie |Zapytanie, które jest uruchamiane w celu określenia, czy dane są dostępne dla widoku. Jeśli zapytanie nie zwraca żadnych wyników, zamiast wartości głównej kwerendy zostanie wyświetlony komunikat. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli zapytanie weryfikacyjne przepływu danych nie zwraca żadnych danych. Jeśli nie podano komunikatu, zostanie wyświetlony komunikat o stanie *wykonywanie oceny* . |


## <a name="line-chart-tile"></a>Kafelek wykresu liniowego
Ten kafelek to wykres liniowy, który wyświetla wiele serii z zapytania dziennika w czasie. 

![Zrzut ekranu przedstawiający kafelek wykresu liniowego w projektancie widoków Azure Monitor.](media/view-designer-tiles/tile-line-chart.png)

| Ustawienie | Opis |
|:--- |:--- |
| Nazwa |Tekst wyświetlany w górnej części kafelka. |
| Opis |Tekst wyświetlany pod nazwą kafelka. |
| **Wykres liniowy** | |
| Zapytanie |Zapytanie, które jest uruchamiane dla wykresu liniowego. Pierwsza właściwość jest wartością tekstową, a druga właściwość jest wartością numeryczną. To zapytanie zwykle używa słowa kluczowego *Measure* do podsumowywania wyników. Jeśli zapytanie używa słowa kluczowego *Interval* , oś x używa tego przedziału czasowego. Jeśli zapytanie nie używa słowa kluczowego *Interval* , oś x używa interwałów godzinowych. |
| **Wykres liniowy** |**> oś Y** |
| Użyj skali logarytmicznej |Wybierz ten link, aby użyć skali logarytmicznej dla osi y. |
| Lekcji |Określ jednostki dla wartości zwracanych przez zapytanie. Te informacje służą do wyświetlania etykiet na wykresie wskazujących typy wartości i opcjonalnie na potrzeby konwertowania wartości. **Typ jednostki** Określa kategorię jednostki i definiuje bieżące dostępne wartości **typu jednostki** . W przypadku wybrania wartości w **konwersji na** następnie wartości liczbowe są konwertowane z bieżącego typu **jednostki** na typ **konwersji na** . |
| Etykieta niestandardowa |Tekst wyświetlany dla osi y obok etykiety dla typu *jednostki* . Jeśli etykieta nie zostanie określona, zostanie wyświetlony tylko typ *jednostki* . |
| **Zaawansowany** |**> weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz ten link, jeśli dla kafelka powinna być włączona weryfikacja przepływu danych. Takie podejście zapewnia alternatywny komunikat, jeśli dane są niedostępne. Zwykle używasz podejścia do dostarczania komunikatu w okresie tymczasowym, gdy widok zostanie zainstalowany, a dane staną się dostępne. |
| Zapytanie |Zapytanie, które jest uruchamiane w celu określenia, czy dane są dostępne dla widoku. Jeśli zapytanie nie zwraca żadnych wyników, zamiast wartości głównej kwerendy zostanie wyświetlony komunikat. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli zapytanie weryfikacyjne przepływu danych nie zwraca żadnych danych. Jeśli nie podano komunikatu, zostanie wyświetlony komunikat o stanie *wykonywanie oceny* . |


## <a name="line-chart-and-callout-tile"></a>Kafelek wykres liniowy i objaśnienie
Ten kafelek ma zarówno wykres liniowy, który wyświetla wiele serii z zapytania dziennika w czasie, jak i objaśnienie ze zagregowaną wartością. 

![Zrzut ekranu przedstawiający wykres liniowy i kafelek z objaśnieniem w projektancie widoków Azure Monitor. Objaśnienie rozszerza wykres liniowy, wyświetlając wartość podsumowania.](media/view-designer-tiles/tile-line-chart-callout.png)

| Ustawienie | Opis |
|:--- |:--- |
| Nazwa |Tekst wyświetlany w górnej części kafelka. |
| Opis |Tekst wyświetlany pod nazwą kafelka. |
| **Wykres liniowy** | |
| Zapytanie |Zapytanie, które jest uruchamiane dla wykresu liniowego. Pierwsza właściwość jest wartością tekstową, a druga właściwość jest wartością numeryczną. To zapytanie zwykle używa słowa kluczowego *Measure* do podsumowywania wyników. Jeśli zapytanie używa słowa kluczowego *Interval* , oś x używa tego przedziału czasowego. Jeśli zapytanie nie używa słowa kluczowego *Interval* , oś x używa interwałów godzinowych. |
| **Wykres liniowy** |**Objaśnienie>** |
| Tytuł objaśnienia | Tekst wyświetlany powyżej wartości objaśnienia. |
| Nazwa serii |Wartość właściwości serii, która ma być używana jako wartość objaśnienia. Jeśli nie podano żadnej serii, są używane wszystkie rekordy z zapytania. |
| Operacja |Operacja wykonywana na właściwości Value, która podsumowuje ją jako pojedynczą wartość dla objaśnienia.<ul><li>Średnia: średnia wartości ze wszystkich rekordów.</li><li>Liczba: liczba wszystkich rekordów zwracanych przez zapytanie.</li><li>Ostatnia próbka: wartość ostatniego interwału, który znajduje się na wykresie.</li><li>Max: maksymalna wartość interwałów uwzględnionych na wykresie.</li><li>Minimum: minimalna wartość interwałów uwzględnionych na wykresie.</li><li>Sum: suma wartości ze wszystkich rekordów.</li></ul> |
| **Wykres liniowy** |**> oś Y** |
| Użyj skali logarytmicznej |Wybierz ten link, aby użyć skali logarytmicznej dla osi y. |
| Lekcji |Określ jednostki wartości, które mają być zwracane przez zapytanie. Te informacje służą do wyświetlania etykiet wykresu wskazujących typy wartości i, opcjonalnie, do konwersji wartości. Typ *jednostki* Określa kategorię jednostki i definiuje dostępne bieżące wartości typu *jednostki* . W przypadku wybrania wartości w *konwersji na*, wartości liczbowe są konwertowane z bieżącego typu *jednostki* na typ *konwersji na* . |
| Etykieta niestandardowa |Tekst wyświetlany dla osi y obok etykiety dla typu *jednostki* . Jeśli etykieta nie zostanie określona, zostanie wyświetlony tylko typ *jednostki* . |
| **Zaawansowany** |**> weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz ten link, jeśli dla kafelka powinna być włączona weryfikacja przepływu danych. Takie podejście zapewnia alternatywny komunikat, jeśli dane są niedostępne. Zwykle używasz podejścia do dostarczania komunikatu w okresie tymczasowym, gdy widok zostanie zainstalowany, a dane staną się dostępne. |
| Zapytanie |Zapytanie, które jest uruchamiane w celu określenia, czy dane są dostępne dla widoku. Jeśli zapytanie nie zwraca żadnych wyników, zamiast wartości głównej kwerendy zostanie wyświetlony komunikat. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli zapytanie weryfikacyjne przepływu danych nie zwraca żadnych danych. Jeśli nie podano komunikatu, zostanie wyświetlony komunikat o stanie *wykonywanie oceny* . |


## <a name="two-timelines-tile"></a>Kafelek dwóch osi czasu
Kafelek **dwie osie czasu** wyświetla wyniki dwóch zapytań dzienników w czasie jako wykresy kolumnowe. Dla każdej serii jest wyświetlane objaśnienie. 

![Kafelek dwóch osi czasu](media/view-designer-tiles/tile-two-timelines.png)

| Ustawienie | Opis |
|:--- |:--- |
| Nazwa |Tekst wyświetlany w górnej części kafelka. |
| Opis |Tekst wyświetlany pod nazwą kafelka. |
| Pierwszy wykres | |
| Legenda |Tekst wyświetlany w objaśnieniu dla pierwszej serii. |
| Kolor |Kolor używany dla kolumn w pierwszej serii. |
| Zapytanie wykresu |Zapytanie, które jest uruchamiane dla pierwszej serii. Liczba rekordów dla każdego przedziału czasu jest reprezentowana przez kolumny wykresu. |
| Operacja |Operacja wykonywana na właściwości Value, która podsumowuje ją jako pojedynczą wartość dla objaśnienia.<ul><li>Średnia: średnia wartości ze wszystkich rekordów.</li><li>Liczba: liczba wszystkich rekordów zwracanych przez zapytanie.</li><li>Ostatnia próbka: wartość ostatniego interwału, który znajduje się na wykresie.</li><li>Max: maksymalna wartość interwałów uwzględnionych na wykresie.</li></ul> |
| **Drugi wykres** | |
| Legenda |Tekst wyświetlany w objaśnieniu dla drugiej serii. |
| Kolor |Kolor używany dla kolumn w drugiej serii. |
| Zapytanie wykresu |Zapytanie, które jest uruchamiane dla drugiej serii. Liczba rekordów dla każdego przedziału czasu jest reprezentowana przez kolumny wykresu. |
| Operacja |Operacja wykonywana na właściwości Value, która podsumowuje ją jako pojedynczą wartość dla objaśnienia.<ul><li>Średnia: średnia wartości ze wszystkich rekordów.</li><li>Liczba: liczba wszystkich rekordów zwracanych przez zapytanie.</li><li>Ostatnia próbka: wartość ostatniego interwału, który znajduje się na wykresie.</li><li>Max: maksymalna wartość interwałów uwzględnionych na wykresie. |
| **Zaawansowany** |**> weryfikacja przepływu danych** |
| Enabled (Włączony) |Wybierz ten link, jeśli dla kafelka powinna być włączona weryfikacja przepływu danych. Takie podejście zapewnia alternatywny komunikat, jeśli dane są niedostępne. Zwykle używasz podejścia do dostarczania komunikatu w okresie tymczasowym, gdy widok zostanie zainstalowany, a dane staną się dostępne. |
| Zapytanie |Zapytanie, które jest uruchamiane w celu określenia, czy dane są dostępne dla widoku. Jeśli zapytanie nie zwraca żadnych wyników, zamiast wartości głównej kwerendy zostanie wyświetlony komunikat. |
| Komunikat |Komunikat, który jest wyświetlany, jeśli zapytanie weryfikacyjne przepływu danych nie zwraca żadnych danych. Jeśli nie podano komunikatu, zostanie wyświetlony komunikat o stanie *wykonywanie oceny* . |


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej na temat [zapytań dzienników](../logs/log-query-overview.md) do obsługi zapytań na kafelkach.
* Dodaj [części wizualizacji](view-designer-parts.md) do widoku niestandardowego.