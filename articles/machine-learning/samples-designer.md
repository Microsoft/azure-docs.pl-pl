---
title: Przykładowe potoki projektanta
titleSuffix: Azure Machine Learning
description: Użyj przykładów w programie Azure Machine Learning Designer, aby przeskakuje i uruchamiać potoki uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.openlocfilehash: f9a8b0a4c51024d91e517db2f6ae10a4dba62384
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80389345"
---
# <a name="designer-sample-pipelines"></a>Przykładowe potoki projektanta

Skorzystaj z wbudowanych przykładów w programie Azure Machine Learning Designer, aby szybko rozpocząć tworzenie własnych potoków uczenia maszynowego. [Repozytorium usługi GitHub](https://github.com/Azure/MachineLearningDesigner) Azure Machine Learning Designer zawiera szczegółową dokumentację ułatwiającą zrozumienie niektórych typowych scenariuszy uczenia maszynowego.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://aka.ms/AMLFree).
* Azure Machine Learning obszar roboczy z jednostką SKU przedsiębiorstwa.


## <a name="how-to-use-sample-pipelines"></a>Jak używać potoków przykładowych

Projektant zapisuje kopię przykładowych potoków w obszarze roboczym programu Studio. Możesz edytować potok, aby dostosować go do swoich potrzeb i zapisać jako własny. Użyj ich jako punktu początkowego, aby szybko Rozpocznij pracę projekty.

### <a name="open-a-sample-pipeline"></a>Otwieranie przykładowego potoku

1. Zaloguj się do <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>i wybierz obszar roboczy, z którym chcesz współpracować.

1. Wybierz pozycję **Projektant**.

1. Wybierz przykładowy potok w sekcji **nowe potoku** .

    Wybierz pozycję **Pokaż więcej próbek** , aby uzyskać pełną listę przykładów.

### <a name="submit-a-pipeline-run"></a>Przesyłanie uruchomienia potoku

Aby uruchomić potok, najpierw musisz ustawić domyślny cel obliczeń, aby uruchomić potok na.

1. W okienku **Ustawienia** z prawej strony kanwy wybierz pozycję **Wybierz element docelowy obliczeń**.

1. W wyświetlonym oknie dialogowym wybierz istniejący element docelowy obliczeń lub Utwórz nowy. Wybierz pozycję **Zapisz**.

1. Wybierz pozycję **Prześlij** w górnej części kanwy, aby przesłać uruchomienie potoku.

W zależności od potoku przykładowego i ustawień obliczeń uruchomienie może zająć trochę czasu. Domyślne ustawienia obliczeń mają minimalny rozmiar węzła równy 0, co oznacza, że projektant musi przydzielić zasoby po stanie bezczynności. Powtarzające się uruchomienia potoku będą trwać krócej od czasu przydziału zasobów obliczeniowych. Ponadto projektant używa buforowanych wyników dla każdego modułu, aby zwiększyć wydajność.


### <a name="review-the-results"></a>Sprawdzanie wyników

Po zakończeniu potoku możesz przejrzeć potok i wyświetlić dane wyjściowe dla każdego modułu, aby dowiedzieć się więcej.

Wykonaj następujące kroki, aby wyświetlić dane wyjściowe modułu:

1. Wybierz moduł na kanwie.

1. W okienku Szczegóły modułu z prawej strony kanwy wybierz pozycję dane **wyjściowe + dzienniki**. Wybierz ikonę](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) wizualizacji ![ikona Graf, aby zobaczyć wyniki każdego modułu. 

Użyj przykładów jako punktów początkowych dla niektórych najpopularniejszych scenariuszy uczenia maszynowego.

## <a name="regression-samples"></a>Próbki regresji

Dowiedz się więcej na temat wbudowanych przykładów regresji.

| Przykładowy tytuł | Opis | 
| --- | --- |
| [Przykład 1: regresja — Prognoza cen dla samochodów (podstawowa)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Przewidywanie cen samochodów przy użyciu regresji liniowej. |
| [Przykład 2: regresja — Prognoza cen dla samochodów (Zaawansowane)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Przewidywanie cen samochodów przy użyciu lasu decyzyjnego i podwyższanie drzewa decyzyjnego regresorów. Porównaj modele, aby znaleźć najlepszy algorytm.

## <a name="classification-samples"></a>Przykłady klasyfikacji

Dowiedz się więcej o wbudowanych przykładach klasyfikacji. Aby dowiedzieć się więcej o przykładach bez linków do dokumentacji, należy otworzyć przykłady i wyświetlić komentarze do modułu.

| Przykładowy tytuł | Opis | 
| --- | --- |
| [Przykład 3: klasyfikacja binarna z wyborem funkcji — prognozowanie dochodu](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Przewidywanie przychodów jako wysokie lub niskie przy użyciu dwuklasowego drzewa decyzyjnego. Użyj korelacji Pearsona, aby wybrać funkcje.
| [Przykład 4: klasyfikacja binarna z niestandardowym skryptem języka Python — przewidywanie ryzyka kredytowego](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Klasyfikowanie aplikacji kredytowych jako wysokiego lub niskiego ryzyka. Użyj modułu skryptu języka Python do ważenia danych.
| [Przykład 5: klasyfikacja binarna — Prognoza relacji klienta](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Przewidywanie zmian klientów przy użyciu dwuklasowych drzew decyzyjnych. Użyj SMOTE do próbkowania danych.
| [Przykład 7: Klasyfikacja tekstu — zestaw danych witryny Wikipedia SP 500](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Klasyfikowanie typów firmy z artykułów witryny Wikipedia przy użyciu wieloklasowej regresji logistycznej. |
| Przykład 12: Rozpoznawanie litery w klasyfikacji wieloklasowej | Utwórz kompletną liczbę klasyfikatorów binarnych do klasyfikowania pisanych liter. |

## <a name="recommender-samples"></a>Przykłady rekomendacji

Dowiedz się więcej na temat wbudowanych przykładów zalecenia. Aby dowiedzieć się więcej o przykładach bez linków do dokumentacji, należy otworzyć przykłady i wyświetlić komentarze do modułu.

| Przykładowy tytuł | Opis | 
| --- | --- |
| Przykład 10: rekomendacje — tweety klasyfikacji filmów | Kompiluj aparat polecanego filmu z tytułów i klasyfikacji filmów. |

## <a name="utility-samples"></a>Przykłady narzędzi

Dowiedz się więcej o przykładach demonstrujących narzędzia i funkcje uczenia maszynowego. Aby dowiedzieć się więcej o przykładach bez linków do dokumentacji, należy otworzyć przykłady i wyświetlić komentarze do modułu.

| Przykładowy tytuł | Opis | 
| --- | --- |
| [Przykład 6: Używanie niestandardowego skryptu języka R — prognozowanie opóźnień lotów](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Przykład 8: krzyżowe sprawdzanie poprawności dla klasyfikacji binarnej — przewidywanie dochodu dla dorosłych | Użyj operacji krzyżowego sprawdzania poprawności, aby utworzyć klasyfikator binarny dla dorosłych dochodów.
| Przykład 9: ważność funkcji permutacji | Użyj ważności funkcji permutacji, aby obliczyć wyniki ważności dla zestawu danych testowych. 
| Przykład 11: dostrajanie parametrów dla klasyfikacji binarnej — przewidywanie dochodu dla dorosłych | Użyj dopasowywania parametrów modelu, aby znaleźć optymalne parametry do kompilowania klasyfikatora binarnego. |

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak tworzyć i wdrażać modele uczenia maszynowego za pomocą [samouczka: przewidywanie ceny za samochód z użyciem projektanta](tutorial-designer-automobile-price-train-score.md)
