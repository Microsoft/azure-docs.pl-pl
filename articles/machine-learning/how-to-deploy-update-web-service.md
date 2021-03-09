---
title: Aktualizowanie usług sieci Web
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak odświeżyć usługę sieci Web, która została już wdrożona w Azure Machine Learning. Można aktualizować ustawienia, takie jak model, środowisko i skrypt wprowadzania.
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.custom: deploy
ms.openlocfilehash: da018f212844e1587ad0e434902e49765f72e521
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520101"
---
# <a name="update-a-deployed-web-service"></a>Aktualizowanie wdrożonej usługi sieci Web

W tym artykule dowiesz się, jak zaktualizować usługę sieci Web, która została wdrożona przy użyciu Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że usługa sieci Web została już wdrożona za pomocą Azure Machine Learning. Jeśli chcesz dowiedzieć się, jak wdrożyć usługę sieci Web, [wykonaj następujące kroki](how-to-deploy-and-where.md).

## <a name="update-web-service"></a>Aktualizowanie usługi internetowej

Aby zaktualizować usługę sieci Web, należy użyć `update` metody. Usługę sieci Web można zaktualizować tak, aby korzystała z nowego modelu, nowego skryptu wprowadzania lub nowych zależności, które można określić w konfiguracji wnioskowania. Aby uzyskać więcej informacji, zobacz dokumentację usługi [WebService. Update](/python/api/azureml-core/azureml.core.webservice.webservice.webservice#update--args-).

Zobacz [AKS usługi Update.](/python/api/azureml-core/azureml.core.webservice.akswebservice#update-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)

Zobacz [ACI usługi Update.](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#update-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)

> [!IMPORTANT]
> Podczas tworzenia nowej wersji modelu należy ręcznie zaktualizować każdą usługę, która ma być używana.
>
> Nie można użyć zestawu SDK w celu zaktualizowania usługi sieci web opublikowanej przy użyciu programu Azure Machine Learning Designer.

**Używanie zestawu SDK**

Poniższy kod pokazuje, jak używać zestawu SDK do aktualizowania skryptu modelu, środowiska i wpisu dla usługi sieci Web:

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

Usługę sieci Web można również zaktualizować za pomocą interfejsu wiersza polecenia ML. Poniższy przykład ilustruje rejestrowanie nowego modelu, a następnie aktualizowanie usługi sieci Web do korzystania z nowego modelu:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> W tym przykładzie dokument JSON jest używany do przekazywania informacji o modelu z polecenia rejestracji do polecenia Update.
>
> Aby zaktualizować usługę tak, aby korzystała z nowego skryptu lub środowiska wprowadzania, Utwórz [plik konfiguracji wnioskowania](./reference-azure-machine-learning-cli.md#inference-configuration-schema) i określ go za pomocą `ic` parametru.

Aby uzyskać więcej informacji, zobacz [AZ ml Service Update](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-update) documentation.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z niepowodzeniem wdrożenia](how-to-troubleshoot-deployment.md)
* [Wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Tworzenie aplikacji klienckich w celu korzystania z usług sieci Web](how-to-consume-web-service.md)
* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS](how-to-secure-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Tworzenie alertów zdarzeń i wyzwalaczy na potrzeby wdrożeń modeli](how-to-use-event-grid.md)
