---
title: 'Regresja liniowa: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu regresja liniowa w Azure Machine Learning, aby utworzyć model regresji liniowej do użycia w potoku.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 809b1be4f9f12e1963ff5caeaacd109c84db154f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93323650"
---
# <a name="linear-regression-module"></a>Moduł regresji liniowej
W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Użyj tego modułu, aby utworzyć model regresji liniowej do użycia w potoku.  Regresja liniowa próbuje nawiązać liniową relację między jedną lub wieloma niezależnymi zmiennymi oraz wynikiem liczbowym lub zmienną zależną. 

Ten moduł służy do definiowania metody regresji liniowej, a następnie uczenia modelu przy użyciu oznaczonego zestawu danych. Model przeszkolony może być następnie używany do prognozowania.

## <a name="about-linear-regression"></a>Regresja liniowa — informacje

Regresja liniowa to wspólna metoda statystyczna, która została przyjęta w uczeniu maszynowym i ulepszona z wieloma nowymi metodami dopasowania linii i błędów pomiaru. Po prostu funkcja regresji odwołuje się do przewidywania liczbowych elementów docelowych. Regresja liniowa jest nadal dobrym wyborem, gdy chcesz utworzyć prosty model dla podstawowego zadania predykcyjnego. Regresja liniowa również znacznie działa w przypadku wysoce wielowymiarowych, rozrzedzonych zestawów danych, które nie mają złożoności.

Oprócz regresji liniowej Azure Machine Learning obsługuje różne modele regresji. Jednak termin "regresja" może być interpretowany w sposób luźny, a niektóre typy regresji dostępne w innych narzędziach nie są obsługiwane.

+ Klasyczny problem z regresją dotyczy pojedynczej zmiennej niezależnej i zmiennej zależnej. Ta nazwa jest nazywana *prostą regresją*.  Ten moduł obsługuje prostą regresję.

+ *Wielokrotne regresja liniowa* obejmuje dwie lub więcej niezależnych zmiennych, które przyczyniają się do pojedynczej zmiennej zależnej. Problemy, w których wielokrotne dane wejściowe są używane do przewidywania pojedynczego wyniku liczbowego, są również nazywane *regresją liniową wieloczynnikowa*.

    Moduł **regresja liniowa** może rozwiązać te problemy, jak większość innych modułów regresji.

+ *Regresja z wieloma etykietami* to zadanie przewidywania wielu zmiennych zależnych w ramach jednego modelu. Na przykład w przypadku regresji logistycznej z wieloma etykietami można przypisać przykład do wielu różnych etykiet. (Różni się to od zadania przewidywania wielu poziomów w ramach pojedynczej zmiennej klasy).

    Ten typ regresji nie jest obsługiwany w Azure Machine Learning. Aby przewidzieć wiele zmiennych, należy utworzyć osobne informacje o danych wyjściowych, które mają być przewidywane.

W latach statystyków opracowano coraz bardziej zaawansowane metody regresji. Jest to prawdziwe nawet w przypadku regresji liniowej. Ten moduł obsługuje dwie metody pomiaru błędu i dopasowuje linię regresji: Metoda zwykła najmniejsza kwadraty i Gradient.

- **Nachylenie gradientu** to metoda, która minimalizuje ilość błędów w każdym kroku procesu szkolenia modelu. Istnieje wiele różnic w odniesieniu do gradientu i jego Optymalizacja dla różnych problemów szkoleniowych została rozległie przetestowana. W przypadku wybrania tej opcji dla **metody rozwiązania** można ustawić różne parametry, aby kontrolować rozmiar kroku, szybkość uczenia i tak dalej. Ta opcja obsługuje również korzystanie z odchylenia zintegrowanego parametru.

