---
title: 'Las decyzyjny wieloklasowej: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć model uczenia maszynowego na podstawie algorytmu *lasu decyzyjnego* za pomocą wieloklasowego modułu lasu w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: e22881a904ed33214e90604d71489d8691fe820b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90883252"
---
# <a name="multiclass-decision-forest-module"></a>Wieloklasowy moduł lasu decyzyjnego

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do tworzenia modelu uczenia maszynowego na podstawie algorytmu *lasu decyzyjnego* . Las decyzyjny jest modelem modelu, który szybko kompiluje szereg drzew decyzyjnych, a jednocześnie uczenie się na podstawie oznakowanych danych.

## <a name="more-about-decision-forests"></a>Więcej informacji o lasach decyzyjnych

Algorytm lasu decyzyjnego to metoda uczenia się w celu klasyfikacji. Algorytm działa przez utworzenie wielu drzew decyzyjnych, a następnie *głosujących* się na najpopularniejszej klasie wyjściowej. Głosowanie jest formą agregacji, w której każde drzewo w lesie decyzyjnym klasyfikacji wyprowadza nieznormalizowane histogramy częstotliwości etykiet. Proces agregacji sumuje te histogramy i normalizuje wynik, aby uzyskać "prawdopodobieństwa" dla każdej etykiety. Drzewa mające wysoki poziom zaufania mają większą wagę w ostatecznej decyzji kompletu.

Drzewa decyzyjne ogólnie są modelami nieparametrycznymi, co oznacza, że obsługują dane z różnymi dystrybucjami. W każdym drzewie sekwencja prostych testów jest uruchamiana dla każdej klasy, zwiększając poziomy struktury drzewa do momentu osiągnięcia węzła liścia (decyzja).

Drzewa decyzyjne mają wiele zalet:

+ Mogą reprezentować granice decyzyjne nieliniowe.
+ Są one wydajne w przypadku obliczeń i wykorzystania pamięci podczas szkoleń i przewidywania.
+ Wykonują one zintegrowane Wybieranie funkcji i klasyfikację.
+ Są one odporne na obecność funkcji szumów.

Klasyfikator lasu decyzyjnego w Azure Machine Learning składa się z części drzewa decyzyjnego. Ogólnie rzecz biorąc, modele kompletów zapewniają lepszy zakres i dokładność niż pojedyncze drzewa decyzyjne. Aby uzyskać więcej informacji, zobacz [drzewa decyzyjne](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Jak skonfigurować Las decyzyjny wieloklasowej

1. Dodaj moduł **lasu decyzyjnej klasy** do potoku w projektancie. Ten moduł można znaleźć w obszarze **Machine Learning**, **zainicjuj model**i **klasyfikację**.

2. Kliknij dwukrotnie moduł, aby otworzyć okienko **Właściwości** .

3. Dla **metody ponowne próbkowanie**wybierz metodę używaną do tworzenia poszczególnych drzew.  Możesz wybrać jedną z opcji Working lub Replication (replikacja).

    + **Working**: w workach jest również wywoływana *agregacja Bootstrap*. W tej metodzie Każde drzewo jest uprawiane na nowym przykładzie, utworzonym losowo próbkowanie oryginalnego zestawu danych z zastępowaniem do momentu, gdy zestaw danych ma rozmiar oryginalny. Dane wyjściowe modeli są łączone przez *głosowanie*, który jest formą agregacji. Aby uzyskać więcej informacji, zobacz wpis Wikipedia na potrzeby agregowania Bootstrap.

    + **Replikacja**: w replikacji Każde drzewo jest przeszkolone dokładnie na te same dane wejściowe. Określenie, który predykat Split jest używany dla każdego węzła drzewa, pozostaje losowy, tworząc różne drzewa.

   

4. Określ, w jaki sposób ma być szkolony model, ustawiając opcję **tworzenia trybu Trainer** .

    + **Pojedynczy parametr**: zaznacz tę opcję, Jeśli wiesz, jak chcesz skonfigurować model i udostępnić zestaw wartości jako argumenty.

    + **Zakres parametrów**: Wybierz tę opcję, jeśli nie masz pewności co do najlepszych parametrów i chcesz uruchomić odchylenia parametrów. Wybierz zakres wartości do iteracji, a [Parametry dostrojenia modelu](tune-model-hyperparameters.md) przechodzą na wszystkie możliwe kombinacje ustawień, które podano, aby określić parametry, które generują optymalne wyniki.   

5. **Liczba drzew decyzyjnych**: wpisz maksymalną liczbę drzew decyzyjnych, które można utworzyć w części. Przez utworzenie większej liczby drzew decyzyjnych można potencjalnie uzyskać lepszy zakres, ale może to zwiększyć czas uczenia.

    Ta wartość kontroluje również liczbę drzew wyświetlanych w wynikach podczas wizualizacji przeszkolonego modelu. Aby wyświetlić lub wydrukować pojedyncze drzewo, można ustawić wartość 1. oznacza to jednak, że można utworzyć tylko jedno drzewo (drzewo z początkowym zestawem parametrów) i nie są wykonywane żadne dalsze iteracje.

6. **Maksymalna głębokość drzew decyzyjnych**: wpisz liczbę, aby ograniczyć maksymalną głębokość każdego drzewa decyzyjnego. Zwiększenie głębokości drzewa może zwiększyć precyzję w przypadku ryzyka pewnego przekroczenia i zwiększenia czasu uczenia się.

7. **Liczba losowych podziałów na węzeł**: wpisz liczbę podziałów, która ma być używana podczas kompilowania każdego węzła drzewa. *Podział* oznacza, że funkcje na każdym poziomie drzewa (węzeł) są losowo podzielone.

8. **Minimalna liczba próbek na węzeł liścia**: wskazuje minimalną liczbę przypadków, które są wymagane do utworzenia dowolnego węzła terminalu (liścia) w drzewie. Zwiększenie tej wartości spowoduje zwiększenie wartości progowej tworzenia nowych reguł.

    Na przykład, z wartością domyślną 1, nawet pojedynczy przypadek może spowodować utworzenie nowej reguły. W przypadku zwiększenia wartości do 5 dane szkoleniowe muszą zawierać co najmniej pięć przypadków, które spełniają te same warunki.



10. Połącz zestaw danych z etykietą i Przeszkol model:

    + Jeśli ustawisz **tryb tworzenia Trainer** na **pojedynczy parametr**, Połącz znacznikowy zestaw danych i moduł [uczenie modelu](train-model.md) .  
  
    + Jeśli ustawisz **tryb Trainer** na **zakres parametrów**, Połącz znacznikowy zestaw danych i nauczysz model przy użyciu funkcji [dostrajania parametrów modelu](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Jeśli przejdziesz do zakresu parametrów w celu [nauczenia modelu](train-model.md), zostanie użyta tylko wartość domyślna na liście jednego parametru.  
    > 
    > W przypadku przekazania jednego zestawu wartości parametrów do modułu [strojenia modelu](tune-model-hyperparameters.md) , gdy oczekuje on zakresu ustawień dla każdego parametru, zignoruje wartości i użyje wartości domyślnych dla dodanej informacji.  
    > 
    > Jeśli wybierzesz opcję **zakres parametrów** i wprowadzisz pojedynczą wartość dla każdego parametru, ta pojedyncza wartość zostanie użyta w całym wyczyszczeniu, nawet jeśli inne parametry zmienią się w zakres wartości.

11. Prześlij potok.



## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 