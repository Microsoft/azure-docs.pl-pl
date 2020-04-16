---
title: Uczenie głębokie a uczenie maszynowe
titleSuffix: Azure
description: Dowiedz się, jak uczenie głębokie odnosi się do uczenia maszynowego i sztucznej inteligencji. Uczenie głębokie jest używane w scenariuszach, takich jak wykrywanie oszustw, rozpoznawanie głosu & twarzy, analiza opinii i prognozowanie szeregów czasowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 03/05/2020
ms.openlocfilehash: b024010583ba1c6e0ffdf663f7335011ce212bf1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414580"
---
# <a name="deep-learning-vs-machine-learning"></a>Uczenie głębokie a uczenie maszynowe

Ten artykuł ułatwia porównywanie uczenia głębokiego a uczenia maszynowego. Dowiesz się, jak te dwie koncepcje porównują się i jak wpisują się w szerszą kategorię sztucznej inteligencji. W artykule opisano również, w jaki sposób głębokie uczenie można zastosować do rzeczywistych scenariuszy, takich jak wykrywanie oszustw, rozpoznawanie głosu i twarzy, analiza opinii i prognozowanie szeregów czasowych.

## <a name="deep-learning-machine-learning-and-ai"></a>Uczenie głębokie, uczenie maszynowe i ai

![Diagram relacji: AI vs. uczenie maszynowe a uczenie głębokie](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Należy wziąć pod uwagę następujące definicje, aby zrozumieć uczenia głębokiego vs uczenia maszynowego vs AI:

- **Uczenie głębokie** jest podzbiorem uczenia maszynowego opartego na sztucznych sieciach neuronowych. _Proces uczenia się_ jest _głęboki,_ ponieważ struktura sztucznych sieci neuronowych składa się z wielu wejść, danych wyjściowych i ukrytych warstw. Każda warstwa zawiera jednostki, które przekształcają dane wejściowe w informacje, których następna warstwa może być używana dla określonego zadania predykcyjnego. Dzięki tej strukturze maszyna może uczyć się poprzez własne przetwarzanie danych.

- **Uczenie maszynowe** to podzbiór sztucznej inteligencji, który wykorzystuje techniki (takie jak uczenie głębokie), które umożliwiają maszynom korzystanie z doświadczenia w celu poprawy zadań. _Proces uczenia się_ opiera się na następujących krokach:

   1. Przesyłaj dane do algorytmu. (W tym kroku można podać dodatkowe informacje do modelu, na przykład, wykonując wyodrębnianie operacji).)
   1. Użyj tych danych, aby trenować model.
   1. Przetestuj i wdrażaj model.
   1. Użyj wdrożonego modelu, aby wykonać zautomatyzowane zadanie predykcyjne. (Innymi słowy wywołać i używać wdrożonego modelu, aby odbierać prognozy zwracane przez model.)

- **Sztuczna inteligencja (AI)** to technika, która umożliwia komputerom naśladowanie ludzkiej inteligencji. Obejmuje uczenie maszynowe. 
 
Ważne jest, aby zrozumieć związek między SI, uczeniem maszynowym i uczeniem głębokim. Uczenie maszynowe to sposób na osiągnięcie sztucznej inteligencji. Za pomocą uczenia maszynowego i technik uczenia głębokiego można tworzyć systemy komputerowe i aplikacje, które wykonują zadania, które są często skojarzone z ludzką inteligencją. Zadania te obejmują rozpoznawanie obrazów, rozpoznawanie mowy i tłumaczenie języka.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Techniki uczenia głębokiego a uczenie maszynowe 

Teraz, gdy masz przegląd uczenia maszynowego vs uczenia głębokiego, porównajmy te dwie techniki. W uczeniu maszynowym algorytm musi być poinformowany, jak dokonać dokładnego przewidywania przez użycie większej ilości informacji (na przykład przez wykonanie wyodrębniania funkcji). W uczeniu głębokim algorytm może nauczyć się, jak dokonać dokładnego przewidywania poprzez własne przetwarzanie danych, dzięki sztucznej strukturze sieci neuronowej.

W poniższej tabeli porównano te dwie techniki bardziej szczegółowo:

