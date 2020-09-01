---
title: dołączanie pliku
description: dołączanie pliku
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/19/2020
ms.openlocfilehash: 54328c7ae4fe2112a718bd82b9f7330c9e95ec69
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146741"
---
**Cele obliczeń mogą być ponownie używane z jednego zadania szkoleniowego do następnego**. Na przykład po dołączeniu zdalnej maszyny wirtualnej do obszaru roboczego można użyć jej ponownie dla wielu zadań.  W przypadku potoków uczenia maszynowego Użyj odpowiedniego [kroku potoku](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) dla każdego elementu docelowego obliczeń.

|Cele szkoleniowe &nbsp;|[Zautomatyzowane uczenie maszynowe](../articles/machine-learning/concept-automated-ml.md) | [Potoki uczenia maszynowego](../articles/machine-learning/concept-ml-pipelines.md) | [Projektant Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Komputer lokalny](../articles/machine-learning/how-to-create-attach-compute-sdk.md#local)| tak | &nbsp; | &nbsp; |
|[Azure Machine Learning klaster obliczeniowy](../articles/machine-learning/how-to-create-attach-compute-sdk.md#amlcompute)| tak & <br/>&nbsp;dostrajanie parametrów | tak | tak |
|[Wystąpienie obliczeniowe usługi Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-sdk.md#instance) | tak & <br/>Dostrajanie parametrów | tak |  |
|[Zdalna maszyna wirtualna](../articles/machine-learning/how-to-create-attach-compute-sdk.md#vm) | tak & <br/>Dostrajanie parametrów | tak | &nbsp; |
|[Azure &nbsp; datakostki](../articles/machine-learning/how-to-create-attach-compute-sdk.md#databricks)| tak (tylko tryb lokalny zestawu SDK) | tak | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-attach-compute-sdk.md#adla) | &nbsp; | tak | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-create-attach-compute-sdk.md#hdinsight) | &nbsp; | tak | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-create-attach-compute-sdk.md#azbatch) | &nbsp; | tak | &nbsp; |
