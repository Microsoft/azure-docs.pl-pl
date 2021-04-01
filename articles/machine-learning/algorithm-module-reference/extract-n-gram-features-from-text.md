---
title: Wyodrębnij funkcje N-gramowe z odwołania do modułu tekstowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać modułu Wyodrębnij N-gramy w projektancie Azure Machine Learning, aby cechowanie dane tekstowe.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 12/08/2019
ms.openlocfilehash: 37a10d90fa0e277fbe45d9f1377e365cb3d42996
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96861464"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>Wyodrębnij funkcje N-gramowe z odwołania do modułu tekstowego

W tym artykule opisano moduł w programie Azure Machine Learning Designer. Użycie funkcji Wyodrębnij N-gramy z modułu tekstowego do *cechowanie* danych tekstowych bez struktury. 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Konfiguracja funkcji wyodrębniania N-gramów z modułu tekstowego

Moduł obsługuje następujące scenariusze dotyczące korzystania ze słownika n-gram:

* [Utwórz nowy słownik n-gramowy](#create-a-new-n-gram-dictionary) z kolumny bezpłatnego tekstu.

* [Użyj istniejącego zestawu funkcji tekstowych](#use-an-existing-n-gram-dictionary) , aby cechowanie niezależną kolumnę tekstową.

* [Ocena lub wdrożenie modelu](#build-inference-pipeline-that-uses-n-grams-to-deploy-a-real-time-endpoint) używającego n-gramów.

### <a name="create-a-new-n-gram-dictionary"></a>Tworzenie nowego słownika n-gramowego

1.  Dodaj funkcje Wyodrębnij N-gram z modułu tekstowego do potoku i Połącz zestaw danych z tekstem, który chcesz przetworzyć.

1.  **Kolumna tekstowa** służy do wybierania kolumny zawierającej tekst, który ma zostać wyodrębniony. Ze względu na to, że wyniki są pełne, można przetwarzać tylko pojedynczą kolumnę w danym momencie.

1. Ustaw **tryb słownictwa** na **Utwórz** , aby wskazać, że tworzysz nową listę funkcji n-gramowych. 

1. Ustaw **rozmiar n-gramy** , aby wskazać *Maksymalny* rozmiar n-gramów, które mają zostać wyodrębnione i zapisane. 

    Na przykład, jeśli wprowadzisz 3, unigrams, i trigrams zostanie utworzony.

1. **Funkcja ważenia** określa sposób tworzenia wektora funkcji dokumentu oraz wyodrębniania słownictwa z dokumentów.

    * **Waga binarna**: przypisuje wartość obecności binarnej do wyodrębnionych n-gramów. Wartość dla każdego n-gramu wynosi 1, gdy istnieje w dokumencie, i 0 w przeciwnym razie.

    * **TF wagi**: przypisuje wynik częstotliwości okresowej (TF) do wyodrębnionych n-gramów. Wartość każdego n-gramu jest częstotliwością występowania w dokumencie.

    * **Waga IDF**: przypisuje wynik odwrotnej częstotliwości dokumentu (IDF) do wyodrębnionych n-gramów. Wartość każdego n-grama to dziennik o rozmiarze korpus podzielonym według częstotliwości występowania w całej korpus.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **TF-IDF wag**: przypisuje wynikowe częstotliwość/częstotliwość dokumentu odwrotnego (TF/IDF) do wyodrębnionych n-gramów. Wartość dla każdego n-gramu to wynik TF pomnożony przez jego ocenę IDF.

1. Ustaw **minimalną długość wyrazu** na minimalną liczbę liter, które mogą być używane w *pojedynczym wyrazie* w n-gramowej.

1. Aby ustawić maksymalną liczbę liter, które mogą być używane w *pojedynczym wyrazie* w n-gramach, należy użyć **maksymalnej długości wyrazu** .

    Domyślnie dozwolone są maksymalnie 25 znaków na słowo lub token.

1. Użyj **minimalnej częstotliwości bezwzględnej dokumentu n-gramowego** , aby ustawić minimalne wystąpienia wymagane przez dowolne n-gramy do uwzględnienia w słowniku n-gramów. 

    Jeśli na przykład zostanie użyta wartość domyślna 5, wszystkie n-gramy muszą pojawić się co najmniej pięć razy w korpus, aby uwzględnić je w słowniku n-gram. 

1.  Ustaw **maksymalny współczynnik dokumentu n-gramowego** na maksymalny stosunek liczby wierszy zawierających określoną liczbę n-gramów względem liczby wierszy w ogólnej korpus.

    Na przykład, stosunek 1 wskazuje, że nawet jeśli konkretny n-gram jest obecny w każdym wierszu, można dodać n-gram do słownika n-gram. Zwykle wyraz występujący w każdym wierszu jest traktowany jako słowo ignorowane i zostałby usunięty. Aby odfiltrować słowa akustyczne zależne od domeny, spróbuj zmniejszyć ten współczynnik.

    > [!IMPORTANT]
    > Częstotliwość występowania określonych słów nie jest jednolita. Różni się od dokumentu do dokumentu. Na przykład jeśli analizujesz Komentarze klienta dotyczące określonego produktu, nazwa produktu może być bardzo wysoka i znajdować się w wyrazie szumu, ale być znaczącym terminem w innych kontekstach.

1. Wybierz opcję **normalizuje wektory funkcji n-gramy** , aby znormalizować wektory funkcji. Jeśli ta opcja jest włączona, każdy wektor funkcji n-gram jest podzielony według normy L2.

1. Prześlij potok.

### <a name="use-an-existing-n-gram-dictionary"></a>Użyj istniejącego słownika n-gramowego

1.  Dodaj funkcje Wyodrębnij N-gram z modułu tekstowego do potoku i Połącz zestaw danych z tekstem, który chcesz przetworzyć na port **DataSet** .

1.  **Kolumna tekstowa** służy do zaznaczania kolumny tekstowej zawierającej tekst, który ma zostać cechowanie. Domyślnie moduł wybiera wszystkie kolumny typu **String**. Aby uzyskać najlepsze wyniki, przetwórz pojedynczą kolumnę w czasie.

1. Dodaj zapisany zestaw danych, który zawiera wcześniej wygenerowany słownik n-gram i połącz go z portem **słownika danych wejściowych** . Istnieje również możliwość połączenia danych wyjściowych **słownika wynikowego** wystąpienia funkcji Wyodrębnij N-gram z modułu tekstowego.

1. W obszarze **tryb słownictwa** wybierz opcję aktualizacji **tylko do odczytu** z listy rozwijanej.

   Opcja **ReadOnly** reprezentuje korpus wejściowy dla słownictwa wejściowego. Zamiast obliczania częstotliwości okresów z nowego tekstu zestawu danych (po lewej stronie), wagi n-gramy ze słownika wejściowego są stosowane w miarę jak.

   > [!TIP]
   > Użyj tej opcji, gdy oceniasz klasyfikator tekstowy.

1.  Wszystkie inne opcje można znaleźć w opisach właściwości w [poprzedniej sekcji](#create-a-new-n-gram-dictionary).

1.  Prześlij potok.

### <a name="build-inference-pipeline-that-uses-n-grams-to-deploy-a-real-time-endpoint"></a>Kompiluj potok wnioskowania, który używa n-gramów do wdrożenia punktu końcowego w czasie rzeczywistym

Potok szkoleniowy, który zawiera **funkcję Wyodrębnij N-gramów z modelu tekstu** i **oceny** , aby utworzyć prognozowanie dla zestawu danych testowych, jest zbudowany w następującej strukturze:

:::image type="content" source="./media/module/extract-n-gram-training-pipeline-score-model.png" alt-text="Wyodrębnij przykład potoku szkolenia N-gramy" border="true":::

**Tryb słownictwa** **funkcji "Wyodrębnij do" z modułu tekstowego "i"** **, która** **łączy się z** modułem **modelu oceny** , jest **tylko do odczytu**.

Po pomyślnym przesłaniu potoku szkoleniowego można zarejestrować dane wyjściowe modułu z kółkiem jako zestaw danych.

:::image type="content" source="./media/module/extract-n-gram-output-voc-register-dataset.png" alt-text="Zarejestruj zestaw danych" border="true":::

Następnie można utworzyć potok wnioskowania w czasie rzeczywistym. Po utworzeniu potoku wnioskowania należy dostosować swój potok wnioskowania ręcznie, podobnie jak poniżej:

:::image type="content" source="./media/module/extract-n-gram-inference-pipeline.png" alt-text="Potok wnioskowania" border="true":::

Następnie prześlij potok wnioskowania i Wdróż punkt końcowy w czasie rzeczywistym.

## <a name="results"></a>Wyniki

Funkcja wyodrębnianie N-gramów z modułu tekstu tworzy dwa typy danych wyjściowych: 

* **Zestaw danych wyników**: to wyjście jest podsumowaniem analizowanego tekstu połączonego z oddzielonymi n-gramami. Kolumny, które nie zostały wybrane w **kolumnie tekstowej** , są przekazywane do danych wyjściowych. Dla każdej kolumny analizowanego tekstu moduł generuje następujące kolumny:

  * **Macierz wystąpień n-gram**: moduł generuje kolumnę dla każdego n-gramu znalezionego w łącznej korpus i dodaje ocenę w każdej kolumnie, aby wskazać wagę n-grama dla tego wiersza. 

* **Słownictwo wyniku**: słownictwo zawiera rzeczywisty słownik n-gramowy wraz z wynikami częstotliwości, które są generowane w ramach analizy. Zestaw danych można zapisać do ponownego użycia z innym zestawem danych wejściowych lub w późniejszej aktualizacji. Możesz również ponownie użyć słownika do modelowania i oceniania.

### <a name="result-vocabulary"></a>Słownictwo wyniku

Słownictwo zawiera słownik n-gram z wynikami częstotliwości, które są generowane w ramach analizy. Wyniki DF i IDF są generowane niezależnie od innych opcji.

+ **Identyfikator**: Identyfikator wygenerowany dla każdego unikatowego n-gramu.
+ **NGram**: n-gram. Spacje lub inne separatory wyrazów są zastępowane znakiem podkreślenia.
+ **DF**: termin częstotliwości dla n-gramu w pierwotnej korpus.
+ **IDF**: wynik odwrotnej częstotliwości dokumentu dla n-gramów w oryginalnym korpus.

Możesz ręcznie zaktualizować ten zestaw danych, ale możesz wprowadzić błędy. Na przykład:

* Błąd jest zgłaszany, jeśli moduł znajdzie zduplikowane wiersze z tym samym kluczem w słowniku wejściowym. Upewnij się, że żadne dwa wiersze w słowniku nie mają tego samego wyrazu.
* Schemat wejściowy zestawów danych słownictwa musi dokładnie pasować, włącznie z nazwami kolumn i typami kolumn. 
* Kolumny **ID** i **DF** muszą być typu Integer. 
* Kolumna **IDF** musi być typu zmiennoprzecinkowego.

> [!Note]
> Nie łącz bezpośrednio danych wyjściowych z modułem uczenia modelu. Przed wprowadzeniem ich do modelu uczenia należy usunąć kolumny wolnych kolumn. W przeciwnym razie kolumny tekstu wolnego będą traktowane jako funkcje kategorii.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning.
