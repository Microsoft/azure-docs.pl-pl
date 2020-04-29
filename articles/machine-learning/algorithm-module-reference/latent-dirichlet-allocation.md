---
title: Alokacja ukrytej zmiennej Dirichleta
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą modułu ukrytego Dirichlet przydzielić niesklasyfikowany tekst w różnych kategoriach.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/11/2020
ms.openlocfilehash: 1384491489c175ffc338f80a99aa8d5050f835d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80109227"
---
# <a name="latent-dirichlet-allocation"></a>Alokacja ukrytej zmiennej Dirichleta

W tym artykule opisano sposób używania **ukrytego modułu alokacji Dirichlet** w programie Azure Machine Learning Designer (wersja zapoznawcza) w celu grupowania w inny sposób niesklasyfikowanego tekstu do kilku kategorii. 

Ukryty Dirichlet Allocation (LDA) jest często używany w przetwarzaniu języka naturalnego (NLP) w celu znalezienia podobnych tekstów. Innym często spotykanym terminem jest *modelowanie tematów*.

Ten moduł pobiera kolumnę tekstu i generuje następujące dane wyjściowe:

+ Tekst źródłowy wraz z oceną dla każdej kategorii.

+ Macierz funkcji zawierająca wyodrębnione terminy i współczynniki dla każdej kategorii

+ Transformacja, którą można zapisać i ponownie zastosować do nowego tekstu używanego jako dane wejściowe

