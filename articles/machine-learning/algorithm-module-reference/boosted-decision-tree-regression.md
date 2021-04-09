---
title: 'Regresja drzewa decyzyjnej: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób użyć modułu regresja drzewa decyzyjnego w Azure Machine Learning, aby utworzyć zbiór drzew regresji przy użyciu podwyższania poziomu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 664943fc5535883b3df77b2795383e5c0586a71c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94375333"
---
# <a name="boosted-decision-tree-regression-module"></a>Moduł regresji drzewa decyzyjnego

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do tworzenia kompletu drzew regresji przy użyciu zwiększania. *Zwiększenie wydajności* oznacza, że każde drzewo jest zależne od wcześniejszych drzew. Algorytm uczy się, instalując resztę drzew, które je poprzedza. W tym celu zwiększenie dokładności w drzewie decyzyjnym pozwala zwiększyć dokładność z mniejszym ryzykiem.  

Ten moduł jest oparty na algorytmie LightGBM.
  
Ta metoda regresji jest metodą uczenia nadzorowanego i dlatego wymaga *oznaczonego zestawu danych*. Kolumna Label musi zawierać wartości numeryczne.  

> [!NOTE]
> Tego modułu należy używać tylko z zestawami danych, które używają zmiennych liczbowych.  

Po zdefiniowaniu modelu nauczenie go przy użyciu [modelu uczenia](./train-model.md).

  
## <a name="more-about-boosted-regression-trees"></a>Więcej informacji o wzmocnionych drzewach regresji  

Zwiększenie wydajności jest jedną z wielu metod klasycznych do tworzenia modeli zestawów, a także do worków, losowych lasów i tak dalej.  W Azure Machine Learning, podwyższane drzewa decyzyjne wykorzystują wydajną implementację algorytmu zwiększania gradientów SKŁADNI. Zwiększanie gradientu jest techniką uczenia maszynowego w przypadku problemów z regresją. Kompiluje Każde drzewo regresji w sposób funkcjonalny, przy użyciu wstępnie zdefiniowanej funkcji utraty do pomiaru błędu w każdym kroku i poprawnego dla niego w następnym. W ten sposób model przewidywania jest w rzeczywistości integralną częścią modeli predykcyjnych.  
  
W przypadku problemów z regresją zwiększenie wydajności kompiluje szereg drzew w sposób krokowy, a następnie wybiera optymalne drzewo przy użyciu dowolnej funkcji differentiable strat.  
  
Aby uzyskać dodatkowe informacje, zobacz następujące artykuły:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Ten artykuł w witrynie Wikipedia na temat zwiększania gradientów zawiera niektóre w tle dla podwyższających drzew. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: od RankNet do LambdaRank do LambdaMART: Omówienie. Według J.C. Burges.

