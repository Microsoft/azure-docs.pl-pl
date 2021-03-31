---
title: 'Wieloklasowa sieć neuronowych: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać wieloklasowego modułu sieci neuronowych w programie Azure Machine Learning Designer do przewidywania obiektu docelowego, który ma wartości wieloklasowe.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: a4f7f8a7793f31ffbf2099cbfb314fc5097319f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421264"
---
# <a name="multiclass-neural-network-module"></a>Wieloklasowy moduł sieci neuronowych

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do tworzenia modelu sieci neuronowych, który może służyć do przewidywania celu, który ma wiele wartości. 

Na przykład sieci neuronowych tego rodzaju mogą być używane w złożonych zadaniach przetwarzania obrazów, takich jak rozpoznawanie cyfr lub liter, klasyfikacja dokumentu i Rozpoznawanie wzorców.

Klasyfikacja przy użyciu sieci neuronowych to nadzorowana Metoda uczenia i dlatego wymaga *oznakowanego zestawu danych* , który zawiera kolumnę etykieta.

Możesz nauczyć model, dostarczając model i otagowany zestaw danych jako dane wejściowe do [modelu uczenia](./train-model.md). Model przeszkolony może być następnie używany do przewidywania wartości dla nowych przykładów wejściowych.  

## <a name="about-neural-networks"></a>Informacje o sieciach neuronowych

Sieć neuronowych to zestaw wzajemnie połączonych warstw. Dane wejściowe są pierwszą warstwą i są połączone z warstwą wyjściową przez wykres acykliczne, składający się z ważonych krawędzi i węzłów.

Między warstwami wejściowymi i wyjściowymi można wstawiać wiele ukrytych warstw. Większość zadań predykcyjnych można łatwo wykonać przy użyciu tylko jednej lub kilku ukrytych warstw. Jednak ostatnie badania wykazały, że głębokie neuronowych Networks (DNN) z wieloma warstwami mogą być skuteczne w złożonych zadaniach, takich jak rozpoznawanie obrazów lub mowy. Kolejne warstwy są używane do modelowania rosnącego poziomu głębokości semantycznej.

Relacja między danymi wejściowymi i wyjściowymi jest uzyskiwana z uczenia sieci neuronowych w danych wejściowych. Kierunek wykresu przechodzi z danych wejściowych za pośrednictwem warstwy Ukryta i do warstwy wyjściowej. Wszystkie węzły w warstwie są połączone przy użyciu ważonych krawędzi do węzłów w następnej warstwie.

Aby obliczyć dane wyjściowe sieci dla konkretnych danych wejściowych, wartość jest obliczana w każdym węźle ukrytych warstw i w warstwie wyjściowej. Wartość jest ustawiana przez obliczenie ważonej sumy wartości węzłów z poprzedniej warstwy. Funkcja aktywacji zostanie następnie zastosowana do tej kwoty ważonej.

## <a name="configure-multiclass-neural-network"></a>Konfiguruj wieloklasową sieć neuronowych

1. Dodaj **wieloklasowy moduł sieci neuronowych** do potoku w projektancie. Ten moduł można znaleźć w obszarze **Machine Learning**, **zainicjować**, w kategorii **Klasyfikacja** .

2. **Utwórz tryb Trainer**: Użyj tej opcji, aby określić, w jaki sposób model ma być szkolony:

    - **Pojedynczy parametr**: Wybierz tę opcję, jeśli już wiesz, jak chcesz skonfigurować model.

    - **Zakres parametrów**: Wybierz tę opcję, jeśli nie masz pewności co do najlepszych parametrów i chcesz uruchomić odchylenia parametrów. Wybierz zakres wartości do iteracji, a [Parametry dostrojenia modelu](tune-model-hyperparameters.md) przechodzą na wszystkie możliwe kombinacje ustawień, które podano, aby określić parametry, które generują optymalne wyniki.  

3. **Specyfikacja warstwy ukrytej**: Wybierz typ architektury sieci do utworzenia.

    - W **pełni połączony przypadek**: Wybierz tę opcję, aby utworzyć model przy użyciu domyślnej architektury sieci neuronowych. W przypadku modeli sieci z wieloklasową neuronowych wartości domyślne są następujące:

        - Jedna ukryta warstwa
        - Warstwa wyjściowa jest w pełni połączona z ukrytą warstwą.
        - Warstwa ukryta jest w pełni podłączona do warstwy wejściowej.
        - Liczba węzłów w warstwie wejściowej jest określana na podstawie liczby funkcji w danych szkoleniowych.
        - Liczba węzłów w warstwie Ukryta może być ustawiana przez użytkownika. Wartość domyślna to 100.
        - Liczba węzłów w warstwie wyjściowej zależy od liczby klas.
  
   

5. **Liczba ukrytych węzłów**: Ta opcja umożliwia dostosowanie liczby ukrytych węzłów w architekturze domyślnej. Wpisz liczbę ukrytych węzłów. Wartość domyślna to jedna ukryta warstwa z węzłami 100.

6. **Szybkość uczenia**: Zdefiniuj rozmiar kroku wykonany w każdej iteracji przed poprawką. Większa wartość współczynnika uczenia może spowodować szybsze zbieżność modelu, ale może przekroczyć wartości minimalne.

7. **Liczba iteracji uczenia** się: Określ maksymalną liczbę przypadków, w których algorytm powinien przetwarzać przypadki szkoleniowe.

8. **Średnica początkowej wagi uczenia**: Określ wagi węzłów na początku procesu uczenia.

9. **Pędu**: Określ wagę do zastosowania podczas uczenia się z węzłami z poprzednich iteracji.
  
11. **Przykłady losowe**: Wybierz tę opcję, aby losowo rozmieścić przypadki między iteracjami.

    W przypadku zaznaczenia tej opcji przypadki są przetwarzane w dokładnie tym samym porządku przy każdym uruchomieniu potoku.

12. **Inicjator liczb losowych**: wpisz wartość, która ma być używana jako inicjator, jeśli chcesz zapewnić powtarzalność między przebiegami tego samego potoku.

14. Uczenie modelu:

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

- Aby zapisać migawkę przeszkolonego modelu, wybierz kartę dane **wyjściowe** w prawym panelu modułu **uczenie modelu** . Wybierz ikonę **zarejestruj zestaw danych** , aby zapisać model jako moduł wielokrotnego użytku.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 