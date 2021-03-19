---
title: 'PCA-Based wykrywanie anomalii: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą modułu wykrywania anomalii PCA-Based utworzyć model wykrywania anomalii oparty na głównej analizie składników (PPW).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: fa90fcb9ebc17be9a658b08873234eada98b0fba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898456"
---
# <a name="pca-based-anomaly-detection-module"></a>PCA-Based moduł wykrywania anomalii

W tym artykule opisano sposób używania modułu wykrywania anomalii PCA-Based w programie Azure Machine Learning Designer do tworzenia modelu wykrywania anomalii na podstawie głównej analizy składników (PPW).

Ten moduł ułatwia tworzenie modeli w scenariuszach, w których można łatwo uzyskać dane szkoleniowe z jednej klasy, takie jak prawidłowe transakcje, ale trudno jest uzyskać wystarczającą ilość próbek dla określonych anomalii. 

Na przykład w celu wykrywania fałszywych transakcji często nie masz wystarczającej liczby przykładów oszustw do uczenia się. Może jednak istnieć wiele przykładów dobrych transakcji. Moduł wykrywania anomalii PCA-Based rozwiązuje problem, analizując dostępne funkcje, aby określić, co stanowi Klasa "normal". Następnie moduł stosuje metryki odległości, aby identyfikować przypadki reprezentujące anomalie. Takie podejście umożliwia uczenie modelu przy użyciu istniejących niezrównoważonych danych.

## <a name="more-about-principal-component-analysis"></a>Więcej informacji o głównej analizie składników

UPW to ustalona technika w uczeniu maszynowym. Jest on często używany w analizie danych, ponieważ ujawnia wewnętrzną strukturę danych i objaśnia wariancję danych.

UPW działa przez analizowanie danych, które zawierają wiele zmiennych. Szuka korelacji między zmiennymi i określa kombinację wartości, które najlepiej przechwytuje różnice w wyników. Te połączone wartości funkcji są używane do tworzenia bardziej kompaktowego miejsca funkcji o nazwie *składniki główne*.

W przypadku wykrywania anomalii każde nowe dane wejściowe są analizowane. Algorytm wykrywania anomalii oblicza jego projekcję w eigenvectors, wraz z znormalizowanym błędem odbudowy. Znormalizowany błąd jest używany jako wynik anomalii. Im większy błąd, tym bardziej anomalia wystąpienia.

Aby uzyskać więcej informacji o działaniu UPW oraz o implementacji wykrywania anomalii, zobacz następujące dokumenty:

