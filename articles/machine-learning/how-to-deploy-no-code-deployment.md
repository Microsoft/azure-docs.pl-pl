---
title: Bez wdrożenia kodu (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Wdrożenie kodu nie pozwala na wdrożenie modelu jako usługi sieci Web bez konieczności ręcznego tworzenia skryptu wejścia.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: deploy
ms.reviewer: larryfr
ms.openlocfilehash: a17126695aa5138d1df7fd17cfaa2f5f75ad1004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93324916"
---
# <a name="preview-no-code-model-deployment"></a>Przeglądania Wdrożenie modelu bez kodu

Wdrożenie modelu bez kodu jest obecnie dostępne w wersji zapoznawczej i obsługuje następujące platformy uczenia maszynowego:

## <a name="tensorflow-savedmodel-format"></a>TensorFlow SavedModel
Modele TensorFlow muszą być zarejestrowane w **formacie SavedModel** do pracy z wdrożeniem modelu bez kodu.

[Ten link](https://www.tensorflow.org/guide/saved_model) zawiera informacje na temat tworzenia SavedModel.

Obsługujemy wszystkie wersje TensorFlow, które są wymienione w sekcji "Tagi" na [TensorFlow obsługującym DockerHub](https://registry.hub.docker.com/r/tensorflow/serving/tags).

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

## <a name="onnx-models"></a>Modele ONNX

Rejestracja i wdrożenie modelu ONNX są obsługiwane w przypadku grafu wnioskowania ONNX. Kroki preprocesora i PostProcess nie są obecnie obsługiwane.

Oto przykład sposobu rejestrowania i wdrażania modelu ONNX MNIST ręcznie:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

Aby wypróbować model, zobacz temat [Korzystanie z modelu Azure Machine Learning wdrożonego jako usługa sieci Web](./how-to-consume-web-service.md). Wiele projektów ONNX korzysta z plików protobuf, aby kompaktować dane szkoleniowe i weryfikacyjne, co może utrudnić znać format danych oczekiwany przez usługę. Jako deweloper modelu należy dokumentować dla deweloperów:

* Format wejściowy (JSON lub binarny)
* Kształt danych wejściowych i typ (na przykład tablica zmiennoprzecinkowa kształtu [100100, 3])
* Informacje o domenie (na przykład dla obrazu, przestrzeń kolorów, kolejność składników i czy wartości są znormalizowane)

Jeśli używasz Pytorch, [Eksportowanie modeli z Pytorch do ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) zawiera szczegółowe informacje dotyczące konwersji i ograniczeń. 

## <a name="scikit-learn-models"></a>Scikit — uczenie modeli

Żadne wdrożenie modelu kodu nie jest obsługiwane dla wszystkich wbudowanych typów modeli scikit.

Oto przykład sposobu rejestrowania i wdrażania modelu skryptu sklearn bez dodatkowego kodu:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

> [!NOTE]
> Modele, które obsługują predict_proba, będą używać tej metody domyślnie. Aby zastąpić ten sposób, można zmodyfikować treść wpisu w następujący sposób:

```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

> [!NOTE]
> Te zależności są zawarte we wstępnie skompilowanym kontenerze wnioskowania scikit:

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
```
## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z niepowodzeniem wdrożenia](how-to-troubleshoot-deployment.md)
* [Wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Tworzenie aplikacji klienckich w celu korzystania z usług sieci Web](how-to-consume-web-service.md)
* [Aktualizowanie usługi internetowej](how-to-deploy-update-web-service.md)
* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS](how-to-secure-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Tworzenie alertów zdarzeń i wyzwalaczy na potrzeby wdrożeń modeli](how-to-use-event-grid.md)