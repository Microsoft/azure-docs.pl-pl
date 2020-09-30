---
title: 'Konwertuj słowo na wektor: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak korzystać z trzech udostępnionych modeli Word2Vec, aby wyodrębnić słownictwo i odpowiednie osadzania wyrazów z korpus tekstu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: 5fad3e4862b0c40c9edd00a5b9d47b245e529396
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536736"
---
# <a name="convert-word-to-vector-module"></a>Konwertuj słowo na moduł Vector

W tym artykule opisano sposób użycia modułu Konwertuj wyraz do wektora w programie Azure Machine Learning Designer do wykonywania następujących zadań:

- Zastosuj różne modele Word2Vec (Word2Vec, FastText, dokładne prepociąg model) na korpus tekstu, który został określony jako dane wejściowe.
- Generuj słownictwo przy użyciu osadzania wyrazów.

Ten moduł używa biblioteki Gensim. Aby uzyskać więcej informacji na temat Gensim, zobacz jego [oficjalną witrynę sieci Web](https://radimrehurek.com/gensim/apiref.html), w tym samouczki i objaśnienie algorytmów.

### <a name="more-about-converting-words-to-vectors"></a>Więcej informacji o konwertowaniu wyrazów na wektory

Konwersja wyrazów na wektors lub Word wektoryzacji jest procesem przetwarzania języka naturalnego (NLP). Proces używa modeli języka do mapowania słów na przestrzeń wektorową. Przestrzeń wektorowa reprezentuje każdy wyraz według wektora liczb rzeczywistych. Pozwala również wyrazom podobnym do oznaczania podobnej reprezentacji.

Używaj osadzania słów jako początkowych danych wejściowych dla zadań podrzędnych NLP, takich jak Klasyfikacja tekstu i analiza tonacji.

Między różnymi technologiami osadzania wyrazów w tym module wprowadziliśmy trzy powszechnie używane metody. Dwa, Word2Vec i FastText są modelami szkoleń online. Druga to wstępnie przemieszczony model, dokładne-wiki-gigaword-100. 

Online — modele szkoleń są przeszkolone na danych wejściowych. Modele przedszkolony są przeszkolone w trybie offline na większym korpus tekstowym (na przykład Wikipedia, Google News), które zwykle zawiera około 100 000 000 000 słów. Osadzenie wyrazów, a następnie pozostaje stałe w programie Word wektoryzacji. Modele słów przedszkolonych zapewniają korzyści, takie jak skrócenie czasu szkoleniowego, zakodowane lepsze wektory słów i udoskonalona ogólna wydajność.

Oto kilka informacji na temat metod:

+ Word2Vec to jedna z najpopularniejszych technik, które umożliwiają naukę osadzania wyrazów za pomocą płytki sieci neuronowych. Teoretyczne rozwiązanie jest omówione w tym dokumencie, dostępne jako pliki PDF do pobrania: [wydajne oszacowanie wyrazów w obszarze wektora](https://arxiv.org/pdf/1301.3781.pdf). Implementacja w tym module jest oparta na [bibliotece Gensim dla Word2Vec](https://radimrehurek.com/gensim/models/word2vec.html).

+ Teoretycznie FastText jest wyjaśnione w tym dokumencie, dostępne jako pliki PDF do pobrania: [wzbogacanie wektorów wyrazów za pomocą informacji o podwyrazie](https://arxiv.org/pdf/1607.04606.pdf). Implementacja w tym module jest oparta na [bibliotece Gensim dla FastText](https://radimrehurek.com/gensim/models/fasttext.html).

+ Wstępnie przemieszczony model dokładne to dokładne-wiki-gigaword-100. Jest to kolekcja premieszczonych wektorów oparta na korpus tekstowym witryny Wikipedia, która zawiera 5 600 000 000 tokeny i 400 000 wyrazy słownictwa. Dostępne jest pobieranie plików PDF: [dokładne: globalne wektory dla reprezentacji programu Word](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>Jak skonfigurować słowo Convert do wektora

Ten moduł wymaga zestawu danych, który zawiera kolumnę tekstu. Wstępnie przetworzony tekst jest lepszy.

1. Dodaj **słowo konwersji do modułu Vector** do potoku.

2. Jako dane wejściowe dla modułu Podaj zestaw danych, który zawiera jedną lub więcej kolumn tekstowych.

3. Dla **kolumny Target**wybierz tylko jedną kolumnę zawierającą tekst do przetworzenia.

    Ponieważ ten moduł tworzy słownictwo z tekstu, zawartość kolumn różni się, co prowadzi do różnych zawartości słownika. Dlatego moduł akceptuje tylko jedną kolumnę docelową.

4. W przypadku  **strategii Word2Vec**wybierz pozycję z **dokładne premieszczonego w języku angielskim**, **Gensim Word2Vec**i **Gensim FastText**.

5. Jeśli **strategia Word2Vec** jest **Gensim Word2Vec** lub **Gensim FastText**:

    + Dla **algorytmu szkolenia Word2Vec**wybierz spośród **Skip_gram** i **CBOW**. Różnica jest wprowadzana w [oryginalnym papierze (PDF)](https://arxiv.org/pdf/1301.3781.pdf).

        Domyślna metoda to **Skip_gram**.

    + W przypadku **długości osadzania wyrazów**należy określić wymiary wektorów wyrazów. To ustawienie odpowiada `size` parametrowi w Gensim.

        Domyślny rozmiar osadzania to 100.

    + Dla **rozmiaru okna kontekstu**należy określić maksymalną odległość między przewidywanym słowem a bieżącym słowem. To ustawienie odpowiada `window` parametrowi w Gensim.

        Domyślny rozmiar okna to 5.

    + W przypadku **liczby epok**należy określić liczbę epok (iteracji) w korpus. Odpowiada `iter` parametrowi w Gensim.

        Domyślny numer epoki to 5.

6. W polu **Maksymalny rozmiar słownika**Określ maksymalną liczbę słów w wygenerowanym słownictwie.

    Jeśli jest więcej unikatowych słów niż maksymalny rozmiar, Oczyść rzadko używane.

    Domyślny rozmiar słownika to 10 000.

7. Dla **minimalnej liczby słów**Podaj minimalną liczbę wyrazów. Moduł zignoruje wszystkie wyrazy mające częstotliwość mniejszą niż ta wartość.

    Wartość domyślna to 5.

8. Prześlij potok.

## <a name="examples"></a>Przykłady

Moduł ma jedno wyjście:

+ **Słownictwo z osadzaniem**: zawiera wygenerowany słownictwo wraz z osadzaniem każdego wyrazu. Jeden wymiar zajmuje jedną kolumnę.

Poniższy przykład pokazuje, jak działa Konwertuj wyraz na wektor. Używa funkcji Convert Word do wektora z ustawieniami domyślnymi wstępnie przetworzonym zestawem danych programu Wikipedia SP 500.

### <a name="source-dataset"></a>Zestaw danych źródłowych

Zestaw danych zawiera kolumnę kategorii wraz z pełnym tekstem pobieranym z witryny Wikipedia. W poniższej tabeli przedstawiono kilka reprezentatywnych przykładów.

|Tekst|
|----------|
|NASDAQ 100 składnika s p 500 składnika usługi Team Foundation założyciel Location City (miasto), nieskończona pętla (ulica), pętla nieograniczona, Cupertino Kalifornii Cupertino stan Stanów Zjednoczonych kraj...|
|br Nasdaq 100 Nasdaq 100 składnik br s p 500 s p 500 Component Industry Computer Software Foundation br założyciel Charles Geschke br Jan Warnock Location firmy Adobe Systems...|
|s p 500 s p 500 — branżowe branże motoryzacyjne, z których pozostało poprzednie Motors 1908 2009 Corporation|
|s p 500 s p 500 Component Industry konglomerat firma konglomerat Foundation założyciel Location City Fairfield Connecticut Fairfield Connecticut lokalizacja kraj USA...|
|br s p 500 s p 500 Component Foundation 1903 założyciel William s Harley br Arthur Davidson Harley Davidson założyciel Arthur Davidson br Waltera Davidson br William lokalizacji Davidson...|

### <a name="output-vocabulary-with-embeddings"></a>Słownictwo wyjściowe z osadzaniem

Poniższa tabela zawiera dane wyjściowe tego modułu, pobierając zestaw danych programu Wikipedia SP 500 jako dane wejściowe. Kolumna z lewej strony wskazuje słownictwo. Wektor osadzania jest reprezentowany przez wartości pozostałych kolumn w tym samym wierszu.

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

W tym przykładzie użyto domyślnej strategii **Gensim Word2Vec** for **Word2Vec**, a **algorytm uczenia** to **Skip-gram**. **Długość osadzania wyrazów** to 100, więc mamy 100 kolumny osadzania.

## <a name="technical-notes"></a>Uwagi techniczne

Ta sekcja zawiera porady i odpowiedzi na często zadawane pytania.

+ Różnica między kursem online a preszkolonym modelem:

    W tym przypadku konwersja wyrazu do modułu Vector zapewnia trzy różne strategie: dwa modele szkoleń online i jeden wstępnie przeszkolony model. Modele szkoleń online wykorzystują wejściowy zestaw danych jako dane szkoleniowe i generują słownictwo i wektory wyrazów podczas szkoleń. Wcześniej przemieszczony model jest już szkolony przez znacznie większy korpus tekstu, taki jak Wikipedia lub Twitter. Model przeddany jest w rzeczywistości kolekcją par wyrazów/osadzania.  

    Dokładne wstępnie szkolony model podsumowuje słownictwo z wejściowego zestawu danych i generuje wektor osadzania dla każdego wyrazu ze wstępnie przemieszczonego modelu. Bez uczenia online korzystanie z preszkolonych modeli może zaoszczędzić czas uczenia. Zapewnia lepszą wydajność, zwłaszcza gdy rozmiar wejściowego zestawu danych jest stosunkowo mały.

+ Rozmiar osadzania:

    Ogólnie rzecz biorąc, Długość osadzania wyrazów jest ustawiona na kilka setek. Na przykład 100, 200, 300. Mały rozmiar osadzania oznacza małą przestrzeń wektorową, co może spowodować kolizje programu Word.  

    Długość osadzania wyrazów jest stała dla modeli przedszkolowych. W tym przykładzie rozmiar osadzania dokładne-wiki-gigaword-100 to 100.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 

Aby zapoznać się z listą błędów specyficznych dla modułów projektanta, zobacz [Machine Learning kodów błędów](designer-error-codes.md).