Ten moduł używa biblioteki scikit. Aby uzyskać więcej informacji na temat scikit, zobacz [repozytorium GitHub, w tym samouczki i objaśnienie algorytmu.

### <a name="more-about-latent-dirichlet-allocation-lda"></a>Więcej informacji na temat ukrytego Dirichlet alokacji (LDA)

Ogólnie mówiąc, LDA nie jest metodą klasyfikacji na SE, ale używa podejścia do tworzenia. Oznacza to, że nie musisz podawać znanych etykiet klas, a następnie wywnioskować wzorców.  Zamiast tego algorytm generuje model probabilistyczne, który jest używany do identyfikowania grup tematów. Model probabilistyczne można użyć do klasyfikowania istniejących przypadków szkoleniowych lub nowych przypadków, które udostępniamy modelowi jako dane wejściowe.

Model odzyskania może być preferowany, ponieważ pozwala na uniknięcie wykonywania wszelkich silnych założeń dotyczących relacji między tekstem i kategoriami, a także używa tylko dystrybucji wyrazów do matematycznie tematów wzorów.

+ W tym dokumencie jest to teoretyczne, dostępne jako pobieranie pliku PDF: [ukryte Dirichlet alokacji: Blei, NG i Jordania](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf)

+ Implementacja w tym module jest oparta na [bibliotece scikit-Dowiedz się](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) Lda.

Aby uzyskać więcej informacji, zobacz sekcję [Uwagi techniczne](#technical-notes) .

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Jak skonfigurować alokację ukrytego Dirichlet

Ten moduł wymaga zestawu danych, który zawiera kolumnę tekstu — nieprzetworzony lub wstępnie przetworzony.

1. Dodaj do potoku moduł **Dirichlet** .

2. Jako dane wejściowe dla modułu Podaj zestaw danych zawierający co najmniej jedną kolumnę tekstową.

3. W przypadku **kolumn docelowych**wybierz co najmniej jedną kolumnę zawierającą tekst do przeanalizowania.

    Można wybrać wiele kolumn, ale muszą one być typu String.

    Ogólnie rzecz biorąc, ze względu na to, że LDA tworzy dużą macierz funkcji na podstawie tekstu, przeważnie przeanalizuje pojedynczą kolumnę tekstową.

4. W przypadku **liczby tematów do modelowania**wpisz liczbę całkowitą z zakresu od 1 do 1000, która wskazuje liczbę kategorii lub tematów, które mają być wyprowadzane z tekstu wejściowego.

    Domyślnie są tworzone 5 tematów.

5. W przypadku **N-gramów**należy określić maksymalną długość N-gramów wygenerowaną podczas tworzenia skrótu.

    Wartość domyślna to 2, co oznacza, że generowane są obie gramy i unigrams.

6. Wybierz opcję **normalizing** , aby przekonwertować wartości wyjściowe na prawdopodobieństwa. W związku z tym, zamiast przedstawiać przekształcone wartości jako liczby całkowite, wartości w danych wyjściowych i funkcji DataSet byłyby przekształcone w następujący sposób:

    + Wartości w zestawie danych będą reprezentowane jako prawdopodobieństwo, gdzie `P(topic|document)`.

    + Wartości w macierzy tematu funkcji będą reprezentowane jako prawdopodobieństwo, gdzie `P(word|topic)`.

    > [!NOTE] 
    > W programie Azure Machine Learning Designer (wersja zapoznawcza), ponieważ biblioteka, w której Bazujemy, scikit — nie obsługuje już nieznormalizowanych *doc_topic_distr* danych wyjściowych z wersji 0,19, dlatego w tym module można zastosować **normalizację** parametru tylko do danych wyjściowych **macierzy tematu funkcji** , **przekształcony zestaw danych** wyjściowy jest zawsze znormalizowany.

7. Wybierz opcję, **Pokaż wszystkie opcje**, a następnie ustaw wartość na true, jeśli chcesz wyświetlić, a następnie ustawić dodatkowe parametry zaawansowane.

    Te parametry są specyficzne dla implementacji scikit-uczenia LDA. Istnieją pewne dobre samouczki dotyczące LDA w scikit — uczenie się, jak również oficjalnego [dokumentu scikit-uczenie](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html)się.

    + **Parametr Rho**. Podaj poprzednie prawdopodobieństwo dla rzadkości rozkładów tematu. Odpowiada `topic_word_prior` parametrowi skryptu sklearn. Jeśli spodziewasz się, że dystrybucja słów jest płaska, użyj wartości 1. oznacza to, że wszystkie słowa założono equiprobable. Jeśli uważasz, że większość wyrazów jest nierozrzedzona, możesz ustawić ją na znacznie niższą wartość.

    + **Parametr alfa**. Określ poprzednie prawdopodobieństwo dla rzadkości wag poszczególnych dokumentów.  Odpowiada `doc_topic_prior` parametrowi skryptu sklearn.

    + **Szacowana Liczba dokumentów**. Wpisz liczbę, która reprezentuje najlepsze oszacowanie liczby dokumentów (wierszy), które zostaną przetworzone. Dzięki temu moduł przydziela tabelę skrótów o wystarczającym rozmiarze.  Odnosi się do `total_samples` parametru w scikit — uczenie się.

    + **Rozmiar wsadu**. Wpisz liczbę, która wskazuje liczbę wierszy do uwzględnienia w każdej partii tekstu wysyłanej do modelu LDA. Odnosi się do `batch_size` parametru w scikit — uczenie się.

    + **Początkowa wartość iteracji użyta w harmonogramie aktualizacji szkoleniowej**. Określ wartość początkową, która downweights szybkość uczenia dla wczesnych iteracji w uczeniu online. Odnosi się do `learning_offset` parametru w scikit — uczenie się.

    + **Moc zastosowana do iteracji podczas aktualizacji**. Wskazuje poziom mocy stosowanej do liczby iteracji w celu kontrolowania szybkości uczenia w trakcie aktualizacji online. Odnosi się do `learning_decay` parametru w scikit — uczenie się.

    + **Liczba przebiegów przez dane**. Określ maksymalną liczbę przypadków, przez jaką algorytm będzie przekroczyć dane. Odnosi się do `max_iter` parametru w scikit — uczenie się.

8. Aby utworzyć listę n-gramów w początkowym przebiegu, przed sklasyfikowaniem tekstu wybierz opcję, **Utwórz słownik kompilacji ngrams** lub **słownika kompilacji ngrams**.

    Jeśli wcześniej utworzysz słownik początkowy, możesz użyć słownika podczas przeglądania modelu. Możliwość mapowania wyników do tekstu, a nie indeksów liczbowych, jest ogólnie łatwiejsza do interpretacji. Jednak zapisanie słownika zajmuje więcej czasu i będzie korzystać z dodatkowego magazynu.

9. W polu **Maksymalny rozmiar słownika ngram**wpisz całkowitą liczbę wierszy, które można utworzyć w słowniku n-gram.

    Ta opcja jest przydatna do kontrolowania rozmiaru słownika. Jeśli jednak liczba ngrams w danych wejściowych przekracza ten rozmiar, kolizje mogą wystąpić.

10. Prześlij potok. Moduł LDA używa Bayesa theorem, aby określić, które tematy mogą być skojarzone z poszczególnymi wyrazami. Słowa nie są wyłącznie powiązane z żadnym tematem lub grupą; Zamiast tego każde n-gramy ma zdobyte prawdopodobieństwo związane z dowolnymi z odnalezionych klas.

## <a name="results"></a>Wyniki

Moduł ma dwa dane wyjściowe:

+ **Przekształcony zestaw danych**: zawiera tekst wejściowy i określoną liczbę odnalezionych kategorii wraz z wynikami dla każdego przykładu tekstu dla każdej kategorii.

+ **Macierz tematu funkcji**: kolumna z lewej strony zawiera wyodrębnioną funkcję tekstu i istnieje kolumna dla każdej kategorii zawierającej wynik dla tej funkcji w tej kategorii.


### <a name="lda-transformation"></a>Transformacja LDA

Ten moduł generuje również *transformację LDA* , która stosuje LDA do zestawu danych.

Transformację można zapisać, rejestrując zestaw danych na karcie dane **wyjściowe + dzienniki** w prawym okienku modułu i ponownie używając innych zestawów danych. Może to być przydatne, jeśli przeszkolony jest duży korpus i chcesz ponownie wykorzystać współczynniki lub kategorie.

### <a name="refining-an-lda-model-or-results"></a>Udoskonalanie modelu LDA lub wyników

Zazwyczaj nie można utworzyć pojedynczego modelu LDA, który będzie spełniał wszystkie wymagania, a nawet model przeznaczony dla jednego zadania może wymagać wielu iteracji, aby poprawić dokładność. Zalecamy, aby wypróbować wszystkie te metody, aby ulepszyć model:

+ Zmiana parametrów modelu
+ Używanie wizualizacji do zrozumienia wyników
+ Uzyskanie opinii ekspertów z dziedziny tematów w celu sprawdzenia, czy wygenerowane tematy są użyteczne.

Miary jakościowe mogą być również przydatne do oceny wyników. Aby oszacować wyniki modelowania tematów, rozważ:

+ Dokładność — przypomina podobne elementy?
+ Różnorodność — czy model może odróżniać się między podobnymi elementami, gdy jest to wymagane dla problemu biznesowego?
+ Skalowalność — działa z szeroką gamę kategorii tekstu lub tylko w domenie docelowej z wąskimi wersjami?

Dokładność modeli opartych na LDA można często ulepszyć przy użyciu przetwarzania języka naturalnego w celu czyszczenia, podsumowywania i uproszczenia lub kategoryzacji tekstu. Na przykład następujące techniki, które są obsługiwane w Azure Machine Learning, mogą poprawić dokładność klasyfikacji:

+ Zatrzymaj usuwanie wyrazów

+ Normalizacja przypadku

+ Lematyzacja lub z rdzeniem

+ Rozpoznawanie jednostek nazwanych

Aby uzyskać więcej informacji, zobacz [preprocess Text](preprocess-text.md).

W projektancie można także użyć bibliotek języka R lub Python do przetwarzania tekstu: [Wykonaj skrypt języka r](execute-r-script.md), [Wykonaj skrypt](execute-python-script.md) w języku Python



## <a name="technical-notes"></a>Uwagi techniczne

Ta sekcja zawiera szczegóły implementacji, porady i odpowiedzi na często zadawane pytania.

### <a name="implementation-details"></a>Szczegóły implementacji

Domyślnie dystrybucje danych wyjściowych dla przekształconych elementów DataSet i macierzy tematów są znormalizowane jako prawdopodobieństwa.

+ Przekształcony zestaw danych jest znormalizowany jako prawdopodobieństwo warunkowe tematów danego dokumentu. W tym przypadku suma każdego wiersza jest równa 1.

+ Macierz tematu funkcji jest znormalizowana jako warunkowe prawdopodobieństwo wyrazów podaną w temacie. W tym przypadku suma każdej kolumny jest równa 1.

> [!TIP]
> Czasami moduł może zwrócić pusty temat, który jest najczęściej spowodowany przez pseudo losowe inicjowanie algorytmu.  W takim przypadku można spróbować zmienić powiązane parametry, takie jak maksymalny rozmiar słownika N-gramów lub liczbę bitów do użycia na potrzeby tworzenia skrótów funkcji.

### <a name="lda-and-topic-modeling"></a>LDA i modelowanie tematów

Dirichleta alokacja (LDA) jest często używana do *modelowania tematów opartych na zawartości*, co oznacza, że kategorie uczenia są niesklasyfikowane tekstem. W przypadku modelowania tematów opartych na zawartości temat jest dystrybucją w odniesieniu do wyrazów.

Załóżmy na przykład, że podano korpus przeglądów klientów, którzy zawierają wiele produktów. Tekst przeglądów, które zostały przesłane przez wielu klientów w miarę upływu czasu, może zawierać wiele warunków, a niektóre z nich są używane w wielu tematach.

**Temat** , który jest identyfikowany przez proces LDA, może reprezentować przegląd dla danego produktu A lub może reprezentować grupę przeglądów produktów. Aby LDA, sam temat to po prostu rozkład prawdopodobieństwa w czasie dla zestawu wyrazów.

Warunki są rzadko wyłączne dla każdego produktu, ale mogą odwoływać się do innych produktów lub mieć ogólne warunki, które mają zastosowanie do wszystkiego ("doskonały", "Awful"). Inne warunki mogą być słowami dźwiękowymi.  Jednak ważne jest, aby zrozumieć, że metoda LDA nie ma na celu przechwycenia wszystkich wyrazów w wszechświatu lub zrozumienia, jak wyrazy są powiązane, z prawdopodobieństwem wystąpienia obu wystąpień. Może grupować tylko wyrazy, które były używane w domenie docelowej.

Po obliczeniach indeksów warunków poszczególne wiersze tekstu są porównywane przy użyciu miary podobieństwa opartej na odległości, aby określić, czy dwa fragmenty tekstu są podobne.  Na przykład, może się okazać, że produkt ma wiele nazw, które są silnie skorelowane. Może się też okazać, że silnie negatywne warunki są zwykle skojarzone z konkretnym produktem. Możesz użyć miary podobieństwa, aby zidentyfikować powiązane warunki i utworzyć zalecenia.

###  <a name="module-parameters"></a>Parametry modułu

|Nazwa|Typ|Zakres|Optional|Domyślny|Opis|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Kolumny docelowe|Wybór kolumny||Wymagany|StringFeature|Nazwa lub indeks kolumny docelowej|  
|Liczba tematów do modelowania|Liczba całkowita|[1; 1000]|Wymagany|5|Modelowanie dystrybucji dokumentu względem N tematów|  
|N-gramy|Liczba całkowita|[1; 10]|Wymagany|2|Kolejność N-gramów wygenerowana podczas tworzenia skrótów|  
|Normalizuj|Boolean|Prawda lub FAŁSZ|Wymagany|true|Normalizowanie danych wyjściowych do prawdopodobieństwa.  Przekształcony zestaw danych będzie P (temat&#124;dokument), a macierz tematu funkcji będzie P (temat Word&#124;temat)|  
|Pokaż wszystkie opcje|Boolean|Prawda lub FAŁSZ|Wymagany|Fałsz|Przedstawia dodatkowe parametry charakterystyczne dla scikit — uczenie online LDA|  
|Parametr Rho|Liczba zmiennoprzecinkowa|[0.00001; 1.0]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|0,01|Wcześniejsza dystrybucja wyrazów tematu|  
|Parametr alfa|Liczba zmiennoprzecinkowa|[0.00001; 1.0]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|0,01|Wcześniejsza dystrybucja dokumentu|  
|Szacowana Liczba dokumentów|Liczba całkowita|[1; int. MaxValue|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|1000|Szacowana Liczba dokumentów (odpowiada parametrowi total_samples)|  
|Rozmiar partii|Liczba całkowita|[1; 1024]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|32|Rozmiar partii|  
|Początkowa wartość iteracji użyta w harmonogramie aktualizacji szybkości uczenia|Liczba całkowita|[0; int. MaxValue|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|0|Wartość początkowa, która downweights szybkość uczenia dla wczesnych iteracji. Odpowiada parametrowi learning_offset|  
|Moc zastosowana do iteracji podczas aktualizacji|Liczba zmiennoprzecinkowa|[0.0; 1.0]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|0,5|Moc zastosowana do liczby iteracji w celu kontrolowania stawki szkoleniowej. Odpowiada parametrowi learning_decay |  
|Liczba iteracji szkoleniowych|Liczba całkowita|[1; 1024]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|25|Liczba iteracji szkoleniowych|  
|Słownik kompilacji ngrams|Boolean|Prawda lub FAŁSZ|Ma zastosowanie, gdy pole wyboru **Pokaż wszystkie opcje** *nie* jest zaznaczone|Prawda|Kompiluje słownik ngrams przed przetwarzaniem LDA. Przydatny do przeprowadzania inspekcji i interpretacji modelu|  
|Maksymalny rozmiar słownika ngram|Liczba całkowita|[1; int. MaxValue|Stosuje się, gdy **słownik kompilacji opcji ngrams** ma wartość true|20000|Maksymalny rozmiar słownika ngrams. Jeśli liczba tokenów w danych wejściowych przekracza ten rozmiar, kolizje mogą wystąpić|  
|Liczba bitów do użycia na potrzeby tworzenia skrótów funkcji|Liczba całkowita|[1; 31]|Stosuje się, gdy pole wyboru **Pokaż wszystkie opcje** *nie* jest zaznaczone, a **słownik kompilacji ngrams** ma wartość FAŁSZ|12|Liczba bitów do użycia na potrzeby tworzenia skrótów funkcji| 
|Kompilowanie słownika ngrams przed LDA|Boolean|Prawda lub FAŁSZ|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje**|Prawda|Kompiluje słownik ngrams przed LDA. Przydatny do przeprowadzania inspekcji i interpretacji modelu|  
|Maksymalna liczba ngrams w słowniku|Liczba całkowita|[1; int. MaxValue|Stosuje się, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje** , a **słownik kompilacji opcji Ngrams** ma wartość true.|20000|Maksymalny rozmiar słownika. Jeśli liczba tokenów w danych wejściowych przekracza ten rozmiar, kolizje mogą wystąpić|  
|Liczba bitów skrótu|Liczba całkowita|[1; 31]|Ma zastosowanie, gdy zaznaczone jest pole wyboru **Pokaż wszystkie opcje** , a **słownik kompilacji opcji Ngrams** ma wartość FAŁSZ|12|Liczba bitów do użycia podczas tworzenia skrótów funkcji|   


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning.   
Aby zapoznać się z listą błędów specyficznych dla modułów, zobacz [wyjątki i kody błędów dla projektanta](designer-error-codes.md).
