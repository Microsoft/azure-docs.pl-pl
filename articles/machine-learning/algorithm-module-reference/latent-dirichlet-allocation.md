---
title: 'Ukryte Dirichlet alokacji: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać modułu ukrytego Dirichlet Allocation do grupowania niesklasyfikowanego tekstu w kategorii.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/05/2020
ms.openlocfilehash: f9f239ea69aaf71e591a447feb300c13a45ba1a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90907856"
---
# <a name="latent-dirichlet-allocation-module"></a>Ukryty moduł alokacji Dirichlet

W tym artykule opisano sposób używania ukrytego modułu alokacji Dirichlet w programie Azure Machine Learning Designer do grupowania niesklasyfikowanego tekstu w kategorii. 

Ukryty Dirichlet Allocation (LDA) jest często używany w przetwarzaniu języka naturalnego, aby znaleźć podobne dokumenty. Innym często spotykanym terminem jest *modelowanie tematów*.

Ten moduł pobiera kolumnę tekstu i generuje następujące dane wyjściowe:

+ Tekst źródłowy wraz z oceną dla każdej kategorii.

+ Macierz funkcji, która zawiera wyodrębnione terminy i współczynniki dla każdej kategorii

+ Transformacja, którą można zapisać i ponownie zastosować do nowego tekstu używanego jako dane wejściowe

