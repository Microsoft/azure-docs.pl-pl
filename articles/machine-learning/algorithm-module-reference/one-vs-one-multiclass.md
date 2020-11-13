---
title: Wiele klas
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać wieloklasowego modułu wieloklasowego w Azure Machine Learning, aby utworzyć model klasyfikacji wieloklasy na podstawie wielu elementów binarnych modeli klasyfikacji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 92ef5b372113d0d226335b8ec464a75a5a684bc3
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592910"
---
# <a name="one-vs-one-multiclass"></a>Wiele klas

W tym artykule opisano sposób użycia wieloklasowego modułu, który jest używany w programie Azure Machine Learning Designer. Celem jest utworzenie modelu klasyfikacji, który może przewidzieć wiele klas, przy użyciu podejścia *jedno-do-jednego* .

Ten moduł jest przydatny do tworzenia modeli predykcyjnych trzy lub więcej wyników, gdy wynik zależy od zmiennych predykcyjnych ciągłych lub kategorii. Ta metoda umożliwia także używanie binarnych metod klasyfikacji dla problemów wymagających wielu klas wyjściowych.

### <a name="more-about-one-versus-one-models"></a>Więcej informacji o modelach jeden-do-jednego

Niektóre algorytmy klasyfikacji umożliwiają użycie więcej niż dwóch klas według konstrukcji. Inne ograniczają możliwe wyniki do jednej z dwóch wartości (binarny lub dwuklasowy model). Jednak nawet binarne algorytmy klasyfikacji można dostosować do wieloklasowych zadań klasyfikacji za pomocą różnych strategii. 

Ten moduł implementuje metodę jednokierunkową, w której jest tworzony model binarny dla pary klas. W czasie przewidywania jest wybierana Klasa, która otrzymała najwięcej głosów. Ponieważ wymaga to dopasowania do `n_classes * (n_classes - 1) / 2` klasyfikatorów, ta metoda jest zwykle wolniejsza niż jeden-w, ze względu na jego złożoność (n_classes ^ 2). Jednakże ta metoda może być korzystna dla algorytmów, takich jak algorytmy jądra, które nie są dobrze skalowane w programie `n_samples` . Wynika to z faktu, że każdy indywidualny problem uczenia dotyczy tylko małego podzestawu danych, a jeden z nich jest używany `n_classes` .

W zasadzie moduł tworzy kompletność poszczególnych modeli, a następnie scala wyniki, aby utworzyć jeden model, który przewiduje wszystkie klasy. Dowolny klasyfikator binarny może być używany jako podstawa dla modelu jeden-do-jednego.  

Załóżmy na przykład, że skonfigurowano model [maszyny wektorowej obsługujący dwie klasy](two-class-support-vector-machine.md) i zapewnia to, że jako dane wejściowe do modułu wieloklasowego z jednym elementem. Moduł utworzy modele maszyn wektorowych obsługujące dwie klasy dla wszystkich elementów członkowskich klasy wyjściowej. Następnie zastosuje metodę "jeden do jednego", aby połączyć wyniki dla wszystkich klas.  

Moduł używa OneVsOneClassifier of skryptu sklearn i więcej szczegółów można znaleźć [tutaj](https://scikit-learn.org/stable/modules/generated/sklearn.multiclass.OneVsOneClassifier.html).

## <a name="how-to-configure-the-one-vs-one-multiclass-classifier"></a>Jak skonfigurować jednostronicowy klasyfikator jednoklasowy  

Ten moduł tworzy kompletną liczbę binarnych modeli klasyfikacji do analizowania wielu klas. Aby użyć tego modułu, należy najpierw skonfigurować i szkolić *binarny model klasyfikacji* . 

Model binarny jest podłączany do jednego modułu wieloklasowego. Następnie należy wyszkolić zestaw modeli przy użyciu [modelu uczenia](train-model.md) z zestawu danych szkolenia z etykietą.

W przypadku łączenia modeli funkcja jednoklasowa i jedna z nich tworzy wiele binarnych modeli klasyfikacji, optymalizuje algorytm dla każdej klasy, a następnie scala modele. Moduł wykonuje te zadania mimo tego, że zestaw danych szkoleniowych może mieć wiele wartości klas.

1. Dodaj moduł wieloklasowy do potoku w projektancie. Ten moduł można znaleźć w obszarze **Machine Learning-Initialize** w kategorii **Klasyfikacja** .

   Jednostronicowy klasyfikator jednoklasowy nie ma samodzielnie skonfigurowanych parametrów. Wszelkie dostosowania należy wykonać w modelu klasyfikacji binarnej, który jest dostarczany jako dane wejściowe.

2. Dodaj binarny model klasyfikacji do potoku i skonfiguruj ten model. Można na przykład użyć [dwuklasowej maszyny wektorowej](two-class-support-vector-machine.md) lub [dwuklasowego drzewa decyzyjnego](two-class-boosted-decision-tree.md).

3. Dodaj moduł [uczenie modelu](train-model.md) do potoku. Połącz niepociąg klasyfikatora, który jest danymi wyjściowymi wieloklasowego elementu.

4. Na innym wejściu [modelu uczenia](train-model.md)Połącz zestaw danych szkoleń z etykietą, który ma wiele wartości klas.

5. Prześlij potok.

## <a name="results"></a>Wyniki

Po zakończeniu szkolenia możesz użyć modelu, aby utworzyć prognozy wieloklasowe.

Alternatywnie, można przekazać [niesprawdzony klasyfikator do modelu krzyżowego](cross-validate-model.md) walidacji w celu krzyżowego sprawdzania poprawności względem zestawu danych walidacji z etykietami.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
