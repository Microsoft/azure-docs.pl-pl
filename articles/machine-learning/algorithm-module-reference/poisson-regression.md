---
title: 'Regresja Poissona: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć model regresji Poissona przy użyciu modułu regresji Poissona.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: a1a09357c7f80d4af0198a33a2e0007782ef232f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905252"
---
# <a name="poisson-regression"></a>Regresja Poissona

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do tworzenia modelu regresji Poissona w potoku. Regresja Poissona jest przeznaczona do przewidywania wartości liczbowych, zazwyczaj liczy. W związku z tym należy używać tego modułu do tworzenia modelu regresji tylko wtedy, gdy wartości, które próbujesz przewidzieć, spełniają następujące warunki:

- Zmienna odpowiedzi ma [rozkład Poissona](https://en.wikipedia.org/wiki/Poisson_distribution).  

- Liczby nie mogą być ujemne. Metoda zakończy się niepowodzeniem, jeśli spróbujesz użyć jej z etykietami ujemnymi.

- Rozkład Poissona jest dystrybucją dyskretną; w związku z tym nie ma znaczenia, aby użyć tej metody z liczbami niecałkowitymi.

> [!TIP]
> Jeśli obiekt docelowy nie jest liczbą, regresja Poissona prawdopodobnie nie jest odpowiednią metodą. Wypróbuj [inne moduły regresji w projektancie](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference#machine-learning-algorithms). 

Po skonfigurowaniu metody regresji należy przeprowadzić uczenie modelu przy użyciu zestawu danych zawierającego przykłady wartości, która ma zostać przewidywalna. Model przeszkolony może być następnie używany do prognozowania.

## <a name="more-about-poisson-regression"></a>Więcej informacji na temat regresji Poissona

Regresja Poissona jest specjalnym typem analizy regresji, która zwykle jest używana do zliczania modeli. Na przykład regresja Poissona byłaby przydatna w następujących scenariuszach:

- Modelowanie liczby chłodni skojarzonych z samolotami lotów

- Szacowanie liczby wywołań usługi w sytuacjach krytycznych podczas zdarzenia

- Projekcja liczby zapytań klientów po podwyższeniu poziomu

- Tworzenie tabel awaryjnych

Ze względu na to, że zmienna odpowiedzi ma rozkład Poissona, model wykonuje różne założenia dotyczące danych i jego rozkład prawdopodobieństwa, niż, mówi, co najmniej kwadratowe regresja. W związku z tym modele Poissona należy interpretować inaczej niż w przypadku innych modeli regresji.

## <a name="how-to-configure-poisson-regression"></a>Jak skonfigurować regresję Poissona

1. Dodaj moduł **regresji Poissona** do potoku w projektancie. Ten moduł można znaleźć w obszarze **algorytmy Machine Learning**w kategorii **regresja** .

2. Dodaj zestaw danych zawierający dane szkoleniowe o poprawnym typie. 

    Zalecamy użycie [normalizowania danych](normalize-data.md) do normalizacji wejściowego zestawu danych przed użyciem go do uczenia regresor.

3. W prawym okienku modułu **regresja Poissona** Określ, w jaki sposób ma być szkolony model, ustawiając opcję **tworzenia trybu Trainer** .  
  
    - **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować model, podaj określony zestaw wartości jako argumenty.
  
    - **Zakres parametrów**: Jeśli nie masz pewności co do najlepszych parametrów, Przeczyść parametry przy użyciu modułu [dostrajania parametrów modelu](tune-model-hyperparameters.md) . Trainer wykonuje iterację wielu określonych wartości, aby znaleźć optymalną konfigurację.
  
4. **Tolerancja optymalizacji**: wpisz wartość, która definiuje interwał tolerancji podczas optymalizacji. Im niższa wartość, tym wolniejsze i dokładniejsze dopasowanie.

5. **Waga uregulowania L1** i **waga rozważenia (L2**): wpisz wartości, które mają być używane dla uregulowania L1 i L2. *Uregulowanie* dodaje ograniczenia do algorytmu dotyczące aspektów modelu, które są niezależne od danych szkoleniowych. Uregulowanie jest często stosowane w celu uniknięcia zamontowania. 

    - Uregulowanie L1 jest przydatne, jeśli celem jest posiadanie modelu, który jest tak jak to możliwe.

        Uregulowanie L1 jest wykonywane przez odjęcie wagi L1 wektora wagi od wyrażenia straty, które próbuje zminimalizować. Norma L1 jest dobrym przybliżeniem do normy P0, która jest liczbą niezerowych współrzędnych.

    - Rozważenia L2 uniemożliwia zbyt dużą współrzędną w wektorze wagi. Uregulowanie L2 jest przydatne, jeśli celem jest posiadanie modelu z niewielkimi ogólnymi wagami.

    W tym module można zastosować kombinację regulacji L1 i L2. Łącząc rozłożenie L1 i L2, można nałożyć karę na wielkość wartości parametrów. Dowiedzenie próbuje zminimalizować karę, w wyniku zminimalizowania straty.

    Aby zapoznać się z dobrą dyskusją dotyczącej uregulowania L1 i L2, zobacz temat [uregulowanie L1 i L2 dla Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx).

6. **Rozmiar pamięci dla L-BFGS**: Określ ilość pamięci do zarezerwowania na potrzeby dopasowywania i optymalizacji modelu.

     L-BFGS to określona metoda optymalizacji oparta na algorytmie Broyden – Fletcher – Goldfarb – Shanno (BFGS). Metoda używa ograniczonej ilości pamięci (L) do obliczenia następnego etapu.

     Zmieniając ten parametr, można wpływać na liczbę wcześniejszych pozycji i gradientów, które są przechowywane do obliczeń następnego kroku.

7. Połącz zestaw danych szkoleniowych i nieszkolony model z jednym z modułów szkoleniowych: 

    - W przypadku ustawienia opcji **Utwórz tryb Trainer** na **pojedynczy parametr**Użyj modułu [uczenie modelu](train-model.md) .

    - W przypadku ustawienia opcji **Utwórz tryb Trainer** na **zakres parametrów**należy użyć modułu [dostrajania parametrów modelu](tune-model-hyperparameters.md) .

    > [!WARNING]
    > 
    > - Jeśli przejdziesz do zakresu parametrów w celu [nauczenia modelu](train-model.md), zostanie użyta tylko pierwsza wartość z listy zakres parametrów.
    > 
    > - W przypadku przekazania jednego zestawu wartości parametrów do modułu [strojenia modelu](tune-model-hyperparameters.md) , gdy oczekuje on zakresu ustawień dla każdego parametru, zignoruje wartości i użyje wartości domyślnych dla tego obiektu.
    > 
    > - Jeśli wybierzesz opcję **zakres parametrów** i wprowadzisz pojedynczą wartość dla każdego parametru, ta pojedyncza wartość zostanie użyta w całym wyczyszczeniu, nawet jeśli inne parametry zmienią się w zakres wartości.

8.  Prześlij potok.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby zapisać migawkę przeszkolonego modelu, wybierz moduł szkoleń, a następnie przejdź do karty Output **+ Logs** na prawym panelu. Kliknij ikonę **zarejestruj zestaw danych**.  Zapisany model można znaleźć jako moduł w drzewie modułów. 

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
