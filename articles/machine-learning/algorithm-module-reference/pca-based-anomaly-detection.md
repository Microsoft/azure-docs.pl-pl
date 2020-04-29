---
title: 'Wykrywanie anomalii oparte na UPW: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać modułu wykrywania anomalii opartego na usłudze UPW do tworzenia modelu wykrywania anomalii na podstawie głównej analizy składników (PPW).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 0672b9769feae65c73a6f752a268968a7bad9e4b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502986"
---
# <a name="pca-based-anomaly-detection"></a>Wykrywanie anomalii oparte na analizie głównych składowych

W tym artykule opisano, jak używać modułu **wykrywania anomalii opartego na usłudze UPW** w programie Azure Machine Learning Designer (wersja zapoznawcza) w celu utworzenia modelu wykrywania anomalii na podstawie głównej analizy składników (PPW).

Ten moduł ułatwia tworzenie modeli w scenariuszach, w których łatwo jest uzyskać dane szkoleniowe z jednej klasy, takie jak prawidłowe transakcje, ale trudno jest uzyskać wystarczającą ilość próbek dla określonych anomalii. 

Na przykład w celu wykrywania fałszywych transakcji bardzo często nie masz wystarczającej liczby przykładów oszustw do uczenia się, ale masz wiele przykładów dobrych transakcji. Moduł **wykrywania anomalii opartej na UPW** rozwiązuje problem, analizując dostępne funkcje, aby określić, co stanowi Klasa "normalna" i stosując metryki odległości, aby identyfikować przypadki, które reprezentują anomalie. Dzięki temu można nauczyć model przy użyciu istniejących niezrównoważonych danych.

## <a name="more-about-principal-component-analysis"></a>Więcej informacji o głównej analizie składników

*Podstawowa analiza składników*, która jest często skracana do UPW, jest ustaloną techniką w uczeniu maszynowym. Usługa PPW jest często używana w analizie danych, ponieważ ujawnia wewnętrzną strukturę danych i objaśnia wariancję danych.

UPW działa przez analizowanie danych, które zawierają wiele zmiennych. Szuka korelacji między zmiennymi i określa kombinację wartości, które najlepiej przechwytuje różnice w wyników. Te połączone wartości funkcji są używane do tworzenia bardziej kompaktowego miejsca funkcji o nazwie *składniki główne*.

W przypadku wykrywania anomalii każde nowe dane wejściowe jest analizowane, a algorytm wykrywania anomalii oblicza jego projekcję na eigenvectors, wraz z znormalizowanym błędem odbudowy. Znormalizowany błąd jest używany jako wynik anomalii. Im większy błąd, tym bardziej anomalia wystąpienia.

Aby uzyskać więcej informacji o działaniu UPW oraz o implementacji wykrywania anomalii, zobacz następujące dokumenty:

