---
title: Publikowanie potoków ML
titleSuffix: Azure Machine Learning
description: Uruchamiaj przepływy pracy uczenia maszynowego przy użyciu potoków uczenia maszynowego i zestawu Azure Machine Learning SDK dla języka Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: 08529d1bb50a1a5d5b3c7d0296aa36f021f45e98
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646091"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>Publikowanie i śledzenie potoków uczenia maszynowego

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule przedstawiono sposób udostępniania potoku uczenia maszynowego współpracownikom lub klientom.

Potoki usługi Machine Learning to przepływy pracy wielokrotnego użytku dla zadań uczenia maszynowego. Jedną z korzyści płynących z potoku jest zwiększona Współpraca. Możesz również potoki wersji, umożliwiając klientom korzystanie z bieżącego modelu podczas pracy nad nową wersją. 

## <a name="prerequisites"></a>Wymagania wstępne

* Tworzenie [obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md) do przechowywania wszystkich zasobów potoku

* [Skonfiguruj środowisko programistyczne](how-to-configure-environment.md) , aby zainstalować zestaw SDK Azure Machine Learning, lub Użyj [wystąpienia obliczeniowego Azure Machine Learning](concept-compute-instance.md) z już zainstalowanym zestawem SDK

* Utwórz i uruchom potok uczenia maszynowego, na przykład w następującym [samouczku: Tworzenie potoku Azure Machine Learning na potrzeby oceniania partii](tutorial-pipeline-batch-scoring-classification.md). Aby poznać inne opcje, zobacz [Tworzenie i uruchamianie potoków uczenia maszynowego za pomocą zestawu SDK Azure Machine Learning](how-to-create-your-first-pipeline.md)

## <a name="publish-a-pipeline"></a>Publikowanie potoku

Po utworzeniu potoku i uruchomieniu można opublikować potok, aby działał z innymi danymi wejściowymi. Dla punktu końcowego REST już opublikowanego potoku, aby akceptować parametry, należy skonfigurować potok tak, aby używał `PipelineParameter` obiektów dla argumentów, które będą się różnić.

1. Aby utworzyć parametr potoku, użyj obiektu [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py&preserve-view=true) z wartością domyślną.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Dodaj ten `PipelineParameter` obiekt jako parametr do dowolnego z kroków w potoku w następujący sposób:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Opublikuj ten potok, który zostanie zaakceptowany przez wywoływany parametr.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>Uruchom opublikowany potok

Wszystkie opublikowane potoki mają punkt końcowy REST. Za pomocą punktu końcowego potoku można wyzwolić uruchomienie potoku z dowolnego systemu zewnętrznego, w tym klientów spoza języka Python. Ten punkt końcowy włącza "zarządzaną powtarzalność" w ramach oceniania wsadowego i scenariuszy ponownego szkolenia.

Aby wywołać poprzedni potok, wymagany jest token nagłówka Azure Active Directory Authentication. Ten token jest opisany w dokumentacji [klasy AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py&preserve-view=true) i w obszarze [uwierzytelnianie w Azure Machine Learning](https://aka.ms/pl-restep-auth) notesie.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Tworzenie punktu końcowego potoku z wersją

Można utworzyć punkt końcowy potoku z wieloma opublikowanymi potokami. Zapewnia to stały punkt końcowy REST podczas iteracji i aktualizacji potoków ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>Przesyłanie zadania do punktu końcowego potoku

Można przesłać zadanie do domyślnej wersji punktu końcowego potoku:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

Możesz również przesłać zadanie do określonej wersji:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Tę samą funkcję można wykonać przy użyciu interfejsu API REST:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

## <a name="use-published-pipelines-in-the-studio"></a>Używanie opublikowanych potoków w Studio

Możesz również uruchomić opublikowany potok z programu Studio:

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com).

1. [Wyświetlanie obszaru roboczego](how-to-manage-workspace.md#view).

1. Po lewej stronie wybierz pozycję **punkty końcowe**.

1. W górnej części wybierz pozycję **punkty końcowe potoku**.
 ![Lista opublikowanych potoków uczenia maszynowego](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Wybierz konkretny potok, który ma zostać uruchomiony, zużyty lub przejrzany wyniki poprzednich przebiegów punktu końcowego potoku.

## <a name="disable-a-published-pipeline"></a>Wyłącz opublikowany potok

Aby ukryć potok z listy opublikowanych potoków, należy go wyłączyć w programie Studio lub z zestawu SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Można włączyć ją ponownie za pomocą `p.enable()` . Aby uzyskać więcej informacji, zobacz [PublishedPipeline klasy](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py&preserve-view=true) Reference.

## <a name="next-steps"></a>Następne kroki

- [Te notesy Jupyter w usłudze GitHub](https://aka.ms/aml-pipeline-readme) umożliwiają dalsze Eksplorowanie potoków usługi Machine Learning.
- Zapoznaj się z dokumentacją zestawu SDK dla pakietu [Azure-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py&preserve-view=true) oraz pakietem [kroków potoków usługi Azure](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true) .
- Zobacz Porady [dotyczące debugowania](how-to-debug-pipelines.md) i rozwiązywania problemów z potokami.
