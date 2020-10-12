---
title: Przewodnik referencyjny dotyczący części projektanta widoków w Azure Monitor | Microsoft Docs
description: Za pomocą projektanta widoków w Azure Monitor można utworzyć niestandardowe widoki, które są wyświetlane w Azure Portal i zawierać różne wizualizacje danych w obszarze roboczym Log Analytics. Ten artykuł zawiera Przewodnik dotyczący ustawień części wizualizacji, które są dostępne w niestandardowych widokach.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2018
ms.openlocfilehash: 1e674bf157d55c9b5f7cf49e8df5e17acb99ee3a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86539401"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Przewodnik referencyjny do wyświetlania części wizualizacji projektanta w Azure Monitor
Za pomocą projektanta widoków w Azure Monitor można utworzyć różne widoki niestandardowe w Azure Portal, które mogą ułatwić wizualizację danych w obszarze roboczym Log Analytics. Ten artykuł zawiera Przewodnik dotyczący ustawień części wizualizacji, które są dostępne w niestandardowych widokach.

Aby uzyskać więcej informacji na temat projektanta widoków, zobacz:

* [Projektant widoków](view-designer.md): zawiera omówienie projektanta widoków i procedur tworzenia i edytowania widoków niestandardowych.
* [Odwołanie do kafelka](view-designer-tiles.md): zawiera odwołanie do ustawień dla każdego dostępnego kafelka w widokach niestandardowych.


Dostępne typy kafelków projektanta widoków są opisane w poniższej tabeli:

