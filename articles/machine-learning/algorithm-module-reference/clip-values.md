---
title: Obcinanie wartości
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu wartości klipów w Azure Machine Learning, aby wykrywać elementy odstające i wycinania, lub zamieniać ich wartości.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 99fb41542dff28997438881abad71da11e927a78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96014894"
---
# <a name="clip-values"></a>Obcinanie wartości

W tym artykule opisano moduł programu Azure Machine Learning Designer.

Użyj modułu wartości klipów, aby identyfikować i opcjonalnie zamieniać wartości danych powyżej lub poniżej określonego progu ze średnią, stałą lub inną wartością zastępczą.  

Moduł jest połączony z zestawem danych, który ma liczby, które chcesz przyciąć, wybierz kolumny, z którymi chcesz korzystać, a następnie ustaw próg lub zakres wartości oraz metodę zastępczą. Moduł może wyprowadzać tylko wyniki lub zmienione wartości dołączone do oryginalnego zestawu danych.

## <a name="how-to-configure-clip-values"></a>Jak skonfigurować wartości klipów

Przed rozpoczęciem Określ kolumny, które chcesz przyciąć, i metodę, która ma zostać użyta. Zalecamy, aby najpierw przetestować każdą metodę przycinającą dla małego podzestawu danych.

Moduł stosuje te same kryteria i metodę zastępującą do **wszystkich** kolumn uwzględnionych w zaznaczeniu. W związku z tym Pamiętaj, aby wykluczyć kolumny, których nie chcesz zmienić.

Jeśli musisz zastosować metody przycinające lub inne kryteria do niektórych kolumn, musisz użyć nowego wystąpienia **wartości klipów** dla każdego zestawu podobnych kolumn.

1.  Dodaj moduł **wartości klipu** do potoku i połącz go z zestawem danych, który chcesz zmodyfikować. Ten moduł można znaleźć w obszarze **Przekształcanie danych** w kategorii **skalowanie i zmniejszanie** . 
  
1.  Na **liście kolumn** Użyj selektora kolumn, aby wybrać kolumny, do których zostaną zastosowane **wartości klipów** .  
  
1.  Dla **zestawu progów** wybierz jedną z następujących opcji z listy rozwijanej. Te opcje określają, jak ustawić górną i dolną granicę dla akceptowalnych wartości a wartości, które muszą być przycinane.  
  
    - **ClipPeaks**: podczas przycinania wartości według szczytów należy określić tylko górną granicę. Wartości większe niż ta wartość granicy są zastępowane.
  
    -  **ClipSubpeaks**: podczas przycinania wartości według szczytów należy określić tylko dolną granicę. Wartości, które są mniejsze niż ta wartość granicy, są zastępowane.  
  
    - **ClipPeaksAndSubpeaks**: podczas przycinania wartości według szczytów i ich szczytów można określić górną i dolną granicę. Wartości, które znajdują się poza tym zakresem, są zastępowane. Wartości, które pasują do wartości granicznych, nie są zmieniane.
  
1.  W zależności od wyboru w poprzednim kroku można ustawić następujące wartości progowe: 

    + **Dolny próg**: wyświetlany tylko w przypadku wybrania opcji **ClipSubPeaks**
    + **Próg górny**: wyświetlany tylko w przypadku wybrania opcji **ClipPeaks**
    + **Próg**: wyświetlane tylko w przypadku wybrania opcji **ClipPeaksAndSubPeaks**

    Dla każdego typu progu wybierz wartość **stała** lub **percentyl**.

1. W przypadku wybrania opcji **stała** wpisz wartość maksymalną lub minimalną w polu tekstowym. Załóżmy na przykład, że wiesz, że wartość 999 została użyta jako wartość symbolu zastępczego. Można wybrać **stałą** dla górnego progu i typ 999 w **wartości stałej dla górnego progu**.
  
