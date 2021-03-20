---
title: Odwołanie do modułu wyznaczania wartości skrótu funkcji
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak cechowanie dane tekstowe przy użyciu modułu tworzenia skrótów funkcji w projektancie Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd48b32afee320aa3d252540d566317c374c73a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420788"
---
# <a name="feature-hashing-module-reference"></a>Odwołanie do modułu wyznaczania wartości skrótu funkcji

W tym artykule opisano moduł dostępny w programie Azure Machine Learning Designer.

Użyj modułu wyznaczania wartości skrótu funkcji, aby przekształcić strumień tekstu w języku angielskim do zestawu funkcji całkowitych. Następnie można przekazać tę funkcję skrótu do algorytmu uczenia maszynowego, aby szkolić model analizy tekstu.

Funkcje wyznaczania wartości skrótu funkcji dostępne w tym module bazują na strukturze nimbusml. Aby uzyskać więcej informacji, zobacz [Klasa NgramHash](/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest&preserve-view=true).

## <a name="what-is-feature-hashing"></a>Co to jest mieszanie funkcji?

Mieszanie funkcji działa przez konwersję unikatowych tokenów na liczby całkowite. Działa on na dokładnych ciągach dostarczanych jako dane wejściowe i nie wykonuje żadnej analizy językowej ani przetwarzania wstępnego. 

Na przykład należy wykonać zestaw prostych zdań takich jak te, po których następuje wynik tonacji. Załóżmy, że chcesz użyć tego tekstu do skompilowania modelu.

|Tekst użytkownika|Opinia|
|--------------|---------------|
|Uwielbiane tę książkę|3|
|Hated tę książkę|1|
|Ta książka była świetna|3|
|Lubię książki|2|

Wewnętrznie moduł mieszania funkcji tworzy słownik n-gramy. Na przykład lista wielogramów dla tego zestawu danych będzie wyglądać następująco:

|Termin (w gramach)|Częstotliwość|
|------------|---------------|
|Ta książka|3|
|Uwielbiane|1|
|Hated|1|
|Kocham|1|

Można kontrolować rozmiar n-gramów przy użyciu właściwości **n-Grams** . W przypadku wybrania opcji rozgramy są również obliczane unigrams. Słownik zawiera również pojedyncze warunki, takie jak następujące:

|Termin (unigrams)|Częstotliwość|
|------------|---------------|
|książka|3|
|I|3|
|książki|1|
|błędu|1|

Po skompilowaniu słownika moduł tworzenia skrótów funkcji konwertuje warunki słownika na wartości skrótu. Następnie oblicza, czy funkcja została użyta w każdym przypadku. Dla każdego wiersza danych tekstowych moduł wyprowadza zestaw kolumn, jedną kolumnę dla każdej funkcji skrótu.

Na przykład po utworzeniu skrótu kolumny funkcji mogą wyglądać następująco:

|Klasyfikacja|Funkcja mieszania 1|Funkcja mieszania 2|Funkcja mieszania 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Jeśli wartość w kolumnie to 0, wiersz nie zawiera funkcji skrótu.
* Jeśli wartość wynosi 1, wiersz zawiera funkcję.

Funkcja tworzenia skrótów pozwala reprezentować dokumenty tekstowe o zmiennej długości jako wektory funkcji liczbowych o równej długości, aby zmniejszyć liczbę wymiarów. Jeśli podjęto próbę użycia kolumny tekstu do szkolenia w takiej postaci, będzie ona traktowana jako kolumna funkcji kategorii z wieloma unikatowymi wartościami.

Wyniki liczbowe umożliwiają również korzystanie z typowych metod uczenia maszynowego, takich jak Klasyfikacja, klastrowanie i pobieranie informacji. Ponieważ operacje wyszukiwania mogą używać skrótów liczb całkowitych zamiast porównywania ciągów, pobieranie wag funkcji jest również znacznie szybsze.

## <a name="configure-the-feature-hashing-module"></a>Konfigurowanie modułu wyznaczania wartości skrótu funkcji

1.  Dodaj moduł skrótu funkcji do potoku w projektancie.

