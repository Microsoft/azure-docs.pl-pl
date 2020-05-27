---
title: Konwertuj słowo na wektor
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak korzystać z trzech udostępnionych modeli Word2Vec, aby wyodrębnić słownictwo i odpowiednie osadzania wyrazów z korpus tekstu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: e0e796b75690bcacc6be8ef29b8b490c7faa40af
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853771"
---
# <a name="convert-word-to-vector"></a>Konwertuj słowo na wektor

W tym artykule opisano sposób użycia modułu **Konwertuj wyraz do wektora** w programie Azure Machine Learning Designer (wersja zapoznawcza), aby zastosować różne modele Word2Vec (Word2Vec, FastText, dokładne preszkolony model) na korpus tekstu, który został określony jako dane wejściowe, i wygenerować słownictwo z osadzaniem słów.

Ten moduł używa biblioteki Gensim. Aby uzyskać więcej informacji na temat Gensim, zobacz jego [oficjalną witrynę sieci Web](https://radimrehurek.com/gensim/apiref.html) , która zawiera samouczki i objaśnienie algorytmów.

### <a name="more-about-convert-word-to-vector"></a>Więcej informacji na temat konwertowania słowa na wektor

Ogólnie mówiąc, konwertowanie wyrazów na wektor lub Word wektoryzacji jest procesem przetwarzania języka naturalnego, który używa modeli języka lub technik do mapowania wyrazów na przestrzeń wektorową, czyli do reprezentowania każdego wyrazu w postaci wektorów liczb rzeczywistych, a tym samym umożliwia używanie wyrazów o podobnym znaczeniu.

Osadzania słów mogą służyć jako początkowe dane wejściowe dla zadań podrzędnych NLP, takich jak Klasyfikacja tekstu, analiza tonacji itp.

Między różnymi technologiami osadzania wyrazów w tym module wprowadziliśmy trzy powszechnie używane metody, w tym dwa modele szkoleń online, Word2Vec i FastText, a także jeden model wstępnie szkolony, dokładne-wiki-gigaword-100. Online — modele szkoleniowe są przeszkolone na danych wejściowych, a wstępnie nauczone modele są przeszkolone na większym korpus tekstowym, (na przykład Wikipedia, Google News) zwykle zawiera około 100 000 000 000 słów, a następnie osadzanie wyrazów pozostaje stałe w programie Word wektoryzacji. Wstępnie przeszkolone modele słów zapewniają korzyści, takie jak skrócenie czasu szkoleniowego, zakodowane lepsze wektory słów i lepsza wydajność.

+ Word2Vec to jedna z najpopularniejszych technik, które umożliwiają naukę osadzania wyrazów za pomocą płytki sieci neuronowych, teoretyczne jest to omawiane w tym dokumencie, dostępne jako pobieranie PDF: [wydajne oszacowanie wyrazów w przestrzeni wektorowej, Mikolov, Tomas, et al](https://arxiv.org/pdf/1301.3781.pdf). Implementacja w tym module jest oparta na [bibliotece gensim dla Word2Vec](https://radimrehurek.com/gensim/models/word2vec.html).

+ W tym dokumencie wyjaśniono teoretyczne FastText, dostępne jako pliki PDF do pobrania: [wzbogacanie wektorów wyrazów z informacjami o podwyrazie, Bojanowski, Piotr, et al](https://arxiv.org/pdf/1607.04606.pdf). Implementacja w tym module jest oparta na [bibliotece gensim dla FastText](https://radimrehurek.com/gensim/models/fasttext.html).

+ Dokładne wstępnie nauczonego modelu: dokładne-wiki-gigaword-100, to kolekcja wstępnie przeszkolonych wektorów oparta na korpus tekstowym witryny Wikipedia, która zawiera tokeny 5.6 B i 400 000 słownictwo, plik PDF jest dostępny: [dokładne: globalne wektory dla reprezentacji programu Word](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>Jak skonfigurować słowo Convert do wektora

Ten moduł wymaga zestawu danych, który zawiera kolumnę tekstu, a wstępnie przetworzony tekst jest lepszy.

1. Dodaj **słowo konwersji do modułu Vector** do potoku.

2. Jako dane wejściowe dla modułu Podaj zestaw danych zawierający co najmniej jedną kolumnę tekstową.

3. Dla **kolumny Target**wybierz tylko jedną kolumnę zawierającą tekst do przetworzenia.

    Ogólnie rzecz biorąc, ponieważ ten moduł tworzy słownictwo z tekstu, zawartość różnych kolumn różni się od tego, który prowadzi do różnych zawartości słownictwa, w związku z tym moduł akceptuje tylko jedną kolumnę docelową.

4. W przypadku **strategii Word2Vec**wybierz z `GloVe pretrained English Model` , `Gensim Word2Vec` , i `Gensim FastText` .

5. Jeśli **strategia Word2Vec** jest `Gensim Word2Vec` lub `Gensim FastText` :

    + **Algorytm szkolenia Word2Vec**. Wybierz z `Skip_gram` i `CBOW` . Różnica jest wprowadzana w oryginalnym [dokumencie](https://arxiv.org/pdf/1301.3781.pdf).

        Metoda domyślna to `Skip_gram` .

    + **Długość osadzania wyrazów**. Określ wymiary wektorów wyrazów. Odpowiada `size` parametrowi w gensim.

        Domyślny embedding_size to 100.

    + **Rozmiar okna kontekstu**. Określ maksymalną odległość między przewidywanym słowem a bieżącym słowem. Odpowiada `window` parametrowi w gensim.

        Domyślny rozmiar okna to 5.

    + **Liczba epok**. Określ liczbę epok (iteracji) w korpus. Odpowiada `iter` parametrowi w gensim.

        Domyślna liczba epok to 5.

6. W polu **Maksymalny rozmiar słownika**Określ maksymalną liczbę słów w wygenerowanym słownictwie.

    Jeśli nie ma więcej unikatowych słów, należy oczyścić rzadko występujące.

    Domyślny rozmiar słownika to 10000.

7. W przypadku **minimalnej liczby słów**Podaj minimalną liczbę słów, która powoduje, że moduł ignoruje wszystkie wyrazy, które mają częstotliwość mniejszą niż ta wartość.

    Wartość domyślna to 5.

8. Prześlij potok.

## <a name="examples"></a>Przykłady

Moduł ma jedno wyjście:

+ **Słownictwo z osadzaniem**: zawiera wygenerowany słownictwo wraz z osadzaniem każdego wyrazu, jeden wymiar zajmuje jedną kolumnę.

### <a name="result-examples"></a>Przykłady wyników

Aby zilustrować, jak działa **Konwersja wyrazu do modułu Vector** , w poniższym przykładzie zastosowano ten moduł z ustawieniami domyślnymi do wstępnie przetworzonego zestawu danych programu Wikipedia SP 500 dostarczonego w Azure Machine Learning (wersja zapoznawcza).

#### <a name="source-dataset"></a>Zestaw danych źródłowych

Zestaw danych zawiera kolumnę kategorii oraz pełny tekst pobierany z witryny Wikipedia. W tej tabeli przedstawiono tylko kilka reprezentatywnych przykładów.

|tekst|
|----------|
|NASDAQ 100 składnika s p 500 składnika usługi Team Foundation założyciel Location City (miasto), nieskończona pętla (ulica), pętla nieograniczona, Cupertino Kalifornii Cupertino stan Stanów Zjednoczonych kraj...|
|br Nasdaq 100 Nasdaq 100 składnik br s p 500 s p 500 Component Industry Computer Software Foundation br założyciel Charles Geschke br Jan Warnock Location firmy Adobe Systems...|
|s p 500 s p 500 — branżowe branże motoryzacyjne, z których pozostało poprzednie Motors 1908 2009 Corporation|
|s p 500 s p 500 Component Industry konglomerat firma konglomerat Foundation założyciel Location City Fairfield Connecticut Fairfield Connecticut lokalizacja kraj USA...|
|br s p 500 s p 500 Component Foundation 1903 założyciel William s Harley br Arthur Davidson Harley Davidson założyciel Arthur Davidson br Waltera Davidson br William lokalizacji Davidson...|

#### <a name="output-vocabulary-with-embeddings"></a>Słownictwo wyjściowe z osadzaniem

Poniższa tabela zawiera dane wyjściowe tego modułu z zestawem danych programu Wikipedia SP 500 jako dane wejściowe. Kolumna z lewej strony wskazuje słownictwo, jego wektor osadzania jest reprezentowany przez wartości pozostałych kolumn w tym samym wierszu.

|Słownika|Osadzanie Dim 0|Przygasić 1|Osadzanie 2|Wymiar osadzania 3|Przygasić 4|Przygasić wymiar 5|...|Osadzanie Dim 99|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|nasdaq|-0,375865|0,609234|0,812797|-0,002236|0,319071|-0,591986|...|0,364276
|składnik|0,081302|0,40001|0,121803|0,108181|0,043651|-0,091452|...|0,636587
|s|-0,34355|-0,037092|-0,012167|0,151542|0,601019|0,084501|...|0,149419
|p|-0,133407|0,073244|0,170396|0,326706|0,213463|-0,700355|...|0,530901
rocznic|-0,166819|0,10883|-0,07933|-0,073753|0,262137|0,045725|...|0,27487
założyciel|-0,297408|0,493067|0,316709|-0,031651|0,455416|-0,284208|...|0,22798
location|-0,375213|0,461229|0,310698|0,213465|0,200092|0,314288|...|0,14228
city|-0,460828|0,505516|-0,074294|-0,00639|0,116545|0,494368|...|-0,2403
klienckim|0,05779|0,672657|0,597267|-0,898889|0,099901|0,11833|...|0,4636
dużej|-0,281835|0,29312|0,106966|-0,031385|0,100777|-0,061452|...|0,05978
ograniczon|-0,263074|0,245753|0,07058|-0,164666|0,162857|-0,027345|...|-0,0525
pętla|-0,391421|0,52366|0,141503|-0,105423|0,084503|-0,018424|...|-0,0521

W tym przykładzie użyto wartości domyślnej `Gensim Word2Vec` jako **strategii Word2Vec**, ale **algorytmem szkoleniowym** jest `Skip-gram` , że **Długość osadzania wyrazów** to 100, w związku z czym mamy 100 kolumny osadzania.

## <a name="technical-notes"></a>Uwagi techniczne

Ta sekcja zawiera porady i odpowiedzi na często zadawane pytania.

+ Różnica między kursem online a modelem przedszkolenym

    W tym przypadku **Konwersja wyrazu do modułu Vector**zapewnia trzy różne strategie, dwa modele szkoleń online i jeden model wstępnie szkolony. Model uczenia online korzysta z wejściowego zestawu danych jako dane szkoleniowe, generuje słownictwo słownika i słowa w trakcie szkolenia, podczas gdy wstępnie szkolony model jest już szkolony przez dużo większy korpus tekstu, taki jak Wikipedia lub Twitter.  

    Jeśli model wstępnie szkolony dokładne jest wybierany jako strategia programu Word wektoryzacji, podsumowuje słownictwo z wejściowego zestawu danych i generuje wektor osadzania dla każdego wyrazu ze wstępnie przeszkolonego modelu, bez uczenia online, użycie modelu preszkolonego może zaoszczędzić czas uczenia i uzyskać lepszą wydajność, szczególnie gdy wejściowy zestaw danych jest stosunkowo mały.

+ Rozmiar osadzania

    Ogólnie rzecz biorąc, Długość osadzania wyrazów jest ustawiana na kilka setek (na przykład 100, 200, 300) w celu uzyskania dobrej wydajności, ponieważ mały rozmiar osadzania oznacza małe miejsce wektorowe, co może spowodować kolizje obiektów programu Word.  

    W przypadku modeli wstępnie przewidzianych długość osadzania wyrazów jest stała, w tej implementacji rozmiar osadzania dokładne-wiki-gigaword-100 to 100.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 

Aby zapoznać się z listą błędów specyficznych dla modułów projektant (wersja zapoznawcza), zobacz [Machine Learning kodów błędów](designer-error-codes.md).
