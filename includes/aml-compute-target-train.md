---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/17/2020
ms.openlocfilehash: 7c89527218e34bea0819a1e2446441216cde45ae
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104717"
---
**Cele obliczeń mogą być ponownie używane z jednego zadania szkoleniowego do następnego.** Na przykład po dołączeniu zdalnej maszyny wirtualnej do obszaru roboczego można użyć jej ponownie dla wielu zadań. W przypadku potoków uczenia maszynowego Użyj odpowiedniego [kroku potoku](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) dla każdego elementu docelowego obliczeń.

W przypadku większości zadań można użyć dowolnego z poniższych zasobów dla celu obliczeń szkoleniowych. Nie wszystkie zasoby mogą być używane do automatycznego uczenia maszynowego, potoków uczenia maszynowego lub projektanta.

|Cele szkoleniowe &nbsp;|[Zautomatyzowane uczenie maszynowe](../articles/machine-learning/concept-automated-ml.md) | [Potoki uczenia maszynowego](../articles/machine-learning/concept-ml-pipelines.md) | [Projektant usługi Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Komputer lokalny](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Tak | &nbsp; | &nbsp; |
|[Azure Machine Learning klaster obliczeniowy](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Tak | Tak | Tak |
|[Wystąpienie obliczeniowe usługi Azure Machine Learning](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Tak (za poorednictwem zestawu SDK)  | Tak |  |
|[Zdalna maszyna wirtualna](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Tak  | Tak | &nbsp; |
|[Azure &nbsp; datakostki](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Tak (tylko tryb lokalny zestawu SDK) | Tak | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Tak | &nbsp; |
|[Usługa Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Tak | &nbsp; |
|[Usługa Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Tak | &nbsp; |

> [!TIP]
> Wystąpienie obliczeniowe ma dysk systemu operacyjnego o 120 GB. Jeśli zabrakło miejsca na dysku, przed podjęciem próby zatrzymania lub ponownego uruchomienia wystąpienia obliczeniowego Wyczyść wystarczającą ilość miejsca.