1. Połącz zestaw danych zawierający tekst, który chcesz przeanalizować.

    > [!TIP]
    > Ponieważ funkcja tworzenia skrótów nie wykonuje operacji leksykalnych, takich jak rdzenie lub obcinanie, można czasami uzyskać lepsze wyniki, przenosząc tekst przed zastosowaniem skrótu funkcji. 

1. Ustaw **kolumny docelowe** na kolumny tekstowe, które mają być konwertowane na funkcje zmieszane. Należy pamiętać, że:

    * Kolumny muszą być typu String.
    
    * Wybranie wielu kolumn tekstowych może mieć znaczący wpływ na wymiarowanie funkcji. Na przykład liczba kolumn dla wartości 10-bitowej jest równa 1 024 dla pojedynczej kolumny do 2 048 dla dwóch kolumn.

1. Użyj **Bitsize mieszania** , aby określić liczbę bitów do użycia podczas tworzenia tabeli skrótów.
    
    Domyślny rozmiar bitowy to 10. W przypadku wielu problemów ta wartość jest odpowiednia. Może być potrzebne więcej miejsca, aby uniknąć kolizji, w zależności od rozmiaru słownictwa n-gramowego w tekście szkoleniowym.
    
1. W przypadku **N-gramów** wprowadź liczbę określającą maksymalną długość N-gramów, która ma zostać dodana do słownika szkoleniowego. N-gram jest sekwencją *n* wyrazów, traktowaną jako unikatowa jednostka.

    Na przykład, jeśli wprowadzisz 3, unigrams, i trigrams zostanie utworzony.

1. Prześlij potok.

## <a name="results"></a>Wyniki

Po zakończeniu przetwarzania moduł wyprowadza przekształcony zestaw danych, w którym oryginalna kolumna tekstowa została przekonwertowana na wiele kolumn. Każda kolumna reprezentuje funkcję w tekście. W zależności od tego, jaki jest istotny słownik, zestaw danych może być duży:

|Nazwa kolumny 1|Typ kolumny 2|
|-------------------|-------------------|
|USERTEXT|Oryginalna kolumna danych|
|TONACJI|Oryginalna kolumna danych|
|USERTEXT — funkcja 1|Kolumna funkcji z mieszaniem|
|USERTEXT — funkcja 2|Kolumna funkcji z mieszaniem|
|USERTEXT — funkcja skrótu n|Kolumna funkcji z mieszaniem|
|USERTEXT — funkcja wyznaczania wartości skrótu 1024|Kolumna funkcji z mieszaniem|

Po utworzeniu przekształconego zestawu danych można go użyć jako danych wejściowych modułu uczenie modelu.
 
## <a name="best-practices"></a>Najlepsze rozwiązania

Poniższe najlepsze rozwiązania mogą pomóc w optymalnym wykorzystaniu modułu tworzenia skrótów funkcji:

* Dodaj wstępnie przetworzony moduł tekstowy przed użyciem mieszania funkcji do wstępnego przetworzenia tekstu wejściowego. 

* Aby usunąć kolumny tekstowe z wyjściowego zestawu danych, Dodaj moduł SELECT Columns (Wybieranie kolumn). Po wygenerowaniu funkcji tworzenia skrótów nie są potrzebne kolumny tekstowe.
    
* Należy rozważyć użycie tych opcji wstępnego przetwarzania tekstu, aby uprościć wyniki i poprawić dokładność:

    * Dzielenie wyrazów
    * Zatrzymywanie usuwania wyrazów
    * Normalizacja przypadku
    * Usuwanie interpunkcji i znaków specjalnych
    * Uszkodzeniem  

Optymalny zestaw metod przetwarzania wstępnego, które mają być stosowane w dowolnym rozwiązaniu, zależy od domeny, słownika i potrzeby biznesowe. potoku z danymi, aby zobaczyć, które metody przetwarzania tekstu są najbardziej skuteczne.

## <a name="next-steps"></a>Następne kroki
            
Zobacz [zestaw modułów dostępnych](module-reference.md) do Azure Machine Learning