---
title: Dostrajanie hiperparametrów modelu
titleSuffix: Azure Machine Learning
description: Za pomocą modułu dostrajania parametrów modelu w projektancie można wykonać wyczyszczenie parametrów w celu dostosowania parametrów funkcji Hyper-Parameter.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: 2bbf75ba5de4ad20e11261bdcfd1204b1a0b0766
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420635"
---
# <a name="tune-model-hyperparameters"></a>Dostrajanie hiperparametrów modelu

W tym artykule opisano, jak używać modułu dostrajania parametrów modelu w programie Azure Machine Learning Designer. Celem jest określenie optymalnych parametrów dla modelu uczenia maszynowego. Moduł kompiluje i testuje wiele modeli przy użyciu różnych kombinacji ustawień. Porównuje metryki dla wszystkich modeli w celu uzyskania kombinacji ustawień. 

*Parametr* terms i *parametr preparameter* mogą być mylące. *Parametry* modelu są ustawiane w prawym okienku modułu. Zasadniczo ten moduł wykonuje *odczyszczenie parametrów* względem określonych ustawień parametrów. Poznasz optymalny zestaw _parametrów_ , które mogą być różne dla każdego określonego drzewa decyzyjnego, zestawu danych lub metody regresji. Proces znajdowania optymalnej konfiguracji jest czasami nazywany *dostrajaniem*. 

Moduł obsługuje następującą metodę wyszukiwania optymalnych ustawień modelu: *zintegrowane uczenie i dostrajanie.* W tej metodzie należy skonfigurować zestaw parametrów do użycia. Następnie pozwól, aby moduł przekroczy wiele kombinacji. Moduł mierzy dokładność do momentu znalezienia "najlepszego" modelu. W przypadku większości modułów szkoleniowych można wybrać parametry, które należy zmienić podczas procesu szkolenia, a które powinny pozostać stałe.

W zależności od tego, jak długo proces strojenia ma działać, możesz zdecydować się na wyczerpujące przetestowanie wszystkich kombinacji. Lub można skrócić proces, ustanawiając siatkę kombinacji parametrów i testując losowo podzestaw siatki parametrów.

Ta metoda generuje model szkolony, który można zapisać do ponownego użycia.  

> [!TIP] 
> Można wykonać powiązane zadanie. Przed rozpoczęciem dostrajania Zastosuj wybór funkcji, aby określić kolumny lub zmienne, które mają największą wartość informacji.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Jak skonfigurować dostrojenie parametrów modelu  

Poznanie optymalnych parametrów modelu uczenia maszynowego wymaga znacznego użycia potoków.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Uczenie modelu przy użyciu odchylenia parametrów  

W tej sekcji opisano, jak wykonać podstawowe odwzorowanie parametrów, które pociąga za pomocą modułu strojenia modelu.

1.  Dodaj moduł dostrajania parametrów modelu do potoku w projektancie.

2.  Połącz niepociąg model z lewej strony wejściowej. 

    > [!NOTE] 
    > **Dostrojenie parametrów modelu** może być nawiązywane tylko z wbudowanymi modułami algorytmu uczenia maszynowego i nie może obsługiwać dostosowanego modelu wbudowanego w **Tworzenie modelu języka Python**.


3.  Dodaj zestaw danych, który ma być używany na potrzeby szkolenia, i połącz go z środkowym wejściem parametrów strojenia modelu.  

    Opcjonalnie, jeśli masz oznakowany zestaw danych, możesz połączyć go z najbardziej przyłączonym portem wejściowym ( **opcjonalny zestaw danych walidacji** ). Pozwala to mierzyć dokładność podczas uczenia i dostrajania.

