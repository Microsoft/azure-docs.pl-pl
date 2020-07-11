---
title: Azure Machine Learning a Machine Learning Studio (wersja klasyczna)
description: Jaka jest różnica między Azure Machine Learning i Machine Learning Studio (klasyczny)?
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 1d4896a2dd2115e5654baffe394618b30a294d5b
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231747"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure Machine Learning vs Machine Learning Studio (klasyczny)

Ten artykuł zawiera informacje o różnicach między Azure Machine Learning i Machine Learning Studio (klasyczne). 

Azure Machine Learning oferuje narzędzia Python i R SDK **oraz** projektanta "przeciągnij i upuść", aby kompilować i wdrażać modele uczenia maszynowego. Studio (klasyczny) oferuje tylko autonomiczne środowisko przeciągania i upuszczania.

Zalecamy, aby nowi użytkownicy wybierali Azure Machine Learning w celu uzyskania szerokiej gamy narzędzi do uczenia maszynowego.

## <a name="quick-comparison"></a>Szybkie porównanie

W poniższej tabeli zestawiono niektóre kluczowe różnice między Azure Machine Learning i Studio (klasyczne):

| Obiekt feature | Usługa Machine Learning Studio (klasyczna) | Azure Machine Learning |
|---| --- | --- |
| Przeciąganie i upuszczanie interfejsu | Obsługiwane | Obsługiwane — [projektant Azure Machine Learning (wersja zapoznawcza)](concept-designer.md) <br/>(Wymaga obszaru roboczego przedsiębiorstwa) | 
| Experiment | Skalowalne (limit danych szkolenia 10 GB) | Skalowanie za pomocą elementu docelowego obliczeń |
| Szkoleniowe cele obliczeniowe | Własnościowy obiekt docelowy obliczeń, tylko obsługa procesora CPU | Szeroki zakres dostosowywalnych [obiektów docelowych obliczeń szkoleniowych](concept-compute-target.md#train). Obejmuje obsługę procesora GPU i procesorów | 
| Cele obliczeń wdrożenia | Własny format usługi sieci Web, niedostosowywalny | Szeroka gama [elementów docelowych obliczeniowych wdrożenia](concept-compute-target.md#deploy). Obejmuje obsługę procesora GPU i procesorów |
| Potok ML | Nieobsługiwane | Twórz elastyczne, modularne [potoki](concept-ml-pipelines.md) do automatyzowania przepływów pracy |
| MLOps | Podstawowe Zarządzanie modelami i wdrażanie | Przechowywanie wersji jednostek (model, dane, przepływy pracy), Automatyzacja przepływu pracy, integracja z narzędziami CICD [i inne](concept-model-management-and-deployment.md) |
| Format modelu | Format własnościowy, Studio (klasyczny) | Wiele obsługiwanych formatów w zależności od typu zadania szkoleniowego |
| Automatyczne szkolenie modelu i dostrajanie parametrów |  Nieobsługiwane | [Obsługiwane w zestawie SDK i obszarze roboczym Visual](concept-automated-ml.md) | 
| Wykrywanie dryfowania danych | Nieobsługiwane | [Obsługiwane w zestawach SDK i Visual Workspace](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Migrowanie z Machine Learning Studio (klasyczne)

Obecnie nie ma możliwości migrowania zasobów Studio (klasycznych) do programu Azure Machine Learning Designer (wersja zapoznawcza). Bieżący Studio (klasyczny) użytkownicy mogą nadal korzystać z swoich zasobów uczenia maszynowego. Zachęcamy jednak wszystkich użytkowników do rozważania przy użyciu projektanta, który zapewnia znane środowisko przeciągania i upuszczania z ulepszonym przepływem pracy **oraz** skalowalnością, kontrolą wersji i zabezpieczeniami przedsiębiorstwa.

## <a name="get-started-with-azure-machine-learning"></a>Wprowadzenie do Azure Machine Learning

Następujące zasoby mogą pomóc Ci rozpocząć pracę z Azure Machine Learning. 

- Zapoznaj się z [omówieniem Azure Machine Learning](overview-what-is-azure-ml.md).

- Tworzenie [pierwszego eksperymentu za pomocą zestawu SDK języka Python](tutorial-1st-experiment-sdk-setup.md).

- [Utwórz swój pierwszy potok projektanta](tutorial-designer-automobile-price-train-score.md) , aby przewidzieć ceny dla samochodów.

![Przykład projektanta Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Następne kroki

Oprócz funkcji przeciągania i upuszczania w projektancie Azure Machine Learning dostępne są inne narzędzia:  
  + [Korzystanie z notesów Python do uczenia & Wdrażanie modeli ML](tutorial-1st-experiment-sdk-setup.md)
  + [Używanie R Markdown do uczenia & wdrażania modeli ML](tutorial-1st-r-experiment.md) 
  + [Używanie automatycznej uczenia maszynowego do uczenia & Wdrażanie modeli ML](tutorial-first-experiment-automated-ml.md)  
  + [Korzystanie z interfejsu wiersza polecenia Machine Learning w celu uczenia i wdrożenia modelu](tutorial-train-deploy-model-cli.md)

