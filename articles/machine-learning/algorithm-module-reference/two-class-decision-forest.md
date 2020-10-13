---
title: 'Two-Class Las decyzyjny: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć model uczenia maszynowego na podstawie algorytmu lasów decyzyjnych w programie Azure Machine Learning przy użyciu Two-Classowego modułu lasu decyzyjnego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/03/2020
ms.openlocfilehash: 9870b5366ac629fe1b10cfde15a58b85c7c6fbc3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907723"
---
# <a name="two-class-decision-forest-module"></a>Moduł Las decyzyjny Two-Class

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do tworzenia modelu uczenia maszynowego na podstawie algorytmu lasów decyzyjnych.  

Lasy decyzyjne są szybkimi, nadzorowanymi modelami kompletów. Ten moduł jest dobrym rozwiązaniem, jeśli chcesz przewidzieć element docelowy z maksymalnie dwoma wynikami. 

## <a name="understanding-decision-forests"></a>Zrozumienie lasów decyzyjnych

Ten algorytm lasu decyzyjnego to metoda uczenia się, która jest przeznaczona dla zadań klasyfikacji. Metody kompletności opierają się na ogólnej zasadzie, która nie polega na pojedynczym modelu, można uzyskać lepsze wyniki i bardziej Uogólniony model, tworząc wiele powiązanych modeli i łącząc je w jakiś sposób. Ogólnie rzecz biorąc, modele kompletów zapewniają lepszy zakres i dokładność niż pojedyncze drzewa decyzyjne. 

Istnieje wiele sposobów tworzenia pojedynczych modeli i łączenia ich w kompletną. Ta konkretna implementacja lasu decyzyjnego działa przez utworzenie wielu drzew decyzyjnych, a następnie **głosujących** na najpopularniejszą klasę wyjściową. Głosowanie jest jedną z lepszych metod generowania wyników w modelu typu "kompletna". 

+ Wiele pojedynczych drzew klasyfikacji jest tworzonych przy użyciu całego zestawu danych, ale różne (zazwyczaj losowe) punkty początkowe. Różni się to od przypadkowego podejścia lasów, w którym poszczególne drzewa decyzyjne mogą korzystać tylko z części danych lub funkcji.
+ Każde drzewo w drzewie lasu decyzyjnego wyprowadza histogram o nieznormalizowanej częstotliwości etykiet. 
+ Proces agregacji sumuje te histogramy i normalizuje wynik, aby uzyskać "prawdopodobieństwa" dla każdej etykiety. 
+ Drzewa, które mają wysoki poziom zaufania, będą mieć większą wagę w ostatecznej decyzji o zestawie.

Drzewa decyzyjne ogólnie posiadają wiele zalet zadań klasyfikacji:
  
- Mogą przechwytywać granice decyzyjne nieliniowe.
- Możesz nauczyć i przewidzieć duże ilości danych, ponieważ są one wydajne w zakresie obliczeń i użycia pamięci.
- Wybór funkcji jest zintegrowany w procesach szkolenia i klasyfikacji.  
- Drzewa mogą obsługiwać dane szumu i wiele funkcji.  
- Nie są to modele parametryczne, co oznacza, że mogą obsługiwać dane przy użyciu różnych dystrybucji. 

Jednak proste drzewa decyzyjne mogą overfit na danych i są mniej możliwe do wyróżnienia niż w drzewie drzewa.

