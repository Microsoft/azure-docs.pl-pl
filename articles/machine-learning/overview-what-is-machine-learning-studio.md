---
title: Co to jest usługa Azure Machine Learning Studio?
description: Azure Machine Learning Studio to portal sieci Web dla Azure Machine Learning obszarów roboczych. W programie Studio nie są połączona żadne kod i pierwsze środowisko, aby utworzyć platformę do nauki o danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/24/2020
ms.openlocfilehash: 345c925d3b1350dfc2ea69d65d8d14688c6930b9
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310167"
---
# <a name="what-is-azure-machine-learning-studio"></a>Co to jest usługa Azure Machine Learning Studio?

Ten artykuł zawiera informacje na temat Azure Machine Learning Studio — portalu sieci Web dla deweloperów analityków danych w programie [Azure Machine Learning](overview-what-is-azure-ml.md). W programie Studio nie są połączona żadne kod i pierwsze środowisko do nauki o danych.

W tym artykule przedstawiono następujące informacje:
>[!div class="checklist"]
> - Jak [tworzyć projekty uczenia maszynowego](#author-machine-learning-projects) w programie Studio.
> - Jak [zarządzać zasobami i zasobami](#manage-assets-and-resources) w programie Studio.
> - Różnice między [Azure Machine Learning Studio i ml Studio (klasyczne)](#ml-studio-classic-vs-azure-machine-learning-studio).


## <a name="author-machine-learning-projects"></a>Tworzenie projektów uczenia maszynowego

Studio oferuje wiele środowisk tworzenia w zależności od projektu typu i poziomu środowiska użytkownika.

+ **Notesy**

  Pisz i uruchamiaj własny kod w zarządzanych [serwerach Jupyter Notebook](how-to-run-jupyter-notebooks.md) , które są bezpośrednio zintegrowane w programie Studio. 

+ **Projektant usługi Azure Machine Learning**

  Użyj projektanta, aby nauczyć i wdrożyć modele uczenia maszynowego bez konieczności pisania kodu. Przeciągnij i upuść zestawy danych i moduły, aby utworzyć potoki ML. Wypróbuj [samouczek projektanta](tutorial-designer-automobile-price-train-score.md).

    ![Przykład projektanta Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

+ **Zautomatyzowany interfejs użytkownika uczenia maszynowego**

  Dowiedz się, jak tworzyć [zautomatyzowane eksperymenty ml](tutorial-first-experiment-automated-ml.md) przy użyciu łatwego w użyciu interfejsu. 

  [![Okienko nawigacji Azure Machine Learning Studio](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

+ **Etykietowanie danych**

    Używaj [etykiet danych Azure Machine Learning](how-to-create-labeling-projects.md) do wydajnej koordynacji projektów etykietowania danych.

## <a name="manage-assets-and-resources"></a>Zarządzanie elementami zawartości i zasobami

Zarządzaj zasobami uczenia maszynowego bezpośrednio w przeglądarce. Zasoby są współużytkowane w tym samym obszarze roboczym między zestawem SDK i programem Studio w celu zapewnienia bezproblemowego środowiska. Zarządzanie programem przy użyciu programu Studio:

- Modele
- Zestawy danych
- Magazyny danych
- Zasoby obliczeniowe
- Notebooks
- Eksperymenty
- Uruchamianie dzienników
- Pipelines 
- Punkty końcowe potoku

Nawet jeśli jesteś doświadczonym deweloperem, program Studio może uprościć zarządzanie zasobami obszaru roboczego.

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>ML Studio (klasyczny) vs Azure Machine Learning Studio

Opublikowano w 2015, **ml Studio (klasyczny)** był naszym pierwszym konstruktorem uczenia maszynowego w ramach przeciągania i upuszczania. Jest to Usługa autonomiczna, która oferuje tylko wizualizację. Studio (klasyczny) nie współdziała z Azure Machine Learning.

**Azure Machine Learning** to oddzielna i nowoczesny usługa, która zapewnia kompletną platformę do nauki o danych. Obsługuje zarówno środowisko w kodzie, jak i w małym kodzie.

**Azure Machine Learning Studio** to portal sieci web *w* Azure Machine Learning, który zawiera opcje niskiej ilości kodu i bez kodu dla tworzenia projektów i zarządzania zasobami. 

Zalecamy, aby nowi użytkownicy wybierali **Azure Machine Learning** , a nie ml Studio (klasyczny), aby uzyskać najnowszy zakres narzędzi do nauki o danych.

### <a name="feature-comparison"></a>Porównanie funkcji

Poniższa tabela zawiera podsumowanie podstawowych różnic między ML Studio (klasyczne) i Azure Machine Learning.

| Cechy | ML Studio (klasyczny) | Azure Machine Learning |
|---| --- | --- |
| Przeciąganie i upuszczanie interfejsu | Środowisko klasyczne | Zaktualizowane środowisko — [projektant Azure Machine Learning](concept-designer.md)| 
| Zestawy SDK kodu | Nieobsługiwane | W pełni zintegrowane z [Azure Machine Learning Python](/python/api/overview/azure/ml/) i [języka R](tutorial-1st-r-experiment.md) SDK |
| Experiment | Skalowalne (limit danych szkolenia 10 GB) | Skalowanie za pomocą elementu docelowego obliczeń |
| Szkoleniowe cele obliczeniowe | Własnościowy obiekt docelowy obliczeń, tylko obsługa procesora CPU | Szeroki zakres dostosowywalnych [obiektów docelowych obliczeń szkoleniowych](concept-compute-target.md#train). Obejmuje obsługę procesora GPU i procesorów | 
| Cele obliczeń wdrożenia | Własny format usługi sieci Web, niedostosowywalny | Szeroka gama [elementów docelowych obliczeniowych wdrożenia](concept-compute-target.md#deploy). Obejmuje obsługę procesora GPU i procesorów |
| Potok ML | Nieobsługiwane | Twórz elastyczne, modularne [potoki](concept-ml-pipelines.md) do automatyzowania przepływów pracy |
| MLOps | Podstawowe Zarządzanie modelami i ich wdrażanie; Wdrożenia wyłącznie procesora | Przechowywanie wersji jednostek (model, dane, przepływy pracy), Automatyzacja przepływów pracy, integracja z narzędziami CICD, wdrożenia procesora CPU i procesora GPU [itd](concept-model-management-and-deployment.md) . |
| Format modelu | Format własnościowy, Studio (klasyczny) | Wiele obsługiwanych formatów w zależności od typu zadania szkoleniowego |
| Automatyczne szkolenie modelu i dostrajanie parametrów |  Nieobsługiwane | [Obsługiwane](concept-automated-ml.md). Opcje Code-First i No-Code. | 
| Wykrywanie dryfowania danych | Nieobsługiwane | [Obsługiwane](how-to-monitor-datasets.md) |
| Projekty etykietowania danych | Nieobsługiwane | [Obsługiwane](how-to-create-labeling-projects.md) |


## <a name="next-steps"></a>Następne kroki

Odwiedź [Studio](https://ml.azure.com)lub zapoznaj się z różnymi opcjami tworzenia, korzystając z następujących samouczków:  

- + [Rozpocznij pracę we własnym środowisku programistycznym](tutorial-1st-experiment-sdk-setup-local.md)
  + [Korzystanie z notesów Jupyter w wystąpieniu obliczeniowym do uczenia & wdrażania modeli](tutorial-1st-experiment-sdk-setup.md)
  + [Używanie automatycznego uczenia maszynowego do uczenia & wdrażania modeli](tutorial-first-experiment-automated-ml.md)  
  + [Korzystanie z projektanta do uczenia & wdrażania modeli](tutorial-designer-automobile-price-train-score.md)