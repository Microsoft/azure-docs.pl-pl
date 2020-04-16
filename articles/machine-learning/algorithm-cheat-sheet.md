---
title: Machine Learning Algorithm Cheat Sheet - projektant
titleSuffix: Azure Machine Learning
description: Arkusz cheat sheet algorytmu uczenia maszynowego do druku pomaga wybrać odpowiedni algorytm dla modelu predykcyjnego w projektancie usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
ms.openlocfilehash: accc4766f25f5d58ba2f31c153fe1c5d23413170
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399080"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Arkusz cheatów algorytmu uczenia maszynowego dla projektanta usługi Azure Machine Learning

**Arkusz ściągawki algorytmu usługi Azure Machine Learning** pomaga wybrać odpowiedni algorytm od projektanta dla modelu analizy predykcyjnej.

Usługa Azure Machine Learning ma dużą bibliotekę algorytmów z ***rodzin klasyfikacji,*** ***systemów polecających,*** ***klastrowania,*** ***wykrywania anomalii,*** ***regresji***i ***analiz tekstu.*** Każdy z nich jest przeznaczony do rozwiązania problemu uczenia maszynowego innego typu.

Aby uzyskać dodatkowe wskazówki, zobacz [Jak wybrać algorytmy](how-to-select-algorithms.md)

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Pobierz: Arkusz algorytmów uczenia maszynowego

**Pobierz ściągawkę tutaj: [Machine Learning Algorytm Ściągawki (11x17 in.)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Arkusz cheatów algorytmu uczenia maszynowego: Dowiedz się, jak wybrać algorytm uczenia maszynowego.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Pobierz i wydrukuj arkusz kodu algorytmu uczenia maszynowego w rozmiarze tabloidu, aby był przydatny i uzyskać pomoc w wyborze algorytmu.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Jak korzystać z arkusza ściągawki algorytmu uczenia maszynowego

Sugestie oferowane w tym arkuszu ściągawki algorytmu są przybliżone zasady-of-thumb. Niektóre mogą być wygięte, a niektóre mogą być rażąco naruszone. Ta ściągawka ma sugerować punkt wyjścia. Nie bój się prowadzić head-to-head konkurencji między kilkoma algorytmami na danych. Po prostu nie ma substytutu dla zrozumienia zasad każdego algorytmu i systemu, który wygenerował twoje dane.

Każdy algorytm uczenia maszynowego ma swój własny styl lub indukcyjne odchylenie. W przypadku określonego problemu kilka algorytmów może być odpowiednie, a jeden algorytm może być lepiej dopasowany niż inne. Ale nie zawsze jest możliwe, aby wiedzieć wcześniej, który jest najlepszy pasuje. W takich przypadkach kilka algorytmów są wymienione razem w ściągawce. Odpowiednią strategią byłoby wypróbowanie jednego algorytmu, a jeśli wyniki nie są jeszcze zadowalające, spróbuj innych. 

Aby dowiedzieć się więcej o algorytmach w projektancie usługi Azure Machine Learning, przejdź do [odwołania algorytm i moduł](algorithm-module-reference/module-reference.md).

## <a name="kinds-of-machine-learning"></a>Rodzaje uczenia maszynowego

Istnieją trzy główne kategorie uczenia maszynowego: *uczenie nadzorowane,* *uczenie się bez nadzoru*i *uczenie wzmacniające.*

### <a name="supervised-learning"></a>Nadzorowana nauka

W uczeniu nadzorowanym każdy punkt danych jest oznaczony etykietą lub skojarzony z kategorią lub wartością zainteresowania. Przykładem etykiety kategorycznej jest przypisywanie obrazu jako "kota" lub "psa". Przykładem etykiety wartości jest cena sprzedaży skojarzona z używanym samochodem. Celem nadzorowanego uczenia się jest zbadanie wielu oznaczonych przykładów, takich jak te, a następnie możliwość przewidywania przyszłych punktów danych. Na przykład identyfikacja nowych zdjęć z właściwym zwierzęciem lub przypisanie dokładnych cen sprzedaży do innych używanych samochodów. Jest to popularny i przydatny typ uczenia maszynowego.

### <a name="unsupervised-learning"></a>Uczenie się bez nadzoru

W uczeniu nienadzorowanym punkty danych nie mają z nimi skojarzonych etykiet. Zamiast tego celem algorytmu uczenia nienadzorowanego jest zorganizowanie danych w jakiś sposób lub opisanie ich struktury. Nienadzorowane dane szkoleniowe grupuje dane w klastrach, jak k-oznacza, lub znajduje różne sposoby patrzenia na złożone dane, tak aby wydawało się prostsze.

### <a name="reinforcement-learning"></a>Uczenie przez wzmacnianie

W uczeniu wzmacniania algorytm pobiera wybrać akcję w odpowiedzi na każdy punkt danych. Jest to powszechne podejście w robotyce, gdzie zestaw odczytów czujników w jednym momencie jest punktem danych, a algorytm musi wybrać następną akcję robota. Jest to również naturalne dopasowanie do aplikacji Internetu rzeczy. Algorytm uczenia się otrzymuje również sygnał nagrody chwilę później, wskazując, jak dobra była decyzja. Na podstawie tego sygnału algorytm modyfikuje swoją strategię w celu osiągnięcia najwyższej nagrody. 

## <a name="next-steps"></a>Następne kroki

* Zobacz dodatkowe wskazówki [dotyczące wybierania algorytmów](how-to-select-algorithms.md)

* [Dowiedz się więcej o studio w usłudze Azure Machine Learning i witrynie Azure portal.](overview-what-is-azure-ml.md)

* [Samouczek: Tworzenie modelu przewidywania w projektancie usługi Azure Machine Learning.](tutorial-designer-automobile-price-train-score.md)

* [Dowiedz się więcej o uczeniu głębokim i uczeniu maszynowym](concept-deep-learning-vs-machine-learning.md).
