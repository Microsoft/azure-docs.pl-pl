---
title: Ocenianie i łagodzenie sprawiedliwych modeli uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o atrakcyjności modeli uczenia maszynowego oraz o sposobie, w jaki pakiet języka Python Fairlearn może pomóc w tworzeniu atrakcyjnych modeli.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/02/2020
ms.openlocfilehash: c21ec0329a7b5716a00262b7422296df3afe208b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598605"
---
# <a name="fairness-in-machine-learning-models"></a>Sprawiedliwe modele uczenia maszynowego

Dowiedz się więcej o atrakcyjności uczenia maszynowego oraz o sposobie, w jaki pakiet języka Python Fairlearn open-source może pomóc w tworzeniu bardziej uczciwych modeli.

## <a name="what-is-fairness-in-machine-learning-systems"></a>Co to jest sprawiedliwe działanie w systemach uczenia maszynowego?

Sztuczna inteligencja i systemy uczenia maszynowego mogą wyświetlać nieuczciwe zachowanie. Jednym ze sposobów zdefiniowania nieuczciwego zachowania jest ich szkody lub wpływ na ludzi. Istnieje wiele typów szkód, które mogą prowadzić systemy AI. Dwa popularne typy sztucznie spowodowały uszkodzenie:

- Szkoda alokacji: system AI rozszerza lub wstrzymuje szanse, zasoby lub informacje. Przykłady obejmują zatrudnienie, przyjmowanie szkolne i kredyty, w których model może być dużo lepszy przy wybieraniu dobrych kandydatów między konkretną grupą osób niż w innych grupach.

- Uszkodzenie jakości usługi: system AI nie działa prawidłowo dla jednej grupy osób, tak jak w przypadku innych. Na przykład system rozpoznawania głosu może nie działać jak również dla kobiet, tak jak w przypadku mężczyzn.

Aby zmniejszyć nieuczciwe zachowanie w systemach AI, należy ocenić i rozwiązać te szkody.

>[!NOTE]
> Sprawiedliwe jest wyzwanie społeczno-techniczne. Wiele aspektów godziwych, takich jak sprawiedliwości i proces wymagalny, nie są przechwytywane w metrykach ilościowych. Ponadto nie wszystkie metryki ilościowej nie mogą być jednocześnie spełnione. Celem jest umożliwienie użytkownikom oceny różnych strategii zaradczych, a następnie podwyższenie poziomu, które są odpowiednie dla scenariusza.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Ocena uczciwości i łagodzenie przy użyciu Fairlearn

Fairlearn to pakiet języka Python "open source", który umożliwia deweloperom systemów uczenia maszynowego ocenę atrakcyjności systemów i łagodzenie obserwowanych problemów z uczciwym wykorzystaniem.

Fairlearn ma dwa składniki:

- Pulpit nawigacyjny oceny: widżet Jupyter Notes służący do oceniania, jak przewidywania modelu wpływają na różne grupy. Umożliwia również porównywanie wielu modeli przy użyciu metryk uczciwości i wydajności.
- Algorytmy ograniczenia: zestaw algorytmów pozwalających ograniczyć nieuczciwą klasyfikację binarną i regresję.

Wspólnie te składniki umożliwiają analitykom danych i liderom biznesowym nawigowanie po atrakcyjności i wydajności oraz wybór strategii zaradczej, która najlepiej odpowiada potrzebom.

## <a name="fairness-assessment"></a>Ocena uczciwości

W Fairlearn, sprawiedliwe koncepcje są koncepcyjne, mimo że podejście znane jako **sprawiedliwe grupy**, które prosi o to, które grupy użytkowników indywidualnych są zagrożone?

Odpowiednie grupy, znane także jako podpopulacji, są definiowane za pomocą **poufnych funkcji** lub atrybutów poufnych. Poufne funkcje są przesyłane do Fairlearn szacowania jako wektor lub macierz o nazwie `sensitive_features` . W tym przypadku należy zasugerować, że projektant systemu powinien być poufny dla tych funkcji podczas oceniania sprawiedliwości grupy. Informacje o tym, czy te funkcje zawierają kwestie prywatności wynikające z informacji umożliwiających zidentyfikowanie użytkownika. Ale słowo "poufne" nie oznacza, że te funkcje nie powinny być używane do prognozowania.

W fazie oceny słuszność jest policzalna przy użyciu metryki różnicowych. **Metryki różnicowe** mogą oszacować i porównać zachowanie modelu w różnych grupach jako współczynnik lub różnice. Fairlearn obsługuje dwie klasy metryki różnicowej:


- Różnice w wydajności modelu: te zestawy metryk obliczają różnice (różnice) w wartościach wybranej metryki wydajności dla różnych podgrup. Oto niektóre przykłady:

  - różnice w współczynniku dokładności
  - różnice w współczynniku błędów
  - różnice w precyzji
  - różnice w odwołaniu
  - różnice w MAE
  - wiele innych

