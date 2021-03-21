---
title: 'Wybór funkcji oparty na filtrowaniu: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać modułu wyboru funkcji opartego na filtrze w Azure Machine Learning, aby identyfikować funkcje w zestawie danych z największą potęgą predykcyjną.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: f4a7f5581703ae6932f3b40e62085fed76f5e6f2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91945706"
---
# <a name="filter-based-feature-selection"></a>Wybór funkcji oparty na filtrze

W tym artykule opisano, jak używać modułu wyboru funkcji opartego na filtrze w programie Azure Machine Learning Designer. Ten moduł pomaga identyfikować kolumny w wejściowym zestawie danych, które mają największą siłę predykcyjną. 

Ogólnie rzecz biorąc, *wybór funkcji* odnosi się do procesu zastosowania testów statystycznych do danych wejściowych, z uwzględnieniem określonych danych wyjściowych. Celem jest określenie, które kolumny są bardziej predykcyjne w danych wyjściowych. Moduł wyboru funkcji oparty na filtrze zawiera wiele algorytmów wyboru funkcji do wyboru. Moduł zawiera metody korelacji, takie jak korelacja Pearsona i wartości chi-kwadrat. 

W przypadku korzystania z modułu wyboru funkcji opartego na filtrze należy podać zestaw danych i określić kolumnę, która zawiera etykietę lub zmienną zależną. Następnie należy określić jedną metodę do użycia podczas mierzenia znaczenia funkcji.

Moduł wyprowadza zestaw danych, który zawiera kolumny najlepszych funkcji, zgodnie z możliwością predykcyjną. Powoduje także wyprowadzanie nazw funkcji i ich ocen z wybranej metryki.  

## <a name="what-filter-based-feature-selection-is"></a>Wybór funkcji opartej na filtrze to  

Ten moduł dla wyboru funkcji jest nazywany "opartym na filtrowaniu", ponieważ jest używana wybrana Metryka do znajdowania nieistotnych atrybutów. Następnie należy odfiltrować nadmiarowe kolumny z modelu. Wybierasz jedną miarę statystyczną, która będzie odpowiadała danym, a moduł oblicza ocenę dla każdej kolumny funkcji. Kolumny są zwracane w rankingu według ich wyników funkcji. 

Po wybraniu odpowiednich funkcji można poprawić dokładność i wydajność klasyfikacji. 

Zwykle używane są tylko kolumny z najlepszymi wynikami w celu utworzenia modelu predykcyjnego. Kolumny z nieprawidłowymi wynikami wyboru funkcji mogą pozostać w zestawie danych i ignorowane podczas kompilowania modelu.  

## <a name="how-to-choose-a-feature-selection-metric"></a>Jak wybrać metrykę wyboru funkcji

