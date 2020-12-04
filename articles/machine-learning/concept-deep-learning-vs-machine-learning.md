---
title: Uczenie głębokie a uczenie maszynowe
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak głębokie uczenie odnosi się do uczenia maszynowego i AI. W Azure Machine Learning używaj modeli uczenia głębokiego do wykrywania oszustw, wykrywania obiektów i nie tylko.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 09/22/2020
ms.custom: contperfq1
ms.openlocfilehash: c612e2a95e2596b2732ed8282037c8ded908a0a0
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571389"
---
# <a name="deep-learning-vs-machine-learning-in-azure-machine-learning"></a>Uczenie głębokie a uczenie maszynowe w Azure Machine Learning

W tym artykule opisano uczenie głębokie i uczenie maszynowe oraz ich dopasowanie do szerszej kategorii sztucznej analizy. Dowiedz się więcej na temat rozwiązań do uczenia głębokiego, które możesz kompilować na Azure Machine Learning, takich jak wykrywanie oszustw, rozpoznawanie głosu i twarzy, analiza tonacji i prognozowanie szeregów czasowych.

Aby uzyskać wskazówki dotyczące wybierania algorytmów dla rozwiązań, zobacz [arkusz Machine Learning Algorithm Ściągawka](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri).

## <a name="deep-learning-machine-learning-and-ai"></a>Uczenie głębokie, uczenie maszynowe i AI

