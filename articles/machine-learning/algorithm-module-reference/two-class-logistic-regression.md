---
title: 'Two-Class regresja logistyczna: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać modułu regresja logistyczna Two-Class w Azure Machine Learning do tworzenia klasyfikatora binarnego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 2e29a666f4d478e11986f834cff94d9743223f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96012650"
---
# <a name="two-class-logistic-regression-module"></a>Two-Class moduł regresji logistycznej

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Użyj tego modułu, aby utworzyć model regresji logistycznej, który może służyć do przewidywania dwóch (i tylko dwóch) wyników. 

Regresja logistyczna to dobrze znana technika statystyczna, która jest używana do modelowania wielu rodzajów problemów. Ten algorytm to *nadzorowana Metoda uczenia* się;  w związku z tym musisz dostarczyć zestaw danych, który zawiera już wyniki do uczenia modelu.  

### <a name="about-logistic-regression"></a>Regresja logistyczna — informacje  

Regresja logistyczna to dobrze znana metoda w statystyce, która jest używana do przewidywania prawdopodobieństwa wyniku i jest szczególnie popularna w przypadku zadań klasyfikacji. Algorytm przewiduje prawdopodobieństwo wystąpienia zdarzenia przez dopasowanie danych do funkcji logistycznej.
  
W tym module algorytm klasyfikacji jest zoptymalizowany pod kątem dichotomous lub zmiennych binarnych. Jeśli potrzebujesz sklasyfikować wiele wyników, użyj modułu [regresja logistyczna](./multiclass-logistic-regression.md) dla wielu klas.

##  <a name="how-to-configure"></a>Jak skonfigurować  

Aby szkolić ten model, należy dostarczyć zestaw danych, który zawiera etykietę lub kolumnę klasy. Ponieważ ten moduł jest przeznaczony do rozwiązywania problemów z dwoma klasami, etykieta lub kolumna klasy musi zawierać dokładnie dwie wartości. 

Na przykład kolumna etykieta może być [głosowana] z możliwymi wartościami "yes" lub "No". Lub może to być [ryzyko kredytowe], z możliwymi wartościami "High" lub "Low". 
  
1.  Dodaj moduł **regresja logistyczna dla dwóch klas** do potoku.  
  
2.  Określ, w jaki sposób ma być szkolony model, ustawiając opcję **tworzenia trybu Trainer** .  
  
    -   **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować model, możesz podać określony zestaw wartości jako argumenty.  

    -   **Zakres parametrów**: Jeśli nie masz pewności co do najlepszych parametrów, możesz znaleźć optymalne parametry przy użyciu modułu [dostrajania parametrów modelu](tune-model-hyperparameters.md) . Podajesz kilka wartości, a Trainer wykonuje iterację wielu kombinacji ustawień, aby określić kombinację wartości, które tworzą najlepszy wynik.
  
3.  Aby uzyskać **tolerancję optymalizacji**, określ wartość progową, która ma być używana podczas optymalizowania modelu. Jeśli poprawa między iteracjami spadnie poniżej określonego progu, algorytm jest uznawany za zbieżny w rozwiązaniu, a szkolenie zostanie zatrzymane.  
  
4.  W przypadku **wag uregulowania** i **wagi L2** wpisz wartość, która ma być używana dla parametrów uregulowania L1 i L2. Dla obu tych zaleca się wartość różną od zera.  
     *Uregulowanie* to metoda zapobiegania zastępowaniu poprzez nakładanie się modeli o wartości skrajnego współczynnika. Uregulowanie działa przez dodanie kary, która jest skojarzona z wartościami współczynnika, do błędu hipotezy. W ten sposób dokładny model z wartościami współczynnika skrajnie byłby bardziej karany, ale mniej dokładny model z bardziej bardziej nieprawidłowymi wartościami będzie mniejszy.  
  
     Uregulowanie L1 i L2 mają różne efekty i używa.  
  
    -   L1 można zastosować do modeli rozrzedzonych, co jest przydatne podczas pracy z danymi o dużym wymiarze.  
  
    -   Z kolei w przypadku danych, które nie są rozrzedzone, preferowane jest uregulowanie L2.  
  
     Ten algorytm obsługuje liniową kombinację wartości rozliczania L1 i L2: to jest, jeśli <code>x = L1</code> i <code>y = L2</code> , następnie <code>ax + by = c</code> definiuje liniowy zakres warunków uregulowania.  
  
    > [!NOTE]
    >  Chcesz dowiedzieć się więcej na temat uregulowania L1 i L2? Poniższy artykuł zawiera omówienie sposobu, w jaki uregulowania L1 i L2 są różne i jak wpływają na ich dopasowanie, z przykładami kodu na potrzeby regresji logistycznej i modeli sieci neuronowych:  [w przypadku Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)  
    >
    > Dla modeli regresji logistycznej opracowano różne liniowe kombinacje warunków L1 i L2: na przykład [elastyczne uregulowanie netto](https://wikipedia.org/wiki/Elastic_net_regularization). Sugerujemy, aby odwoływać się do tych kombinacji w celu zdefiniowania kombinacji liniowej, która obowiązuje w modelu.
      
5.  W polu **rozmiar pamięci dla l-BFGS** określ ilość pamięci, która ma być używana dla optymalizacji *l-BFGS* .  
  
     L-BFGS oznacza "Limited Memory Broyden-Fletcher-Goldfarb-Shanno". Jest to algorytm optymalizacji, który jest popularny dla szacowania parametrów. Ten parametr wskazuje liczbę wcześniejszych pozycji i gradientów do przechowania w obliczeniach następnego kroku.  
  
     Ten parametr optymalizacji ogranicza ilość pamięci, która jest używana do obliczania następnego etapu i kierunku. W przypadku określenia mniejszej ilości pamięci szkolenie jest szybsze, ale mniej dokładne.  
  
6.  W przypadku **liczby losowej inicjatora** wpisz wartość całkowitą. Definiowanie wartości inicjatora jest ważne, jeśli chcesz, aby wyniki były odtwarzalne dla wielu przebiegów tego samego potoku.  
  
  
8. Dodawanie oznaczonego zestawu danych do potoku i uczenie modelu:

    + Jeśli ustawisz **tryb tworzenia Trainer** na **pojedynczy parametr**, Połącz znacznikowy zestaw danych i moduł [uczenie modelu](train-model.md) .  
  
    + Jeśli ustawisz **tryb Trainer** na **zakres parametrów**, Połącz znacznikowy zestaw danych i nauczysz model przy użyciu funkcji [dostrajania parametrów modelu](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Jeśli przejdziesz do zakresu parametrów w celu [nauczenia modelu](train-model.md), zostanie użyta tylko wartość domyślna na liście jednego parametru.  
    > 
    > W przypadku przekazania jednego zestawu wartości parametrów do modułu [strojenia modelu](tune-model-hyperparameters.md) , gdy oczekuje on zakresu ustawień dla każdego parametru, zignoruje wartości i użyje wartości domyślnych dla dodanej informacji.  
    > 
    > Jeśli wybierzesz opcję **zakres parametrów** i wprowadzisz pojedynczą wartość dla każdego parametru, ta pojedyncza wartość zostanie użyta w całym wyczyszczeniu, nawet jeśli inne parametry zmienią się w zakres wartości.  
  
9. Prześlij potok.  
  
## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:
 
  
+ Aby dokonać prognoz dotyczących nowych danych, użyj przeszkolonego modelu i nowych danych jako danych wejściowych w module [model oceny](./score-model.md) . 


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning.