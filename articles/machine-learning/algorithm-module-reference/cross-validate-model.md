---
title: 'Model krzyżowego sprawdzania poprawności: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Moduł modelu krzyżowego sprawdzania poprawności w programie Azure Machine Learning Designer służy do sprawdzania poprawności szacunków parametrów dla modeli klasyfikacji lub regresji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: d4099ecf6e6bcc6654391e54292878393fb22914
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421349"
---
# <a name="cross-validate-model"></a>Krzyżowa weryfikacja modelu

W tym artykule opisano sposób korzystania z modułu weryfikacji krzyżowej w programie Azure Machine Learning Designer. *Wzajemne sprawdzanie poprawności* jest techniką często używaną w uczeniu maszynowym do oceny zmienności zestawu danych i niezawodności dowolnego modelu przeszkolonego za pomocą tych danych.  

Moduł sprawdzania poprawności krzyżowej przyjmuje jako wejściowy zestaw danych, wraz z niesprawdzonym modelem klasyfikacji lub regresji. Dzieli zestaw danych na pewną liczbę podzestawów (*zgięcia*), kompiluje model dla każdego zgięcia, a następnie zwraca zestaw statystyk dokładności dla każdego złożenia. Porównując statystyki dokładności dla wszystkich zagięć, można interpretować jakość zestawu danych. Następnie można zrozumieć, czy model jest podatny na różnice w danych.  

Model weryfikacji krzyżowej zwraca również przewidywane wyniki i prawdopodobieństwa dla zestawu danych, dzięki czemu można ocenić niezawodność prognoz.  

### <a name="how-cross-validation-works"></a>Jak działa sprawdzanie krzyżowe

1. Wzajemne sprawdzanie poprawności dzieli dane szkoleniowe na zgięcia. 

   Algorytm domyślnie przyjmuje wartość 10, jeśli zestaw danych nie został wcześniej podzielony na partycje. Aby podzielić zestaw danych na inną liczbę zagięć, można użyć [partycji i przykładowego](partition-and-sample.md) modułu i wskazać, ile zagięć ma być używanych.  

2.  Moduł ustawia dane w zgięciu 1 do użycia na potrzeby walidacji. (Jest to czasami nazywane *wstrzymania zgięciem*). Moduł używa pozostałych zagięć do uczenia modelu. 

    Jeśli na przykład utworzysz pięć zagięć, moduł generuje pięć modeli podczas weryfikacji krzyżowej. Moduł pociąga za sobą cztery piąte danych. Sprawdza każdy model w pozostałej jednej piątej.  

3.  Podczas testowania modelu dla każdego zgięcia moduł szacuje wiele statystyk dokładności. Które statystyki są używane przez moduł, zależy od typu modelu, który jest oceniany. Różne statystyki są używane do obliczania modeli klasyfikacji zamiast modeli regresji.  

4.  Po zakończeniu procesu kompilowania i oceny dla wszystkich zgięcia model krzyżowy umożliwia wygenerowanie zestawu metryk wydajności i wyników oceny dla wszystkich danych. Zapoznaj się z tymi metrykami, aby sprawdzić, czy pojedyncze zgięcie ma wysoką lub niską dokładność. 

### <a name="advantages-of-cross-validation"></a>Zalety wzajemnej weryfikacji

Inny i typowy sposób oceny modelu polega na podzieleniu danych na zestaw szkoleniowy i testowy przy użyciu [danych podziału](split-data.md), a następnie zweryfikowaniu modelu na danych szkoleniowych. Ale krzyżowe sprawdzanie poprawności zapewnia pewne korzyści:  

-   Wzajemne sprawdzanie poprawności używa większej ilości danych testowych.

    Wzajemne sprawdzanie poprawności mierzy wydajność modelu z określonymi parametrami w większym obszarze danych. Oznacza to, że krzyżowe sprawdzanie poprawności używa całego zestawu danych szkoleniowych dla szkolenia i oceny, a nie od części. Z drugiej strony, jeśli sprawdzasz poprawność modelu przy użyciu danych wygenerowanych z losowego podziału, zazwyczaj szacujesz model na tylko 30% lub mniej dostępnych danych.  

    Jednak ze względu na to, że wzajemne sprawdzanie poprawności pociąga i weryfikuje model wielokrotnie w większym zestawie danych, jest znacznie bardziej intensywnie czasochłonne. Sprawdzanie poprawności losowej może zająć dużo czasu.  

-   Wzajemne sprawdzanie poprawności oblicza zarówno zestaw danych, jak i model.

    Krzyżowe sprawdzanie poprawności nie powoduje po prostu mierzenia dokładności modelu. Daje również pewne pomysły dotyczące reprezentatywnego zestawu danych oraz sposobu, w jaki model może być zróżnicowany w danych.  

## <a name="how-to-use-cross-validate-model"></a>Jak używać modelu weryfikacji krzyżowej

Jeśli zestaw danych jest duży, proces sprawdzania poprawności może zająć dużo czasu.  Dlatego można użyć modelu weryfikacji krzyżowej w początkowej fazie kompilowania i testowania modelu. W tej fazie można oszacować dobrą jakość parametrów modelu (przy założeniu, że czas obliczeń jest dopuszczalny). Następnie można wyszkolić i oszacować model przy użyciu ustanowionych parametrów z [modelem uczenia](train-model.md) i [oszacować moduły modelu](evaluate-model.md) .