| Typ widoku | Opis |
|:--- |:--- |
| [Lista zapytań](#list-of-queries-part) |Wyświetla listę kwerend dzienników. Możesz wybrać każde zapytanie, aby wyświetlić jego wyniki. |
| [Liczba i lista](#number-and-list-part) |W nagłówku zostanie wyświetlona jedna liczba, która pokazuje liczbę rekordów z zapytania dziennika. Na liście wyświetlane są dziesięć najważniejszych wyników zapytania, z wykresem wskazującym wartość względną kolumny liczbowej lub zmiany w czasie. |
| [Dwie liczby i lista](#two-numbers-and-list-part) |W nagłówku są wyświetlane dwie liczby, które pokazują liczbę rekordów z oddzielnych zapytań dzienników. Na liście wyświetlane są dziesięć najważniejszych wyników zapytania, z wykresem wskazującym wartość względną kolumny liczbowej lub zmiany w czasie. |
| [Pierścień i lista](#donut-and-list-part) |Nagłówek Wyświetla pojedynczą liczbę, która podsumowuje kolumnę wartości w zapytaniu dziennika. Pierścień wyświetla graficznie wyniki trzech pierwszych rekordów. |
| [Dwie osie czasu i lista](#two-timelines-and-list-part) |W nagłówku są wyświetlane wyniki dwóch zapytań dzienników w miarę upływu czasu jako wykresy kolumnowe, z objaśnieniem wyświetlającym jedną liczbę, która podsumowuje kolumnę wartości w zapytaniu dziennika. Na liście wyświetlane są dziesięć najważniejszych wyników zapytania, z wykresem wskazującym wartość względną kolumny liczbowej lub zmiany w czasie. |
| [Zawartych](#information-part) |W nagłówku jest wyświetlany statyczny tekst i opcjonalny link. Na liście jest wyświetlany jeden lub więcej elementów o statycznym tytule i tekście. |
| [Wykres liniowy, objaśnienie i lista](#line-chart-callout-and-list-part) |W nagłówku jest wyświetlany wykres liniowy z wieloma seriami na podstawie zapytania dziennika w czasie i z objaśnieniem o wartości podsumowującej. Na liście wyświetlane są dziesięć najważniejszych wyników zapytania, z wykresem wskazującym wartość względną kolumny liczbowej lub zmiany w czasie. |
| [Wykres liniowy i lista](#line-chart-and-list-part) |W nagłówku zostanie wyświetlony wykres liniowy z wieloma seriami na podstawie zapytania dziennika w czasie. Na liście wyświetlane są dziesięć najważniejszych wyników zapytania, z wykresem wskazującym wartość względną kolumny liczbowej lub zmiany w czasie. |
| [Stos części wykresów liniowych](#stack-of-line-charts-part) |Wyświetla trzy oddzielne wykresy liniowe z wieloma seriami z kwerendy dziennika w czasie. |

W następnych sekcjach szczegółowo opisano typy kafelków i ich właściwości.

> [!NOTE]
> Części w widokach opierają się na [zapytaniach dzienników](../log-query/log-query-overview.md) w obszarze roboczym log Analytics. Nie obsługują one obecnie [zapytań między zasobami](../log-query/cross-workspace-query.md) w celu pobierania danych z Application Insights.

## <a name="list-of-queries-part"></a>Lista części zapytań
Lista zapytań zawiera listę kwerend dzienników. Możesz wybrać każde zapytanie, aby wyświetlić jego wyniki. Widok domyślnie zawiera pojedyncze zapytanie i można wybrać opcję **+ zapytanie** , aby dodać dodatkowe zapytania.

![Lista widoku zapytań](media/view-designer-parts/view-list-queries.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł |Tekst wyświetlany w górnej części widoku. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, rozpoczynając od bieżącego widoku. |
| Wstępnie wybrane filtry |Rozdzielana przecinkami lista właściwości do uwzględnienia w lewym okienku filtru podczas wybierania zapytania. |
| Tryb renderowania |Początkowy widok, który jest wyświetlany po wybraniu zapytania. Po otwarciu zapytania można wybrać dowolne dostępne widoki. |
| **Zapytania** | |
| Zapytanie wyszukiwania |Zapytanie do uruchomienia. |
| Przyjazna nazwa | Wyświetlana nazwa opisowa. |

## <a name="number-and-list-part"></a>Liczba i część listy
W nagłówku zostanie wyświetlona jedna liczba, która pokazuje liczbę rekordów z zapytania dziennika. Na liście wyświetlane są dziesięć najważniejszych wyników zapytania, z wykresem wskazującym wartość względną kolumny liczbowej lub zmiany w czasie.

![Lista widoku zapytań](media/view-designer-parts/view-number-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany w górnej części widoku. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, rozpoczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wyniku w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Tytuł** | |
| Legenda |Tekst wyświetlany w górnej części nagłówka. |
| Zapytanie |Zapytanie do uruchomienia dla nagłówka. Zostanie wyświetlona liczba rekordów zwracanych przez zapytanie. |
| Nawigacja przez klikanie | Akcja wykonywana po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** | |
| Zapytanie |Zapytanie do uruchomienia dla listy. Zostaną wyświetlone pierwsze dwie właściwości pierwszych dziesięciu rekordów w wynikach. Pierwsza właściwość jest wartością tekstową, a druga właściwość jest wartością numeryczną. Słupki są tworzone automatycznie na podstawie względnej wartości kolumny liczbowej.<br><br>Użyj `Sort` polecenia w zapytaniu, aby posortować rekordy na liście. Aby uruchomić zapytanie i zwrócić wszystkie rekordy, można wybrać pozycję **Zobacz wszystko**. |
| Ukryj wykres |Wybierz ten link, aby wyłączyć wykres po prawej stronie kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie, a nie poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresów przebiegu w czasie. |
| Separator nazw i wartości |Ogranicznik pojedynczego znaku, który ma być używany do analizowania właściwości Text w wielu wartościach. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Nawigacja przez klikanie | Akcja wykonywana po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> tytuły kolumn** |
| Nazwa |Tekst wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst wyświetlany w górnej części drugiej kolumny. |
| **Lista** |** Progi>** |
| Włącz progi |Wybierz ten link, aby włączyć progi. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Dwie liczby i część listy
Nagłówek ma dwie liczby, które wyświetlają liczbę rekordów z oddzielnych zapytań dzienników. Na liście wyświetlane są dziesięć najważniejszych wyników zapytania, z wykresem wskazującym wartość względną kolumny liczbowej lub zmiany w czasie.

![Dwie liczby & widoku listy](media/view-designer-parts/view-two-numbers-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany w górnej części widoku. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, rozpoczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wyniku w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Nawigacja po tytule** | |
| Nawigacja przez klikanie | Akcja wykonywana po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Tytuł** | |
| Legenda |Tekst wyświetlany w górnej części nagłówka. |
| Zapytanie |Zapytanie do uruchomienia dla nagłówka. Zostanie wyświetlona liczba rekordów zwracanych przez zapytanie. |
| **Lista** | |
| Zapytanie |Zapytanie do uruchomienia dla listy. Zostaną wyświetlone pierwsze dwie właściwości pierwszych dziesięciu rekordów w wynikach. Pierwsza właściwość jest wartością tekstową, a druga właściwość jest wartością numeryczną. Słupki są tworzone automatycznie na podstawie względnej wartości kolumny liczbowej.<br><br>Użyj `Sort` polecenia w zapytaniu, aby posortować rekordy na liście. Aby uruchomić zapytanie i zwrócić wszystkie rekordy, można wybrać pozycję **Zobacz wszystko**. |
| Ukryj wykres |Wybierz ten link, aby wyłączyć wykres po prawej stronie kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie, a nie poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresów przebiegu w czasie. |
| Operacja |Operacja do wykonania dla wykresu przebiegu w czasie. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Separator nazw i wartości |Ogranicznik pojedynczego znaku, który ma być używany do analizowania właściwości Text w wielu wartościach. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Nawigacja przez klikanie | Akcja wykonywana po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> tytuły kolumn** |
| Nazwa |Tekst wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst wyświetlany w górnej części drugiej kolumny. |
| **Lista** |** Progi>** |
| Włącz progi |Wybierz ten link, aby włączyć progi. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="donut-and-list-part"></a>Pierścień i część listy
Nagłówek Wyświetla pojedynczą liczbę, która podsumowuje kolumnę wartości w zapytaniu dziennika. Pierścień wyświetla graficznie wyniki trzech pierwszych rekordów.

![Pierścień i widok listy](media/view-designer-parts/view-donut-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, rozpoczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wyniku w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Nagłówki** | |
| Tytuł |Tekst wyświetlany w górnej części nagłówka. |
| Podtytuł |Tekst wyświetlany pod tytułem w górnej części nagłówka. |
| **Pierścień** | |
| Zapytanie |Zapytanie, które ma zostać uruchomione dla pierścienia. Pierwsza właściwość jest wartością tekstową, a druga właściwość jest wartością numeryczną. |
| Nawigacja przez klikanie | Akcja wykonywana po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Pierścień** |** Centrum>** |
| Tekst |Tekst wyświetlany w obszarze wartości w pierścieniu. |
| Operacja |Operacja do wykonania na właściwości Value, która podsumowuje ją jako pojedynczą wartość.<ul><li>Sum: dodaje wartości wszystkich rekordów.</li><li>Wartość procentowa: stosunek rekordów zwracanych przez wartości w **wartościach wynikowych używanych w środku operacji** do łącznej liczby rekordów w zapytaniu.</li></ul> |
| Wartości wynikowe używane w środku operacji |Opcjonalnie wybierz znak plus (+), aby dodać jedną lub więcej wartości. Wyniki zapytania są ograniczone do rekordów o określonych wartościach właściwości. Jeśli nie zostaną dodane żadne wartości, wszystkie rekordy zostaną uwzględnione w zapytaniu. |
| **Opcje dodatkowe** |** Kolory>** |
| Kolor 1<br>Kolor 2<br>Kolor 3 |Wybierz kolor dla każdej wartości, która jest wyświetlana na pierścieniu. |
| **Opcje dodatkowe** |** Zaawansowane mapowanie kolorów>** |
| Wartość pola |Wpisz nazwę pola, aby wyświetlić je jako inny kolor, jeśli jest ono zawarte w pierścieniu. |
| Kolor |Wybierz kolor unikatowego pola. |
| **Lista** | |
| Zapytanie |Zapytanie do uruchomienia dla listy. Zostanie wyświetlona liczba rekordów zwracanych przez zapytanie. |
| Ukryj wykres |Wybierz ten link, aby wyłączyć wykres po prawej stronie kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie, a nie poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresów przebiegu w czasie. |
| Operacja |Operacja do wykonania dla wykresu przebiegu w czasie. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Separator nazw i wartości |Ogranicznik pojedynczego znaku, który ma być używany do analizowania właściwości Text w wielu wartościach. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Nawigacja przez klikanie | Akcja wykonywana po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> tytuły kolumn** |
| Nazwa |Tekst wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst wyświetlany w górnej części drugiej kolumny. |
| **Lista** |** Progi>** |
| Włącz progi |Wybierz ten link, aby włączyć progi. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Dwie osie czasu i część listy
W nagłówku są wyświetlane wyniki dwóch zapytań dzienników w miarę upływu czasu jako wykresy kolumnowe, z objaśnieniem wyświetlającym jedną liczbę, która podsumowuje kolumnę wartości w zapytaniu dziennika. Na liście wyświetlane są dziesięć najważniejszych wyników zapytania, z wykresem wskazującym wartość względną kolumny liczbowej lub zmiany w czasie.

![Dwie osie czasu i widok listy](media/view-designer-parts/view-two-timelines-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, rozpoczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wyniku w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Nawigacja po tytule** | |
| Nawigacja przez klikanie | Akcja wykonywana po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Pierwszy wykres <br> drugiego wykresu** | |
| Legenda |Tekst wyświetlany w objaśnieniu dla pierwszej serii. |
| Kolor |Kolor, który ma być używany w odniesieniu do kolumn w serii. |
| Zapytanie |Zapytanie do uruchomienia dla pierwszej serii. Liczba rekordów w każdym przedziale czasu jest reprezentowana przez kolumny wykresu. |
| Operacja |Operacja do wykonania na właściwości Value, która podsumowuje ją jako pojedynczą wartość dla objaśnienia.<ul><li>Sum: suma wartości ze wszystkich rekordów.</li><li>Średnia: średnia wartości ze wszystkich rekordów.</li><li>Ostatnia próbka: wartość z ostatniego interwału, który znajduje się na wykresie.</li><li>Pierwsza próbka: wartość z pierwszego interwału, która jest uwzględniona na wykresie.</li><li>Liczba: liczba wszystkich rekordów zwracanych przez zapytanie.</li></ul> |
| **Lista** | |
| Zapytanie |Zapytanie do uruchomienia dla listy. Zostanie wyświetlona liczba rekordów zwracanych przez zapytanie. |
| Ukryj wykres |Wybierz ten link, aby wyłączyć wykres po prawej stronie kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie, a nie poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresów przebiegu w czasie. |
| Operacja |Operacja do wykonania dla wykresu przebiegu w czasie. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Nawigacja przez klikanie | Akcja wykonywana po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> tytuły kolumn** |
| Nazwa |Tekst wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst wyświetlany w górnej części drugiej kolumny. |
| **Lista** |** Progi>** |
| Włącz progi |Wybierz ten link, aby włączyć progi. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="information-part"></a>Część informacji
W nagłówku jest wyświetlany statyczny tekst i opcjonalny link. Na liście jest wyświetlany jeden lub więcej elementów o statycznym tytule i tekście.

![Widok informacji](media/view-designer-parts/view-information.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, rozpoczynając od bieżącego widoku. |
| Kolor |Kolor tła nagłówka. |
| **Nagłówki** | |
| Obraz |Plik obrazu, który jest wyświetlany w nagłówku. |
| Etykieta |Tekst wyświetlany w nagłówku. |
| **Nagłówki** |**> łącze** |
| Etykieta |Tekst łącza. |
| Url |Adres URL łącza. |
| **Elementy informacji** | |
| Tytuł |Tekst wyświetlany dla tytułu każdego elementu. |
| Zawartość |Tekst wyświetlany dla każdego elementu. |

## <a name="line-chart-callout-and-list-part"></a>Wykres liniowy, objaśnienie i składnik listy
W nagłówku jest wyświetlany wykres liniowy z wieloma seriami na podstawie zapytania dziennika w czasie i z objaśnieniem o wartości podsumowującej. Na liście wyświetlane są dziesięć najważniejszych wyników zapytania, z wykresem wskazującym wartość względną kolumny liczbowej lub zmiany w czasie.

![Wykres liniowy, objaśnienie i widok listy](media/view-designer-parts/view-line-chart-callout-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, rozpoczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wyniku w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Nagłówki** | |
| Tytuł |Tekst wyświetlany w górnej części nagłówka. |
| Podtytuł |Tekst wyświetlany pod tytułem w górnej części nagłówka. |
| **Wykres liniowy** | |
| Zapytanie |Zapytanie, które ma zostać uruchomione dla wykresu liniowego. Pierwsza właściwość jest wartością tekstową, a druga właściwość jest wartością numeryczną. To zapytanie zwykle używa słowa kluczowego *Measure* do podsumowywania wyników. Jeśli zapytanie używa słowa kluczowego *Interval* , oś x wykresu używa tego przedziału czasowego. Jeśli zapytanie nie zawiera słowa kluczowego *Interval* , na osi x stosowane są interwały godzinowe. |
| Nawigacja przez klikanie | Akcja wykonywana po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Wykres liniowy** |** Objaśnienie>** |
| Tytuł objaśnienia |Tekst wyświetlany powyżej wartości objaśnienia. |
| Nazwa serii |Wartość właściwości dla serii, która ma być używana dla wartości objaśnienia. Jeśli nie podano żadnej serii, są używane wszystkie rekordy z zapytania. |
| Operacja |Operacja do wykonania na właściwości Value, która podsumowuje ją jako pojedynczą wartość dla objaśnienia.<ul><li>Średnia: średnia wartości ze wszystkich rekordów.</li><li>Liczba: liczba wszystkich rekordów zwracanych przez zapytanie.</li><li>Ostatnia próbka: wartość z ostatniego interwału, który znajduje się na wykresie.</li><li>Max: wartość maksymalna z interwałów uwzględnionych na wykresie.</li><li>Min: wartość minimalna z interwałów uwzględnionych na wykresie.</li><li>Sum: suma wartości ze wszystkich rekordów.</li></ul> |
| **Wykres liniowy** |**> oś Y** |
| Użyj skali logarytmicznej |Wybierz ten link, aby użyć skali logarytmicznej dla osi y. |
| Lekcji |Określ jednostki wartości, które mają być zwracane przez zapytanie. Te informacje służą do wyświetlania etykiet wykresu wskazujących typy wartości i, opcjonalnie, do konwersji wartości. Typ *jednostki* Określa kategorię jednostki i definiuje dostępne bieżące wartości typu *jednostki* . W przypadku wybrania wartości w *konwersji na*, wartości liczbowe są konwertowane z bieżącego typu *jednostki* na typ *konwersji na* . |
| Etykieta niestandardowa |Tekst wyświetlany dla osi y obok etykiety dla typu *jednostki* . Jeśli etykieta nie zostanie określona, zostanie wyświetlony tylko typ *jednostki* . |
| **Lista** | |
| Zapytanie |Zapytanie do uruchomienia dla listy. Zostanie wyświetlona liczba rekordów zwracanych przez zapytanie. |
| Ukryj wykres |Wybierz ten link, aby wyłączyć wykres po prawej stronie kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie, a nie poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresów przebiegu w czasie. |
| Operacja |Operacja do wykonania dla wykresu przebiegu w czasie. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Separator nazw i wartości |Ogranicznik pojedynczego znaku, który ma być używany do analizowania właściwości Text w wielu wartościach. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Nawigacja przez klikanie | Akcja wykonywana po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> tytuły kolumn** |
| Nazwa |Tekst wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst wyświetlany w górnej części drugiej kolumny. |
| **Lista** |** Progi>** |
| Włącz progi |Wybierz ten link, aby włączyć progi. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="line-chart-and-list-part"></a>Wykres liniowy i składnik listy
W nagłówku zostanie wyświetlony wykres liniowy z wieloma seriami na podstawie zapytania dziennika w czasie. Na liście wyświetlane są dziesięć najważniejszych wyników zapytania, z wykresem wskazującym wartość względną kolumny liczbowej lub zmiany w czasie.

![Wykres liniowy i widok listy](media/view-designer-parts/view-line-chart-callout-list.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, rozpoczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wyniku w nagłówku. |
| Ikona użycia |Wybierz ten link, aby wyświetlić ikonę. |
| **Nagłówki** | |
| Tytuł |Tekst wyświetlany w górnej części nagłówka. |
| Podtytuł |Tekst wyświetlany pod tytułem w górnej części nagłówka. |
| **Wykres liniowy** | |
| Zapytanie |Zapytanie, które ma zostać uruchomione dla wykresu liniowego. Pierwsza właściwość jest wartością tekstową, a druga właściwość jest wartością numeryczną. To zapytanie zwykle używa słowa kluczowego *Measure* do podsumowywania wyników. Jeśli zapytanie używa słowa kluczowego *Interval* , oś x wykresu używa tego przedziału czasowego. Jeśli zapytanie nie zawiera słowa kluczowego *Interval* , na osi x stosowane są interwały godzinowe. |
| Nawigacja przez klikanie | Akcja wykonywana po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Wykres liniowy** |**> oś Y** |
| Użyj skali logarytmicznej |Wybierz ten link, aby użyć skali logarytmicznej dla osi y. |
| Lekcji |Określ jednostki wartości, które mają być zwracane przez zapytanie. Te informacje służą do wyświetlania etykiet wykresu wskazujących typy wartości i, opcjonalnie, do konwersji wartości. Typ *jednostki* Określa kategorię jednostki i definiuje dostępne bieżące wartości typu *jednostki* . W przypadku wybrania wartości w *konwersji na*, wartości liczbowe są konwertowane z bieżącego typu *jednostki* na typ *konwersji na* . |
| Etykieta niestandardowa |Tekst wyświetlany dla osi y obok etykiety dla typu *jednostki* . Jeśli etykieta nie zostanie określona, zostanie wyświetlony tylko typ *jednostki* . |
| **Lista** | |
| Zapytanie |Zapytanie do uruchomienia dla listy. Zostanie wyświetlona liczba rekordów zwracanych przez zapytanie. |
| Ukryj wykres |Wybierz ten link, aby wyłączyć wykres po prawej stronie kolumny liczbowej. |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie, a nie poziomy pasek. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Kolor |Kolor słupków lub wykresów przebiegu w czasie. |
| Operacja |Operacja do wykonania dla wykresu przebiegu w czasie. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Separator nazw i wartości |Ogranicznik pojedynczego znaku, który ma być używany do analizowania właściwości Text w wielu wartościach. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#sparklines). |
| Nawigacja przez klikanie | Akcja wykonywana po kliknięciu elementu na liście.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Lista** |**> tytuły kolumn** |
| Nazwa |Tekst wyświetlany w górnej części pierwszej kolumny. |
| Wartość |Tekst wyświetlany w górnej części drugiej kolumny. |
| **Lista** |** Progi>** |
| Włącz progi |Wybierz ten link, aby włączyć progi. Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Stos części wykresów liniowych
Na stosie wykresu liniowego są wyświetlane trzy oddzielne wykresy liniowe z wieloma seriami z zapytania dziennika w czasie, jak pokazano poniżej:

![Stos wykresów liniowych](media/view-designer-parts/view-stack-line-charts.png)

| Ustawienie | Opis |
|:--- |:--- |
| **Ogólne** | |
| Tytuł grupy |Tekst wyświetlany w górnej części kafelka. |
| Nowa grupa |Wybierz ten link, aby utworzyć nową grupę w widoku, rozpoczynając od bieżącego widoku. |
| Ikona |Plik obrazu, który jest wyświetlany obok wyniku w nagłówku. |
| **Wykres 1 wykres <br> 2 wykres <br> 3** |** Nagłówek>** |
| Tytuł |Tekst wyświetlany w górnej części wykresu. |
| Podtytuł |Tekst wyświetlany pod tytułem w górnej części wykresu. |
| **Wykres 1 wykres <br> 2 wykres <br> 3** |**Wykres liniowy** |
| Zapytanie |Zapytanie, które ma zostać uruchomione dla wykresu liniowego. Pierwsza właściwość jest wartością tekstową, a druga właściwość jest wartością numeryczną. To zapytanie zwykle używa słowa kluczowego *Measure* do podsumowywania wyników. Jeśli zapytanie używa słowa kluczowego *Interval* , oś x wykresu używa tego przedziału czasowego. Jeśli zapytanie nie zawiera słowa kluczowego *Interval* , na osi x stosowane są interwały godzinowe. |
| Nawigacja przez klikanie | Akcja wykonywana po kliknięciu nagłówka.  Aby uzyskać więcej informacji, zobacz [typowe ustawienia](#click-through-navigation). |
| **Wykres** |**> oś Y** |
| Użyj skali logarytmicznej |Wybierz ten link, aby użyć skali logarytmicznej dla osi y. |
| Lekcji |Określ jednostki wartości, które mają być zwracane przez zapytanie. Te informacje służą do wyświetlania etykiet wykresu wskazujących typy wartości i, opcjonalnie, do konwersji wartości. Typ *jednostki* Określa kategorię jednostki i definiuje dostępne bieżące wartości typu *jednostki* . W przypadku wybrania wartości w *konwersji na*, wartości liczbowe są konwertowane z bieżącego typu *jednostki* na typ *konwersji na* . |
| Etykieta niestandardowa |Tekst wyświetlany dla osi y obok etykiety dla typu *jednostki* . Jeśli etykieta nie zostanie określona, zostanie wyświetlony tylko typ *jednostki* . |

## <a name="common-settings"></a>Typowe ustawienia
W poniższych sekcjach opisano ustawienia, które są wspólne dla kilku części wizualizacji.

### <a name="name-and-value-separator"></a><a name="name-value-separator"></a>Separator nazw i wartości
Separator nazwy i wartości jest ogranicznikiem pojedynczego znaku, który ma być używany do analizowania właściwości text z zapytania listy do wielu wartości. W przypadku określenia ogranicznika można podać nazwy dla każdego pola, oddzielone tego samego ogranicznika w polu **Nazwa** .

Rozważmy na przykład właściwość o nazwie *Location* , która zawiera wartości, takie jak *Redmond-Building 41* i *Bellevue-Building 12*. Można określić kreskę (-) dla separatora *nazwy i wartości oraz dla* nazwy. Takie podejście analizuje każdą wartość w dwie właściwości o nazwie *miasto* i *budynek*.

### <a name="click-through-navigation"></a><a name="click-through-navigation"></a>Nawigacja przez klikanie
Nawigacja po kliknięciu definiuje, jaka akcja zostanie podjęta po kliknięciu nagłówka lub elementu listy w widoku.  Spowoduje to otwarcie zapytania w [log Analytics](../log-query/log-query-overview.md) lub uruchomienie innego widoku.

W poniższej tabeli opisano ustawienia nawigowania po kliknięciu.

| Ustawienie           | Opis |
|:--|:--|
| Przeszukiwanie dzienników (Auto) | Zapytanie dziennika do uruchomienia po zaznaczeniu elementu nagłówka.  Jest to ta sama kwerenda dziennika, na której jest oparty element.
| Przeszukiwanie dzienników        | Zapytanie dziennika do uruchomienia po wybraniu elementu na liście.  Wpisz zapytanie w polu **zapytania nawigacji** .   Użyj *{Selected Item}* , aby dołączyć składnię dla elementu wybranego przez użytkownika.  Na przykład, jeśli zapytanie zawiera kolumnę o nazwie *Computer* , a zapytanie nawigacji to *{Selected Item}*, podczas wybierania komputera zostanie uruchomione zapytanie, takie jak *Computer = "MójKomputer"* . Jeśli zapytanie nawigacji jest *typu = Event {selectedd Item}*, uruchamiany jest *typ zapytania = Event Computer = "MójKomputer"* . |
| Widok              | Widok, który ma zostać otwarty po zaznaczeniu elementu nagłówka lub elementu na liście.  W polu **Nazwa widoku** wybierz nazwę widoku w obszarze roboczym. |



### <a name="sparklines"></a><a name="sparklines"></a>Wykresy przebiegu
Wykresy przebiegu w czasie to mały wykres liniowy, który ilustruje wartość wpisu listy w czasie. W przypadku części wizualizacji z listą można wybrać, czy ma być wyświetlany poziomy pasek, który wskazuje wartość względną kolumny liczbowej lub wykres przebiegu w czasie, który wskazuje jego wartość w czasie.

W poniższej tabeli opisano ustawienia dla wykresów przebiegowych:

| Ustawienie | Opis |
|:--- |:--- |
| Włącz wykresy przebiegu w czasie |Wybierz ten link, aby wyświetlić wykres przebiegu w czasie, a nie poziomy pasek. |
| Operacja |Jeśli wykresy przebiegu w czasie włączonym, jest to operacja do wykonania na każdej właściwości na liście w celu obliczenia wartości wykresu przebiegu w czasie.<ul><li>Ostatnia próbka: Ostatnia wartość dla serii w przedziale czasu.</li><li>Max: maksymalna wartość dla serii w przedziale czasu.</li><li>Min: wartość minimalna dla serii w przedziale czasu.</li><li>Sum: suma wartości dla serii w przedziale czasu.</li><li>Podsumowanie: używa tego samego `measure` polecenia, co zapytanie w nagłówku.</li></ul> |

### <a name="thresholds"></a><a name="thresholds"></a>Progi
Korzystając z progów, można wyświetlić kolorową ikonę obok każdego elementu na liście. Progi zapewniają szybki Wskaźnik wizualny elementów, które przekraczają określoną wartość lub mieszczą się w określonym zakresie. Na przykład można wyświetlić zieloną ikonę dla elementów o akceptowalnej wartości, żółty, jeśli wartość znajduje się w zakresie, który wskazuje na ostrzeżenie, i czerwony, jeśli przekroczy wartość błędu.

Po włączeniu progów dla części należy określić co najmniej jeden próg. Jeśli wartość elementu jest większa niż wartość progowa i mniejsza niż wartość następnej wartości progowej, używany jest kolor tej wartości. Jeśli element jest większy niż wartość najwyższej wartości progowej, używany jest inny kolor. 

Każdy zestaw progów ma jeden próg z wartością **domyślną**. Jest to kolor ustawiony w przypadku braku innych wartości. Możesz dodawać lub usuwać progi, wybierając przycisk **Dodaj** (+) lub **Usuń** (x).

W poniższej tabeli opisano ustawienia progów:

| Ustawienie | Opis |
|:--- |:--- |
| Włącz progi |Wybierz ten link, aby wyświetlić ikonę koloru po lewej stronie każdej wartości. Ikona wskazuje prawidłowość wartości względem określonych progów. |
| Nazwa |Nazwa wartości progowej. |
| Próg |Wartość progu. Kolor kondycji dla każdego elementu listy jest ustawiony na kolor najwyższej wartości progowej, który został przekroczony przez wartość elementu. Jeśli wartości progowe nie zostaną przekroczone, zostanie użyty kolor domyślny. |
| Kolor |Kolor wskazujący wartość progową. |

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zapytaniach dzienników](../log-query/log-query-overview.md) do obsługi zapytań w częściach wizualizacji.