1. W przypadku wybrania **percentylu** należy ograniczyć wartości kolumn do zakresu percentylu. 

    Załóżmy na przykład, że chcesz zachować tylko wartości z zakresu od 10-80 do percentyla, i Zastąp wszystkie pozostałe. Wybierz **percentyl**, a następnie wpisz 10 dla **wartości percentyl dolnego progu** i wpisz 80 dla **wartości percentyl dla górnego progu**. 

    Zapoznaj się z sekcją [percentyla](#examples-for-clipping-using-percentiles) , aby poznać kilka przykładów użycia zakresów percentylu.  
  
1.  Zdefiniuj wartość zastępczą.

    Liczby, które dokładnie pasują do określonych granic, są uznawane za mieszczące się w dozwolonym zakresie wartości i w tym przypadku nie są zastępowane. Wszystkie liczby, które wykraczają poza określony zakres, są zastępowane wartość zastępczą. 
  
    + **Podstawianie wartości szczytowych**: definiuje wartość do zastąpienia dla wszystkich wartości kolumn, które są większe niż określony próg.  
    + **Podstawianie wartości dla podszczytów**: definiuje wartość do użycia jako substytut dla wszystkich wartości kolumn, które są mniejsze niż określony próg.  
    + Jeśli używasz opcji **ClipPeaksAndSubpeaks** , możesz określić oddzielne wartości zastępcze dla górnych i niższych wartości.  

    Obsługiwane są następujące wartości zastępcze:  
  
    -   **Próg**: zamienia wartości przycinane na określoną wartość progową.  
  
    -   **Średnia**: zamienia wartości przycinane na średnią wartości kolumn. Średnia jest obliczana przed przycinaniem wartości.  
  
    -   **Mediana**: zamienia wartości przycinane na medianę wartości kolumn. Mediana jest obliczana przed przycinaniem wartości.   
  
    -   **Brak**. Zamienia wartości przycinane na brakującą wartość (pustą).  
  
1.  **Dodaj kolumny wskaźnika**: zaznacz tę opcję, jeśli chcesz wygenerować nową kolumnę, która informuje o tym, czy określona operacja przycinania została zastosowana do danych w tym wierszu. Ta opcja jest przydatna w przypadku testowania nowego zestawu wartości wycinków i podstawiania.  
  
1. **Flaga zastąpienia**: wskaż, w jaki sposób chcesz generować nowe wartości. Domyślnie **wartości klipów** konstruują nową kolumnę z wartościami szczytowymi przyciętymi do żądanego progu. Nowe wartości zastępują pierwotną kolumnę.  
  
    Aby zachować pierwotną kolumnę i dodać nową kolumnę z przyciętymi wartościami, usuń zaznaczenie tej opcji.  
  
1.  Prześlij potok.  
  
    Kliknij prawym przyciskiem myszy moduł **wartości klipów** i wybierz polecenie **Wizualizacja** lub wybierz moduł i przejdź do karty dane **wyjściowe** w prawym panelu, kliknij ikonę histogramu w danych **wyjściowych portu**, aby przejrzeć wartości i upewnić się, że operacja przycinania spełniała Twoje oczekiwania.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Przykłady dla wycinków przy użyciu percentylów

Aby zrozumieć, jak działa przycinanie przy użyciu percentylów, weź pod uwagę zestaw danych zawierający 10 wierszy, które mają jedno wystąpienie każdej z wartości 1-10.  
  
- Jeśli używasz percentylu jako górnego progu, na wartość 90 percentylu, 90 procent wszystkich wartości w zestawie danych musi być mniejsza niż ta wartość.  
  
- Jeśli używasz percentylu jako dolnego progu, wartość dla dziesiątego percentylu, 10 procent wszystkich wartości w zestawie danych, musi być mniejsza niż ta wartość.  
  
1.  Dla **zestawu progów** wybierz pozycję **ClipPeaksAndSubPeaks**.  
  
1.  W przypadku **górnego progu** wybierz **percentyl**, a dla **numeru percentylu** wpisz 90.  
  
1.  W przypadku **górnej wartości zastępczej** wybierz **brakującą wartość**.  
  
1.  W przypadku **dolnego progu** wybierz **percentyl**, a dla opcji **numer percentylu** wpisz 10.  
  
1.  W przypadku **dolnej wartości zastępczej** wybierz pozycję **brakująca wartość**.  
  
1.  Usuń zaznaczenie opcji **Zastąp flag** i wybierz opcję, **Dodaj kolumnę wskaźnika**.  
  
Teraz Wypróbuj ten sam potok, używając 60 jako progu górnego percentylu i 30 jako dolnego progu percentylu, a następnie użyj wartości progowej jako wartości zastępczej. W poniższej tabeli porównano te dwa wyniki:  
  
1.  Zamień na brakującą; Górny próg = 90; Dolny próg = 20  
  
1.  Zamień na próg; Górny percentyl = 60; Dolny percentyl = 40  
  
|Oryginalne dane|Zamień na brak|Zamień na próg|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|Prawda<br /><br /> Prawda<br /><br /> 3, FAŁSZ<br /><br /> 4, FAŁSZ<br /><br /> 5, FAŁSZ<br /><br /> 6, FAŁSZ<br /><br /> 7, FAŁSZ<br /><br /> 8, FAŁSZ<br /><br /> 9, FAŁSZ<br /><br /> TRUE|4, PRAWDA<br /><br /> 4, PRAWDA<br /><br /> 4, PRAWDA<br /><br /> 4, PRAWDA<br /><br /> 5, FAŁSZ<br /><br /> 6, FAŁSZ<br /><br /> 7, PRAWDA<br /><br /> 7, PRAWDA<br /><br /> 7, PRAWDA<br /><br /> 7, PRAWDA| 
 
## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
