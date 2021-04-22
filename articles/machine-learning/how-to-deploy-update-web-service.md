---
title: Aktualizowanie usług sieci Web
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak odświeżyć usługę internetową, która została już wdrożona w Azure Machine Learning. Możesz zaktualizować ustawienia, takie jak model, środowisko i skrypt wejściowy.
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.custom: deploy
ms.openlocfilehash: 098dc4be33c82faff1b85a5b7224ecaf1be64944
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875471"
---
# <a name="update-a-deployed-web-service"></a>Aktualizowanie wdrożonej usługi internetowej

Z tego artykułu dowiesz się, jak zaktualizować usługę internetową wdrożoną za pomocą Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że wdrożono już usługę internetową z Azure Machine Learning. Jeśli chcesz dowiedzieć się, jak wdrożyć usługę internetową, [wykonaj następujące kroki.](how-to-deploy-and-where.md)

## <a name="update-web-service"></a>Aktualizowanie usługi internetowej

Aby zaktualizować usługę internetową, użyj `update` metody . Usługę internetową można zaktualizować tak, aby używać nowego modelu, nowego skryptu wprowadzania lub nowych zależności, które można określić w konfiguracji wnioskowania. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą usługi [Webservice.update.](/python/api/azureml-core/azureml.core.webservice.webservice.webservice#update--args-)

Zobacz [AKS Service Update Method (Metoda aktualizacji usługi AKS).](/python/api/azureml-core/azureml.core.webservice.akswebservice#update-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)

Zobacz [ACI Service Update Method (Metoda aktualizacji usługi ACI).](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#update-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)

> [!IMPORTANT]
> Podczas tworzenia nowej wersji modelu należy ręcznie zaktualizować każdą usługę, która ma być z niego korzystać.
>
> Nie można użyć zestawu SDK do zaktualizowania usługi internetowej opublikowanej za pomocą Azure Machine Learning projektanta.

**Używanie zestawu SDK**

Poniższy kod pokazuje, jak za pomocą zestawu SDK zaktualizować model, środowisko i skrypt wejściowy dla usługi internetowej:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
service.wait_for_deployment(show_output=True)
print(service.state)
print(service.get_logs())
```

**Korzystanie z interfejsu wiersza polecenia**

Usługę internetową można również zaktualizować przy użyciu interfejsu wiersza polecenia uczenia maszynowego. W poniższym przykładzie pokazano rejestrowanie nowego modelu, a następnie aktualizowanie usługi internetowej w celu używania nowego modelu:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> W tym przykładzie dokument JSON jest używany do przekazania informacji o modelu z polecenia rejestracji do polecenia update.
>
> Aby zaktualizować usługę do używania nowego [](./reference-azure-machine-learning-cli.md#inference-configuration-schema) skryptu lub środowiska wprowadzania, utwórz plik konfiguracji wnioskowania i określ go za pomocą `ic` parametru .

Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą aktualizacji usługi az ml.](/cli/azure/ml/service#az_ml_service_update)

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z niepowodzeniem wdrożenia](how-to-troubleshoot-deployment.md)
* [Wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Tworzenie aplikacji klienckich do obsługi usług internetowych](how-to-consume-web-service.md)
* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS](how-to-secure-web-service.md)
* [Monitorowanie modeli Azure Machine Learning za pomocą Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Tworzenie alertów zdarzeń i wyzwalaczy dla wdrożeń modelu](how-to-use-event-grid.md)
