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
ms.date: 08/19/2020
ms.openlocfilehash: bbd32f7e40e3039a69a95b4458de86ff35f26867
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91642949"
---
**Cele obliczeń mogą być ponownie używane z jednego zadania szkoleniowego do następnego**. Na przykład po dołączeniu zdalnej maszyny wirtualnej do obszaru roboczego można użyć jej ponownie dla wielu zadań.  W przypadku potoków uczenia maszynowego Użyj odpowiedniego [kroku potoku](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) dla każdego elementu docelowego obliczeń.

|Cele szkoleniowe &nbsp;|[Zautomatyzowane uczenie maszynowe](../articles/machine-learning/concept-automated-ml.md) | [Potoki uczenia maszynowego](../articles/machine-learning/concept-ml-pipelines.md) | [Projektant usługi Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Komputer lokalny](../articles/machine-learning/how-to-create-attach-compute-sdk.md#local)| tak | &nbsp; | &nbsp; |
|[Azure Machine Learning klaster obliczeniowy](../articles/machine-learning/how-to-create-attach-compute-sdk.md#amlcompute)| tak & <br/>&nbsp;dostrajanie parametrów | tak | tak |
|[Wystąpienie obliczeniowe usługi Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-sdk.md#instance) | tak (za poorednictwem zestawu SDK) | tak |  |
|[Zdalna maszyna wirtualna](../articles/machine-learning/how-to-create-attach-compute-sdk.md#vm) | tak & <br/>Dostrajanie parametrów | tak | &nbsp; |
|[Azure &nbsp; datakostki](../articles/machine-learning/how-to-create-attach-compute-sdk.md#databricks)| tak (tylko tryb lokalny zestawu SDK) | tak | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-attach-compute-sdk.md#adla) | &nbsp; | tak | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-create-attach-compute-sdk.md#hdinsight) | &nbsp; | tak | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-create-attach-compute-sdk.md#azbatch) | &nbsp; | tak | &nbsp; |