Ten moduł używa biblioteki scikit. Aby uzyskać więcej informacji na temat scikit, zobacz [repozytorium GitHub](https://github.com/scikit-learn/scikit-learn), w tym samouczki i objaśnienie algorytmu.

## <a name="more-about-latent-dirichlet-allocation"></a>Więcej informacji na temat ukrytego przydziału Dirichlet

LDA zazwyczaj nie jest metodą klasyfikacji. Ale używa metody tworzenia, dlatego nie trzeba podawać znanych etykiet klas, a następnie wywnioskować wzorców.  Zamiast tego algorytm generuje model probabilistyczne, który jest używany do identyfikowania grup tematów. Model probabilistyczne można użyć do klasyfikowania istniejących przypadków szkoleniowych lub nowych przypadków, które udostępniamy modelowi jako dane wejściowe.

Można preferować model do odzyskania, ponieważ pozwala to uniknąć podejmowania silnych założeń dotyczących relacji między tekstem i kategoriami. Używa on tylko dystrybucji wyrazów do matematycznie wzorów tematów.

W tym dokumencie jest to teoretyczne, dostępne jako pobieranie pliku PDF: [ukryte Dirichlet alokacji: Blei, NG i Jordania](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf).

Implementacja w tym module jest oparta na [bibliotece scikit-Dowiedz się](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) Lda.

Aby uzyskać więcej informacji, zobacz sekcję [Uwagi techniczne](#technical-notes) .

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Jak skonfigurować alokację ukrytego Dirichlet

Ten moduł wymaga zestawu danych, który zawiera kolumnę tekstu — nieprzetworzony lub wstępnie przetworzony.

1. Dodaj do potoku moduł **Dirichlet** .

2. Jako dane wejściowe dla modułu Podaj zestaw danych, który zawiera jedną lub więcej kolumn tekstowych.

3. W przypadku **kolumn docelowych** wybierz co najmniej jedną kolumnę, która zawiera tekst do przeanalizowania.

    Można wybrać wiele kolumn, ale muszą one być typu **String** .

    Ponieważ LDA tworzy z tekstu dużą macierz funkcji, przeważnie przeanalizuje pojedynczą kolumnę tekstową.

4. Aby uzyskać informacje o  **liczbie tematów do modelowania**, wprowadź liczbę całkowitą z zakresu od 1 do 1000, która wskazuje liczbę kategorii lub tematów, które mają być wyprowadzane z tekstu wejściowego.

    Domyślnie są tworzone 5 tematów.

5. W przypadku **N-gramów** należy określić maksymalną długość N-gramów wygenerowaną podczas tworzenia skrótu.

    Wartość domyślna to 2, co oznacza, że generowane są obie gramy i unigrams.

6. Wybierz opcję **normalizing** , aby przekonwertować wartości wyjściowe na prawdopodobieństwa. 

    Zamiast przedstawiać przekształcone wartości jako liczby całkowite, wartości w danych wyjściowych i funkcji DataSet zostaną przekształcone w następujący sposób:

    + Wartości w zestawie danych będą reprezentowane jako prawdopodobieństwo, gdzie `P(topic|document)` .

    + Wartości w macierzy tematu funkcji będą reprezentowane jako prawdopodobieństwo, gdzie `P(word|topic)` .

    > [!NOTE] 
    > W projektancie Azure Machine Learning Biblioteka doscikita nie obsługuje już nieznormalizowanych *doc_topic_distr* danych wyjściowych z wersji 0,19. W tym module parametr **normalizing** może być stosowany tylko do danych wyjściowych *macierzy tematu funkcji* . *Przekształcony wynikowy zestaw danych* jest zawsze znormalizowany.

7. Wybierz opcję **Pokaż wszystkie opcje**, a następnie ustaw **wartość na true** , jeśli chcesz ustawić następujące parametry zaawansowane.

    Te parametry są specyficzne dla implementacji scikit-uczenia LDA. Istnieją pewne dobre samouczki dotyczące LDA w scikit — uczenie się, jak również oficjalnego [dokumentu scikit-uczenie](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html)się.

    + **Parametr Rho**. Podaj poprzednie prawdopodobieństwo dla rzadkości rozkładów tematu. Ten parametr odnosi się do `topic_word_prior` parametru skryptu sklearn. Użyj wartości **1** , Jeśli spodziewasz się, że dystrybucja słów jest płaska; oznacza to, że wszystkie słowa założono equiprobable. Jeśli uważasz, że większość wyrazów jest bardziej rozrzedzona, możesz ustawić ją na niższą wartość.

    + **Parametr alfa**. Określ poprzednie prawdopodobieństwo dla rzadkości wag poszczególnych dokumentów. Ten parametr odnosi się do `doc_topic_prior` parametru skryptu sklearn.

    + **Szacowana Liczba dokumentów**. Wprowadź liczbę, która reprezentuje najlepsze oszacowanie liczby dokumentów (wierszy), które zostaną przetworzone. Ten parametr umożliwia modułowi przydzielenie tabeli skrótów o wystarczającym rozmiarze. Odpowiada `total_samples` parametrowi w scikit — uczenie się.

    + **Rozmiar wsadu**. Wprowadź liczbę, która wskazuje liczbę wierszy do uwzględnienia w każdej partii tekstu wysyłanej do modelu LDA. Ten parametr odnosi się do `batch_size` parametru w scikit-Dowiedz się.

    + **Początkowa wartość iteracji użyta w harmonogramie aktualizacji szkoleniowej**. Określ wartość początkową, która downweights stawkę szkoleniową dla wczesnych iteracji w uczeniu online. Ten parametr odnosi się do `learning_offset` parametru w scikit-Dowiedz się.

    + **Moc zastosowana do iteracji podczas aktualizacji**. Wskazuje poziom mocy stosowanej do liczby iteracji w celu kontrolowania szybkości uczenia w trakcie aktualizacji online. Ten parametr odnosi się do `learning_decay` parametru w scikit-Dowiedz się.

    + **Liczba przebiegów przez dane**. Określ maksymalną liczbę przypadków, przez jaką algorytm będzie przekroczyć dane. Ten parametr odnosi się do `max_iter` parametru w scikit-Dowiedz się.

8. Wybierz opcję **Kompilowanie słownika ngrams** lub **Kompilowanie SŁOWNIKA ngrams przed LDA**, jeśli chcesz utworzyć listę n-gramów w początkowym przebiegu przed klasyfikacją tekstu.

    Jeśli wcześniej utworzysz słownik początkowy, możesz użyć słownika podczas przeglądania modelu. Możliwość mapowania wyników do tekstu, a nie indeksów liczbowych, jest ogólnie łatwiejsza do interpretacji. Jednak zapisanie słownika zajmuje więcej czasu i będzie korzystać z dodatkowego magazynu.

9. W polu **Maksymalny rozmiar słownika ngram** wprowadź łączną liczbę wierszy, które można utworzyć w słowniku n-gram.

    Ta opcja jest przydatna do kontrolowania rozmiaru słownika. Ale jeśli liczba ngrams w danych wejściowych przekracza ten rozmiar, mogą wystąpić kolizje.

10. Prześlij potok. Moduł LDA używa Bayesa theorem, aby określić, które tematy mogą być skojarzone z poszczególnymi wyrazami. Słowa nie są wyłącznie powiązane z żadnym tematem lub żadną grupą. Zamiast tego każde n-gramy ma zdobyte prawdopodobieństwo związane z dowolnymi z odnalezionych klas.

## <a name="results"></a>Wyniki

Moduł ma dwa dane wyjściowe:

+ **Przekształcony zestaw danych**: dane wyjściowe zawierają tekst wejściowy, określoną liczbę odnalezionych kategorii i wyniki dla każdego przykładu tekstu dla każdej kategorii.

+ **Macierz tematu funkcji**: kolumna z lewej strony zawiera wyodrębnioną funkcję tekstu. Kolumna dla każdej kategorii zawiera wynik dla tej funkcji w tej kategorii.


### <a name="lda-transformation"></a>Transformacja LDA

Ten moduł generuje również *transformację LDA* , która stosuje LDA do zestawu danych.

Można zapisać tę transformację i użyć jej ponownie dla innych zestawów danych. Ta technika może być przydatna, jeśli przeprowadzono szkolenia na dużą korpuse i chcesz ponownie wykorzystać współczynniki lub kategorie.

Aby ponownie użyć tej transformacji, wybierz ikonę **zarejestruj zestaw danych** w prawym panelu modułu ukrytego Dirichlet alokacji, aby zachować moduł pod kategorią **zestawy** danych na liście modułów. Następnie można podłączyć ten moduł do modułu [zastosowania transformacji](apply-transformation.md) , aby ponownie użyć tej transformacji.

### <a name="refining-an-lda-model-or-results"></a>Udoskonalanie modelu LDA lub wyników

Zazwyczaj nie można utworzyć pojedynczego modelu LDA, który będzie spełniał wszystkie potrzeby. Nawet model zaprojektowany dla jednego zadania może wymagać wielu iteracji, aby poprawić dokładność. Zalecamy, aby wypróbować wszystkie te metody, aby ulepszyć model:

+ Zmiana parametrów modelu
+ Używanie wizualizacji do zrozumienia wyników
+ Uzyskiwanie opinii ekspertów z dziedziny tematów w celu ustalenia, czy wygenerowane tematy są przydatne

Miary jakościowe mogą być również przydatne do oceny wyników. Aby oszacować wyniki modelowania tematów, rozważ:

+ Odpowiedni. Przypomina podobne elementy?
+ Różnorodności. Czy model może odróżniać się między podobnymi elementami, gdy jest to wymagane dla problemu biznesowego?
+ Skalowalność. Czy działa on w szerokim zakresie kategorii tekstowych lub tylko w domenie o wąskiej docelowej?

Często można poprawić dokładność modeli opartych na LDA za pomocą przetwarzania języka naturalnego w celu czyszczenia, podsumowywania i uproszczenia lub kategoryzacji tekstu. Na przykład następujące techniki, które są obsługiwane w Azure Machine Learning, mogą poprawić dokładność klasyfikacji:

+ Zatrzymaj usuwanie wyrazów

+ Normalizacja przypadku

+ Lematyzacja lub z rdzeniem

+ Rozpoznawanie jednostek nazwanych

Aby uzyskać więcej informacji, zobacz [preprocess Text](preprocess-text.md).

W projektancie można także użyć bibliotek języka R lub Python do przetwarzania tekstu: [Wykonaj skrypt języka r](execute-r-script.md)i  [Wykonaj skrypt](execute-python-script.md)w języku Python.



## <a name="technical-notes"></a>Uwagi techniczne

Ta sekcja zawiera szczegóły implementacji, porady i odpowiedzi na często zadawane pytania.

### <a name="implementation-details"></a>Szczegóły implementacji

Domyślnie dystrybucje danych wyjściowych dla przekształconych elementów DataSet i macierzy tematów są znormalizowane jako prawdopodobieństwa:

+ Przekształcony zestaw danych jest znormalizowany jako prawdopodobieństwo warunkowe tematów danego dokumentu. W tym przypadku suma każdego wiersza jest równa 1.

+ Macierz tematu funkcji jest znormalizowana jako warunkowe prawdopodobieństwo wyrazów podaną w temacie. W tym przypadku suma każdej kolumny jest równa 1.

> [!TIP]
> Czasami moduł może zwrócić pusty temat. Najczęściej przyczyną jest inicjowanie losowe algorytmu. W takim przypadku można spróbować zmienić powiązane parametry. Na przykład zmień maksymalny rozmiar słownika N-gramów lub liczbę bitów, które mają być używane do tworzenia skrótów funkcji.

### <a name="lda-and-topic-modeling"></a>LDA i modelowanie tematów

Dirichlet przydziały ukryty jest często używany do *modelowania tematów opartych na zawartości*, co oznacza, że kategorie uczenia są naliczane z niesklasyfikowanego tekstu. W przypadku modelowania tematów opartych na zawartości temat jest dystrybucją w odniesieniu do wyrazów.

Załóżmy na przykład, że podano korpus przeglądów klientów, którzy zawierają wiele produktów. Tekst przeglądów, które zostały przesłane przez klientów z upływem czasu, zawiera wiele warunków, z których niektóre są używane w wielu tematach.

*Temat* , który IDENTYFIKUJE proces LDA, może przedstawiać przeglądy dla pojedynczego produktu lub może reprezentować grupę przeglądów produktów. Aby LDA, sam temat to po prostu rozkład prawdopodobieństwa w czasie dla zestawu wyrazów.

Warunki są rzadko wyłączne dla każdego z nich. Mogą odwoływać się do innych produktów lub mieć ogólne warunki, które mają zastosowanie do wszystkiego ("doskonały", "Awful"). Inne warunki mogą być słowami dźwiękowymi. Jednak Metoda LDA nie próbuje przechwycić wszystkich wyrazów w miejscu ani zrozumieć, jak wyrazy są powiązane, w odniesieniu do prawdopodobieństwa wystąpienia. Może ono grupować tylko wyrazy, które są używane w domenie docelowej.

Po przeprowadzeniu indeksowania indeksów, miara podobieństwa na odległość porównuje pojedyncze wiersze tekstu, aby określić, czy dwa fragmenty tekstu są podobne. Na przykład, może się okazać, że produkt ma wiele nazw, które są silnie skorelowane. Może się też okazać, że silnie negatywne warunki są zwykle skojarzone z konkretnym produktem. Możesz użyć miary podobieństwa, aby zidentyfikować powiązane warunki i utworzyć zalecenia.

###  <a name="module-parameters"></a>Parametry modułu

|Nazwa|Typ|Zakres|Opcjonalne|Domyślne|Opis|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Kolumny docelowe|Wybór kolumny||Wymagane|StringFeature|Nazwa lub indeks kolumny docelowej.|  
|Liczba tematów do modelowania|Liczba całkowita|[1; 1000]|Wymagane|5|Modelowanie dystrybucji dokumentu względem N tematów.|  
|N-gramy|Liczba całkowita|[1; 10]|Wymagane|2|Kolejność N-gramów wygenerowana podczas tworzenia skrótów.|  
|Normalizuj|Wartość logiczna|Prawda lub FAŁSZ|Wymagane|true|Normalizowanie danych wyjściowych do prawdopodobieństwa.  Przekształcony zestaw danych będzie P (temat&#124;dokument), a macierz tematu funkcji będzie P (temat Word&#124;temat).|  
|Pokaż wszystkie opcje|Wartość logiczna|Prawda lub FAŁSZ|Wymagane|Fałsz|Przedstawia dodatkowe parametry charakterystyczne dla scikit — uczenie online LDA.|  
|Parametr Rho|Float|[0.00001; 1.0]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|0,01|Wcześniejsza dystrybucja wyrazów tematu.|  
|Parametr alfa|Float|[0.00001; 1.0]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|0,01|Wcześniejsza dystrybucja dokumentu w temacie.|  
|Szacowana Liczba dokumentów|Liczba całkowita|[1; int. MaxValue|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|1000|Szacowana Liczba dokumentów. Odnosi się do `total_samples` parametru.|  
|Rozmiar partii|Liczba całkowita|[1; 1024]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|32|Rozmiar wsadu.|  
|Początkowa wartość iteracji użyta w harmonogramie aktualizacji szybkości uczenia|Liczba całkowita|[0; int. MaxValue|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|0|Wartość początkowa, która downweights szybkość uczenia dla wczesnych iteracji. Odnosi się do `learning_offset` parametru.|  
|Moc zastosowana do iteracji podczas aktualizacji|Float|[0.0; 1.0]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|0,5|Moc zastosowana do liczby iteracji w celu kontrolowania stawki szkoleniowej. Odnosi się do `learning_decay` parametru. |  
|Liczba iteracji szkoleniowych|Liczba całkowita|[1; 1024]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|25|Liczba iteracji szkoleniowych.|  
|Słownik kompilacji ngrams|Wartość logiczna|Prawda lub FAŁSZ|Stosuje się, gdy pole wyboru **Pokaż wszystkie opcje** *nie* jest zaznaczone|Prawda|Kompiluje słownik ngrams przed przetwarzaniem LDA. Przydatny do przeprowadzania inspekcji i interpretacji modeli.|  
|Maksymalny rozmiar słownika ngram|Liczba całkowita|[1; int. MaxValue|Stosuje się, gdy **słownik kompilacji opcji ngrams** ma **wartość true**|20000|Maksymalny rozmiar słownika ngrams. Jeśli liczba tokenów w danych wejściowych przekracza ten rozmiar, kolizje mogą wystąpić.|  
|Liczba bitów do użycia na potrzeby tworzenia skrótów funkcji.|Liczba całkowita|[1; 31]|Stosuje się, gdy pole wyboru **Pokaż wszystkie opcje** *nie* jest zaznaczone, a **słownik kompilacji ngrams** ma **wartość FAŁSZ**|12|Liczba bitów do użycia na potrzeby tworzenia skrótów funkcji.| 
|Kompilowanie słownika ngrams przed LDA|Wartość logiczna|Prawda lub FAŁSZ|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|Prawda|Kompiluje słownik ngrams przed LDA. Przydatny do przeprowadzania inspekcji i interpretacji modeli.|  
|Maksymalna liczba ngrams w słowniku|Liczba całkowita|[1; int. MaxValue|Stosuje się, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje** , a **słownik kompilacji opcji Ngrams** ma **wartość true** .|20000|Maksymalny rozmiar słownika. Jeśli liczba tokenów w danych wejściowych przekracza ten rozmiar, kolizje mogą wystąpić.|  
|Liczba bitów skrótu|Liczba całkowita|[1; 31]|Stosuje się, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje** , a **słownik kompilacji opcji Ngrams** ma **wartość FAŁSZ**|12|Liczba bitów do użycia podczas tworzenia skrótów funkcji.|   


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 

Aby zapoznać się z listą błędów specyficznych dla modułów, zobacz [wyjątki i kody błędów dla projektanta](designer-error-codes.md).