- [Algorytm losowy dla analizy składników głównych](https://arxiv.org/abs/0809.2274). Rokhlin, Szlan i Tygert

- [Znajdowanie struktury z losowością: algorytmy probabilistyczne dla konstruowania przybliżonych rozkładów macierzy](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (pobieranie plików PDF). Halko, Martinsson i tropp.

## <a name="how-to-configure-pca-anomaly-detection"></a>Jak skonfigurować wykrywanie anomalii Asystenta zgodności

1. Dodaj moduł **wykrywania anomalii opartego na UPW** do potoku w projektancie. Ten moduł można znaleźć w kategorii **wykrywania anomalii** .

2. W panelu po prawej stronie modułu **wykrywania anomalii opartego na UPW** kliknij opcję **tryb uczenia** i wskaż, czy chcesz nauczyć model przy użyciu określonego zestawu parametrów, czy użyć odchylenia parametrów w celu znalezienia najlepszych parametrów.

    - **Pojedynczy parametr**: zaznacz tę opcję, Jeśli wiesz, jak chcesz skonfigurować model i udostępnić określony zestaw wartości jako argumenty.

3. **Liczba składników do użycia w UPW**: Określ liczbę funkcji wyjściowych lub składników, które chcesz wyprowadzić.

    Decyzja o liczbie składników do uwzględnienia jest ważną częścią projektu eksperymentu przy użyciu UPW. Ogólne wskazówki polegają na tym, że nie należy uwzględniać tej samej liczby składników Asystenta zgodności, ponieważ istnieją zmienne. Zamiast tego należy zacząć od pewnej mniejszej liczby składników i zwiększyć je do momentu spełnienia niektórych kryteriów.

    Najlepsze wyniki są uzyskiwane, gdy liczba składników wyjściowych jest **mniejsza niż** liczba kolumn funkcji dostępnych w zestawie danych.

4. Określ ilość przepróbkowania do wykonania podczas losowego szkolenia ASYSTENTa. W przypadku problemów z wykrywaniem anomalii niezrównoważone dane utrudniają stosowanie standardowych technik UPW. Określając pewną ilość przepróbkowania, można zwiększyć liczbę wystąpień docelowych.

    W przypadku określenia 1 Przepróbkowanie jest wykonywane. W przypadku określenia wartości większej niż 1 dodatkowe przykłady są generowane do użycia podczas uczenia modelu.

    Dostępne są dwie opcje, w zależności od tego, czy używasz odchylenia parametrów, czy nie:

    - **Parametr z próbką dla losowych asystentów**samodzielnych: Wpisz pojedynczą liczbę całkowitą, która reprezentuje stosunek przekroczenia próbkowania klasy mniejszości w normalnej klasie. (Dostępne w przypadku korzystania z metody szkolenia **jednego parametru** ).

    > [!NOTE]
    > Nie można wyświetlić przepróbkowanego zestawu danych. Aby uzyskać więcej informacji o tym, jak próbkowanie jest używane z UPW, zobacz [Uwagi techniczne](#technical-notes).

5. **Włącz normalizowanie średniej funkcji wejścia**: zaznacz tę opcję, aby znormalizować wszystkie funkcje wejściowe na średnią wartość zerową. Normalizacja lub skalowanie do zera jest ogólnie zalecana dla UPW, ponieważ celem UPW jest maksymalizacja wariancji między zmiennymi.

     Ta opcja jest domyślnie wybrana. Usuń zaznaczenie tej opcji, jeśli wartości zostały już znormalizowane przy użyciu innej metody lub skali.

6. Połącz zestaw danych szkoleń ze znacznikami i jeden z modułów szkoleniowych:

    - Jeśli ustawisz opcję **tworzenia trybu Trainer** na **pojedynczy parametr**, użyj modułu [wykrywania anomalii](train-anomaly-detection-model.md) .

7. Prześlij potok.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia można zapisać szkolony model lub połączyć go z modułem [modelu oceny](score-model.md) , aby przewidzieć wyniki anomalii.

Ocena wyników modelu wykrywania anomalii wymaga wykonania kilku dodatkowych czynności:

1. Upewnij się, że kolumna oceny jest dostępna w obu zestawach danych

    Jeśli spróbujesz ocenić model wykrywania anomalii i napotkasz błąd, "nie ma kolumny punktacji w obliczanym zestawie danych do porównania, oznacza to, że używasz typowego zestawu danych oceny, który zawiera kolumnę etykiety, ale nie ma wyników prawdopodobieństwa. Musisz wybrać zestaw danych, który pasuje do danych wyjściowych schematu dla modeli wykrywania anomalii, które zawierają **etykiety oceny** i **metryki z wynikami** .

2. Upewnij się, że kolumny etykiet są oznaczone

    Czasami metadane skojarzone z kolumną etykieta są usuwane w grafie potoku. W takim przypadku, gdy używasz modułu [oceny modelu](evaluate-model.md) do porównywania wyników dwóch modeli wykrywania anomalii, może wystąpić błąd, "Brak kolumny etykiet w zestawie danych oceny" lub "nie ma kolumny etykiet w obliczanym zestawie danych do porównania".

    Możesz uniknąć tego błędu przez dodanie modułu [Edytuj metadane](edit-metadata.md) przed modułem [Oceń model](evaluate-model.md) . Użyj selektora kolumn, aby wybrać kolumnę klasy, a następnie na liście rozwijanej **pola** wybierz pozycję **etykieta**.

3. Użyj [skryptu wykonaj skrypt języka Python](execute-python-script.md) , aby dostosować kategorie kolumn etykiet jako 1 (dodatnie, normalne) i 0 (ujemne, nietypowe).

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Uwagi techniczne

Ten algorytm używa programu UPW do przybliżenia podobszaru zawierającego normalną klasę. Miejsce jest łączone przez eigenvectors skojarzone z górną eigenvaluesą macierzy kowariancji danych. Dla każdego nowego danych wejściowych detektor anomalii najpierw obliczy jego projekcję na eigenvectors, a następnie obliczy znormalizowany błąd odbudowy. Ten błąd jest wynikiem anomalii. Im większy błąd, tym bardziej anomalia wystąpienia. Aby uzyskać szczegółowe informacje na temat sposobu obliczania normalnego obszaru, zobacz temat Wikipedia: [główna analiza składników](https://wikipedia.org/wiki/Principal_component_analysis) 


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 

Zobacz [wyjątki i kody błędów dla projektanta (wersja zapoznawcza)](designer-error-codes.md) , aby zapoznać się z listą błędów specyficznych dla modułów projektanta. ' '