---
title: Wdróż modele ml do Azure App Service (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać Azure Machine Learning do wdrożenia przeszkolonego modelu ML w aplikacji internetowej przy użyciu Azure App Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: 3b1b416f3fec9e40261a82c88260c041918c1424
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522006"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Wdróż model uczenia maszynowego w Azure App Service (wersja zapoznawcza)


Dowiedz się, jak wdrożyć model na podstawie Azure Machine Learning jako aplikacji sieci Web w programie Azure App Service.

> [!IMPORTANT]
> Chociaż obie Azure Machine Learning i Azure App Service są ogólnie dostępne, możliwość wdrażania modelu z usługi Machine Learning na App Service jest w wersji zapoznawczej.

Za pomocą Azure Machine Learning można tworzyć obrazy platformy Docker z przeszkolonych modeli uczenia maszynowego. Ten obraz zawiera usługę sieci Web, która odbiera dane, przesyła ją do modelu, a następnie zwraca odpowiedź. Azure App Service można użyć do wdrożenia obrazu i zapewnia następujące funkcje:

* Zaawansowane [uwierzytelnianie](../app-service/configure-authentication-provider-aad.md) na potrzeby zwiększonych zabezpieczeń. Metody uwierzytelniania obejmują zarówno Azure Active Directory, jak i uwierzytelnianie wieloskładnikowe.
* [Skalowanie automatyczne](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json) bez konieczności ponownego wdrażania.
* [Obsługa protokołu TLS](../app-service/configure-ssl-certificate-in-code.md) w celu zapewnienia bezpiecznej komunikacji między klientami a usługą.

Aby uzyskać więcej informacji na temat funkcji zapewnianych przez Azure App Service, zobacz [omówienie App Service](../app-service/overview.md).

> [!IMPORTANT]
> Jeśli potrzebujesz możliwości rejestrowania danych oceniających używanych przez wdrożony model lub wyniki oceny, zamiast tego należy przeprowadzić wdrożenie w usłudze Azure Kubernetes Service. Aby uzyskać więcej informacji, zobacz [zbieranie danych w modelach produkcyjnych](how-to-enable-data-collection.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie obszaru roboczego](how-to-manage-workspace.md) .
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Model uczenia maszynowego zarejestrowany w Twoim obszarze roboczym. Jeśli nie masz modelu, Skorzystaj z [samouczka klasyfikacji obrazów: uczenie modelu](tutorial-train-models-with-aml.md) do uczenia i zarejestrowania go.

    > [!IMPORTANT]
    > W fragmentach kodu w tym artykule przyjęto założenie, że ustawiono następujące zmienne:
    >
    > * `ws` — Obszar roboczy Azure Machine Learning.
    > * `model` -Zarejestrowany model, który zostanie wdrożony.
    > * `inference_config` -Konfiguracja wnioskowania dla modelu.
    >
    > Aby uzyskać więcej informacji na temat ustawiania tych zmiennych, zobacz [Deploying Models with Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia

Przed wdrożeniem należy zdefiniować, co jest potrzebne do uruchomienia modelu jako usługi sieci Web. Na poniższej liście opisano główne elementy, które są związane z wdrożeniem:

* __Skrypt wejściowy__. Ten skrypt akceptuje żądania, ocenia żądanie przy użyciu modelu i zwraca wyniki.

    > [!IMPORTANT]
    > Skrypt wejścia jest specyficzny dla modelu; musi on zrozumieć format danych żądania przychodzącego, format danych oczekiwanych przez model i format danych zwracanych do klientów.
    >
    > Jeśli dane żądania są w formacie, którego nie można używać w modelu, skrypt może przekształcić go w akceptowalny format. Może również przekształcić odpowiedź przed powrotem do klienta programu.

    > [!IMPORTANT]
    > Zestaw SDK Azure Machine Learning nie pozwala na dostęp usługi sieci Web do magazynu danych ani Twoich zestawów. Jeśli chcesz, aby wdrożony model miał dostęp do danych przechowywanych poza wdrożeniem, na przykład na koncie usługi Azure Storage, musisz opracować niestandardowe rozwiązanie kodu przy użyciu odpowiedniego zestawu SDK. Na przykład [zestaw SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python).
    >
    > Kolejną alternatywą, która może posłużyć do danego scenariusza, są [przewidywania wsadowe](./tutorial-pipeline-batch-scoring-classification.md), które zapewniają dostęp do magazynów danych podczas oceniania.

    Aby uzyskać więcej informacji na temat skryptów wprowadzania, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

* **Zależności**, takie jak skrypty pomocnika lub pakiety Python/Conda wymagane do uruchomienia skryptu lub modelu wprowadzania

Te jednostki są hermetyzowane w __konfiguracji wnioskowania__. Konfiguracja wnioskowania przywołuje skrypt wejściowy i inne zależności.

> [!IMPORTANT]
> Podczas tworzenia konfiguracji wnioskowania do użytku z Azure App Service należy użyć obiektu [środowiska](/python/api/azureml-core/azureml.core.environment(class)) . Należy pamiętać, że w przypadku definiowania środowiska niestandardowego należy dodać polecenie "Azure-default" z wersją >= 1.0.45 jako zależność PIP. Ten pakiet zawiera funkcje, które są konieczne do hostowania modelu jako usługi sieci Web. Poniższy przykład ilustruje tworzenie obiektu środowiska i używanie go z konfiguracją wnioskowania:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Aby uzyskać więcej informacji o środowiskach, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](how-to-use-environments.md).

Aby uzyskać więcej informacji na temat konfiguracji wnioskowania, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Podczas wdrażania programu w celu Azure App Service nie trzeba tworzyć __konfiguracji wdrożenia__.

## <a name="create-the-image"></a>Tworzenie obrazu

Aby utworzyć obraz platformy Docker wdrożony w Azure App Service, użyj [modelu model. Package](/python/api/azureml-core/azureml.core.model.model). Poniższy fragment kodu przedstawia sposób tworzenia nowego obrazu z konfiguracji modelu i wnioskowania:

> [!NOTE]
> W fragmencie kodu założono `model` , że zawiera zarejestrowany model i `inference_config` zawiera konfigurację środowiska wnioskowania. Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Gdy `show_output=True` są wyświetlane dane wyjściowe procesu kompilacji platformy Docker. Po zakończeniu procesu obraz został utworzony w Azure Container Registry dla obszaru roboczego. Po skompilowaniu obrazu zostanie wyświetlona lokalizacja w Azure Container Registry. Zwrócona Lokalizacja ma format `<acrinstance>.azurecr.io/package@sha256:<imagename>` . Na przykład `myml08024f78fd10.azurecr.io/package@sha256:20190827151241`.

> [!IMPORTANT]
> Zapisz informacje o lokalizacji, ponieważ są używane podczas wdrażania obrazu.

## <a name="deploy-image-as-a-web-app"></a>Wdrażanie obrazu jako aplikacji sieci Web

1. Użyj poniższego polecenia, aby uzyskać poświadczenia logowania dla Azure Container Registry zawierającej obraz. Zamień na `<acrinstance>` wartość zwróconą wcześniej z `package.location` :

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    Dane wyjściowe tego polecenia są podobne do następującego dokumentu JSON:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Zapisz wartość dla __nazwy użytkownika__ i jednego z __haseł__.

1. Jeśli nie masz jeszcze grupy zasobów lub planu usługi App Service do wdrożenia usługi, następujące polecenia pokazują, jak utworzyć obie:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    W tym przykładzie jest używana __podstawowa__ warstwa cenowa ( `--sku B1` ).

    > [!IMPORTANT]
    > Obrazy utworzone przez Azure Machine Learning używają systemu Linux, dlatego należy użyć `--is-linux` parametru.

1. Aby utworzyć aplikację sieci Web, użyj następującego polecenia. Zamień na `<app-name>` nazwę, której chcesz użyć. Zamień `<acrinstance>` i `<imagename>` na wartości zwracane `package.location` wcześniej:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package@sha256:<imagename>
    ```

    To polecenie zwraca informacje podobne do następującego dokumentu JSON:

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > W tym momencie aplikacja sieci Web została utworzona. Jednak ponieważ nie podano poświadczeń do Azure Container Registry, które zawierają obraz, aplikacja sieci Web nie jest aktywna. W następnym kroku podajesz informacje o uwierzytelnianiu dla rejestru kontenerów.

1. Aby zapewnić aplikacji sieci Web poświadczenia potrzebne do uzyskania dostępu do rejestru kontenerów, użyj następującego polecenia. Zamień na `<app-name>` nazwę, której chcesz użyć. Zamień `<acrinstance>` i `<imagename>` na wartości zwracane `package.location` wcześniej. Zamień `<username>` i na `<password>` pobrane wcześniej informacje logowania ACR:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package@sha256:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    To polecenie zwraca informacje podobne do następującego dokumentu JSON:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package@sha256:20190827195524"
    }
    ]
    ```

W tym momencie aplikacja sieci Web rozpocznie ładowanie obrazu.

> [!IMPORTANT]
> Załadowanie obrazu może potrwać kilka minut. Aby monitorować postęp, użyj następującego polecenia:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> Po załadowaniu obrazu, gdy lokacja jest aktywna, w dzienniku zostanie wyświetlony komunikat z informacją o stanie `Container <container name> for site <app-name> initialized successfully and is ready to serve requests` .

Po wdrożeniu obrazu można znaleźć nazwę hosta za pomocą następującego polecenia:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

To polecenie zwraca informacje podobne do następującej nazwy hosta `<app-name>.azurewebsites.net` . Użyj tej wartości jako części __podstawowego adresu URL__ usługi.

## <a name="use-the-web-app"></a>Korzystanie z aplikacji sieci Web

Usługa sieci Web, która przekazuje żądania do modelu, znajduje się w lokalizacji `{baseurl}/score` . Na przykład `https://<app-name>.azurewebsites.net/score`. Poniższy kod w języku Python pokazuje, jak przesłać dane do adresu URL i wyświetlić odpowiedź:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak skonfigurować aplikację sieci Web w [App Service w](/azure/app-service/containers/) dokumentacji systemu Linux.
* Dowiedz się więcej na temat skalowania w temacie [wprowadzenie do automatycznego skalowania na platformie Azure](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json).
* [W Azure App Service Użyj certyfikatu TLS/SSL](../app-service/configure-ssl-certificate-in-code.md).
* [Skonfiguruj aplikację App Service, aby używała Azure Active Directory logowania](../app-service/configure-authentication-provider-aad.md).
* [Korzystanie z modelu ML wdrożonego jako usługa sieci Web](how-to-consume-web-service.md)