![Diagram relacji: AI a uczenie maszynowe a uczenie głębokie](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Rozważ następujące definicje, aby zrozumieć głębokie uczenie i uczenie maszynowe a AI:

- **Uczenie głębokie** to podzestaw uczenia maszynowego, który jest oparty na sztucznych sieciach neuronowych. _Proces uczenia_ jest _głęboki_ , ponieważ struktura sztucznych sieci neuronowych składa się z wielu warstw danych wejściowych, wyjściowych i ukrytych. Każda warstwa zawiera jednostki, które przekształcają dane wejściowe w informacje, których następne warstwy mogą używać do wykonania pewnego zadania predykcyjnego. Dzięki tej strukturze komputer może poznać swoje własne przetwarzanie danych.

- **Uczenie maszynowe** to podzbiór sztucznej analizy wykorzystującej techniki (takie jak uczenie głębokie), które umożliwiają maszynom używanie środowiska w celu usprawnienia zadań. _Proces uczenia_ jest oparty na następujących krokach:

   1. Źródło danych do algorytmu. (W tym kroku można podać dodatkowe informacje do modelu, na przykład przez wykonanie wyodrębniania funkcji).
   1. Użyj tych danych do uczenia modelu.
   1. Przetestuj i Wdróż model.
   1. Korzystaj ze wdrożonego modelu, aby wykonać zautomatyzowane zadanie predykcyjne. (Innymi słowy, wywołaj i użyj wdrożonego modelu, aby otrzymać przewidywania zwracane przez model).

- **Sztuczna inteligencja (AI)** to technika, która umożliwia komputerom naśladowanie analizy ludzkiej. Obejmuje to uczenie maszynowe. 
 
Korzystając z technik uczenia maszynowego i uczenia głębokiego, można tworzyć systemy komputerowe i aplikacje, które wykonują zadania, które są często powiązane z inteligencją ludzką. Te zadania obejmują rozpoznawanie obrazu, rozpoznawanie mowy i tłumaczenie języka.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Techniki uczenia głębokiego a uczenie maszynowe 

Teraz, gdy masz Omówienie uczenia maszynowego i uczenia głębokiego, porównujemy dwie techniki. W usłudze Machine Learning algorytm musi być powiadamiany o tym, jak wprowadzić dokładne przewidywania, wykorzystując więcej informacji (na przykład przez przeprowadzenie wyodrębniania funkcji). W ramach uczenia głębokiego algorytm może dowiedzieć się, jak przeprowadzić dokładne prognozowanie za pomocą własnego przetwarzania danych, dzięki strukturze sieci sztucznej neuronowych.

Poniższa tabela zawiera porównanie dwóch technik w bardziej szczegółowy sposób:

| |Wszystkie Uczenie maszynowe |Tylko uczenie głębokie|
|---|---|---|
|  **Liczba punktów danych** | Można używać małych ilości danych do prognozowania. | Do prognozowania należy używać dużej ilości danych szkoleniowych. |
|  **Zależności sprzętowe** | Może współpracować z maszynami z niską końcówką. Nie wymaga to dużej ilości mocy obliczeniowej. | Zależy od maszyn wysokiej klasy. Jest to z założenia wiele operacji mnożenia macierzy. Procesor GPU może efektywnie zoptymalizować te operacje. |
|  **Proces cechowania** | Wymaga, aby funkcje były dokładnie identyfikowane i tworzone przez użytkowników. | Poznaj funkcje wysokiego poziomu z danych i tworzenie nowych funkcji. |
|  **Podejście szkoleniowe** | Dzieli proces uczenia na mniejsze kroki. Następnie łączy wyniki każdego kroku w jedno wyjście. | Przechodzi przez proces uczenia, rozwiązując problem na całej trasie. |
|  **Czas wykonywania** | Trwa znacznie nieco więcej czasu na wyuczenie, w zakresie od kilku sekund do kilku godzin. | Szkolenie zwykle trwa długo, ponieważ algorytm uczenia głębokiego obejmuje wiele warstw. |
|  **Dane wyjściowe** | Dane wyjściowe są zwykle wartościami liczbowymi, takimi jak wynik lub Klasyfikacja. | Dane wyjściowe mogą mieć wiele formatów, takich jak tekst, wynik lub dźwięk. |

## <a name="deep-learning-use-cases"></a>Przypadki użycia głębokiego uczenia

Ze względu na sztuczną strukturę sieci neuronowych, głębokie uczenie programu Excel na identyfikowaniu wzorców w danych bez struktury, takich jak obrazy, dźwięk, wideo i tekst. Z tego powodu Szczegółowa nauka szybko przekształca wiele branż, takich jak opieka zdrowotna, energia, finanse i transport. Te branże teraz uznają tradycyjne procesy biznesowe. 

Niektóre z najpopularniejszych aplikacji do uczenia głębokiego są opisane w poniższych akapitach. W Azure Machine Learning można użyć modelu z kompilacji z platformy typu "open source" lub skompilować model przy użyciu dostępnych narzędzi.

### <a name="named-entity-recognition"></a>Rozpoznawanie jednostek nazwanych

Rozpoznawanie jednostek nazwanych to metoda uczenia głębokiego, która przyjmuje fragment tekstu jako dane wejściowe i przekształca je w wstępnie określoną klasę. Nowe informacje mogą być kodem pocztowym, datą i IDENTYFIKATORem produktu. Informacje mogą być następnie przechowywane w schemacie strukturalnym, aby utworzyć listę adresów lub służyć jako wzorzec dla aparatu weryfikacji tożsamości.

### <a name="object-detection"></a>Wykrywanie obiektów

Uczenie głębokie zostało zastosowane w wielu przypadkach użycia wykrywania obiektów. Wykrywanie obiektów obejmuje dwie części: klasyfikację obrazów, a następnie lokalizację obrazu. _Klasyfikacja_ obrazu identyfikuje obiekty obrazu, takie jak samochody lub osoby. Lokalizacja _obrazu zapewnia_ określoną lokalizację tych obiektów. 

Wykrywanie obiektów jest już używane w branżach, takich jak gry, handel detaliczny, turystyka i samoobsługowe samochody.

Dowiedz się, jak używać modelu klasyfikacji obrazów z platformy typu "open source" w Azure Machine Learning: [klasyfikowanie obrazów przy użyciu modelu Pytorch](./how-to-train-pytorch.md?WT.mc_id=docs-article-lazzeri)

### <a name="image-caption-generation"></a>Generowanie podpisu obrazu

Podobnie jak w przypadku rozpoznawania obrazów, w przypadku obrazków obrazów dla danego obrazu system musi wygenerować podpis opisujący zawartość obrazu. Aby wykryć i oznaczyć obiekty na fotografiach, następnym krokiem jest przekształcenie tych etykiet w zdania opisowe. 

Zwykle aplikacje do podpisania obrazów używają splotowych neuronowych Networks do identyfikowania obiektów w obrazie, a następnie używania reaktualnej sieci neuronowych do przekształcania etykiet w spójne zdania.

### <a name="machine-translation"></a>Tłumaczenie maszynowe

Tłumaczenie maszynowe przyjmuje słowa lub zdania z jednego języka i automatycznie tłumaczy je na inny język. Tłumaczenie maszynowe było przez długi czas, ale głębokie uczenie realizuje imponujące wyniki w dwóch określonych obszarach: automatyczne tłumaczenie tekstu (oraz Tłumaczenie mowy na tekst) i automatyczne tłumaczenie obrazów.

Dzięki odpowiedniej transformacji danych sieć neuronowych może zrozumieć sygnały tekstowe, dźwiękowe i wizualne. Tłumaczenie maszynowe może służyć do identyfikowania fragmentów dźwięku w większych plikach audio i transkrypcja wymawianego wyrazu lub obrazu jako tekstu.

### <a name="text-analytics"></a>Analiza tekstu

Analiza tekstu oparta na metodach uczenia głębokiego polega na analizowaniu dużych ilości danych tekstowych (na przykład dokumentów medycznych lub przyjęć wydatków), rozpoznawaniu wzorców oraz tworzeniu zorganizowanych i zwięzłych informacji.

Firmy wykorzystują głębokie uczenie do przeprowadzania analizy tekstu w celu wykrywania handlu niejawnego i zgodności z przepisami obowiązującymi w instytucji rządowych. Innym typowym przykładem jest oszustwo ubezpieczeniowe: Analiza tekstu często została użyta do przeanalizowania dużych ilości dokumentów w celu rozpoznania szans oszustw związanych z ubezpieczeniem. 

## <a name="artificial-neural-networks"></a>Sztuczne sieci neuronowych

Sztuczne sieci neuronowych są tworzone przez warstwy połączonych węzłów. Modele uczenia głębokiego używają sieci neuronowych, które mają dużą liczbę warstw. 

W poniższych sekcjach opisano najpopularniejsze Popularne sieci sztucznej neuronowych Network typologies.

### <a name="feedforward-neural-network"></a>Sieć feedforward neuronowych

Sieć feedforward neuronowych jest najbardziej prostym typem sieci sztucznej neuronowych. W sieci feedforward informacje są przenoszone tylko jeden kierunek z warstwy wejściowej do warstwy wyjściowej. Feedforward sieci neuronowych przekształcają dane wejściowe, umieszczając je w szeregu ukrytych warstw. Każda warstwa składa się z zestawu neurons, a każda warstwa jest w pełni podłączona do wszystkich neurons w warstwie. Ostatnia w pełni łączona warstwa (warstwa wyjściowa) reprezentuje wygenerowane prognozy.

### <a name="recurrent-neural-network"></a>Rebieżąca sieć neuronowych

Recurrent neuronowych Networks to szeroko wykorzystywana sieć sztuczna neuronowych. Te sieci zapisują dane wyjściowe warstwy i odsyłają ją z powrotem do warstwy wejściowej w celu ułatwienia przewidywania wyniku warstwy. Neuronowych sieci mają doskonałe możliwości uczenia się. Są one szeroko stosowane do złożonych zadań, takich jak prognozowanie szeregów czasowych, uczenie ręczne i rozpoznawanie języka.

### <a name="convolutional-neural-networks"></a>Splotowych neuronowych sieci

Sieć splotowych neuronowych to szczególnie skuteczna sztuczna sieć neuronowych, która przedstawia unikatową architekturę. Warstwy są zorganizowane w trzy wymiary: Szerokość, Wysokość i głębokość. Neurons w jednej warstwie nie łączy się ze wszystkimi neurons w następnej warstwie, ale tylko do małego regionu neurons warstwy. Końcowe dane wyjściowe są skracane do jednego wektora wyników prawdopodobieństwa, zorganizowane wzdłuż wymiaru głębokości. 

Sieci neuronowych splotowych są używane w takich obszarach jak rozpoznawanie wideo, rozpoznawanie obrazów i systemy zalecające.

## <a name="next-steps"></a>Następne kroki

W poniższych artykułach przedstawiono więcej opcji korzystania z modeli uczenia głębokiego "open source" w [Azure Machine Learning](./index.yml?WT.mc_id=docs-article-lazzeri):


- [Klasyfikowanie odręcznych cyfr przy użyciu modelu TensorFlow](./how-to-train-tensorflow.md?WT.mc_id=docs-article-lazzeri) 

- [Klasyfikowanie odręcznych cyfr przy użyciu TensorFlow szacowania i Keras](./how-to-train-keras.md?WT.mc_id=docs-article-lazzeri)

- [Klasyfikowanie odręcznych cyfr przy użyciu modelu łańcucha](./how-to-set-up-training-targets.md)
