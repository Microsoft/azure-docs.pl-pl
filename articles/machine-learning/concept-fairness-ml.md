---
title: Eliminowanie sprawiedliwych modeli uczenia maszynowego (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o atrakcyjności modeli uczenia maszynowego oraz o sposobie, w jaki pakiet języka Python Fairlearn może pomóc w tworzeniu atrakcyjnych modeli.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 08/05/2020
ms.openlocfilehash: 459cb1f7ea6c756b8cf6eba70af5ebabe76cc8b0
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335762"
---
# <a name="mitigate-fairness-in-machine-learning-models-preview"></a>Eliminowanie sprawiedliwych modeli uczenia maszynowego (wersja zapoznawcza)

Dowiedz się więcej o atrakcyjności uczenia maszynowego oraz o sposobie, w jaki pakiet języka Python [Fairlearn](https://fairlearn.github.io/) open-source może pomóc w ograniczeniu problemów z uczciwymi informacjami w modelach uczenia maszynowego. Jeśli nie chcesz, aby poznać problemy z uczciwymi działaniami i ocenić sprawiedliwość podczas kompilowania modeli uczenia maszynowego, możesz tworzyć modele, które generują nieuczciwe wyniki.

Poniższe podsumowanie [podręcznika użytkownika](https://fairlearn.github.io/master/user_guide/index.html) dla pakietu Fairlearn Open Source zawiera opis sposobu korzystania z niego do oceny sprawiedliwej liczby tworzonych systemów AI.  Pakiet Fairlearn open source może również oferować opcje pozwalające ograniczyć lub zmniejszyć liczbę problemów, które należy obserwować.  Zobacz sekcję [instrukcje](how-to-machine-learning-fairness-aml.md) i [przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) , aby włączyć ocenę godziwą systemów AI podczas szkolenia na Azure Machine Learning.


## <a name="what-is-fairness-in-machine-learning-models"></a>Co to jest sprawiedliwe w modelach uczenia maszynowego?

>[!NOTE]
> Sprawiedliwe jest wyzwanie społeczno-techniczne. Wiele aspektów godziwych, takich jak sprawiedliwości i proces wymagalny, nie są przechwytywane w metrykach ilościowych. Ponadto nie wszystkie metryki ilościowej nie mogą być jednocześnie spełnione. Celem z pakietem Open-Source Fairlearn jest umożliwienie ludzi oceny różnych efektów i strategii zaradczych. Ostatecznie jest to do użytkowników ludzkich tworzących modele sztucznej analizy i uczenia maszynowego, aby zwiększyć możliwości, które są odpowiednie dla ich scenariusza.

Sztuczna inteligencja i systemy uczenia maszynowego mogą wyświetlać nieuczciwe zachowanie. Jednym ze sposobów zdefiniowania nieuczciwego zachowania jest ich szkody lub wpływ na ludzi. Istnieje wiele typów szkód, które mogą prowadzić systemy AI. Aby dowiedzieć się więcej, zobacz [NeurIPS 2017 prezentację przez Kate Crawford](https://www.youtube.com/watch?v=fMym_BKWQzk) .

Dwa popularne typy sztucznie spowodowały uszkodzenie:

- Szkoda alokacji: system AI rozszerza lub wstrzymuje szanse, zasoby lub informacje dla niektórych grup. Przykłady obejmują zatrudnienie, przyjmowanie szkolne i kredyty, w których model może być dużo lepszy przy wybieraniu dobrych kandydatów między konkretną grupą osób niż w innych grupach.

- Uszkodzenie jakości usługi: system AI nie działa prawidłowo dla jednej grupy osób, tak jak w przypadku innych. Na przykład system rozpoznawania głosu może nie działać jak również dla kobiet, tak jak w przypadku mężczyzn.

Aby zmniejszyć nieuczciwe zachowanie w systemach AI, należy ocenić i rozwiązać te szkody.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Ocena uczciwości i łagodzenie przy użyciu Fairlearn

Fairlearn to pakiet języka Python "open source", który umożliwia deweloperom systemów uczenia maszynowego ocenę atrakcyjności systemów i łagodzenie obserwowanych problemów z uczciwym wykorzystaniem.

Pakiet Fairlearn Open Source ma dwa składniki:

- Pulpit nawigacyjny oceny: widżet Jupyter Notes służący do oceniania, jak przewidywania modelu wpływają na różne grupy. Umożliwia również porównywanie wielu modeli przy użyciu metryk uczciwości i wydajności.
- Algorytmy ograniczenia: zestaw algorytmów pozwalających ograniczyć nieuczciwą klasyfikację binarną i regresję.

Wspólnie te składniki umożliwiają analitykom danych i liderom biznesowym nawigowanie po atrakcyjności i wydajności oraz wybór strategii zaradczej, która najlepiej odpowiada potrzebom.

## <a name="assess-fairness-in-machine-learning-models"></a>Oceniaj sprawiedliwe modele uczenia maszynowego

W pakiecie Fairlearn Open Source godziwa wartość jest koncepcyjna, chociaż podejście znane jako **sprawiedliwe grupy** , z pytaniem, które grupy osób narażonych są zagrożone? Odpowiednie grupy, znane także jako podpopulacji, są definiowane za pomocą **poufnych funkcji** lub atrybutów poufnych. Funkcje poufne są przesyłane do szacowania w pakiecie Fairlearn typu open source jako wektor lub macierz o nazwie  `sensitive_features` . W tym przypadku należy zasugerować, że projektant systemu powinien być poufny dla tych funkcji podczas oceniania sprawiedliwości grupy. 

Należy się zastanowić, czy te funkcje zawierają kwestie dotyczące prywatności wynikające z danych prywatnych. Ale słowo "poufne" nie oznacza, że te funkcje nie powinny być używane do prognozowania.

>[!NOTE]
> Ocena uczciwości nie jest czysto technicznym ćwiczeniem.  Pakiet typu open source Fairlearn może pomóc ocenić atrakcyjność modelu, ale nie wykona oceny.  Pakiet typu open source Fairlearn pomaga identyfikować metryki ilościowe w celu oceny sprawiedliwości, ale deweloperzy muszą również przeprowadzać analizę jakościową, aby ocenić atrakcyjność własnych modeli.  Poufne funkcje wymienione powyżej to przykład analizy jakościowej.     

W fazie oceny słuszność jest policzalna przy użyciu metryki różnicowych. **Metryki różnicowe** mogą oszacować i porównać zachowanie modelu w różnych grupach jako współczynnik lub różnice. Pakiet typu open source Fairlearn obsługuje dwie klasy metryki różnicowej:


- Różnice w wydajności modelu: te zestawy metryk obliczają różnice (różnice) w wartościach wybranej metryki wydajności dla różnych podgrup. Oto niektóre przykłady:

  - różnice w współczynniku dokładności
  - różnice w współczynniku błędów
  - różnice w precyzji
  - różnice w odwołaniu
  - różnice w MAE
  - wiele innych

- Różnice w współczynniku wyboru: Ta Metryka zawiera różnicę w współczynniku wyboru między różnymi podgrupami. Przykładem jest różnice w stawkach zatwierdzenia pożyczki. Współczynnik wyboru oznacza ułamek punktów danych w każdej klasie sklasyfikowany jako 1 (w klasyfikacji binarnej) lub rozkład wartości przewidywania (w regresji).

## <a name="mitigate-unfairness-in-machine-learning-models"></a>Eliminowanie nieuczciwych modeli uczenia maszynowego

### <a name="parity-constraints"></a>Ograniczenia parzystości

Pakiet Fairlearn Open Source zawiera różne algorytmy ograniczania nieuczciwych skutków. Te algorytmy obsługują zestaw ograniczeń dotyczących zachowań predykcyjnych o nazwie **ograniczenia parzystości** lub kryteria. Ograniczenia parzystości wymagają, aby niektóre aspekty zachowania predykcyjnego były porównywalne w grupach, które są określone przez funkcje poufne (np. różne Races). Algorytmy zaradcze w pakiecie Fairlearn Open Source używają takich ograniczeń parzystości, aby zmniejszyć zaobserwowane problemy z nieuczciwymi zabezpieczeniami.

>[!NOTE]
> Eliminacja nieuczciwości w modelu oznacza zmniejszenie nieuczciwości, ale nie można całkowicie wyeliminować tego ograniczenia.  Algorytmy łagodzenia nieuczciwości w pakiecie Fairlearn Open Source mogą zapewniać sugerowane strategie zaradcze, aby pomóc w zmniejszeniu nieuczciwej dostępności modelu uczenia maszynowego, ale nie są to rozwiązania umożliwiające całkowite wyeliminowanie nieuczciwych działań.  Mogą istnieć inne ograniczenia związane z parzystością lub kryteria, które należy wziąć pod uwagę w przypadku każdego określonego modelu uczenia maszynowego dla deweloperów. Deweloperzy korzystający z Azure Machine Learning muszą samodzielnie określić, czy środki zaradcze odpowiednio eliminują wszelkie niesprawiedliwe użycie i wdrażanie modeli uczenia maszynowego.  

Pakiet typu "open source" Fairlearn obsługuje następujące typy ograniczeń parzystości: 

|Ograniczenie parzystości  | Przeznaczenie  |Zadanie uczenia maszynowego  |
|---------|---------|---------|
|Kontrola demograficzna     |  Eliminowanie szkody związanej z przydziałem | Klasyfikacja binarna, regresja |
|Szanse równe  | Diagnozuj szkody związane z alokacją i jakością usług | Klasyfikacja binarna        |
|Równa szansa | Diagnozuj szkody związane z alokacją i jakością usług | Klasyfikacja binarna        |
|Powiązana utrata grupy     |  Eliminowanie szkód związanych z jakością usług | Regresja |



### <a name="mitigation-algorithms"></a>Algorytmy ograniczenia

Pakiet Fairlearn Open Source zapewnia dostosujesz i zmniejszanie algorytmów ograniczania nieuczciwości:

- Redukcja: te algorytmy wykorzystują standardowe szacowania uczenia maszynowego (np. model LightGBM) i generują zestaw przeszkolonych modeli przy użyciu sekwencji ponownie ważonych zestawów danych szkoleniowych. Na przykład w przypadku kandydatów o określonej płci może być w pełni ważona lub w dół w celu pouczenia modeli i zmniejszenia różnic między różnymi grupami płci. Następnie użytkownicy mogą wybrać model, który zapewnia najlepszy kompromis między dokładnością (lub inną metryką wydajności) i różnicami, które zwykle muszą być oparte na regułach i kosztach firmy.  
- Przetwarzanie końcowe: te algorytmy mają istniejący klasyfikator i poufną funkcję jako dane wejściowe. Następnie uzyskują transformację przewidywania klasyfikatora, aby wymusić określone ograniczenia godziwe. Największą zaletą optymalizacji progu jest prostota i elastyczność, ponieważ nie ma potrzeby ponownego uczenia modelu. 

| Algorytm | Opis | Zadanie uczenia maszynowego | Funkcje poufne | Obsługiwane ograniczenia parzystości | Typ algorytmu |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | Podejście czarne do uczciwej klasyfikacji opisanej w [ramach zmniejszenia podejścia do uczciwej klasyfikacji](https://arxiv.org/abs/1803.02453) | Klasyfikacja binarna | Podzielone na kategorie | [Parzystość demograficzna](#parity-constraints), [szansee](#parity-constraints) | Szum |
| `GridSearch` | Podejście z czernią opisane w [zmniejszeniu podejścia do uczciwej klasyfikacji](https://arxiv.org/abs/1803.02453)| Klasyfikacja binarna | Binarne | [Parzystość demograficzna](#parity-constraints), [szansee](#parity-constraints) | Szum |
| `GridSearch` | Podejście czarne, które implementuje odmianę wyszukiwania z siatką z zastosowaniem algorytmu dla pozostałej straty grupy opisanej w ramach [uczciwej regresji: definicje ilościowe i algorytmy oparte na redukcji](https://arxiv.org/abs/1905.12843) | Regresja | Binarne | [Powiązana utrata grupy](#parity-constraints) | Szum |
| `ThresholdOptimizer` | Algorytm dostosujesz w oparciu o równość z papieru [w nadzorowanym uczeniu](https://arxiv.org/abs/1610.02413). Ta technika przyjmuje jako wejściowy istniejący klasyfikator i funkcję poufną i poprowadzi transformację przekształcenia klasyfikatora w celu wymuszenia określonych ograniczeń parzystości. | Klasyfikacja binarna | Podzielone na kategorie | [Parzystość demograficzna](#parity-constraints), [szansee](#parity-constraints) | Przetwarzanie końcowe |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak korzystać z różnych składników, sprawdzając w witrynie [GitHub](https://github.com/fairlearn/fairlearn/)Fairlearn, [podręczniku użytkownika](https://fairlearn.github.io/master/user_guide/index.html), [przykłady](https://fairlearn.github.io/master/auto_examples/)i [przykładowe notesy](https://github.com/fairlearn/fairlearn/tree/master/notebooks).
- Dowiedz się [, jak](how-to-machine-learning-fairness-aml.md) włączyć ocenę godziwą modeli uczenia maszynowego w Azure Machine Learning.
- Zapoznaj się z [przykładowymi notesami](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) , aby uzyskać dodatkowe scenariusze oceny uczciwości w Azure Machine Learning. 