4.  W prawym panelu strojenia parametrów modelu wybierz wartość dla **trybu odchylenia parametru**. Ta opcja określa, jak są wybierane parametry.

    - **Cała siatka** : w przypadku wybrania tej opcji moduł jest pętlą względem siatki wstępnie zdefiniowanej przez system, aby wypróbować różne kombinacje i zidentyfikować najlepszą naukę. Ta opcja jest przydatna, gdy nie wiesz, jakie są ustawienia najlepszych parametrów, i chcesz wypróbować wszystkie możliwe kombinacje wartości.

    - **Losowe wyczyszczenie** : po wybraniu tej opcji moduł będzie losowo wybierać wartości parametrów w zakresie zdefiniowanym przez system. Należy określić maksymalną liczbę uruchomień wykonywanych przez moduł. Ta opcja jest przydatna, gdy chcesz zwiększyć wydajność modelu przy użyciu wybranych przez siebie metryk, ale nadal zachowuj zasoby obliczeniowe.    

5.  Dla **kolumny etykieta** Otwórz selektor kolumny, aby wybrać jedną kolumnę etykiety.

6.  Wybierz liczbę uruchomień:

    - **Maksymalna liczba przebiegów na losowym wyczyszczeniu: w** przypadku wybrania losowego wycierania można określić, ile razy model ma być szkolony, używając losowej kombinacji wartości parametrów.

7.  W celu **określania rankingu** wybierz pojedynczą metrykę, która ma być używana do klasyfikowania modeli.

    Po uruchomieniu odchylenia parametrów moduł oblicza wszystkie odpowiednie metryki dla typu modelu i zwraca je w raporcie **wyniki odchylenia** . Moduł używa oddzielnych metryk dla modeli regresji i klasyfikacji.

    Wybrana Metryka określa jednak, w jaki sposób modele są klasyfikowane. Tylko górny model, uporządkowany według wybranej metryki, jest wyprowadzany jako model przeszkolony do użycia na potrzeby oceniania.

8.  W przypadku **losowego inicjatora** wprowadź liczbę, która ma być używana do uruchamiania odchylenia parametrów. 

9. Prześlij potok.

## <a name="results-of-hyperparameter-tuning"></a>Wyniki strojenia parametru

Po zakończeniu szkolenia:

+ Aby wyświetlić wyniki odchylenia, kliknij prawym przyciskiem myszy moduł, a następnie wybierz polecenie **Wizualizacja** lub kliknij prawym przyciskiem myszy lewy port wyjściowy modułu do wizualizacji.

    **Wyniki odchylenia** obejmują wszystkie metryki odchylenia i dokładności parametrów, które są stosowane do typu modelu, a Metryka wybrana dla klasyfikacji określa model, który jest uważany za "najlepszy".

+ Aby zapisać migawkę przeszkolonego modelu, wybierz kartę **wyjściowe + dzienniki** w prawym panelu modułu **uczenie modelu** . Wybierz ikonę **zarejestruj zestaw danych** , aby zapisać model jako moduł wielokrotnego użytku.


## <a name="technical-notes"></a>Uwagi techniczne

Ta sekcja zawiera szczegóły i porady dotyczące implementacji.

### <a name="how-a-parameter-sweep-works"></a>Jak działa odczyszczenie parametrów

Podczas konfigurowania odchylenia parametrów należy zdefiniować zakres wyszukiwania. Wyszukiwanie może używać skończonej liczby parametrów wybranych losowo. Lub może to być wyczerpujące wyszukiwanie na zdefiniowanej przestrzeni parametrów.

+ **Losowe wyczyszczenie** : Ta opcja pociąga za model przy użyciu zestawu iteracji. 

  Należy określić zakres wartości do iteracji, a moduł używa losowo wybranego podzbioru tych wartości. Wartości są wybierane z zastępowaniem, co oznacza, że numery wcześniej wybrane losowo nie są usuwane z puli dostępnych numerów. Dlatego, że jakakolwiek wybrana wartość pozostaje taka sama we wszystkich przebiegach.  

+ **Cała siatka** : Opcja używania całej siatki oznacza, że każda kombinacja jest testowana. Ta opcja jest najbardziej dokładna, ale wymaga najwięcej czasu. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Kontrolowanie długości i złożoności szkolenia