| |Całe uczenie maszynowe |Tylko głębokie uczenie się|
|---|---|---|
|  **Liczba punktów danych** | Można użyć małych ilości danych do prognozowania. | Musi używać dużych ilości danych szkoleniowych do prognozowania. |
|  **Zależności sprzętowe** | Może pracować na maszynach low-end. Nie potrzebuje dużej mocy obliczeniowej. | Zależy od maszyn wysokiej klasy. Z natury wykonuje dużą liczbę operacji mnożenia macierzy. Procesor GPU może skutecznie optymalizować te operacje. |
|  **Proces featurization** | Wymaga, aby funkcje były dokładnie identyfikowane i tworzone przez użytkowników. | Uczy się funkcji wysokiego poziomu z danych i tworzy nowe funkcje samodzielnie. |
|  **Podejście do uczenia się** | Dzieli proces uczenia się na mniejsze kroki. Następnie łączy wyniki z każdego kroku w jeden wynik. | Przechodzi przez proces uczenia się, rozwiązując problem na zasadzie end-to-end. |
|  **Czas wykonywania** | Trwa stosunkowo mało czasu na trening, począwszy od kilku sekund do kilku godzin. | Zwykle zajmuje dużo czasu, ponieważ algorytm uczenia głębokiego obejmuje wiele warstw. |
|  **Dane wyjściowe** | Dane wyjściowe są zwykle wartością liczbową, taką jak wynik lub klasyfikacja. | Dane wyjściowe mogą mieć wiele formatów, takich jak tekst, wynik lub dźwięk. |

## <a name="deep-learning-use-cases"></a>Przypadki użycia uczenia głębokiego

Ze względu na sztuczną strukturę sieci neuronowej głębokie uczenie się wyróżnia się w identyfikowaniu wzorców w nieustrukturyzowanych danych, takich jak obrazy, dźwięk, wideo i tekst. Z tego powodu głębokie uczenie szybko przekształca wiele branż, w tym opiekę zdrowotną, energetykę, finanse i transport. Branże te rozważają obecnie tradycyjne procesy biznesowe. 

Niektóre z najczęstszych aplikacji do uczenia głębokiego są opisane w poniższych akapitach.

### <a name="named-entity-recognition"></a>Rozpoznawanie nazwanych jednostek

Rozpoznawanie nazwanych jednostek jest metodą uczenia głębokiego, która przyjmuje fragment tekstu jako dane wejściowe i przekształca go w wstępnie określoną klasę. Te nowe informacje mogą być kod pocztowy, data, identyfikator produktu. Informacje mogą być następnie przechowywane w schemacie strukturalnym do tworzenia listy adresów lub służyć jako punkt odniesienia dla aparatu sprawdzania poprawności tożsamości.

### <a name="object-detection"></a>Wykrywanie obiektów

Uczenie głębokie zostało zastosowane w wielu przypadkach użycia wykrywania obiektów. Wykrywanie obiektów składa się z dwóch części: klasyfikacji obrazu, a następnie lokalizacji obrazu. _Klasyfikacja obrazów_ identyfikuje obiekty obrazu, takie jak samochody lub osoby. _Lokalizacja obrazu_ zapewnia określoną lokalizację tych obiektów. 

Wykrywanie obiektów jest już stosowane w takich branżach, jak gry, handel detaliczny, turystyka i samochody autonomiczne.

### <a name="image-caption-generation"></a>Generowanie podpisu obrazu

Podobnie jak rozpoznawanie obrazów w podpisach obrazów dla danego obrazu, system musi wygenerować podpis opisujący zawartość obrazu. Gdy można wykrywać i oznaczać obiekty na zdjęciach, następnym krokiem jest przekształcenie tych etykiet w zdania opisowe. 

Zazwyczaj aplikacje do podpisów obrazów używają splotowych sieci neuronowych do identyfikowania obiektów w obrazie, a następnie używają powtarzającej się sieci neuronowej, aby przekształcić etykiety w spójne zdania.

### <a name="machine-translation"></a>Tłumaczenie maszynowe

Tłumaczenie maszynowe bierze słowa lub zdania z jednego języka i automatycznie tłumaczy je na inny język. Tłumaczenie maszynowe istnieje już od dawna, ale głębokie uczenie osiąga imponujące wyniki w dwóch konkretnych obszarach: automatyczne tłumaczenie tekstu (i tłumaczenie mowy na tekst) i automatyczne tłumaczenie obrazów.

Dzięki odpowiedniej transformacji danych sieć neuronowa może zrozumieć sygnały tekstowe, dźwiękowe i wizualne. Tłumaczenie maszynowe może służyć do identyfikowania fragmentów dźwięku w większych plikach audio i transkrybacji słowa mówionego lub obrazu jako tekstu.

### <a name="text-analytics"></a>Analiza tekstu

