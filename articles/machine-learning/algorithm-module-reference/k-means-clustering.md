---
title: 'K-oznacza klastrowanie: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać modułu klastrowanie K-oznaczanie w Azure Machine Learning do uczenia modeli klastrowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/04/2020
ms.openlocfilehash: 97cadfb8f5004cfd2701335172d4416c64f05259
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907865"
---
# <a name="module-k-means-clustering"></a>Moduł: K-oznacza klastrowanie

W tym artykule opisano, jak używać modułu *k-oznaczania klastrowania* w programie Azure Machine Learning Designer do tworzenia nauczenia modelu k-to-klastrów. 
 
K-oznacza to jeden z najprostszych i najlepszych znanych *nienadzorowanych* algorytmów uczenia. Można użyć algorytmu dla różnych zadań uczenia maszynowego, takich jak: 

* [Wykrywanie nieprawidłowych danych](https://msdn.microsoft.com/magazine/jj891054.aspx).
* Klastrowanie dokumentów tekstowych.
* Analizowanie zestawów danych przed użyciem innych metod klasyfikacji lub regresji. 

Aby utworzyć Model klastrowania, należy:

* Dodaj ten moduł do potoku.
* Połącz zestaw danych.
* Ustaw parametry, takie jak liczba oczekiwań klastrów, Metryka odległości do użycia podczas tworzenia klastrów itd. 
  
Po skonfigurowaniu parametrów modułu należy połączyć nauczenie modelu z [modelem klastra usługi Uczenie](train-clustering-model.md). Ze względu na to, że algorytm K-oznacza nienadzorowanej metody uczenia, kolumna etykiety jest opcjonalna. 

+ Jeśli dane zawierają etykietę, można użyć wartości etykiety do przewodnika po wyborze klastrów i zoptymalizować model. 

+ Jeśli dane nie mają etykiety, algorytm tworzy klastry reprezentujące możliwe kategorie na podstawie wyłącznie danych.  

##  <a name="understand-k-means-clustering"></a>Opis K-oznacza klastrowanie
 
Ogólnie rzecz biorąc, klastrowanie używa technik iteracyjnych do grupowania przypadków w zestawie danych do klastrów mających podobne cechy. Te grupowania są przydatne do eksplorowania danych, identyfikowania anomalii w danych, a ostatecznie do tworzenia prognoz. Modele klastrowania mogą również pomóc w identyfikowaniu relacji w zestawie danych, które mogą nie być logicznie wyprowadzane przez przeglądanie lub prostą obserwację. Z tego względu klaster jest często używany w wczesnych fazach zadań uczenia maszynowego, do eksplorowania danych i wykrywania nieoczekiwanych korelacji.  
  
 Podczas konfigurowania modelu klastrowania przy użyciu metody K-oznacza, należy określić liczbę docelową *k* , która wskazuje liczbę *centroids* , które mają być używane w modelu. Centroida to punkt, który jest reprezentatywny dla każdego klastra. Algorytm K-oznacza przypisuje każdy przychodzący punkt danych do jednego z klastrów przez zminimalizowanie łącznej liczby kwadratów w ramach klastra. 
 
Gdy przetwarza dane szkoleniowe, algorytm K-oznacza rozpoczyna się od początkowego zestawu losowo wybranych centroids. Centroids jako punkty wyjścia dla klastrów i stosują algorytm Lloyd's, aby iteracyjnie udoskonalać swoje lokalizacje. K-oznacza, że algorytm kończy Kompilowanie i udoskonalanie klastrów, gdy spełnia jeden lub więcej z następujących warunków:  
  
-   Centroids stabilny, co oznacza, że przypisania klastra dla poszczególnych punktów nie zmieniają się, a algorytm został zbieżny w rozwiązaniu.  
  
-   Algorytm zakończył pracę określoną liczbę iteracji.  
  
 Po zakończeniu fazy szkolenia należy użyć modułu [przypisz dane do klastrów](assign-data-to-clusters.md) , aby przypisać nowe przypadki do jednego z klastrów znalezionych przy użyciu algorytmu K-oznacza. Przypisanie klastra jest wykonywane przez obliczenie odległości między nowym przypadkiem a centroida każdego klastra. Każdy nowy przypadek jest przypisywany do klastra z najbliższą centroida.  

## <a name="configure-the-k-means-clustering-module"></a>Konfigurowanie modułu klastrowanie K-oznaczanie
  
1.  Dodaj moduł **klastrowanie K-oznaczanie** do potoku.  
  
2.  Aby określić sposób, w jaki model ma być szkolony, wybierz opcję **Utwórz tryb Trainer** .  
  
    -   **Pojedynczy parametr**: Jeśli znasz dokładne parametry, których chcesz użyć w modelu klastrowania, możesz podać określony zestaw wartości jako argumenty.  
  
3.  Dla **liczby centroids**wpisz liczbę klastrów, z którymi ma się rozpoczynać algorytm.  
  
     Model nie gwarantuje większej liczby klastrów. Algorytm rozpoczyna się od tej liczby punktów danych i iteruje, aby znaleźć optymalną konfigurację. Można odwołać się do [kodu źródłowego skryptu sklearn](https://github.com/scikit-learn/scikit-learn/blob/fd237278e/sklearn/cluster/_kmeans.py#L1069).
  
4.  **Inicjowanie** właściwości służy do określenia algorytmu, który jest używany do definiowania początkowej konfiguracji klastra.  
  
    -   **Pierwsze N**: pewna początkowa liczba punktów danych jest wybierana z zestawu danych i używana jako początkowy sposób. 
    
         Ta metoda jest również nazywana *metodą Forgy*.  
  
    -   **Losowo**: algorytm losowo umieszcza punkt danych w klastrze, a następnie oblicza początkową wartość jako centroidaą losowo przypisanych punktów klastra. 

         Ta metoda jest również nazywana metodą *losowej partycji* .  
  
    -   **K-oznacza**, że jest to domyślna metoda inicjowania klastrów.  
  
         Algorytm **k----+ +** został zaproponowany w 2007 przez David Arthur i Sergei Vassilvitskii, aby uniknąć słabych klastrów przez standardowy K-oznacza algorytm. **K-oznacza** , że program + + jest ulepszony na standardowym poziomie K — przy użyciu innej metody do wyboru początkowych centrów klastra.  
  
    
5.  W przypadku **liczby losowej inicjatora**opcjonalnie wpisz wartość, która ma być używana jako inicjator dla inicjowania klastra. Ta wartość może mieć znaczny wpływ na wybór klastra.  
  
6.  W polu **Metryka**wybierz funkcję, która ma być używana do mierzenia odległości między wektorami klastra lub między nowymi punktami danych i losowo wybranym centroida. Azure Machine Learning obsługuje następujące metryki dotyczące odległości klastra:  
  
    -   **Euclidean**: odległość Euclidean jest często używana jako miara punktowego klastra dla K-oznacza klastrowanie. Ta Metryka jest preferowana, ponieważ minimalizuje średnią odległość między punktami i centroids.
  
7.  W przypadku **iteracji**wpisz, ile razy algorytm powinien wykonać iterację danych szkoleniowych, zanim zakończy wybór centroids.  
  
     Można dostosować ten parametr, aby zrównoważy dokładność przed czasem szkolenia.  
  
8.  W obszarze **przypisz etykietę**wybierz opcję, która określa, w jaki sposób kolumna etykiety, jeśli znajduje się w zestawie danych, powinna być obsługiwana.  
  
     Ponieważ K-oznacza, że usługa klastrowania jest nienadzorowanym sposobem uczenia maszynowego, etykiety są opcjonalne. Jeśli jednak zestaw danych ma już kolumnę etykieta, można użyć tych wartości do zaznaczania klastrów lub można określić, że wartości będą ignorowane.  
  
    -   **Ignoruj kolumnę etykiety**: wartości w kolumnie etykieta są ignorowane i nie są używane podczas tworzenia modelu.
  
    -   **Wypełnij brakujące wartości**: wartości kolumn etykieta są używane jako funkcje ułatwiające tworzenie klastrów. Jeśli w dowolnym wierszu brakuje etykiety, wartość jest ustawiana za pomocą innych funkcji.  
  
    -   **Zastąp z najbliżej Center**: wartości kolumny etykieta są zamieniane na wartości z przewidywanymi etykietami, używając etykiety punktu znajdującego się najbliżej bieżącej centroida.  

8.  Wybierz opcję **normalizing Features** , jeśli chcesz znormalizować funkcje przed szkoleniem.
  
     W przypadku zastosowania normalizacji przed przeprowadzeniem szkolenia punkty danych są znormalizowane `[0,1]` przez MinMaxNormalizer.

10. Uczenie modelu.  
  
    -   Jeśli ustawisz **tryb Trainer** na **pojedynczy parametr**, Dodaj oznakowany zestaw danych i nauczysz model przy użyciu modułu [uczenie klastra](train-clustering-model.md) .  
  
## <a name="results"></a>Wyniki

Po zakończeniu konfigurowania i uczenia modelu masz model, którego można użyć do generowania wyników. Istnieje jednak wiele sposobów uczenia modelu i wiele sposobów wyświetlania wyników i korzystania z nich: 

### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Przechwytywanie migawki modelu w obszarze roboczym

Jeśli użyto modułu [klastra uczenia](train-clustering-model.md) :

1. Wybierz moduł **uczenie modelu klastra** i Otwórz prawy panel.

2. Wybierz kartę dane **wyjściowe** . Wybierz ikonę **zarejestruj zestaw danych** , aby zapisać kopię nauczonego modelu.

Zapisany model reprezentuje dane szkoleniowe w momencie zapisania modelu. Jeśli później zaktualizujesz dane szkoleniowe używane w potoku, nie zaktualizujesz zapisanego modelu. 

### <a name="see-the-clustering-result-dataset"></a>Zobacz zestaw danych wyników klastrowania 

Jeśli użyto modułu [klastra uczenia](train-clustering-model.md) :

1. Kliknij prawym przyciskiem myszy moduł **uczenie klastra** .

2. Wybierz opcję **Wizualizuj**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Wskazówki dotyczące generowania najlepszego modelu klastra  

Wiadomo *, że proces* umieszczania, który jest używany podczas klastrowania, może znacząco wpłynąć na model. Rozmieszczanie oznacza początkowe rozmieszczenie punktów w potencjalną centroids.
 
Na przykład, jeśli zestaw danych zawiera wiele wartości odstających, a wartość odstająca jest wybierana do wypełniania klastrów, żadne inne punkty danych nie będą dobrze dopasowane do tego klastra, a klaster może być pojedynczy. Oznacza to, że może on mieć tylko jeden punkt.  
  
Można uniknąć tego problemu na kilka sposobów:  
  
-   Zmień liczbę centroids i spróbuj użyć wielu wartości inicjatora.  
  
-   Utwórz wiele modeli, zmieniając metrykę lub przeiterj więcej.  
  
Ogólnie rzecz biorąc, w przypadku modeli klastrowania istnieje możliwość, że każda dana konfiguracja spowoduje przeprowadzenie lokalnie zoptymalizowanego zestawu klastrów. Innymi słowy, zestaw klastrów, które są zwracane przez model, odpowiada tylko bieżącym punktom danych i nie można go uogólniać do innych danych. W przypadku korzystania z innej konfiguracji początkowej Metoda K-oznacza, że może znaleźć inną, wyższą konfigurację. 

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
