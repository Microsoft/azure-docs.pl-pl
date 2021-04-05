---
title: Przygotowywanie danych dla ML Studio (klasyczny) — proces nauki o danych zespołowych
description: Wstępne przetwarzanie i czyszczenie danych w celu przygotowania ich do efektywnego wykorzystania na potrzeby uczenia maszynowego.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: caedcf313ab809e9607907545f26ca1b62bbeca7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96012411"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Zadania w celu przygotowania danych do rozszerzonego uczenia maszynowego
Wstępne przetwarzanie i czyszczenie danych to ważne zadania, które należy wykonać, aby można było użyć zestawu danych do uczenia modelu. Dane pierwotne często są zakłóceniami i niezawodnymi i mogą mieć brakujące wartości. Użycie takich danych do modelowania może dawać mylące wyniki. Te zadania są częścią procesu nauki o danych zespołowych (przetwarzania TDSP) i zwykle postępują zgodnie z początkową eksploracją zestawu danych służącego do odnajdywania i planowania wymaganego przetwarzania wstępnego. Więcej szczegółowych instrukcji dotyczących procesu przetwarzania TDSP można znaleźć w opisie kroków przedstawionych w [procesie nauka danych zespołu](overview.md).

Zadania wstępnego przetwarzania i czyszczenia, takie jak zadanie eksploracji danych, mogą być wykonywane w różnych środowiskach, takich jak SQL lub Hive lub Azure Machine Learning Studio (klasyczny), oraz z różnymi narzędziami i językami, takimi jak R lub Python, w zależności od tego, gdzie dane są przechowywane i jak są sformatowane. Ponieważ przetwarzania TDSP ma charakter iteracyjny, te zadania mogą być wykonywane w różnych krokach przepływu pracy procesu.

W tym artykule wprowadzono różne koncepcje przetwarzania danych i zadania, które można podjąć przed lub po pozyskaniu danych do Azure Machine Learning Studio (klasyczne).

Aby zapoznać się z przykładem eksploracji i wstępnego przetwarzania danych w ramach Azure Machine Learning Studio (klasyczny), zobacz wideo dotyczące [przetwarzania wstępnego danych](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) .

## <a name="why-pre-process-and-clean-data"></a>Dlaczego wstępnie przetwarzasz i czyścisz dane?
Rzeczywiste dane świata są zbierane z różnych źródeł i procesów, a także mogą zawierać niezgodności lub uszkodzone dane. Typowe problemy z jakością danych powstają następujące:

* **Niekompletne**: dane nie mają atrybutów lub zawierają brakujące wartości.
* **Zakłócenia**: dane zawierają błędne rekordy lub elementy odstające.
* **Niespójne**: dane zawierają rekordy powodujące konflikt lub niezgodności.

Dane jakości są wymagane wstępnie dla modeli predykcyjnych. Aby uniknąć "elementów bezużytecznych, wyrzucania elementów bezużytecznych" i poprawiania jakości danych i w związku z tym wydajności modelu, należy bezwzględnie przeprowadzić ekran kondycji danych, aby szybko wypróbować problemy z danymi i decydować o odpowiednim przetwarzaniu i usuwaniu danych.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Jakie są typowe ekrany kondycji danych, które są wykorzystywane?
Można sprawdzić ogólną jakość danych, sprawdzając:

* Liczba **rekordów**.
* Liczba **atrybutów** (lub **funkcji**).
* **Typy danych** atrybutu (nominalny, porządkowy lub ciągły).
* Liczba **brakujących wartości**.
* **Poprawnie sformułowane** dane.
  * Jeśli dane są w postaci TSV lub CSV, należy sprawdzić, czy separatory kolumn i separatory wierszy zawsze są prawidłowo oddzielane kolumnami i liniami.
  * Jeśli dane są w formacie HTML lub XML, sprawdź, czy dane są poprawnie sformułowane na podstawie ich odpowiednich standardów.
  * Analiza może być również niezbędna w celu wyodrębnienia informacji o strukturze z danych z częściową strukturą lub bez struktury.
* **Niespójne rekordy danych**. Sprawdź, czy zakres wartości jest dozwolony. Na przykład, jeśli dane zawierają GPA uczniów (średnia punktu oceny), sprawdź, czy GPA znajduje się w wyznaczonam zakresie, powiedz 0 ~ 4.

Gdy znajdziesz problemy z danymi, **kroki przetwarzania** są konieczne, co często polega na czyszczeniu brakujących wartości, normalizacji danych, dyskretyzacji, przetwarzaniu tekstu, aby usunąć i/lub zastąpić osadzone znaki, które mogą mieć wpływ na wyrównanie danych, mieszane typy danych w wspólnych polach i innych.

**Azure Machine Learning wykorzystuje poprawnie sformułowane dane tabelaryczne**.  Jeśli dane są już w formie tabelarycznej, przetwarzanie wstępne danych może być wykonywane bezpośrednio z Azure Machine Learning Studio (klasyczne) w Machine Learning.  Jeśli dane nie są w formie tabelarycznej, Załóżmy, że jest ona w formacie XML, analiza może być wymagana w celu przekonwertowania danych na formularz tabelaryczny.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Jakie są najważniejsze zadania związane z przetwarzaniem wstępnego danych?
* **Czyszczenie danych**: Wypełnij brakujące wartości, Wykryj i Usuń dane o zakłóceniach i wartości odstające.
* **Przekształcanie danych**: normalizowanie danych w celu zmniejszenia wymiarów i szumów.
* **Zmniejszenie ilości danych**: Przykładowe rekordy danych lub atrybuty w celu ułatwienia obsługi danych.
* **Data dyskretyzacji**: konwertowanie atrybutów ciągłych na atrybuty kategorii w celu ułatwienia używania z określonymi metodami uczenia maszynowego.
* **Czyszczenie tekstu**: Usuwanie znaków osadzonych, które mogą spowodować niewłaściwe wyrównania danych, na przykład karty osadzone w pliku danych rozdzielanych kartami, osadzone nowe wiersze, które mogą przerywać rekordy, na przykład.