Analiza tekstu oparta na metodach uczenia głębokiego polega na analizie dużych ilości danych tekstowych (na przykład dokumentów medycznych lub pokwitowań wydatków), rozpoznawaniu wzorców i tworzeniu zorganizowanych i zwięzłych informacji z nich.

Firmy wykorzystują głębokie uczenie do przeprowadzania analizy tekstu w celu wykrycia wykorzystywania informacji poufnych i zgodności z przepisami rządowymi. Innym częstym przykładem jest oszustwo ubezpieczeniowe: analiza tekstu była często wykorzystywana do analizowania dużych ilości dokumentów w celu rozpoznania szans na oszustwo związane z roszczeniem ubezpieczeniowym. 

## <a name="artificial-neural-networks"></a>Sztuczne sieci neuronowe

Sztuczne sieci neuronowe są tworzone przez warstwy połączonych węzłów. Modele uczenia głębokiego używają sieci neuronowych, które mają dużą liczbę warstw. 

W poniższych sekcjach przeanalizowane są najpopularniejsze typologie sztucznej sieci neuronowej.

### <a name="feedforward-neural-network"></a>Feedforward sieci neuronowej

Sieć neuronowa feedforward jest najbardziej podstawowym rodzajem sztucznej sieci neuronowej. W sieci feedforward informacje są przenoszone tylko w jednym kierunku z warstwy wejściowej do warstwy wyjściowej. Feedforward sieci neuronowe przekształcić dane wejściowe poprzez umieszczenie go przez szereg ukrytych warstw. Każda warstwa składa się z zestawu neuronów, a każda warstwa jest w pełni połączona ze wszystkimi neuronami w warstwie wcześniej. Ostatnia w pełni połączona warstwa (warstwa wyjściowa) reprezentuje wygenerowane prognozy.

### <a name="recurrent-neural-network"></a>Nawracająca sieć neuronowa

Nawracające sieci neuronowe są szeroko stosowaną sztuczną siecią neuronową. Sieci te zapisują dane wyjściowe warstwy i zasilają ją z powrotem do warstwy wejściowej, aby pomóc w przewidywaniu wyników warstwy. Nawracające sieci neuronowe mają wielkie zdolności uczenia się. Są one szeroko stosowane do złożonych zadań, takich jak prognozowanie szeregów czasowych, nauka pisania odręcznego i rozpoznawanie języka.

### <a name="convolutional-neural-networks"></a>Splotowe sieci neuronowe

Splotowa sieć neuronowa jest szczególnie skuteczną sztuczną siecią neuronową i prezentuje unikalną architekturę. Warstwy są zorganizowane w trzech wymiarach: szerokość, wysokość i głębokość. Neurony w jednej warstwie łączą się nie ze wszystkimi neuronami w następnej warstwie, ale tylko z małym obszarem neuronów warstwy. Końcowe wyjście jest zredukowane do pojedynczego wektora wyników prawdopodobieństwa, zorganizowanego wzdłuż wymiaru głębokości. 

Splotowe sieci neuronowe zostały wykorzystane w obszarach takich jak rozpoznawanie wideo, rozpoznawanie obrazu i systemy rekomendatorskie.

## <a name="next-steps"></a>Następne kroki

Poniższe artykuły pokazują, jak korzystać z technologii uczenia głębokiego w [usłudze Azure Machine Learning:](https://docs.microsoft.com/azure/machine-learning/?WT.mc_id=docs-article-lazzeri)

- [Klasyfikowanie cyfr odręcznych przy użyciu modelu TensorFlow](https://docs.microsoft.com/azure/machine-learning/how-to-train-tensorflow?WT.mc_id=docs-article-lazzeri)

- [Klasyfikowanie cyfr odręcznych przy użyciu estymatora TensorFlow i Keras](https://docs.microsoft.com/azure/machine-learning/how-to-train-keras?WT.mc_id=docs-article-lazzeri)

- [Klasyfikowanie obrazów przy użyciu modelu Pytorch](https://docs.microsoft.com/azure/machine-learning/how-to-train-pytorch?WT.mc_id=docs-article-lazzeri)

- [Klasyfikowanie cyfr odręcznych przy użyciu modelu chainera](https://docs.microsoft.com/azure/machine-learning/how-to-train-ml-models)

Ponadto użyj [arkusza ściągawki algorytmu uczenia maszynowego,](algorithm-cheat-sheet.md) aby wybrać algorytmy dla swojego modelu.
