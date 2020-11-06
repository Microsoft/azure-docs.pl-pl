---
title: 'Two-Class Average Perceptron: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć klasyfikator binarny przy użyciu Two-Class średniego modułu Perceptron w projektancie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: f1161dc99b84026bcc830c08b63b95d0bf3b0994
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421145"
---
# <a name="two-class-averaged-perceptron-module"></a>Two-Class średniego modułu Perceptron

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do tworzenia modelu uczenia maszynowego na podstawie średniego algorytmu Perceptron.  
  
Ten algorytm klasyfikacji to nadzorowana Metoda uczenia i wymaga *oznakowanego zestawu danych* , który zawiera kolumnę etykiet. Możesz nauczyć model, dostarczając model i otagowany zestaw danych jako dane wejściowe do [modelu uczenia](./train-model.md). Model przeszkolony może być następnie używany do przewidywania wartości dla nowych przykładów wejściowych.  

### <a name="about-averaged-perceptron-models"></a>Informacje o średnich modelach Perceptron

*Średnia Metoda Perceptron* to wczesna i prosta wersja sieci neuronowych. W tym podejściu dane wejściowe są klasyfikowane do kilku możliwych wyjść opartych na funkcji liniowej, a następnie połączone z zestawem wag, które pochodzą od wektora funkcji, dlatego nazwa "Perceptron".

Prostsze modele Perceptron są odpowiednie do uczenia się liniowo oddzielnych wzorców, natomiast sieci neuronowych (zwłaszcza głębokie sieci neuronowych) mogą modelować bardziej złożone granice klas. Perceptrons są jednak szybsze i ponieważ przetwarzają przypadki seryjnie, Perceptrons może być używany z ciągłym szkoleniem.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Jak skonfigurować Two-Class Perceptron Average

1.  Dodaj do potoku moduł **Perceptron o średniej klasie** .  

2.  Określ, w jaki sposób ma być szkolony model, ustawiając opcję **tworzenia trybu Trainer** .  
  
    -   **Pojedynczy parametr** : Jeśli wiesz, jak chcesz skonfigurować model, podaj określony zestaw wartości jako argumenty.

    -   **Zakres parametrów** : Wybierz tę opcję, jeśli nie masz pewności co do najlepszych parametrów i chcesz uruchomić odchylenia parametrów. Wybierz zakres wartości do iteracji, a [Parametry dostrojenia modelu](tune-model-hyperparameters.md) przechodzą na wszystkie możliwe kombinacje ustawień, które podano, aby określić parametry, które generują optymalne wyniki.  
  
3.  W polu **stawka szkoleniowa** Określ wartość dla *stawki szkoleniowej*. Wartości współczynnika uczenia kontrolują rozmiar kroku, który jest używany w gradiencie stochastycznego przy każdym przetestowaniu i skorygowaniu modelu.
  
     Zmniejszając szybkość, można testować model częściej, z ryzykiem, który może zostać zablokowany w lokalnej Plateau. Dzięki powiększeniu tego kroku można szybciej łączyć się z ryzykiem w przypadku przekroczenia rzeczywistych wartości.
  
4.  Dla **maksymalnej liczby iteracji** wpisz, ile razy algorytm ma przeanalizować dane szkoleniowe.  
  
     Zatrzymywanie wczesne często zapewnia lepszy sposób uogólniania. Zwiększenie liczby iteracji usprawnia dopasowanie w przypadku naruszenia.
  
5.  W przypadku **liczby losowej inicjatora** opcjonalnie wpisz wartość całkowitą, która ma być używana jako inicjator. Użycie inicjatora jest zalecane, jeśli chcesz zapewnić odtwarzalność potoku między przebiegami.  
  
1.  Połącz zestaw danych szkoleniowych i Przeszkol model:

    + Jeśli ustawisz **tryb tworzenia Trainer** na **pojedynczy parametr** , Połącz znacznikowy zestaw danych i moduł [uczenie modelu](train-model.md) .  
  
    + Jeśli ustawisz **tryb Trainer** na **zakres parametrów** , Połącz znacznikowy zestaw danych i nauczysz model przy użyciu funkcji [dostrajania parametrów modelu](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Jeśli przejdziesz do zakresu parametrów w celu [nauczenia modelu](train-model.md), zostanie użyta tylko wartość domyślna na liście jednego parametru.  
    > 
    > W przypadku przekazania jednego zestawu wartości parametrów do modułu [strojenia modelu](tune-model-hyperparameters.md) , gdy oczekuje on zakresu ustawień dla każdego parametru, zignoruje wartości i użyje wartości domyślnych dla dodanej informacji.  
    > 
    > Jeśli wybierzesz opcję **zakres parametrów** i wprowadzisz pojedynczą wartość dla każdego parametru, ta pojedyncza wartość zostanie użyta w całym wyczyszczeniu, nawet jeśli inne parametry zmienią się w zakres wartości.




## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 