Metodę zwiększania gradientu można również użyć w przypadku problemów z klasyfikacją przez zredukowanie ich do regresji przy użyciu odpowiedniej funkcji strat. Aby uzyskać więcej informacji na temat wdrażania podwyższanych drzew dla zadań klasyfikacji, zobacz [dwuklasowe drzewo decyzyjne](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Jak skonfigurować regresję drzewa decyzyjnej podwyższania

1.  Dodaj do potoku moduł **podwyższający decyzje** . Ten moduł można znaleźć w obszarze **Machine Learning**, **zainicjować**, w kategorii **regresja** . 
  
2.  Określ, w jaki sposób ma być szkolony model, ustawiając opcję **tworzenia trybu Trainer** .  
  
    -   **Pojedynczy parametr**: zaznacz tę opcję, Jeśli wiesz, jak chcesz skonfigurować model i udostępnić określony zestaw wartości jako argumenty. 
     
    -   **Zakres parametrów**: Wybierz tę opcję, jeśli nie masz pewności co do najlepszych parametrów i chcesz uruchomić odchylenia parametrów. Wybierz zakres wartości do iteracji, a [Parametry dostrojenia modelu](tune-model-hyperparameters.md) przechodzą na wszystkie możliwe kombinacje ustawień, które podano, aby określić parametry, które generują optymalne wyniki.    
   
  
3. **Maksymalna liczba liści na drzewo**: wskazuje maksymalną liczbę węzłów terminalu (liści), które można utworzyć w dowolnym drzewie.  

    Zwiększając tę wartość, można zwiększyć rozmiar drzewa i uzyskać lepszą precyzję w przypadku ryzyka przekroczenia i dłuższego czasu uczenia się.  

4. **Minimalna liczba próbek na węzeł liścia**: wskazuje minimalną liczbę przypadków wymaganych do utworzenia dowolnego węzła terminalu w drzewie.

    Zwiększenie tej wartości spowoduje zwiększenie wartości progowej tworzenia nowych reguł. Na przykład, z wartością domyślną 1, nawet pojedynczy przypadek może spowodować utworzenie nowej reguły. W przypadku zwiększenia wartości do 5 dane szkoleniowe muszą zawierać co najmniej 5 przypadków, które spełniają te same warunki.

5. **Szybkość uczenia**: wpisz liczbę z zakresu od 0 do 1, która definiuje rozmiar kroku podczas uczenia się. Szybkość uczenia określa, jak szybko lub wolno dowiedzieć się zbieżność z optymalnym rozwiązaniem. Jeśli rozmiar tego kroku jest zbyt duży, można przekroczyć optymalne rozwiązanie. Jeśli rozmiar tego kroku jest zbyt mały, szkolenie trwa dłużej niż w przypadku najlepszego rozwiązania.

6. **Liczba skonstruowanych drzew**: wskazuje całkowitą liczbę drzew decyzyjnych do utworzenia w całości. Przez utworzenie większej liczby drzew decyzyjnych można potencjalnie uzyskać lepszy zakres, ale wydłużyć czas uczenia.

    Jeśli wartość jest ustawiona na 1; jednak tworzone jest tylko jedno drzewo (drzewo z początkowym zestawem parametrów) i nie są wykonywane żadne dalsze iteracje.

7. **Inicjator liczb losowych**: wpisz opcjonalną nieujemną liczbę całkowitą, która ma być używana jako wartość inicjatora losowego. Określenie inicjatora zapewnia odtwarzalność w przebiegach, które mają te same dane i parametry.

    Domyślnie losowy inicjator jest ustawiony na 0, co oznacza, że początkowa wartość inicjatora jest uzyskiwana z zegara systemowego.
  

9. Uczenie modelu:

    + Jeśli ustawisz **tryb tworzenia Trainer** na **pojedynczy parametr**, Połącz znacznikowy zestaw danych i moduł [uczenie modelu](train-model.md) .  
  
    + Jeśli ustawisz **tryb Trainer** na **zakres parametrów**, Połącz znacznikowy zestaw danych i nauczysz model przy użyciu funkcji [dostrajania parametrów modelu](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Jeśli przejdziesz do zakresu parametrów w celu [nauczenia modelu](train-model.md), zostanie użyta tylko wartość domyślna na liście jednego parametru.  
    > 
    > W przypadku przekazania jednego zestawu wartości parametrów do modułu [strojenia modelu](tune-model-hyperparameters.md) , gdy oczekuje on zakresu ustawień dla każdego parametru, zignoruje wartości i użyje wartości domyślnych dla dodanej informacji.  
    > 
    > Jeśli wybierzesz opcję **zakres parametrów** i wprowadzisz pojedynczą wartość dla każdego parametru, ta pojedyncza wartość zostanie użyta w całym wyczyszczeniu, nawet jeśli inne parametry zmienią się w zakres wartości.
    

10. Prześlij potok.  
  
## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby użyć modelu do oceniania, Połącz [model uczenia](train-model.md) z [modelem oceny](./score-model.md), aby przewidzieć wartości dla nowych przykładów wejściowych.

+ Aby zapisać migawkę przeszkolonego modelu, wybierz kartę Output (dane **wyjściowe** ) w prawym panelu **przeszkolonego modelu** i kliknij ikonę **zarejestruj zestaw danych** . Kopia przeszkolonego modelu zostanie zapisana jako moduł w drzewie modułów i nie będzie aktualizowana po kolejnych uruchomieniach potoku.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