- [Algorytm losowy dla analizy składników głównych](https://arxiv.org/abs/0809.2274), przez Rokhlin, Szlan i Tygert

- [Znajdowanie struktury z losowością: algorytmy probabilistyczne do konstruowania przybliżonych dekompozycji macierzy](http://users.cms.caltech.edu/~jtropp/papers/HMT11-Finding-Structure-SIREV.pdf) (pobieranie plików PDF), przez Halko, Martinsson i tropp

## <a name="how-to-configure-pca-based-anomaly-detection"></a>Jak skonfigurować PCA-Based wykrywanie anomalii

1. Dodaj moduł **wykrywania anomalii opartego na UPW** do potoku w projektancie. Ten moduł można znaleźć w kategorii **wykrywania anomalii** .

2. W prawym panelu modułu wybierz opcję **tryb szkoleniowy** . Wskaż, czy chcesz nauczyć model przy użyciu określonego zestawu parametrów, czy użyć odchylenia parametrów, aby znaleźć najlepsze parametry.

    Jeśli wiesz, jak chcesz skonfigurować model, wybierz opcję **Single Parameter** i podaj określony zestaw wartości jako argumenty.

3. Aby uzyskać liczbę składników, które mają być **używane w UPW**, określ liczbę funkcji lub składników wyjściowych, które chcesz.

    Decyzja o liczbie składników do uwzględnienia jest ważną częścią projektu eksperymentu, który korzysta z UPW. Ogólne wskazówki polegają na tym, że nie należy uwzględniać tej samej liczby składników Asystenta zgodności, ponieważ istnieją zmienne. Zamiast tego należy zacząć od mniejszej liczby składników i zwiększyć je do momentu spełnienia pewnego kryterium.

    Najlepsze wyniki są uzyskiwane, gdy liczba składników wyjściowych jest *mniejsza niż* liczba kolumn funkcji dostępnych w zestawie danych.

4. Określ ilość przepróbkowania do wykonania podczas losowego szkolenia ASYSTENTa. W przypadku problemów z wykrywaniem anomalii niezrównoważone dane utrudniają stosowanie standardowych technik UPW. Określając pewną ilość przepróbkowania, można zwiększyć liczbę wystąpień docelowych.

    W przypadku określenia **1** Przepróbkowanie jest wykonywane. W przypadku określenia wartości większej niż **1** dodatkowe przykłady są generowane do użycia podczas uczenia modelu.

    Dostępne są dwie opcje, w zależności od tego, czy używasz odchylenia parametrów, czy nie:

    - **Parametr z próbką dla losowych asystentów** samodzielnych: Wpisz pojedynczą liczbę całkowitą, która reprezentuje stosunek przekroczenia próbkowania klasy mniejszości w normalnej klasie. (Ta opcja jest dostępna w przypadku korzystania z metody szkolenia **jednego parametru** ).

    > [!NOTE]
    > Nie można wyświetlić przepróbkowanego zestawu danych. Aby uzyskać więcej informacji na temat przewyższania próbkowania, zobacz [Uwagi techniczne](#technical-notes).

5. Wybierz opcję **Włącz normalizowanie średniej funkcji wejścia** , aby znormalizować wszystkie funkcje wejściowe na średnią wartość zero. Normalizacja lub skalowanie do zera jest ogólnie zalecana dla UPW, ponieważ celem UPW jest maksymalizacja wariancji między zmiennymi.

    Ta opcja jest domyślnie wybrana. Usuń zaznaczenie tej opcji, jeśli wartości zostały już znormalizowane przez inną metodę lub skalę.

6. Połącz zestaw danych szkoleniowych i jeden z modułów szkoleniowych.

   Jeśli ustawisz opcję **tworzenia trybu Trainer** na **pojedynczy parametr**, użyj modułu [wykrywania anomalii](train-anomaly-detection-model.md) .

7. Prześlij potok.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia można zapisać szkolony model. Można też połączyć go z modułem [model oceny](score-model.md) , aby przewidzieć wyniki anomalii.

Aby oszacować wyniki modelu wykrywania anomalii:

1. Upewnij się, że kolumna oceny jest dostępna w obu zestawach danych.

    Jeśli spróbujesz ocenić model wykrywania anomalii i wystąpi błąd "nie ma kolumny oceny w obliczanym zestawie danych do porównania," używasz typowego zestawu danych oceny, który zawiera kolumnę etykiety, ale nie ma wyników prawdopodobieństwa. Wybierz zestaw danych, który jest zgodny z danymi wyjściowymi schematu dla modeli wykrywania anomalii, które obejmują **etykiety z wynikami** i **oceny prawdopodobieństwa** .

2. Upewnij się, że kolumny etykiet są oznaczone.

    Czasami metadane skojarzone z kolumną etykieta są usuwane w grafie potoku. W takim przypadku, gdy używasz modułu [oceny modelu](evaluate-model.md) do porównywania wyników dwóch modeli wykrywania anomalii, może zostać wyświetlony komunikat o błędzie "nie ma kolumny etykieta w zestawie danych z oceną". Lub może zostać wyświetlony komunikat o błędzie "nie ma kolumny etykiet w wyliczonym zestawie danych do porównania".

    Można uniknąć tych błędów przez dodanie modułu [Edytuj metadane](edit-metadata.md) przed modułem [Oceń model](evaluate-model.md) . Użyj selektora kolumn, aby wybrać kolumnę klasy, a następnie na liście **pola** wybierz pozycję **etykieta**.

3. Użyj modułu [skryptu języka Python](execute-python-script.md) , aby dostosować kategorie kolumn etykiet jako **1 (dodatnie, normalne)** i **0 (ujemne, nietypowe)**.

    ````
    label_column_name = 'XXX'
    anomaly_label_category = YY
    dataframe1[label_column_name] = dataframe1[label_column_name].apply(lambda x: 0 if x == anomaly_label_category else 1)
    ````

    
## <a name="technical-notes"></a>Uwagi techniczne

Ten algorytm korzysta ze współdziałania w celu przybliżenia miejsca, które zawiera normalną klasę. Miejsce jest łączone przez eigenvectors skojarzone z górną eigenvaluesą macierzy kowariancji danych. 

Dla każdego nowego danych wejściowych detektor anomalii najpierw obliczy jego projekcję na eigenvectors, a następnie obliczy znormalizowany błąd odbudowy. Ten błąd jest wynikiem anomalii. Im większy błąd, tym bardziej anomalia wystąpienia. Aby uzyskać szczegółowe informacje na temat sposobu obliczania normalnego obszaru, zobacz temat Wikipedia: [główna analiza składników](https://wikipedia.org/wiki/Principal_component_analysis). 


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 

Zobacz [wyjątki i kody błędów dla projektanta,](designer-error-codes.md) aby zapoznać się z listą błędów specyficznych dla modułów projektanta.