Aby uzyskać więcej informacji, zobacz [lasy decyzyjne](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Jak skonfigurować
  
1.  Dodaj moduł **Las decyzyjny z dwoma klasami** do potoku w Azure Machine Learning i Otwórz okienko **Właściwości** modułu. 

    Moduł można znaleźć w obszarze **Machine Learning**. Rozwiń węzeł **Inicjalizacja**, a następnie pozycję **Klasyfikacja**.  
  
2.  Dla **metody ponowne próbkowanie**wybierz metodę używaną do tworzenia poszczególnych drzew.  Możesz wybrać jedną z opcji **Working** lub **replikacja**.  
  
    -   **Working**: w workach jest również wywoływana *agregacja Bootstrap*. W tej metodzie Każde drzewo jest uprawiane na nowym przykładzie, utworzonym losowo próbkowanie oryginalnego zestawu danych z zastępowaniem do momentu, gdy zestaw danych ma rozmiar oryginalny.  
  
         Dane wyjściowe modeli są łączone przez *głosowanie*, który jest formą agregacji. Każde drzewo w lesie decyzyjnym klasyfikacji wyprowadza nieznormalizowane histogramy częstotliwości etykiet. Agregacją jest sumowanie tych histogramów i normalizowanie w celu uzyskania "prawdopodobieństwa" dla każdej etykiety. W ten sposób drzewa, które mają wysoki poziom zaufania, będą mieć większą wagę w ostatecznej decyzji dotyczącej tego elementu.  
  
         Aby uzyskać więcej informacji, zobacz wpis Wikipedia na potrzeby agregowania Bootstrap.  
  
    -   **Replikacja**: w replikacji Każde drzewo jest przeszkolone dokładnie na te same dane wejściowe. Określenie, który predykat Split jest używany dla każdego węzła drzewa, pozostaje losowy, a drzewa będą różne.   
  
3.  Określ, w jaki sposób ma być szkolony model, ustawiając opcję **tworzenia trybu Trainer** .  
  
    -   **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować model, możesz podać określony zestaw wartości jako argumenty.

    -   **Zakres parametrów**: Jeśli nie masz pewności co do najlepszych parametrów, możesz znaleźć optymalne parametry przy użyciu modułu [dostrajania parametrów modelu](tune-model-hyperparameters.md) . Podajesz kilka wartości, a Trainer wykonuje iterację wielu kombinacji ustawień, aby określić kombinację wartości, które tworzą najlepszy wynik.
  
4.  W przypadku **liczby drzew decyzyjnych**wpisz maksymalną liczbę drzew decyzyjnych, które można utworzyć w części. Przez utworzenie większej liczby drzew decyzyjnych można potencjalnie uzyskać lepszy zakres, ale wydłużyć czas uczenia.  
  
    > [!NOTE]
    >  Ta wartość kontroluje również liczbę drzew wyświetlanych podczas wizualizacji przeszkolonego modelu. Jeśli chcesz zobaczyć lub wydrukować pojedyncze drzewo, możesz ustawić wartość 1. Jednak można utworzyć tylko jedno drzewo (drzewo z początkowym zestawem parametrów) i nie są wykonywane żadne dalsze iteracje.
  
5.  W celu uzyskania **maksymalnej głębokości drzew decyzyjnych**wpisz liczbę, aby ograniczyć maksymalną głębokość każdego drzewa decyzyjnego. Zwiększenie głębokości drzewa może zwiększyć precyzję w przypadku ryzyka pewnego przekroczenia i zwiększenia czasu uczenia się.
  
  
7.  Dla **minimalnej liczby próbek na węzeł liścia**wskaż minimalną liczbę przypadków, które są wymagane do utworzenia dowolnego węzła terminalu (liścia) w drzewie.
  
     Zwiększenie tej wartości spowoduje zwiększenie wartości progowej tworzenia nowych reguł. Na przykład, z wartością domyślną 1, nawet pojedynczy przypadek może spowodować utworzenie nowej reguły. W przypadku zwiększenia wartości do 5 dane szkoleniowe muszą zawierać co najmniej pięć przypadków, które spełniają te same warunki.  
  
8.  Wybierz opcję **Zezwalaj na nieznane wartości dla funkcji kategorii** , aby utworzyć grupę dla nieznanych wartości w zestawach szkoleniowych lub walidacji. Model może być mniej dokładny dla znanych wartości, ale może zapewnić lepsze przewidywania dla nowych wartości (nieznanych). 

     W przypadku zaznaczenia tej opcji model może akceptować tylko wartości, które są zawarte w danych szkoleniowych.
  
9. Dołącz zestaw danych z etykietą i Przeszkol model:

    + Jeśli ustawisz **tryb tworzenia Trainer** na **pojedynczy parametr**, Połącz znacznikowy zestaw danych i moduł [uczenie modelu](train-model.md) .  
  
    + Jeśli ustawisz **tryb Trainer** na **zakres parametrów**, Połącz znacznikowy zestaw danych i nauczysz model przy użyciu funkcji [dostrajania parametrów modelu](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Jeśli przejdziesz do zakresu parametrów w celu [nauczenia modelu](train-model.md), zostanie użyta tylko wartość domyślna na liście jednego parametru.  
    > 
    > W przypadku przekazania jednego zestawu wartości parametrów do modułu [strojenia modelu](tune-model-hyperparameters.md) , gdy oczekuje on zakresu ustawień dla każdego parametru, zignoruje wartości i użyje wartości domyślnych dla dodanej informacji.  
    > 
    > Jeśli wybierzesz opcję **zakres parametrów** i wprowadzisz pojedynczą wartość dla każdego parametru, ta pojedyncza wartość zostanie użyta w całym wyczyszczeniu, nawet jeśli inne parametry zmienią się w zakres wartości.  
    
## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby zapisać migawkę przeszkolonego modelu, wybierz kartę dane **wyjściowe** w prawym panelu modułu **uczenie modelu** . Wybierz ikonę **zarejestruj zestaw danych** , aby zapisać model jako moduł wielokrotnego użytku.

+ Aby użyć modelu do oceniania, Dodaj moduł **model oceny** do potoku.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 