Iterowanie wielu kombinacji ustawień może być czasochłonne, więc moduł oferuje kilka sposobów ograniczenia procesu:

+ Ogranicz liczbę iteracji używanych do testowania modelu.
+ Ogranicz obszar parametru.
+ Ogranicz liczbę iteracji i miejsce parametru.

Zalecamy, aby przetworzyć ustawienia, aby określić najbardziej wydajną metodę szkolenia dla określonego zestawu danych i modelu.

### <a name="choosing-an-evaluation-metric"></a>Wybieranie metryki oceny

Po zakończeniu testowania Model przedstawia raport zawierający informacje o dokładności dla każdego modelu, aby można było przejrzeć wyniki metryk:

- Jednolity zestaw metryk jest używany dla wszystkich modeli klasyfikacji binarnej.
- Dokładność jest używana dla wszystkich modeli klasyfikacji wieloklasowej.
- Dla modeli regresji jest używany inny zestaw metryk. 

Jednak podczas uczenia należy wybrać *jedną* metrykę do użycia w celu klasyfikowania modeli, które są generowane podczas procesu dostrajania. Może się okazać, że Najlepsza Metryka zależy od problemu biznesowego i kosztu fałszywych dodatnich i fałszywych wartości ujemnych.

#### <a name="metrics-used-for-binary-classification"></a>Metryki używane na potrzeby klasyfikacji binarnej

-   **Dokładność** jest proporcją prawdziwych wyników do łącznych przypadków.  

-   **Precyzja** jest proporcją prawdziwych wyników do pozytywnych wyników.  

-   Funkcja **odwoływania** to ułamek wszystkich poprawnych wyników dla wszystkich wyników.  

-   **F-Score** to miara, która równoważy precyzję i odzyskanie.  

-   **AUC** jest wartością, która reprezentuje obszar pod krzywą, gdy fałszywe wartości dodatnie są kreślone na osi x i prawdziwe wartości dodatnie są kreślone na osi y.  

-   **Średnia utrata dzienników** to różnica między dwoma dystrybucjami prawdopodobieństwa: wartość true i jeden w modelu.  

#### <a name="metrics-used-for-regression"></a>Metryki używane na potrzeby regresji

-   Średni **błąd bezwzględny** uśrednia wszystkie błędy w modelu, gdzie *błąd* oznacza odległość przewidywanej wartości od prawdziwej wartości. Często jest skracana jako *Mae*.  

-   **Element główny średniej wartości w kwadracie** mierzy średnią kwadratów błędów, a następnie pobiera element główny tej z nich. Często jest skracana jako *RMSE*.  

-   **Względny błąd bezwzględny** reprezentuje błąd jako procent wartości prawdziwej.  

-   **Kwadratowy błąd względny** normalizuje łączny kwadratowy błąd dzielący przez łączny kwadratowy błąd prognozowanych wartości.  

-   **Współczynnik wyznaczania** jest pojedynczą liczbą, która wskazuje, jak dobre dane pasują do modelu. Wartość jednego oznacza, że model dokładnie dopasowuje dane. Wartość zero oznacza, że dane są losowo lub w przeciwnym razie nie można dopasować do modelu. Często nazywa się *r <sup>2</sup>* , *r <sup>2</sup>* lub *r-kwadrat*.  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>Moduły, które nie obsługują odchylenia parametrów

Prawie wszyscy użytkownicy w Azure Machine Learning obsługują krzyżowe sprawdzanie poprawności przy użyciu zintegrowanego odchylenia parametru, który umożliwia wybranie parametrów do potoku. Jeśli dowiedzenie nie obsługuje ustawiania zakresu wartości, można nadal używać go do wzajemnego sprawdzania poprawności. W takim przypadku do odchylenia jest wybierany zakres dozwolonych wartości. 


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 

