---
author: peterclu
ms.service: machine-learning
ms.topic: include
ms.date: 03/08/2021
ms.author: peterlu
ms.openlocfilehash: ff64a0948402ff152e45bd4702d986f51b9547aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563207"
---
Poniższa tabela zawiera podsumowanie podstawowych różnic między ML Studio (klasyczne) i Azure Machine Learning.

| Cecha | ML Studio (klasyczny) | Azure Machine Learning |
|---| --- | --- |
| Przeciąganie i upuszczanie interfejsu | Środowisko klasyczne | Zaktualizowane środowisko — [projektant Azure Machine Learning](../articles/machine-learning/concept-designer.md)| 
| Zestawy SDK kodu | Nieobsługiwane | W pełni zintegrowane z [Azure Machine Learning Python](/python/api/overview/azure/ml/) i [języka R](https://github.com/Azure/azureml-sdk-for-r) SDK |
| Experiment | Skalowalne (limit danych szkolenia 10 GB) | Skalowanie za pomocą elementu docelowego obliczeń |
| Szkoleniowe cele obliczeniowe | Własnościowy obiekt docelowy obliczeń, tylko obsługa procesora CPU | Szeroki zakres dostosowywalnych [obiektów docelowych obliczeń szkoleniowych](../articles/machine-learning/concept-compute-target.md#train). Obejmuje obsługę procesora GPU i procesorów | 
| Cele obliczeń wdrożenia | Własny format usługi sieci Web, niedostosowywalny | Szeroka gama [elementów docelowych obliczeniowych wdrożenia](../articles/machine-learning/concept-compute-target.md#deploy). Obejmuje obsługę procesora GPU i procesorów |
| Potok ML | Nieobsługiwane | Twórz elastyczne, modularne [potoki](../articles/machine-learning/concept-ml-pipelines.md) do automatyzowania przepływów pracy |
| MLOps | Podstawowe Zarządzanie modelami i ich wdrażanie; Wdrożenia wyłącznie procesora | Przechowywanie wersji jednostek (model, dane, przepływy pracy), Automatyzacja przepływów pracy, integracja z narzędziami CICD, wdrożenia procesora CPU i procesora GPU [itd](../articles/machine-learning/concept-model-management-and-deployment.md) . |
| Format modelu | Format własnościowy, Studio (klasyczny) | Wiele obsługiwanych formatów w zależności od typu zadania szkoleniowego |
| Automatyczne szkolenie modelu i dostrajanie parametrów |  Nieobsługiwane | [Obsługiwane](../articles/machine-learning/concept-automated-ml.md). Opcje Code-First i No-Code. | 
| Wykrywanie dryfowania danych | Nieobsługiwane | [Obsługiwane](../articles/machine-learning/how-to-monitor-datasets.md) |
| Projekty etykietowania danych | Nieobsługiwane | [Obsługiwane](../articles/machine-learning/how-to-create-labeling-projects.md) |
| Kontrola dostępu oparta na rolach (RBAC) | Tylko rola współautor i właściciel | [Elastyczna definicja roli i kontrolka RBAC](../articles/machine-learning/how-to-assign-roles.md) |
| Galeria AI | Obsługiwane ( [https://gallery.azure.ai/](https://gallery.azure.ai/) ) | Nieobsługiwane <br><br> Poznaj [przykładowe notesy zestawu SDK](https://github.com/Azure/MachineLearningNotebooks)w języku Python. |