---
title: 'ML Studio (klasyczny): Optymalizacja algorytmów — Azure'
description: Wyjaśnia, jak wybrać optymalny zestaw parametrów dla algorytmu w Azure Machine Learning Studio (klasyczny).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 0ed958d24a7ff499c8cf33974648afb0f4e0a81d
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91367992"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-machine-learning-studio-classic"></a>Wybierz parametry, aby zoptymalizować algorytmy w Machine Learning Studio (klasyczny)

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  

W tym temacie opisano, jak wybrać prawy parametr ustawiony dla algorytmu w Azure Machine Learning Studio (klasyczny). Większość algorytmów uczenia maszynowego ma parametry do ustawienia. Podczas uczenia modelu należy podać wartości tych parametrów. Skuteczność przeszkolonego modelu zależy od wybranych parametrów modelu. Proces znajdowania optymalnego zestawu parametrów jest znany jako *wybór modelu*.



Istnieją różne sposoby wyboru modelu. W uczeniu maszynowym, krzyżowe sprawdzanie poprawności jest jednym z najczęściej używanych metod wyboru modelu i jest domyślnym mechanizmem wyboru modelu w Azure Machine Learning Studio (klasyczny). Ponieważ Azure Machine Learning Studio (klasyczny) obsługuje zarówno język R, jak i Python, zawsze można zaimplementować własne mechanizmy wyboru modelu przy użyciu języka R lub Python.

W procesie znajdowania najlepszego zestawu parametrów istnieją cztery etapy:

1. **Zdefiniuj miejsce parametru**: dla algorytmu należy najpierw określić dokładne wartości parametrów, które mają być brane pod uwagę.
2. **Zdefiniuj ustawienia dotyczące krzyżowego sprawdzania poprawności**: Zdecyduj, jak wybierać zgięcia krzyżowe dla zestawu danych.
3. **Zdefiniuj metrykę**: Zdecyduj, która Metryka ma być używana do określania najlepszego zestawu parametrów, takich jak dokładność, główny znak średnika, precyzja, odwołanie lub f-Score.
4. **Uczenie, szacowanie i porównywanie**: dla każdej unikatowej kombinacji wartości parametrów, wzajemne sprawdzanie poprawności jest wykonywane w oparciu o określoną metrykę błędu. Po dokonaniu oceny i porównaniu można wybrać model najlepiej wykonujący.

Na poniższej ilustracji przedstawiono, jak można to osiągnąć w Azure Machine Learning Studio (klasyczny).

![Znajdź najlepszy zestaw parametrów](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Zdefiniuj miejsce parametru
Można zdefiniować parametr ustawiony w kroku inicjalizacji modelu. Okienko parametrów wszystkich algorytmów uczenia maszynowego ma dwa tryby Trainer: *pojedynczy parametr* i *zakres parametrów*. Wybierz tryb zakresu parametrów. W trybie zakresu parametrów można wprowadzić wiele wartości dla każdego parametru. W polu tekstowym można wprowadzić wartości rozdzielone przecinkami.

![Drzewo decyzyjne z dwoma klasami, jeden parametr](./media/algorithm-parameters-optimize/fig2.png)

 Alternatywnie można zdefiniować maksymalne i minimalne punkty siatki oraz całkowitą liczbę punktów do wygenerowania przy użyciu **konstruktora zakresu użycia**. Domyślnie wartości parametrów są generowane na skali liniowej. Jeśli jednak **skalowanie dziennika** jest zaznaczone, wartości są generowane w skali dziennika (to oznacza, że proporcja sąsiadujących punktów jest stała, a nie różnica). Dla parametrów Integer można zdefiniować zakres przy użyciu łącznika. Na przykład "1-10" oznacza, że wszystkie liczby całkowite z zakresu od 1 do 10 (włącznie) tworzą zestaw parametrów. Obsługiwany jest również tryb mieszany. Na przykład, zestaw parametrów "1-10, 20, 50" będzie zawierać liczby całkowite 1-10, 20 i 50.

![Drzewo decyzyjne z dwoma klasami, zakres parametrów](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definiowanie zgięcia między walidacjami
Na [partycji i przykładowym][partition-and-sample] module można używać do losowego przypisywania zapisywanych danych. W poniższej przykładowej konfiguracji modułu definiujemy pięć zagięć i losowo przypisujemy numer zgięcia do przykładowych wystąpień.

![Partycja i przykład](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definiowanie metryki
Moduł " [dostrajanie modelu][tune-model-hyperparameters] " pozwala obsłużyć doświadczalnie Wybieranie najlepszego zestawu parametrów dla danego algorytmu i zestawu danych. Oprócz innych informacji dotyczących uczenia modelu, okienko **Właściwości** tego modułu zawiera metrykę służącą do określenia najlepszego zestawu parametrów. Ma dwa różne pola listy rozwijanej odpowiednio dla algorytmów klasyfikacji i regresji. Jeśli algorytm rozważany jest algorytmem klasyfikacji, Metryka regresji jest ignorowana i odwrotnie. W tym konkretnym przykładzie Metryka jest **dokładnością**.   

![Parametry odchylenia](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Uczenie, ocenę i porównanie
Ten sam moduł [strojenia modelu][tune-model-hyperparameters] pociąga za siebie wszystkie modele, które odpowiadają zestawowi parametrów, ocenia różne metryki, a następnie tworzy model najlepiej szkolony na podstawie wybranej metryki. Ten moduł ma dwa obowiązkowe dane wejściowe:

* Pouczenie się
* Zestaw danych

Moduł zawiera również opcjonalne dane wejściowe zestawu danych. Połącz zestaw danych z danymi o złożeniu do obowiązkowego wejścia zestawu danych. Jeśli zestaw danych nie ma przypisanych żadnych informacji o złożeniu, domyślnie zostanie automatycznie wykonane sprawdzanie krzyżowe. Jeśli przypisanie do złożenia nie zostanie wykonane i zestaw danych sprawdzania poprawności jest dostarczany z opcjonalnym portem DataSet, wybierany jest tryb uczenia testowego, a pierwszy zestaw danych jest używany do uczenia modelu dla każdej kombinacji parametrów.

![Klasyfikator drzewa podwyższanych decyzji](./media/algorithm-parameters-optimize/fig6a.png)

Model jest następnie oceniany w zestawie danych walidacji. Lewy port wyjściowy modułu pokazuje różne metryki jako funkcje wartości parametrów. Prawidłowy port wyjściowy zapewnia szkolony model, który odpowiada modelowi najlepiej działającemu zgodnie z wybraną metryką (**dokładność** w tym przypadku).  

![Zestaw danych walidacji](./media/algorithm-parameters-optimize/fig6b.png)

Można zobaczyć dokładne parametry wybrane przez wizualizację właściwego portu wyjściowego. Ten model może być używany podczas oceniania zestawu testów lub w operacyjnej usłudze sieci Web po zapisaniu jako przeszkolonego modelu.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
