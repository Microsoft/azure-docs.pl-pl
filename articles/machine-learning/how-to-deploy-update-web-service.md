---
title: Aktualizowanie wdrożonej usługi sieci Web
author: gvashishtha
ms.service: machine-learning
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: a561a5fd865eba88f63690d39969961a87335def
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544592"
---
# <a name="update-a-deployed-web-service"></a>Aktualizowanie wdrożonej usługi sieci Web

W tym artykule przedstawiono, jak wdrożyć usługę sieci Web, która została wdrożona przy użyciu Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że usługa sieci Web została już wdrożona za pomocą Azure Machine Learning. Jeśli chcesz dowiedzieć się, jak wdrożyć usługę sieci Web, [wykonaj następujące kroki](how-to-deploy-and-where.md).

## <a name="update-web-service"></a>Aktualizowanie usługi sieci Web

Aby zaktualizować usługę sieci Web, należy użyć `update` metody. Usługę sieci Web można zaktualizować tak, aby korzystała z nowego modelu, nowego skryptu wprowadzania lub nowych zależności, które można określić w konfiguracji wnioskowania. Aby uzyskać więcej informacji, zobacz dokumentację usługi [WebService. Update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

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
> Aby zaktualizować usługę tak, aby korzystała z nowego skryptu lub środowiska wprowadzania, Utwórz [plik konfiguracji wnioskowania](/azure/machine-learning/reference-azure-machine-learning-cli#inference-configuration-schema) i określ go za pomocą `ic` parametru.

Aby uzyskać więcej informacji, zobacz [AZ ml Service Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) documentation.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z niepowodzeniem wdrożenia](how-to-troubleshoot-deployment.md)
* [Wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Tworzenie aplikacji klienckich w celu korzystania z usług sieci Web](how-to-consume-web-service.md)
* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Użyj protokołu TLS do zabezpieczenia usługi sieci Web za pomocą Azure Machine Learning](how-to-secure-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Tworzenie alertów zdarzeń i wyzwalaczy na potrzeby wdrożeń modeli](how-to-use-event-grid.md)
