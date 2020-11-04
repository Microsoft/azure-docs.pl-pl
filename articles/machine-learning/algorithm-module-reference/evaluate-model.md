---
title: 'Oceń model: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu oceny modelu w Azure Machine Learning do mierzenia dokładności przeszkolonego modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/27/2020
ms.openlocfilehash: 9abf5a17330566aee2414b8499f228d297880cbf
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323797"
---
# <a name="evaluate-model-module"></a>Oceń moduł modelu

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do mierzenia dokładności nauczonego modelu. Dostarczasz zestaw danych zawierający wyniki wygenerowane na podstawie modelu, a moduł **oceny modelu** obliczy zestaw metryk oceny standardowej w branży.
  
 Metryki zwracane przez **ocenę modelu** zależą od typu ocenianego modelu:  
  
-   **Modele klasyfikacji**    
-   **Modele regresji**  
-   **Modele klastrowania**  


> [!TIP]
> Jeśli dopiero zaczynasz korzystać z wersji ewaluacyjnej modelu, zalecamy korzystanie z serii wideo przez Dr. Stephen Elston w ramach [kursu uczenia maszynowego](/archive/blogs/machinelearning/new-edx-course-data-science-machine-learning-essentials) z usługi EdX. 


## <a name="how-to-use-evaluate-model"></a>Jak używać modelu szacowania
1. Połącz dane wyjściowe **zestawienia** danych wyjściowych [modelu wynikowego](./score-model.md) lub danych wyjściowych zestawu danych wynikowych [przypisywania danych do klastrów](./assign-data-to-clusters.md) do lewego portu wejściowego **oceny modelu**. 
    > [!NOTE] 
    > Jeśli używasz modułów takich jak "Wybieranie kolumn w zestawie danych", aby wybrać część wejściowego zestawu danych, upewnij się, że rzeczywista kolumna etykiety (używana w szkoleniu), kolumna "oceny prawdopodobieństwa" i "oceny etykiet" istnieją do obliczenia metryk, takich jak AUC, dokładność dla binarnej klasyfikacji/wykrywania anomalii.
    > Rzeczywista kolumna etykiet, kolumna "oceny etykiet" istnieje, aby obliczyć metryki dla klasyfikacji/regresji dla wieloklasowego.
    > Kolumna "przypisania", kolumny "DistancesToClusterCenter No. X ' (X jest indeksem centroida, od 0,..., liczba centroids-1) istnieje, aby obliczyć metryki dla klastrowania.

    > [!IMPORTANT]
    > + Aby oszacować wyniki, wyjściowy zestaw danych powinien zawierać określone nazwy kolumn oceny, które spełniają wymagania modułu oceny modelu.
    > + `Labels`Kolumna zostanie uznana za rzeczywiste etykiety.
    > + Dla zadania regresji zestaw danych do oceny musi mieć jedną kolumnę o nazwie `Regression Scored Labels` , która reprezentuje etykiety z wynikami.
    > + W przypadku zadania klasyfikacji binarnej zestaw danych do oceny musi mieć dwie kolumny o nazwie, `Binary Class Scored Labels` `Binary Class Scored Probabilities` która reprezentuje odpowiednio etykiety i prawdopodobieństwa.
    > + W przypadku zadania o wielu klasyfikacjach zestaw danych do oceny musi mieć jedną kolumnę o nazwie `Multi Class Scored Labels` , która reprezentuje etykiety z wynikami.
    > Jeśli dane wyjściowe modułu nadrzędnego nie mają tych kolumn, należy zmodyfikować zgodnie z powyższymi wymaganiami.