- Różnice w współczynniku wyboru: Ta Metryka zawiera różnicę w współczynniku wyboru między różnymi podgrupami. Przykładem jest różnice w stawkach zatwierdzenia pożyczki. Współczynnik wyboru oznacza ułamek punktów danych w każdej klasie sklasyfikowany jako 1 (w klasyfikacji binarnej) lub rozkład wartości przewidywania (w regresji).

## <a name="unfairness-mitigation"></a>Łagodzenie nieuczciwości

### <a name="parity-constraints"></a>Ograniczenia parzystości

Fairlearn zawiera różne algorytmy ograniczania nieuczciwych skutków. Te algorytmy obsługują zestaw ograniczeń dotyczących zachowań predykcyjnych o nazwie **ograniczenia parzystości** lub kryteria. Ograniczenia parzystości wymagają, aby niektóre aspekty zachowania predykcyjnego były porównywalne w grupach, które są określone przez funkcje poufne (np. różne Races). Algorytmy łagodzenia Fairlearn używają takich ograniczeń parzystości w celu ograniczenia zauważalnych problemów z uczciwością.

Fairlearn obsługuje następujące typy ograniczeń parzystości:

|Ograniczenie parzystości  | Przeznaczenie  |Zadanie uczenia maszynowego  |
|---------|---------|---------|
|Kontrola demograficzna     |  Eliminowanie szkody związanej z przydziałem | Klasyfikacja binarna, regresja |
|Szanse równe  | Diagnozuj szkody związane z alokacją i jakością usług | Klasyfikacja binarna        |
|Powiązana utrata grupy     |  Eliminowanie szkód związanych z jakością usług | Regresja |

### <a name="mitigation-algorithms"></a>Algorytmy ograniczenia

Fairlearn zapewnia dostosujesz i zmniejszanie algorytmów ograniczania nieuczciwości:

- Redukcja: te algorytmy wykorzystują standardowe szacowania ML (np. model LightGBM) i generują zestaw przeszkolonych modeli przy użyciu sekwencji ponownie ważonych zestawów danych szkoleniowych. Na przykład w przypadku kandydatów o określonej płci może być w pełni ważona lub w dół w celu pouczenia modeli i zmniejszenia różnic między różnymi grupami płci. Następnie użytkownicy mogą wybrać model, który zapewnia najlepszy kompromis między dokładnością (lub inną metryką wydajności) i różnicami, które zwykle muszą być oparte na regułach i kosztach firmy.  
- Przetwarzanie końcowe: te algorytmy mają istniejący klasyfikator i poufną funkcję jako dane wejściowe. Następnie uzyskują transformację przewidywania klasyfikatora, aby wymusić określone ograniczenia godziwe. Największą zaletą optymalizacji progu jest prostota i elastyczność, ponieważ nie ma potrzeby ponownego uczenia modelu. 

| Algorytm | Opis | Zadanie uczenia maszynowego | Funkcje poufne | Obsługiwane ograniczenia parzystości | Typ algorytmu |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | Podejście czarne do uczciwej klasyfikacji opisanej w [ramach zmniejszenia podejścia do uczciwej klasyfikacji](https://arxiv.org/abs/1803.02453) | Klasyfikacja binarna | Podzielone na kategorie | [Parzystość demograficzna](#parity-constraints), [szansee](#parity-constraints) | Szum |
| `GridSearch` | Podejście z czernią opisane w [zmniejszeniu podejścia do uczciwej klasyfikacji](https://arxiv.org/abs/1803.02453)| Klasyfikacja binarna | plików binarnych | [Parzystość demograficzna](#parity-constraints), [szansee](#parity-constraints) | Szum |
| `GridSearch` | Podejście czarne, które implementuje odmianę wyszukiwania z siatką z zastosowaniem algorytmu dla pozostałej straty grupy opisanej w ramach [uczciwej regresji: definicje ilościowe i algorytmy oparte na redukcji](https://arxiv.org/abs/1905.12843) | Regresja | plików binarnych | [Powiązana utrata grupy](#parity-constraints) | Szum |
| `ThresholdOptimizer` | Algorytm dostosujesz w oparciu o równość z papieru [w nadzorowanym uczeniu](https://arxiv.org/abs/1610.02413). Ta technika przyjmuje jako wejściowy istniejący klasyfikator i funkcję poufną i poprowadzi transformację przekształcenia klasyfikatora w celu wymuszenia określonych ograniczeń parzystości. | Klasyfikacja binarna | Podzielone na kategorie | [Parzystość demograficzna](#parity-constraints), [szansee](#parity-constraints) | Przetwarzanie końcowe |

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak korzystać z różnych składników, zapoznaj się z [repozytorium Fairlearn GitHub](https://github.com/fairlearn/fairlearn/) i [przykładowymi notesami](https://github.com/fairlearn/fairlearn/tree/master/notebooks).
- Informacje o zachowaniu prywatności danych przy użyciu [różnicowej prywatności i pakietu WhisperNoise](concept-differential-privacy.md).