- **Zwykłe najmniejsze kwadraty** są jedną z najczęściej używanych technik w regresji liniowej. Na przykład najmniejsza kwadraty to metoda, która jest używana w dodatku Analysis ToolPak dla programu Microsoft Excel.

    Zwykłe najmniejsze kwadraty odnoszą się do funkcji strat, która oblicza błąd jako sumę kwadratową odległości od wartości rzeczywistej do przewidywanego wiersza i dopasowuje model przez zminimalizowanie kwadratowego błędu. Ta metoda zakłada silną liniową relację między danymi wejściowymi i zmienną zależną.

## <a name="configure-linear-regression"></a>Konfigurowanie regresji liniowej

Ten moduł obsługuje dwie metody dostosowania modelu regresji z różnymi opcjami:

+ [Dopasuj model regresji przy użyciu zwykłych, najmniejszych kwadratów](#create-a-regression-model-using-ordinary-least-squares)

    W przypadku małych zestawów danych najlepiej wybrać zwykłe, najmniejsze kwadraty. Powinno to dawać podobne wyniki do programu Excel.
    
+ [Tworzenie modelu regresji przy użyciu gradientu online](#create-a-regression-model-using-online-gradient-descent)

    Nachylenie gradientu to lepsza funkcja strat dla modeli, które są bardziej skomplikowane lub które mają zbyt małe dane szkoleniowe z uwzględnieniem liczby zmiennych.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Tworzenie modelu regresji przy użyciu zwykłych, najmniejszych kwadratów

1. Dodaj moduł **modelu regresji liniowej** do potoku w projektancie.

    Ten moduł można znaleźć w kategorii **Machine Learning** . Rozwiń węzeł **zainicjuj model**, rozwiń pozycję **regresja**, a następnie przeciągnij moduł **model regresji liniowej** do potoku.

2. W okienku **Właściwości** na liście rozwijanej **Metoda rozwiązania** wybierz pozycję zwykłe, **najmniejsze kwadraty**. Ta opcja określa metodę obliczeń używaną do znajdowania linii regresji.

3. W polu **waga uregulowania L2** wpisz wartość, która ma być używana jako waga dla uregulowania L2. Zalecamy użycie wartości innej niż zero, aby uniknąć przełączenia.

     Aby dowiedzieć się więcej o tym, jak uregulowanie wpływa na dopasowanie modelu, zobacz ten artykuł: [uregulowanie L1 i L2 dla Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)

4. Wybierz opcję, **Dołącz termin przechwycenia**, jeśli chcesz wyświetlić termin przechwycenia.

    Usuń zaznaczenie tej opcji, jeśli nie musisz przeglądać formuły regresji.

5. W przypadku **liczby losowej inicjatora** można opcjonalnie wpisać wartość, aby wypełniać generator liczb losowych używany przez model.

    Użycie wartości inicjatora jest przydatne, jeśli chcesz zachować te same wyniki w różnych przebiegach tego samego potoku. W przeciwnym razie wartością domyślną jest użycie wartości z zegara systemowego.


7. Dodaj moduł [uczenie modelu](./train-model.md) do potoku i Połącz zestaw danych z etykietą.

8. Prześlij potok.

### <a name="results-for-ordinary-least-squares-model"></a>Wyniki dla modelu z najniższymi kwadratami

Po zakończeniu szkolenia:


+ Aby dokonać prognoz, Połącz model przeszkolony z modułem [modelu oceny](./score-model.md) wraz z zestawem danych nowych wartości. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Tworzenie modelu regresji przy użyciu gradientu online

1. Dodaj moduł **modelu regresji liniowej** do potoku w projektancie.

    Ten moduł można znaleźć w kategorii **Machine Learning** . Rozwiń węzeł **zainicjuj model**, rozwiń **regresję** i przeciągnij moduł **modelu regresji liniowej** do potoku

2. W okienku **Właściwości** na liście rozwijanej **Metoda rozwiązania** wybierz **gradient online** , który jest używany do znajdowania linii regresji.

3. W przypadku **tworzenia trybu Trainer** wskaż, czy chcesz nauczyć model ze wstępnie zdefiniowanym zestawem parametrów, czy chcesz zoptymalizować model przy użyciu odchylenia parametrów.

    + **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować sieć regresji liniowej, możesz podać określony zestaw wartości jako argumenty.
    
    + **Zakres parametrów**: Wybierz tę opcję, jeśli nie masz pewności co do najlepszych parametrów i chcesz uruchomić odchylenia parametrów. Wybierz zakres wartości do iteracji, a [Parametry dostrojenia modelu](tune-model-hyperparameters.md) przechodzą na wszystkie możliwe kombinacje ustawień, które podano, aby określić parametry, które generują optymalne wyniki.  

   
4. W polu **stawka szkoleniowa** określ początkową stawkę szkoleniową dla stochastycznegoego gradientu.

5. W przypadku **liczby epoki szkoleniowej** wpisz wartość wskazującą, ile razy algorytm ma wykonać iterację za pomocą przykładów. W przypadku zestawów danych z niewielką liczbą przykładów ten numer powinien być duży, aby osiągnąć zbieżność.

6. **Normalizing Features**: Jeśli zostały już znormalizowane dane liczbowe używane do uczenia modelu, możesz usunąć zaznaczenie tej opcji. Domyślnie moduł normalizuje wszystkie dane wejściowe liczbowe do zakresu od 0 do 1.

    > [!NOTE]
    > 
    > Pamiętaj, aby zastosować tę samą metodę normalizacji do nowych danych używanych do oceniania.

7. W polu **waga uregulowania L2** wpisz wartość, która ma być używana jako waga dla uregulowania L2. Zalecamy użycie wartości innej niż zero, aby uniknąć przełączenia.

    Aby dowiedzieć się więcej o tym, jak uregulowanie wpływa na dopasowanie modelu, zobacz ten artykuł: [uregulowanie L1 i L2 dla Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)


9. Wybierz opcję, aby **zmniejszyć szybkość** uczenia, jeśli chcesz, aby szybkość uczenia była zmniejszana w miarę postępu iteracji.  

10. W przypadku **liczby losowej inicjatora** można opcjonalnie wpisać wartość, aby wypełniać generator liczb losowych używany przez model. Użycie wartości inicjatora jest przydatne, jeśli chcesz zachować te same wyniki w różnych przebiegach tego samego potoku.


12. Uczenie modelu:

    + Jeśli ustawisz **tryb tworzenia Trainer** na **pojedynczy parametr**, Połącz znacznikowy zestaw danych i moduł [uczenie modelu](train-model.md) .  
  
    + Jeśli ustawisz **tryb Trainer** na **zakres parametrów**, Połącz znacznikowy zestaw danych i nauczysz model przy użyciu funkcji [dostrajania parametrów modelu](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Jeśli przejdziesz do zakresu parametrów w celu [nauczenia modelu](train-model.md), zostanie użyta tylko wartość domyślna na liście jednego parametru.  
    > 
    > W przypadku przekazania jednego zestawu wartości parametrów do modułu [strojenia modelu](tune-model-hyperparameters.md) , gdy oczekuje on zakresu ustawień dla każdego parametru, zignoruje wartości i użyje wartości domyślnych dla dodanej informacji.  
    > 
    > Jeśli wybierzesz opcję **zakres parametrów** i wprowadzisz pojedynczą wartość dla każdego parametru, ta pojedyncza wartość zostanie użyta w całym wyczyszczeniu, nawet jeśli inne parametry zmienią się w zakres wartości.

13. Prześlij potok.

### <a name="results-for-online-gradient-descent"></a>Wyniki dla gradientu online

Po zakończeniu szkolenia:

+ Aby dokonać prognoz, Połącz model przeszkolony z modułem [modelu oceny](./score-model.md) wraz z nowymi danymi wejściowymi.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning.