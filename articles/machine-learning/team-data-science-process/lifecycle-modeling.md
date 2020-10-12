---
title: Modelowanie etapów cyklu życia zespołowego procesu nauki danych
description: Cele, zadania i elementy dostarczane dla etapu modelowania projektów analizy danych
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
ms.openlocfilehash: c3406b28c44644053fa984f2cf7739bb53ccf6c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336449"
---
# <a name="modeling-stage-of-the-team-data-science-process-lifecycle"></a>Modelowanie etapów cyklu życia zespołowego procesu nauki danych

W tym artykule opisano cele, zadania i elementy dostarczane powiązane z etapem modelowania procesu nauki o danych zespołowych (przetwarzania TDSP). Ten proces zapewnia Zalecany cykl życia, którego można użyć do struktury projektów analizy danych. Cykl życia przedstawia główne etapy, które są zwykle wykonywane przez projekty, często iteracyjnie:

   1. **Poznawanie firmy**
   2. **Pozyskiwanie danych i ich analiza**
   3. **Modelowanie**
   4. **Wdrożenie**
   5. **Akceptacja klienta**

Oto wizualna reprezentacja cyklu życia przetwarzania TDSP:

![Cykl życia przetwarzania TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cele
* Określ optymalne funkcje danych dla modelu uczenia maszynowego.
* Utwórz model uczenia maszynowego z największą dokładnością.
* Utwórz model uczenia maszynowego, który jest odpowiedni dla środowiska produkcyjnego.

## <a name="how-to-do-it"></a>Jak to zrobić
W tym etapie istnieją trzy główne zadania:

  * **Inżynieria funkcji**: Tworzenie funkcji danych z danych pierwotnych w celu ułatwienia szkolenia modeli.
  * **Uczenie modeli**: Znajdź model, który odbierze najbardziej dokładne wyniki, porównując ich metryki sukcesu.
  * Ustal, czy model jest **odpowiedni dla środowiska produkcyjnego.**

### <a name="feature-engineering"></a>Inżynieria cech
Inżynieria funkcji obejmuje dołączenie, agregację i transformację zmiennych nieprzetworzonych w celu utworzenia funkcji używanych w analizie. Jeśli chcesz uzyskać wgląd w to, co ma wpływ na model, musisz zrozumieć, w jaki sposób funkcje są ze sobą powiązane, i jak algorytmy uczenia maszynowego mają korzystać z tych funkcji. 

Ten krok wymaga twórczej kombinacji wiedzy o domenie i szczegółowych informacji uzyskanych z kroku eksploracji danych. Inżynieria funkcji to funkcja równoważenia obciążenia, która umożliwia znajdowanie i uwzględnianie zmiennych, ale jednocześnie próbuje uniknąć zbyt wielu niepowiązanych zmiennych. Zmienne informacyjne ulepszają wynik; zmienne niepowiązane wprowadzają niepotrzebny szum do modelu. Należy również generować te funkcje dla nowych danych uzyskanych podczas oceniania. W związku z tym generowanie tych funkcji może być zależne tylko od danych, które są dostępne w momencie oceniania. 

Aby uzyskać wskazówki techniczne dotyczące inżynierii funkcjonalnej w przypadku korzystania z różnych technologii danych platformy Azure, zobacz [Inżynieria funkcji w procesie analizy danych](create-features.md). 

### <a name="model-training"></a>Trenowanie modelu
W zależności od typu pytania, które próbujesz odpowiedzieć, dostępnych jest wiele algorytmów modelowania. Aby uzyskać wskazówki dotyczące wybierania algorytmów, zobacz [jak wybrać algorytmy dla Microsoft Azure Machine Learning](../how-to-select-algorithms.md). Chociaż w tym artykule są używane Azure Machine Learning, wskazówki, które zapewnia, są przydatne w przypadku wszystkich projektów uczenia maszynowego. 

Proces szkolenia modelu obejmuje następujące kroki: 

   * **Dziel losowo dane wejściowe** do modelowania na zestaw danych szkoleniowych i zestaw danych testowych.
   * **Kompiluj modele** przy użyciu zestawu danych szkoleniowych.
   * **Oceń** szkolenia i zestaw danych testowych. Używaj szeregu konkurencyjnych algorytmów uczenia maszynowego wraz z różnymi, skojarzonymi parametrami strojenia (znanym jako *odchylenia parametrów*), które są skierowane do odpowiedzi na pytania związane z bieżącymi danymi.
   * **Określ najlepsze rozwiązanie** , aby odpowiedzieć na pytanie, porównując metryki sukcesu między metodami alternatywnymi.

> [!NOTE]
> **Unikaj wycieku**: można spowodować wyciek danych, jeśli dołączysz dane spoza zestawu danych szkoleniowych, które umożliwiają modelowi lub algorytmowi uczenia maszynowego wykonywanie nierealistycznie dobrych prognoz. Wyciek jest typowym powodem, dlaczego analityki danych uzyskują nerwy, gdy uzyskują wyniki predykcyjne, które wydaje się zbyt dobre. Wykrywanie tych zależności może być trudne. Aby uniknąć wycieków często wymaga iteracji między kompilowaniem zestawu danych analizy, tworzeniem modelu i ocenianiem dokładności wyników. 
> 
> 

Udostępniamy [zautomatyzowane narzędzie do modelowania i raportowania](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) z przetwarzania TDSP, które jest możliwe do uruchomienia przez wiele algorytmów i odchylenia parametrów w celu utworzenia modelu linii bazowej. Tworzy również podstawowy raport modelowania, który podsumowuje wydajność poszczególnych modeli i kombinacji parametrów, w tym znaczenie zmienne. Ten proces jest również iteracyjny, ponieważ może to ułatwić inżynierowanie funkcji. 

## <a name="artifacts"></a>Artifacts
Artefakty utworzone w tym etapie obejmują:

   * [Zestawy funkcji](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md): funkcje opracowane dla modelowania są opisane w sekcji **zestawy funkcji** w raporcie **definicji danych** . Zawiera wskaźniki do kodu w celu wygenerowania funkcji oraz opis sposobu generowania funkcji.
   * [Raport dotyczący modelu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): dla każdego z modeli, który został wypróbowany, standardowy, oparty na szablonach raport, który zawiera szczegółowe informacje dotyczące każdego eksperymentu.
   * **Decyzja dotycząca punktów kontrolnych**: Oceń, czy model jest wystarczająco wydajny dla środowiska produkcyjnego. Oto kluczowe pytania, które należy zadać:
     * Czy model odpowiada na pytanie z wystarczającym zaufaniem do danych testowych? 
     * Czy chcesz próbować dowolnych alternatywnych metod? Czy należy zbierać dodatkowe dane, wykonywać więcej funkcji, czy eksperymentować z innymi algorytmami?

## <a name="next-steps"></a>Następne kroki

Poniżej przedstawiono linki do poszczególnych etapów cyklu życia przetwarzania TDSP:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie danych i ich analiza](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Zapewniamy kompleksowe przewodniki przedstawiające wszystkie kroki procesu dla konkretnych scenariuszy. [Przykładowy artykuł instruktażowy](walkthroughs.md) zawiera listę scenariuszy z linkami i opisami miniatur. Instruktaże ilustrują sposób łączenia chmur, narzędzi lokalnych i usług do przepływu pracy lub potoku w celu utworzenia inteligentnej aplikacji. 

Aby zapoznać się z przykładami wykonywania kroków w TDSPs, które używają Azure Machine Learning Studio, zobacz [Korzystanie z przetwarzania TDSP z Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/). 