2. Obowiązkowe Połącz dane wyjściowe **zestawienia** danych wyjściowych [modelu oceny](./score-model.md) lub danych wyjściowych zestawu danych wynikowych przypisywania danych do klastrów dla drugiego modelu **na odpowiedni port wejściowy** **oceny modelu**. Możesz łatwo porównać wyniki z dwóch różnych modeli na tych samych danych. Dwa algorytmy wejściowe powinny być tym samym typem algorytmu. Lub można porównać wyniki z dwóch różnych przebiegów nad tymi samymi danymi z różnymi parametrami.

    > [!NOTE]
    > Typ algorytmu odnosi się do "klasyfikacji dwuklasowej", "klasyfikacji wieloklasowej", "regresji", "klastrowanie" w ramach "Machine Learning algorytmów". 

3. Prześlij potok w celu wygenerowania wyników oceny.

## <a name="results"></a>Wyniki

Po uruchomieniu **Oceń model** wybierz moduł, aby otworzyć panel nawigacyjny " **Oceń model** " po prawej stronie.  Następnie wybierz kartę dane **wyjściowe + dzienniki** i na tej karcie sekcja **wyprowadzania danych** zawiera kilka ikon. Ikona **wizualizacji** ma ikonę wykresu słupkowego i jest pierwszym sposobem wyświetlania wyników.

W przypadku klasyfikacji binarnej, po kliknięciu ikony **wizualizacji** , można wizualizować macierz kodu binarnego.
W przypadku wielokrotnej klasyfikacji można znaleźć plik wykresów macierzy o nieporozumieniu na karcie dane **wyjściowe i dzienniki** , tak jak poniżej:
> [!div class="mx-imgBorder"]
> ![Podgląd przekazanego obrazu](media/module/multi-class-confusion-matrix.png)

W przypadku łączenia zestawów danych z obydwoma danymi wejściowymi **modelu szacowania** wyniki będą zawierać metryki dla zestawu danych lub obu modeli.
Model lub dane dołączone do lewego portu są przedstawiane jako pierwsze w raporcie, a następnie metryki dla zestawu danych lub modelu dołączonego do właściwego portu.  

Na przykład poniższa ilustracja przedstawia porównanie wyników z dwóch modeli klastrowania utworzonych na podstawie tych samych danych, ale z innymi parametrami.  

![Comparing2Models](media/module/evaluate-2-models.png)  

Ponieważ jest to Model klastrowania, wyniki oceny różnią się od tego, czy porównano wyniki z dwóch modeli regresji, czy porównano dwa modele klasyfikacji. Jednak ogólna prezentacja jest taka sama. 

## <a name="metrics"></a>Metryki

W tej sekcji opisano metryki zwracane dla określonych typów modeli obsługiwanych do użycia z **modelem oszacowania** :