W poniższych sekcjach szczegółowo przedstawiono niektóre z tych kroków przetwarzania danych.

## <a name="how-to-deal-with-missing-values"></a>Jak radzić sobie z brakującymi wartościami?
Aby obsłużyć brakujące wartości, najlepiej ustalić przyczynę brakujących wartości w celu lepszego obsłużenia problemu. Typowe metody obsługi braku wartości to:

* **Usuwanie**: usuwanie rekordów z brakującymi wartościami
* **Podstawienie fikcyjne**: Zastąp brakujące wartości wartością fikcyjną: np. *nieznane* dla kategorii lub 0 dla wartości liczbowych.
* **Znaczenie znaczenia**: jeśli brakujące dane są liczbowe, Zastąp brakujące wartości średnią.
* **Częste podstawienie**: jeśli brakujące dane to kategorii, Zastąp brakujące wartości z najczęściej występującym elementem
* **Podstawienie regresji**: Użyj metody regresji, aby zastąpić brakujące wartości wartościami uległa pogorszeniu.  

## <a name="how-to-normalize-data"></a>Jak znormalizować dane?
Normalizacja danych przeskaluje wartości liczbowe do określonego zakresu. Popularne metody normalizacji danych obejmują:

* **Minimalna-maksymalna normalizacja**: liniowo Przekształć dane w zakres, powiedzmy od 0 do 1, gdzie wartość minimalna jest skalowana do 0, a maksymalna wartość 1.
* **Normalizacja wyniku Z**: Skaluj dane na podstawie średniego i odchylenia standardowego: Podziel różnicę między danymi a średnią przez odchylenie standardowe.
* **Skalowanie dziesiętne**: Skaluj dane przez przeniesienie przecinka dziesiętnego wartości atrybutu.  

## <a name="how-to-discretize-data"></a>Jak dyskretyzowania dane?
Dane można zdyskretyzowanej, konwertując wartości ciągłe na nominalne atrybuty lub interwały. Oto kilka sposobów na to:

* **Pakowania o równej szerokości**: Podziel zakres wszystkich możliwych wartości atrybutu na N grup o tym samym rozmiarze i przypisz wartości, które znajdują się w pojemniku z numerem pojemnika.
* **Pakowania o równej wysokości**: Podziel zakres wszystkich możliwych wartości atrybutu na N grup, z których każdy zawiera tę samą liczbę wystąpień, a następnie przypisz wartości, które znajdują się w pojemniku z numerem pojemnika.  

## <a name="how-to-reduce-data"></a>Jak zmniejszyć dane?
Istnieją różne metody zmniejszania rozmiaru danych w celu ułatwienia obsługi danych. W zależności od rozmiaru danych i domeny można zastosować następujące metody:

* **Rejestruj próbkowanie**: Przykładowe rekordy danych i wybierz tylko reprezentatywny podzbiór danych.
* **Próbkowanie atrybutów**: Wybierz tylko podzestaw najważniejszych atrybutów z danych.  
* **Agregacja**: Podziel dane na grupy i przechowuj numery dla każdej grupy. Na przykład dzienne numery przychodów w łańcuchu restauracji w ciągu ostatnich 20 lat można agregować do miesięcznego przychodu w celu zmniejszenia rozmiaru danych.  

## <a name="how-to-clean-text-data"></a>Jak wyczyścić dane tekstowe?
**Pola tekstowe w danych tabelarycznych** mogą zawierać znaki, które mają wpływ na wyrównanie kolumn i/lub granice rekordów. Na przykład karty osadzone w pliku rozdzielonym tabulatorem powodują nieprawidłowe wyrównanie kolumn i osadzonych znaków nowego wiersza. Niewłaściwa obsługa kodowania tekstu podczas zapisywania lub odczytywania tekstu prowadzi do utraty informacji, przypadkowe wprowadzenie nieczytelnych znaków (takich jak null) i może mieć wpływ na analizowanie tekstu. Dokładne analizowanie i edytowanie może być wymagane w celu czyszczenia pól tekstowych do właściwego wyrównania i/lub wyodrębnienia danych strukturalnych z danych tekstowych bez struktury lub z częściową strukturą.

**Eksploracja danych** oferuje wczesny wgląd w dane. W trakcie tego kroku można odzyskać wiele problemów z danymi, a odpowiednie metody mogą być stosowane w celu rozwiązania tych problemów.  Ważne jest, aby zadawać pytania, takie jak to, co jest źródłem problemu i jak można było wprowadzić problem. Ten proces pomaga również w wyborze kroków przetwarzania danych, które należy podjąć w celu ich rozwiązania. Określenie ostatecznych przypadków użycia i osób może być również używane do określania priorytetów nakładu pracy przetwarzania danych.

## <a name="references"></a>Odwołania
> *Wyszukiwanie danych: koncepcje i techniki*, wydanie trzecie, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber i Jian PEI
> 
> 

