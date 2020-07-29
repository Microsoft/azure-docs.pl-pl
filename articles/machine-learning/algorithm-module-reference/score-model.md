---
title: 'Model oceny: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu modelu Score w Azure Machine Learning do generowania prognoz przy użyciu przeszkolonego modelu klasyfikacji lub regresji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 361199bd380b126ec41fb8a5587a2ebd78cd6f24
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84447615"
---
# <a name="score-model"></a>Klasyfikacja modelu

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Ten moduł służy do generowania prognoz przy użyciu przeszkolonego modelu klasyfikacji lub regresji.

## <a name="how-to-use"></a>Sposób użycia

1. Dodaj moduł **modelu oceny** do potoku.

2. Dołącz model przeszkolony i zestaw danych zawierający nowe dane wejściowe. 

    Dane powinny mieć format zgodny z typem używanego modelu nauczonego. Schemat wejściowego zestawu danych powinien również zwykle odpowiadać schematowi danych używanych do uczenia modelu.

3. Prześlij potok.

## <a name="results"></a>Wyniki

Po wygenerowaniu zestawu wyników przy użyciu [modelu oceny](./score-model.md):

+ Aby wygenerować zestaw metryk używanych do oceny dokładności modelu (wydajność), można połączyć wynikowy zestaw danych do [oceny modelu](./evaluate-model.md), 
+ Kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Wizualizacja** , aby wyświetlić przykładowe wyniki.
<!-- + To Save the results to a dataset. -->

Wynik lub przewidywana wartość może być w wielu różnych formatach, w zależności od modelu i danych wejściowych:

- W przypadku modeli klasyfikacji [model oceny](./score-model.md) generuje prognozowaną wartość klasy, a także prawdopodobieństwo wartości przewidywanej.
- W przypadku modeli regresji [model oceny](./score-model.md) generuje tylko przewidywaną wartość liczbową.


## <a name="publish-scores-as-a-web-service"></a>Publikowanie wyników jako usługi sieci Web

Typowym użyciem oceniania jest zwrócenie danych wyjściowych w ramach predykcyjnej usługi sieci Web. Aby uzyskać więcej informacji, zobacz [ten samouczek](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy) dotyczący wdrażania punktu końcowego w czasie rzeczywistym na podstawie potoku w programie Azure Machine Learning Designer.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 