+ [modele klasyfikacji](#metrics-for-classification-models)
+ [modele regresji](#metrics-for-regression-models)
+ [modele klastrowania](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Metryki dla modeli klasyfikacji


Podczas oceniania binarnych modeli klasyfikacji są raportowane następujące metryki.
  
-   **Dokładność** mierzy dobrą jakość modelu klasyfikacji jako proporcje prawdziwych wyników do łącznych przypadków.  
  
-   **Precyzja** jest proporcją prawdziwych wyników dla wszystkich pozytywnych wyników. Precision = TP/(TP + FP)  
  
-   Funkcja **odwoływania** to ułamek całkowitej ilości odpowiednich wystąpień, które zostały faktycznie pobrane. Odwołaj = TP/(TP + FN)  
  
-   **Wynik F1** jest obliczany jako średnia ważona precyzji i wartość odwołania z zakresu od 0 do 1, gdzie idealny wynik jest równy 1.  
  
-   **AUC** mierzy obszar pod krzywą na podstawie prawdziwej liczby dodatniej na osi y i fałszywych wartości dodatnich na osi x. Ta Metryka jest przydatna, ponieważ udostępnia pojedynczą liczbę, która umożliwia porównywanie modeli różnych typów.  


### <a name="metrics-for-regression-models"></a>Metryki dla modeli regresji
 
Metryki zwracane dla modeli regresji zaprojektowano w celu oszacowania ilości błędu.  Model jest traktowany jak dopasować dane, jeśli różnica między wartościami obserwowanymi a przewidywanymi jest mała. Jednak, patrząc na wzorzec reszt (różnica między dowolnym przewidzianym punktem a odpowiadającą jej wartością rzeczywistą), może postanowić dużo o potencjalną bias w modelu.  
  
 Następujące metryki są zgłaszane do oceny modeli regresji.
  
- **Średnia wartość błędu bezwzględnego (Mae)** mierzy, jak blisko prognoz są rzeczywiste wyniki; w rezultacie niższy jest lepszy.  
  
- **Element główny średniej wartości kwadratowej (RMSE)** tworzy pojedynczą wartość, która podsumowuje błąd w modelu. Podniesienie różnica polega na tym, że Metryka nie uwzględnia różnicy między przekroczeniem prognozowania a podpowiadaniem.  
  
- **Względny błąd względny (Rae)** jest względną absolutną różnicą między wartościami oczekiwanymi i rzeczywistymi. względne, ponieważ średnia różnica jest dzielona przez średnią arytmetyczną.  
  
- **Względny kwadratowy błąd (RSE)** podobnie normalizuje łączny kwadratowy błąd przewidywanych wartości przez podzielenie przez łączny kwadrat błędów rzeczywistych wartości.  
  

  
- **Współczynnik wyznaczania** , często określany jako R <sup>2</sup>, reprezentuje siłę predykcyjną modelu jako wartość z przedziału od 0 do 1. Zero oznacza, że model jest losowo (wyjaśnia nic); 1 oznacza, że jest idealnym dopasowaniem. Należy zachować ostrożność w interpretacji wartości R<sup>2</sup> , ponieważ niskie wartości mogą być całkowicie normalne i mogą być podejrzane wysokie wartości.

###  <a name="metrics-for-clustering-models"></a>Metryki dla modeli klastrowania

Ponieważ modele klastrów znacznie różnią się od modeli klasyfikacji i regresji w wielu aspektach, funkcja [Oceń model](evaluate-model.md) zwraca również inny zestaw statystyk dla modeli klastrowania.  
  
 Statystyki zwracane przez Model klastrowania opisują liczbę punktów danych przypisanych do każdego klastra, wielkość rozdzielenia między klastrami i jak ściśle te punkty danych są dzielone w ramach poszczególnych klastrów.  
  
 Statystyki dla modelu klastrowania są uśredniane w całym zestawie danych, z dodatkowymi wierszami zawierającymi dane statystyczne na klaster.  
  
Następujące metryki są zgłaszane do oceny modeli klastrowania.
    
-   Wyniki w kolumnie, **Średnia odległość do innego centrum** , reprezentują jak blisko, średnio, każdy punkt w klastrze to centroids wszystkich innych klastrów.   

-   Wyniki w kolumnie, **Średnia odległość do centrum klastra** , reprezentują bliskość wszystkich punktów w klastrze do centroida tego klastra.  
  
-   Kolumna **liczba punktów** pokazuje, ile punktów danych zostało przypisanych do każdego klastra, oraz łączną całkowitą liczbę punktów danych w dowolnym klastrze.  
  
     Jeśli liczba punktów danych przypisanych do klastrów jest mniejsza niż całkowita liczba dostępnych punktów danych, oznacza to, że nie można przypisać punktów danych do klastra.  
  
-   Wyniki w kolumnie, **maksymalnie odległość do centrum klastra** , reprezentują maksymalną odległość między każdym punktem a centroida klastra tego punktu.  
  
     Jeśli ta liczba jest wysoka, może to oznaczać, że klaster jest szeroko rozpraszany. Należy zapoznać się z tą statystyką wraz z **średnim dystansem do centrum klastra** , aby określić rozmieszczenie klastra.   

-   **Łączny wynik oceny** u dołu każdej sekcji wyników zawiera listę średnich wyników dla klastrów utworzonych w tym konkretnym modelu.  
  

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning.