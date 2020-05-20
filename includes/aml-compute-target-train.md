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
ms.date: 12/04/2019
ms.openlocfilehash: 764e4c33182499b922499c798d57fd05d8859742
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673394"
---
**Cele obliczeń mogą być ponownie używane z jednego zadania szkoleniowego do następnego**. Na przykład po dołączeniu zdalnej maszyny wirtualnej do obszaru roboczego można użyć jej ponownie dla wielu zadań.  W przypadku potoków uczenia maszynowego Użyj odpowiedniego [kroku potoku](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) dla każdego elementu docelowego obliczeń.

|Cele szkoleniowe &nbsp;|[Zautomatyzowane uczenie maszynowe](../articles/machine-learning/concept-automated-ml.md) | [Potoki uczenia maszynowego](../articles/machine-learning/concept-ml-pipelines.md) | [Projektant Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Komputer lokalny](../articles/machine-learning/how-to-set-up-training-targets.md#local)| tak | &nbsp; | &nbsp; |
|[Azure Machine Learning klaster obliczeniowy](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| tak & <br/>&nbsp;dostrajanie parametrów | tak | tak |
|[Wystąpienie obliczeniowe Azure Machine Learning](../articles/machine-learning/concept-compute-instance.md) | tak & <br/>Dostrajanie parametrów | tak |  |
|[Zdalna maszyna wirtualna](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | tak & <br/>Dostrajanie parametrów | tak | &nbsp; |
|[Azure &nbsp; datakostki](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| tak (tylko tryb lokalny zestawu SDK) | tak | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | tak | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | tak | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | tak | &nbsp; |
