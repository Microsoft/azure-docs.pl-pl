---
title: Jak wdrażać modele na Azure Container Instances
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wdrażać modele Azure Machine Learning jako usługę internetową przy użyciu Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/12/2020
ms.openlocfilehash: 845a146d9e3f920f3313a80f1bb8c845cb781f37
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875543"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Wdrażanie modelu w usłudze Azure Container Instances

Dowiedz się, jak używać Azure Machine Learning do wdrażania modelu jako usługi internetowej na platformie Azure Container Instances (ACI). Użyj Azure Container Instances, jeśli jeden z następujących warunków jest prawdziwy:

- Musisz szybko wdrożyć i zweryfikować model. Nie musisz wcześniej tworzyć kontenerów ACI. Są one tworzone w ramach procesu wdrażania.
- Testowany jest model, który jest w trakcie opracowywania. 

Aby uzyskać informacje o limitach przydziału i dostępności regionów dla usługi ACI, zobacz artykuł Quotas and region availability for Azure Container Instances article [(Limity](../container-instances/container-instances-quotas.md) przydziału i dostępność regionów dla Azure Container Instances usługi ACI).

> [!IMPORTANT]
> Zdecydowanie zaleca się debugowanie lokalnie przed wdrożeniem w usłudze internetowej. Aby uzyskać więcej informacji, zobacz [Debugowanie lokalne](./how-to-troubleshoot-deployment-local.md)
>
> Możesz również skorzystać z usługi Azure Machine Learning — [Wdrażanie w notesie lokalnym](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie Azure Machine Learning roboczego.](how-to-manage-workspace.md)

- Model uczenia maszynowego zarejestrowany w obszarze roboczym. Jeśli nie masz zarejestrowanego modelu, zobacz [Jak i gdzie wdrażać modele.](how-to-deploy-and-where.md)

- Rozszerzenie [interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md)platformy Azure Machine Learning service , Azure Machine Learning Python [SDK](/python/api/overview/azure/ml/intro)lub rozszerzenie [Azure Machine Learning Visual Studio Code .](tutorial-setup-vscode-extension.md)

- We __fragmentach__ kodu w języku Python w tym artykule założono, że ustawiono następujące zmienne:

    * `ws` — Ustaw na swój obszar roboczy.
    * `model` — ustaw wartość na zarejestrowany model.
    * `inference_config` — ustaw na konfigurację wnioskowania dla modelu.

    Aby uzyskać więcej informacji na temat ustawiania tych zmiennych, zobacz [How and where to deploy models (Jak i gdzie wdrażać modele).](how-to-deploy-and-where.md)

- Fragmenty __kodu__ interfejsu wiersza polecenia w tym artykule zakładają, że utworzono `inferenceconfig.json` dokument. Aby uzyskać więcej informacji na temat tworzenia tego dokumentu, zobacz How and where to deploy models (Jak [i gdzie wdrażać modele).](how-to-deploy-and-where.md)

## <a name="limitations"></a>Ograniczenia

* W przypadku Azure Container Instances w sieci wirtualnej sieć wirtualna musi znajdować się w tej samej grupie zasobów co Azure Machine Learning roboczy.
* W przypadku Azure Container Instances wewnątrz sieci wirtualnej nie Azure Container Registry (ACR) dla obszaru roboczego.

Aby uzyskać więcej informacji, [zobacz How to secure inferencing with virtual networks](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci)(Jak zabezpieczyć wnioskowanie przy użyciu sieci wirtualnych).

## <a name="deploy-to-aci"></a>Wdrażanie w usłudze ACI

Aby wdrożyć model do Azure Container Instances, utwórz konfigurację wdrożenia __opisującą__ wymagane zasoby obliczeniowe. Na przykład liczba rdzeni i pamięć. Potrzebna jest również konfiguracja __wnioskowania__ opisującą środowisko potrzebne do hostowania modelu i usługi internetowej. Aby uzyskać więcej informacji na temat tworzenia konfiguracji wnioskowania, zobacz How and where to deploy models (Jak [i gdzie wdrażać modele).](how-to-deploy-and-where.md)

> [!NOTE]
> * ACI jest odpowiednia tylko w przypadku małych modeli o rozmiarze poniżej 1 GB. 
> * Zalecamy używanie usługi AKS z jednym węzłem do tworzenia i testowania większych modeli.
> * Liczba modeli do wdrożenia jest ograniczona do 1000 modeli na wdrożenie (na kontener). 

### <a name="using-the-sdk"></a>Używanie zestawu SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Aby uzyskać więcej informacji na temat klas, metod i parametrów używanych w tym przykładzie, zobacz następujące dokumenty referencyjne:

* [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model.deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

### <a name="using-the-azure-cli"></a>Przy użyciu interfejsu wiersza polecenia platformy Azure

Aby wdrożyć przy użyciu interfejsu wiersza polecenia, użyj następującego polecenia. Zastąp `mymodel:1` nazwą i wersją zarejestrowanego modelu. Zastąp `myservice` nazwą, aby nadać tej usłudze:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Aby uzyskać więcej informacji, zobacz informacje dotyczące [wdrażania modelu az ml.](/cli/azure/ml/model#az_ml_model_deploy) 

## <a name="using-vs-code"></a>Korzystanie z programu VS Code

Zobacz [wdrażanie modeli za pomocą VS Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Nie musisz tworzyć kontenera ACI w celu wcześniejszego przetestowania. Kontenery ACI są tworzone zgodnie z potrzebami.

> [!IMPORTANT]
> Dołączymy identyfikator obszaru roboczego ze skrótem do wszystkich bazowych zasobów ACI, które są tworzone. Wszystkie nazwy ACI z tego samego obszaru roboczego będą mieć taki sam sufiks. Nazwa Azure Machine Learning service będzie nadal taka sama jak nazwa klienta "service_name", a wszystkie interfejsy API zestawu SDK Azure Machine Learning użytkownika nie wymagają żadnych zmian. Nie udzielamy żadnych gwarancji co do nazw zasobów bazowych, które są tworzone.

## <a name="next-steps"></a>Następne kroki

* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Rozwiązywanie problemów z wdrażaniem](how-to-troubleshoot-deployment.md)
* [Aktualizowanie usługi internetowej](how-to-deploy-update-web-service.md)
* [Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS](how-to-secure-web-service.md)
* [Wykorzystanie modelu uczenia maszynowego wdrożonego jako usługa internetowa](how-to-consume-web-service.md)
* [Monitorowanie modeli Azure Machine Learning za pomocą Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)