---
title: moduł wieloklasowy „jeden przeciw wszystkim”
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu moduł wieloklasowy „jeden przeciw wszystkim” w programie Azure Machine Learning Designer do tworzenia kompletów binarnych modeli klasyfikacji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 4dfe284a00052cbd1915d62355e1d7772f3712ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94591873"
---
# <a name="one-vs-all-multiclass"></a>moduł wieloklasowy „jeden przeciw wszystkim”

W tym artykule opisano sposób korzystania z modułu moduł wieloklasowy „jeden przeciw wszystkim” w programie Azure Machine Learning Designer. Celem jest utworzenie modelu klasyfikacji, który może przewidzieć wiele klas, przy użyciu podejścia *jednokierunkowego* .

Ten moduł jest przydatny do tworzenia modeli predykcyjnych trzy lub więcej wyników, gdy wynik zależy od zmiennych predykcyjnych ciągłych lub kategorii. Ta metoda umożliwia także używanie binarnych metod klasyfikacji dla problemów wymagających wielu klas wyjściowych.

### <a name="more-about-one-versus-all-models"></a>Więcej informacji o modelach jeden-do-wszystkie

Niektóre algorytmy klasyfikacji umożliwiają użycie więcej niż dwóch klas według konstrukcji. Inne ograniczają możliwe wyniki do jednej z dwóch wartości (binarny lub dwuklasowy model). Jednak nawet binarne algorytmy klasyfikacji można dostosować do wieloklasowych zadań klasyfikacji za pomocą różnych strategii. 

Ten moduł implementuje metodę "one-a-All", w której jest tworzony model binarny dla każdej klasy danych wyjściowych. Moduł ocenia każdy z tych modeli binarnych dla poszczególnych klas przed jego uzupełnieniem (wszystkie inne klasy w modelu), tak jakby był to binarny problem z klasyfikacją. Oprócz wydajności obliczeniowej ( `n_classes` wymaga się tylko klasyfikatorów), jedną z zalet tego podejścia jest jego możliwość interpretowania. Ponieważ każda klasa jest reprezentowana przez jeden i tylko jeden klasyfikator, można uzyskać wiedzę na temat klasy, sprawdzając odpowiedni klasyfikator. Jest to najczęściej stosowana strategia klasyfikacji wieloklasowej i jest uczciwym wyborem domyślnym. Następnie moduł wykonuje prognozowanie, uruchamiając te klasyfikatory binarne i wybierając prognozę z najwyższym wynikiem ufności. 

W zasadzie moduł tworzy kompletność poszczególnych modeli, a następnie scala wyniki, aby utworzyć jeden model, który przewiduje wszystkie klasy. Dowolny klasyfikator binarny może być używany jako podstawa dla modelu typu jeden-do-wszystkich.  

Załóżmy na przykład, że skonfigurowano model [maszyny wektorowej obsługujący dwie klasy](two-class-support-vector-machine.md) i zapewnia to, że jako dane wejściowe do modułu moduł wieloklasowy „jeden przeciw wszystkim”. Moduł utworzy modele maszyn wektorowych obsługujące dwie klasy dla wszystkich elementów członkowskich klasy wyjściowej. Następnie zastosuj metodę "jeden do wszystkiego", aby połączyć wyniki dla wszystkich klas.  

Moduł używa OneVsRestClassifier of skryptu sklearn i więcej szczegółów można znaleźć [tutaj](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsRestClassifier.html).

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>Jak skonfigurować klasyfikator moduł wieloklasowy „jeden przeciw wszystkim”  

Ten moduł tworzy kompletną liczbę binarnych modeli klasyfikacji do analizowania wielu klas. Aby użyć tego modułu, należy najpierw skonfigurować i szkolić *binarny model klasyfikacji* . 

Model binarny jest połączony z modułem moduł wieloklasowy „jeden przeciw wszystkim”. Następnie należy wyszkolić zestaw modeli przy użyciu [modelu uczenia](train-model.md) z zestawu danych szkolenia z etykietą.

W przypadku łączenia modeli moduł wieloklasowy „jeden przeciw wszystkim” tworzy wiele binarnych modeli klasyfikacji, optymalizuje algorytm dla każdej klasy, a następnie scala modele. Moduł wykonuje te zadania mimo tego, że zestaw danych szkoleniowych może mieć wiele wartości klas.

1. Dodaj moduł moduł wieloklasowy „jeden przeciw wszystkim” do potoku w projektancie. Ten moduł można znaleźć w obszarze **Machine Learning-Initialize** w kategorii **Klasyfikacja** .

   Klasyfikator moduł wieloklasowy „jeden przeciw wszystkim” nie ma samodzielnie skonfigurowanych parametrów. Wszelkie dostosowania należy wykonać w modelu klasyfikacji binarnej, który jest dostarczany jako dane wejściowe.

2. Dodaj binarny model klasyfikacji do potoku i skonfiguruj ten model. Można na przykład użyć [dwuklasowej maszyny wektorowej](two-class-support-vector-machine.md) lub [dwuklasowego drzewa decyzyjnego](two-class-boosted-decision-tree.md).

3. Dodaj moduł [uczenie modelu](train-model.md) do potoku. Połącz niepociąg klasyfikatora, który jest danymi wyjściowymi moduł wieloklasowy „jeden przeciw wszystkim”.

4. Na innym wejściu [modelu uczenia](train-model.md)Połącz zestaw danych szkoleń z etykietą, który ma wiele wartości klas.

5. Prześlij potok.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia możesz użyć modelu, aby utworzyć prognozy wieloklasowe.

Alternatywnie, można przekazać [niesprawdzony klasyfikator do modelu krzyżowego](cross-validate-model.md) walidacji w celu krzyżowego sprawdzania poprawności względem zestawu danych walidacji z etykietami.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
