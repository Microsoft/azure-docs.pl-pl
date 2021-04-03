---
title: 'Szybkie regresja quantile lasów: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć model regresji, który może przewidywać wartości dla określonej liczby quantiles przy użyciu modułu regresji quantile w szybkim lesie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 6d59d2fabb0b74ef12e33f55467b0ba68e0b7386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90907959"
---
# <a name="fast-forest-quantile-regression"></a>Szybka regresja kwantylowa lasu decyzyjnego

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Użyj tego modułu, aby utworzyć szybki model regresji quantile w potoku. Szybkie regresja quantilei lasów jest przydatne, jeśli chcesz zrozumieć więcej o dystrybucji przewidywanej wartości, zamiast pobierać jedną średnią wartość przewidywania. Ta metoda ma wiele aplikacji, w tym:  
  
- Przewidywanie cen  
  
- Szacowanie wydajności uczniów lub stosowanie wykresów wzrostu do oceny rozwoju elementu podrzędnego  
  
- Odnajdywanie relacji predykcyjnych w przypadkach, gdy istnieje tylko słabe relacje między zmiennymi  
  
Ten algorytm regresji jest metodą uczenia **nadzorowanego** , co oznacza, że wymaga oznakowanego zestawu danych, który zawiera kolumnę etykiet. Ponieważ jest to algorytm regresji, kolumna Label musi zawierać tylko wartości numeryczne.

## <a name="more-about-quantile-regression"></a>Więcej informacji o regresji quantile

Istnieje wiele różnych typów regresji. Po prostu, regresja oznacza dopasowanie modelu do obiektu docelowego wyrażonego jako wektor liczbowy. Jednak statystykówy opracowują coraz bardziej zaawansowane metody regresji.

Najprostsza definicja *quantile* jest wartością, która dzieli zestaw danych na grupy o równym rozmiarze; w ten sposób wartości quantile oznaczają granice między grupami. Statystycznie mówiąc, quantiles są wartościami pobieranymi w regularnych odstępach czasu z odwrotności funkcji rozkładu skumulowanego (CDF) zmiennej losowej.

Modele regresji liniowej próbują przewidzieć wartość zmiennej liczbowej przy użyciu pojedynczego oszacowania, czyli *średniej*, czasami trzeba przewidzieć zakres lub całą dystrybucję zmiennej docelowej. Do tego celu opracowano techniki takie jak regresja bayesowskie i regresja quantile.

Regresja quantile ułatwia zrozumienie rozkładu przewidywanej wartości. Oparte na drzewie modele regresji quantile, takie jak używane w tym module, mają dodatkowe zalety, których można użyć do przewidywania dystrybucji bez parametrów.

  
## <a name="how-to-configure-fast-forest-quantile-regression"></a>Jak skonfigurować regresję szybkiego Quantilenia lasów

1. Dodaj moduł **regresja quantile w lesie** do potoku w projektancie. Ten moduł można znaleźć w obszarze **algorytmy Machine Learning** w kategorii **regresja** .

2. W okienku po prawej stronie modułu **regresja Quantilea w szybkim lesie** Określ, w jaki sposób ma być szkolony model, ustawiając opcję **Utwórz Trainer tryb** .  
  
    - **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować model, podaj określony zestaw wartości jako argumenty. Podczas uczenia modelu Użyj [modelu uczenia](train-model.md).
  
    - **Zakres parametrów**: Jeśli nie masz pewności co do najlepszych parametrów, Przeczyść parametry przy użyciu modułu [dostrajania parametrów modelu](tune-model-hyperparameters.md) . Trainer wykonuje iterację wielu określonych wartości, aby znaleźć optymalną konfigurację.

3. **Liczba drzew**, wpisz maksymalną liczbę drzew, które można utworzyć w częściowej. Jeśli tworzysz więcej drzew, zwykle prowadzi to do większej dokładności, ale kosztem dłuższego czasu uczenia się.  

4. **Liczba liści**, wpisz maksymalną liczbę liści lub węzły terminalu, które można utworzyć w dowolnym drzewie.  

5. **Minimalna liczba wystąpień szkoleniowych wymaganych do utworzenia liścia**, Określ minimalną liczbę przykładów, które są wymagane do tworzenia dowolnego węzła terminalu (liścia) w drzewie.  
  
     Zwiększenie tej wartości spowoduje zwiększenie wartości progowej tworzenia nowych reguł. Na przykład, z wartością domyślną 1, nawet pojedynczy przypadek może spowodować utworzenie nowej reguły. W przypadku zwiększenia wartości do 5 dane szkoleniowe muszą zawierać co najmniej 5 przypadków, które spełniają te same warunki.

6. **Ułamek workowy**, określ liczbę z zakresu od 0 do 1 reprezentującą ułamek próbek do użycia podczas kompilowania każdej grupy quantiles. Próbki są wybierane losowo, z zastępowaniem.

7. **Podziel ułamek**, wpisz liczbę z zakresu od 0 do 1 reprezentującą ułamek funkcji do użycia w każdym rozdziale drzewa. Używane funkcje są zawsze wybierane losowo.

8. **Quantiles do oszacowania**, wpisz rozdzieloną średnikami listę quantiles, dla których model ma być używany, i tworzyć przewidywania.
  
     Na przykład jeśli chcesz skompilować model, który szacuje dla kwartyl, należy wpisać `0.25; 0.5; 0.75` .  

9. Opcjonalnie wpisz wartość **losowego inicjatora liczbowego** , aby wypełniać generator liczb losowych używany przez model.  Wartość domyślna to 0, co oznacza, że wybierany jest losowy inicjator.
  
     Należy podać wartość, jeśli trzeba będzie odtworzyć wyniki po kolejnych uruchomieniach na tych samych danych.  

10. Połącz zestaw danych szkoleniowych i nieszkolony model z jednym z modułów szkoleniowych: 

    - W przypadku ustawienia opcji **Utwórz tryb Trainer** na **pojedynczy parametr** Użyj modułu [uczenie modelu](train-model.md) .

    - W przypadku ustawienia opcji **Utwórz tryb Trainer** na **zakres parametrów** należy użyć modułu [dostrajania parametrów modelu](tune-model-hyperparameters.md) .

    > [!WARNING]
    > 
    > - Jeśli przejdziesz do zakresu parametrów w celu [nauczenia modelu](train-model.md), zostanie użyta tylko pierwsza wartość z listy zakres parametrów.
    > 
    > - W przypadku przekazania jednego zestawu wartości parametrów do modułu [strojenia modelu](tune-model-hyperparameters.md) , gdy oczekuje on zakresu ustawień dla każdego parametru, zignoruje wartości i użyje wartości domyślnych dla tego obiektu.
    > 
    > - Jeśli wybierzesz opcję **zakres parametrów** i wprowadzisz pojedynczą wartość dla każdego parametru, ta pojedyncza wartość zostanie użyta w całym wyczyszczeniu, nawet jeśli inne parametry zmienią się w zakres wartości.

11. Prześlij potok.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby zapisać migawkę przeszkolonego modelu, wybierz moduł szkoleń, a następnie przejdź do karty Output **+ Logs** na prawym panelu. Kliknij ikonę **zarejestruj zestaw danych**.  Zapisany model można znaleźć jako moduł w drzewie modułów.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning.