W tym scenariuszu nauczysz się i testujesz model przy użyciu modelu krzyżowego sprawdzania poprawności.

1. Dodaj moduł odweryfikuj model do potoku. Można go znaleźć w programie Azure Machine Learning Designer, w kategorii **ocena & model oceniania** . 

2. Połącz dane wyjściowe z dowolnym modelem klasyfikacji lub regresji. 

    Na przykład jeśli korzystasz z **dwuetapowego drzewa decyzyjnego klasy** do klasyfikacji, skonfiguruj model przy użyciu żądanych parametrów. Następnie przeciągnij łącznik z **nauczenia portu modelu** klasyfikatora do zgodnego portu modelu krzyżowego sprawdzania poprawności. 

    > [!TIP] 
    > Nie jest konieczne uczenie modelu, ponieważ model weryfikacji krzyżowej automatycznie pociąga za siebie model w ramach oceny.  
3.  W porcie **zestawu danych** modelu krzyżowego sprawdzania poprawności Połącz każdy zestaw danych szkolenia z etykietą.  

4.  W prawym panelu modelu krzyżowego walidacji kliknij pozycję **Edytuj kolumnę**. Wybierz pojedynczą kolumnę zawierającą etykietę klasy lub wartość przewidywalną. 

5. Ustaw wartość parametru **losowego inicjatora** , jeśli chcesz powtórzyć wyniki weryfikacji krzyżowej po kolejnych uruchomieniach na tych samych danych.  

6. Prześlij potok.

7. Opis raportów można znaleźć w sekcji [Results (wyniki](#results) ).

## <a name="results"></a>Wyniki

Po ukończeniu wszystkich iteracji model krzyżowego sprawdzania poprawności tworzy wyniki dla całego zestawu danych. Tworzy również metryki wydajności, których można użyć do oceny jakości modelu.

### <a name="scored-results"></a>Wyniki oceny

Pierwsze dane wyjściowe modułu zawierają dane źródłowe dla każdego wiersza, a także niektóre wartości przewidywane i powiązane prawdopodobieństwa. 

Aby wyświetlić wyniki, w potoku kliknij prawym przyciskiem myszy moduł modelu krzyżowego sprawdzania poprawności. Wybierz pozycję **Wizualizuj wyniki z wynikami**.

| Nazwa nowej kolumny      | Opis                              |
| -------------------- | ---------------------------------------- |
| Etykiety z wynikami        | Ta kolumna jest dodawana na końcu zestawu danych. Zawiera wartość przewidywaną dla każdego wiersza. |
| Oceny prawdopodobieństwa | Ta kolumna jest dodawana na końcu zestawu danych. Wskazuje szacowane prawdopodobieństwo wartości w **etykietach z wynikami**. |
| Numer zgięcia          | Wskazuje indeks (liczony od zera) złożenia, do którego przypisano każdy wiersz danych podczas weryfikacji krzyżowej. |

 ### <a name="evaluation-results"></a>Wyniki oceny

Drugi raport jest pogrupowany według zgięcia. Należy pamiętać, że podczas wykonywania, krzyżowy Walidacja modelu losowo dzieli dane szkoleniowe na *n* zgięcia (domyślnie 10). W każdej iteracji w zestawie danych, krzyżowego sprawdzania poprawności modelu używa jednego zgięcia jako zestawu danych walidacji. Używa pozostałego zgięcia *n-1* do uczenia modelu. Każdy z modeli *n* jest testowany na dane we wszystkich innych zgięciach.

W tym raporcie zgięcia są wyświetlane według wartości indeksu w kolejności rosnącej.  Aby zamówić w dowolnej innej kolumnie, można zapisać wyniki jako zestaw danych.

Aby wyświetlić wyniki, w potoku kliknij prawym przyciskiem myszy moduł modelu krzyżowego sprawdzania poprawności. Wybierz opcję **Wizualizuj wyniki oceny według** złożenia.


|Nazwa kolumny| Opis|
|----|----|
|Numer zgięcia| Identyfikator dla każdego złożenia. Jeśli utworzono pięć zgięciów, będą dostępne pięć podzestawów danych z numerami od 0 do 4.
|Liczba przykładów w złożeniu|Liczba wierszy przypisanych do każdego zgięcia. Powinny one być w przybliżeniu równe. |


Moduł zawiera również następujące metryki dla każdego zgięcia, w zależności od typu modelu, który oceniasz: 

+ **Modele klasyfikacji**: dokładność, odwołanie, F-Score, AUC, dokładność  

+ **Modele regresji**: średni błąd bezwzględny, główny, średni, błąd względny, błąd bezwzględny, względny błąd kwadratowy i współczynnik wyznaczania


## <a name="technical-notes"></a>Uwagi techniczne  

+ Najlepszym rozwiązaniem jest ujednolicenie zestawów danych przed ich użyciem do wzajemnego sprawdzania poprawności. 

+ Sprawdzanie poprawności modelu krzyżowego jest znacznie bardziej czasochłonne i trwa dłużej niż w przypadku zweryfikowania modelu przy użyciu losowo podzielonego zestawu danych. Przyczyną jest to, że model krzyżowego sprawdzania poprawności pociąga i weryfikuje model wielokrotnie.

+ Nie trzeba podzielić zestawu danych na zestawy szkoleniowe i testowe w przypadku używania krzyżowego sprawdzania poprawności do mierzenia dokładności modelu. 


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 