Moduł wyboru funkcji Filter-Based udostępnia różne metryki do oceny wartości informacji w każdej kolumnie. Ta sekcja zawiera ogólny opis każdej metryki i sposób jej zastosowania. Dodatkowe wymagania dotyczące korzystania z każdej metryki w [uwagach technicznych](#technical-notes) i instrukcje dotyczące konfigurowania poszczególnych modułów można znaleźć w [temacie](#how-to-configure-filter-based-feature-selection) .

-   **Korelacja Pearsona**  

    Statystyka korelacji Pearsona lub współczynnik korelacji Pearsona są również znane w modelach statystycznych jako `r` wartość. Dla każdej dwóch zmiennych zwraca wartość wskazującą siłę korelacji.

    Współczynnik korelacji Pearsona jest obliczany przez przejęcie kowariancji dwóch zmiennych i podzielenie przez iloczyn standardowych odchyleń. Zmiany skali w dwóch zmiennych nie wpływają na współczynnik.  

-   **Chi kwadratowy**  

    Dwukierunkowy test chi-kwadrat jest metodą statystyczną, która określa, jak blisko oczekiwanych wartości są rzeczywiste wyniki. Metoda zakłada, że zmienne są losowo i rysowane z odpowiednich próbek zmiennych niezależnych. Wynikowa Statystyka chi-kwadrat wskazuje, jak daleko wynika z oczekiwanego (losowego) wyniku.  


> [!TIP]
> Jeśli potrzebujesz innej opcji niestandardowej metody wyboru funkcji, użyj modułu [skryptu języka R](execute-r-script.md) . 

## <a name="how-to-configure-filter-based-feature-selection"></a>Jak skonfigurować wybór funkcji Filter-Based

Wybierasz standardową metrykę statystyczną. Moduł oblicza korelację między parą kolumn: kolumną etykieta i kolumną funkcji.

1.  Dodaj moduł wyboru funkcji Filter-Based do potoku. Można go znaleźć w kategorii **wybór funkcji** w projektancie.

2. Połącz zestaw danych wejściowych zawierający co najmniej dwie kolumny, które są potencjalnymi funkcjami.  

    Aby zapewnić przeanalizowanie kolumny i wygenerowanie oceny funkcji, użyj modułu [Edytowanie metadanych](edit-metadata.md) , aby ustawić atrybut **isfeature** . 

    > [!IMPORTANT]
    > Upewnij się, że kolumny, które są udostępniane jako dane wejściowe, są potencjalnymi funkcjami. Na przykład kolumna, która zawiera pojedynczą wartość nie ma wartości informacji.
    >
    > Jeśli wiesz, że niektóre kolumny spowodują nieprawidłowe funkcje, możesz je usunąć z zaznaczenia kolumny. Możesz również użyć modułu [Edytowanie metadanych](edit-metadata.md) , aby oznaczyć je jako **kategorii**. 
3.  W przypadku **metody oceny funkcji** należy wybrać jedną z następujących ustalonych metod statystycznych do użycia podczas obliczania wyników.  

    | Metoda              | Wymagania                             |
    | ------------------- | ---------------------------------------- |
    | Korelacja Pearsona | Etykieta może być tekstem lub cyfrą. Funkcje muszą być liczbowe. |
    Chi kwadratowy| Etykiety i funkcje mogą być tekstowe lub liczbowe. Ta metoda służy do obliczania ważności funkcji dla dwóch kolumn kategorii.|

    > [!TIP]
    > Jeśli zmienisz wybraną metrykę, wszystkie pozostałe zaznaczenia zostaną zresetowane. Upewnij się, że najpierw należy ustawić tę opcję.
4.  Wybierz opcję **Pracuj w przypadku kolumn funkcji tylko** w celu wygenerowania oceny tylko dla kolumn, które zostały wcześniej oznaczone jako funkcje. 

    Jeśli wyczyścisz tę opcję, moduł utworzy ocenę dla każdej kolumny, która w przeciwnym razie spełnia kryteria, do liczby kolumn określonych w polu **Liczba żądanych funkcji**.  

5.  Dla **kolumny Target** wybierz pozycję **Uruchom selektor kolumny** , aby wybrać kolumnę etykieta według nazwy lub indeksu. (Indeksy są oparte na jednym z nich).  
    Kolumna etykieta jest wymagana dla wszystkich metod, które obejmują korelację statystyczną. Moduł zwraca błąd czasu projektowania, jeśli nie wybrano kolumny etykiety ani kolumn z wieloma etykietami. 

6.  W polu **Liczba żądanych funkcji** wprowadź liczbę kolumn funkcji, które mają zostać zwrócone w wyniku:  

    - Minimalna liczba funkcji, które można określić, to jeden, ale zalecamy zwiększenie tej wartości.  

    - Jeśli określona liczba żądanych funkcji jest większa niż liczba kolumn w zestawie danych, wszystkie funkcje są zwracane. Zwracane są nawet funkcje z zerowymi wynikami.  

    - Jeśli określisz mniejszą liczbę kolumn wyników niż kolumny funkcji, funkcje są klasyfikowane według malejącego wyniku. Tylko najważniejsze funkcje są zwracane. 

7.  Prześlij potok.

> [!IMPORTANT]
> Jeśli zamierzasz używać **wyboru funkcji opartej na filtrach** w wnioskach, musisz użyć [opcji Przekształć kolumny](./select-columns-transform.md) do przechowywania wybranej funkcji i [zastosować transformację](./apply-transformation.md) , aby zastosować wybraną funkcję transformacji do zestawu danych oceniania.
>
> Zapoznaj się z poniższym zrzutem ekranu, aby utworzyć potok, aby upewnić się, że wybrane kolumny są takie same dla procesu oceniania.
> [!div class="mx-imgBorder"]
> ![Przykładowy potok](media/module/filter-based-feature-selection-score.png)

## <a name="results"></a>Wyniki

Po zakończeniu przetwarzania:

+ Aby wyświetlić pełną listę przeanalizowanych kolumn funkcji i ich ocen, kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Wizualizuj**.  

+ Aby wyświetlić zestaw danych na podstawie kryteriów wyboru funkcji, kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Wizualizuj**. 

Jeśli zestaw danych zawiera mniejszą liczbę kolumn niż oczekiwano, sprawdź ustawienia modułu. Sprawdź również typy danych kolumn dostarczonych jako dane wejściowe. Jeśli na przykład ustawisz **liczbę żądanych funkcji** na 1, wyjściowy zestaw danych zawiera tylko dwie kolumny: kolumnę Label oraz kolumnę o największej rangi.


##  <a name="technical-notes"></a>Uwagi techniczne  

### <a name="implementation-details"></a>Szczegóły implementacji

Jeśli używasz korelacji Pearsona dla funkcji liczbowej i etykiety kategorii, wynik funkcji jest obliczany w następujący sposób:  

1.  Dla każdego poziomu w kolumnie kategorii Obliczanie średniej warunkowej kolumny liczbowej.  

2.  Skorelowanie kolumny warunkowego z kolumną liczbową.  

### <a name="requirements"></a>Wymagania  

-   Nie można wygenerować wyniku wyboru funkcji dla żadnej kolumny, która jest oznaczona jako **etykieta** lub kolumna **oceny** .  

-   Jeśli spróbujesz użyć metody oceniania z kolumną typu danych, która nie jest obsługiwana przez tę metodę, moduł zgłosi błąd. Lub do kolumny zostanie przypisany wynik zerowy.  

-   Jeśli kolumna zawiera wartości logiczne (prawda/fałsz), są one przetwarzane jako `True = 1` i `False = 0` .  

-   Kolumna nie może być funkcją, jeśli została wyoznaczona jako **etykieta** lub **wynik**.  

### <a name="how-missing-values-are-handled"></a>Jak są obsługiwane brakujące wartości  

-   Nie można określić jako kolumny docelowej (etykieta) żadnej kolumny, która ma wszystkie brakujące wartości.  

-   Jeśli kolumna zawiera brakujące wartości, moduł ignoruje je podczas obliczania wyniku dla kolumny.  

-   Jeśli kolumna wyoznaczona jako kolumna funkcji ma wszystkie brakujące wartości, moduł przypisuje wynik zerowy.   


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 

