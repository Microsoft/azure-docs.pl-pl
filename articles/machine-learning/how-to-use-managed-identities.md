---
title: Korzystanie z tożsamości zarządzanych na potrzeby kontroli dostępu (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą zarządzanych tożsamości kontrolować dostęp do zasobów platformy Azure z obszaru roboczego Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 6bcc4ac5561a8bdb721018aa05bf2376579b627b
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079674"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>Korzystanie z tożsamości zarządzanych z Azure Machine Learning (wersja zapoznawcza)

[Zarządzane tożsamości](/azure/active-directory/managed-identities-azure-resources/overview) umożliwiają skonfigurowanie obszaru roboczego z *minimalnymi wymaganymi uprawnieniami dostępu do zasobów*. 

Podczas konfigurowania Azure Machine Learning obszaru roboczego w sposób godny zaufania należy zapewnić, że różne usługi skojarzone z obszarem roboczym mają prawidłowy poziom dostępu. Na przykład podczas przepływu pracy usługi Machine Learning obszar roboczy musi mieć dostęp do Azure Container Registry (ACR) dla obrazów platformy Docker i kont magazynu na potrzeby danych szkoleniowych. 

Ponadto tożsamości zarządzane umożliwiają dokładniejszą kontrolę nad uprawnieniami, na przykład można udzielić lub odwołać dostęp z określonych zasobów obliczeniowych do określonego ACR.

W tym artykule dowiesz się, jak używać tożsamości zarządzanych do:

 * Skonfiguruj i używaj ACR dla obszaru roboczego Azure Machine Learning bez konieczności włączania dostępu administratora do ACR.
 * Uzyskaj dostęp do prywatnego ACR zewnętrznego dla obszaru roboczego, aby ściągnąć podstawowe obrazy na potrzeby szkoleń lub wnioskowania.
 * Dostęp do zestawów danych w celu szkolenia przy użyciu tożsamości zarządzanych zamiast kluczy dostępu do magazynu.

> [!IMPORTANT]
> Korzystanie z tożsamości zarządzanych do kontrolowania dostępu do zasobów za pomocą Azure Machine Learning jest obecnie w wersji zapoznawczej. Funkcje w wersji zapoznawczej są udostępniane "w takiej postaci, w jakiej są" bez gwarancji pomocy technicznej ani umowy dotyczącej poziomu usług. Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych programu Microsoft Azure.
 
## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).
- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning](reference-azure-machine-learning-cli.md)
- [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
- Aby przypisać role, identyfikator logowania dla subskrypcji platformy Azure musi mieć rolę [operatora tożsamości zarządzanej](/azure/role-based-access-control/built-in-roles#managed-identity-operator) lub inną rolę, która przyznaje wymagane akcje (takie jak __właściciel__).
- Musisz znać, jak tworzyć i pracować z [tożsamościami zarządzanymi](/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="configure-managed-identities"></a>Konfigurowanie tożsamości zarządzanych

W niektórych sytuacjach konieczne jest niezezwalanie na dostęp użytkownika administracyjnego do Azure Container Registry. Na przykład ACR może być współużytkowany i trzeba nie zezwalać na dostęp administratora przez innych użytkowników. Lub utworzenie ACR z włączonym użytkownikiem administracyjnym nie jest dozwolone przez zasady na poziomie subskrypcji.

> [!IMPORTANT]
> W przypadku korzystania z Azure Machine Learning do wnioskowania w usłudze Azure Container Instance (ACI) __wymagane__jest uzyskanie dostępu administratora na ACR. Nie należy wyłączać go, jeśli planujesz wdrożenie modeli do ACI na potrzeby wnioskowania.

Podczas tworzenia ACR bez włączania dostępu administratora, tożsamości zarządzane są używane w celu uzyskania dostępu do ACR w celu kompilowania i ściągania obrazów platformy Docker.

Podczas tworzenia obszaru roboczego można przenieść własne ACR z wyłączonym użytkownikiem administracyjnym. Alternatywnie można Azure Machine Learning utworzyć obszar roboczy ACR i później wyłączyć użytkownika administracyjnego.

### <a name="bring-your-own-acr"></a>Przenieś własne ACR

Jeśli użytkownik administracyjny ACR jest niedozwolony przez zasady subskrypcji, należy najpierw utworzyć ACR bez administratora, a następnie skojarzyć go z obszarem roboczym. Ponadto jeśli masz istniejący ACR z wyłączonym użytkownikiem administracyjnym, możesz dołączyć go do obszaru roboczego.

[Utwórz ACR z poziomu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli) bez ustawienia ```--admin-enabled``` argumentu lub z Azure Portal bez włączania użytkownika administracyjnego. Następnie podczas tworzenia obszaru roboczego Azure Machine Learning Określ identyfikator zasobu platformy Azure ACR. Poniższy przykład ilustruje tworzenie nowego obszaru roboczego usługi Azure ML, który używa istniejącej ACR:

> [!TIP]
> Aby uzyskać wartość `--container-registry` parametru, użyj polecenia [AZ ACR show](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az_acr_show) , aby wyświetlić informacje dotyczące ACR. `id`Pole zawiera identyfikator zasobu dla ACR.

```azurecli-interactive
az ml workspace create -w <workspace name> \
-g <workspace resource group> \
-l <region> \
--container-registry /subscriptions/<subscription id>/resourceGroups/<acr resource group>/providers/Microsoft.ContainerRegistry/registries/<acr name>
```

### <a name="let-azure-machine-learning-service-create-workspace-acr"></a>Zezwól Azure Machine Learning tworzenia obszaru roboczego ACR usługi

Jeśli nie podasz własnych ACR, usługa Azure Machine Learning utworzy ją podczas wykonywania operacji wymagającej jednej. Na przykład Prześlij uruchomienie szkolenia, aby środowisko obliczeniowe usługi Machine Learning, skompilować środowisko lub wdrożyć punkt końcowy usługi sieci Web. W ACR utworzonym przez obszar roboczy zostanie włączony użytkownik administracyjny i należy ręcznie wyłączyć administratora.

1. Tworzenie nowego obszaru roboczego

    ```azurecli-interactive
    az ml workspace show -n <my workspace> -g <my resource group>
    ```

1. Wykonaj akcję wymagającą ACR. Na przykład samouczek dotyczący [uczenia modelu](tutorial-train-models-with-aml.md).

1. Pobierz nazwę ACR utworzoną przez klaster:

    ```azurecli-interactive
    az ml workspace show -w <my workspace> \
    -g <my resource group>
    --query containerRegistry
    ```

    To polecenie zwraca wartość podobną do poniższego tekstu. Potrzebujesz tylko ostatniej części tekstu, która jest nazwą wystąpienia ACR:

    ```text
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. Zaktualizuj ACR, aby wyłączyć administratora:

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>Tworzenie obliczeń przy użyciu tożsamości zarządzanej w celu uzyskania dostępu do obrazów platformy Docker na potrzeby szkolenia

Aby uzyskać dostęp do obszaru roboczego ACR, Utwórz klaster obliczeniowy uczenia maszynowego z włączoną tożsamością zarządzaną przypisaną przez system. Tożsamość można włączyć z poziomu Azure Portal lub Studio podczas tworzenia obliczeń lub z poziomu interfejsu wiersza polecenia platformy Azure przy użyciu

# <a name="python"></a>[Python](#tab/python)

Podczas tworzenia klastra obliczeniowego z [AmlComputeProvisioningConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration?view=azure-ml-py)należy użyć parametru, `identity_type` Aby ustawić typ tożsamości zarządzanej.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interaction
az ml computetarget create amlcompute --name cpucluster -w <workspace> -g <resource group> --vm-size <vm sku> --assign-identity '[system]'
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby uzyskać informacje na temat konfigurowania tożsamości zarządzanej podczas tworzenia klastra obliczeniowego w programie Studio, zobacz [Konfigurowanie tożsamości zarządzanej](how-to-create-attach-compute-studio.md#managed-identity).

---

Zarządzana tożsamość zostanie automatycznie udzielona w obszarze roboczym ACR w celu włączenia ściągania obrazów platformy Docker na potrzeby szkolenia.

> [!NOTE]
> Jeśli tworzysz najpierw obliczenia, przed utworzeniem obszaru roboczego ACR musisz ręcznie przypisać rolę ACRPull.

## <a name="access-base-images-from-private-acr"></a>Dostęp do obrazów podstawowych z ACR prywatnego

Domyślnie Azure Machine Learning używa obrazów podstawowych platformy Docker, które pochodzą z publicznego repozytorium zarządzanego przez firmę Microsoft. Następnie kompiluje ono środowisko szkoleniowe lub wnioskowania na tych obrazach. Aby uzyskać więcej informacji, zobacz [co to są środowiska ml?](concept-environments.md)

Aby użyć niestandardowego obrazu podstawowego dla przedsiębiorstwa, możesz użyć tożsamości zarządzanych do uzyskania dostępu do prywatnej ACR. Istnieją dwa przypadki użycia:

 * Użyj obrazu podstawowego do szkolenia w zależności od tego.
 * Kompiluj Azure Machine Learning obraz zarządzany z obrazem niestandardowym jako baza.

### <a name="pull-docker-base-image-to-machine-learning-compute-cluster-for-training-as-is"></a>Pobierz obraz podstawowy platformy Docker do klastra obliczeniowego usługi Machine Learning w celu szkolenia zgodnie z oczekiwaniami

Utwórz klaster obliczeniowy uczenia maszynowego z włączoną tożsamością zarządzaną przez system, zgodnie z wcześniejszym opisem. Następnie określ identyfikator podmiotu zabezpieczeń zarządzanej tożsamości.

```azurecli-interactive
az ml computetarget amlcompute identity show --name <cluster name> -w <workspace> -g <resource group>
```

Opcjonalnie można zaktualizować klaster obliczeniowy, aby przypisać tożsamość zarządzaną przypisaną przez użytkownika:

```azurecli-interactive
az ml computetarget amlcompute identity assign --name cpucluster \
-w $mlws -g $mlrg --identities <my-identity-id>
```

Aby umożliwić klastrowi obliczeniowemu ściąganie obrazów podstawowych, udziel roli ACRPull tożsamość usługi zarządzanej w prywatnej ACR

```azurecli-interactive
az role assignment create --assignee <principal ID> \
--role acrpull \
--scope "/subscriptions/<subscription ID>/resourceGroups/<private ACR resource group>/providers/Microsoft.ContainerRegistry/registries/<private ACR name>"
```

Na koniec podczas przesyłania przebiegu szkoleniowego Określ lokalizację obrazu podstawowego w [definicji środowiska](how-to-use-environments.md#use-existing-environments).

```python
from azureml.core import Environment
env = Environment(name="private-acr")
env.docker.base_image = "<ACR name>.azurecr.io/<base image repository>/<base image version>"
env.python.user_managed_dependencies = True
```

> [!IMPORTANT]
> Aby zapewnić, że obraz podstawowy zostanie pobrany bezpośrednio do zasobu obliczeniowego, ustaw `user_managed_dependencies = True` i nie Określ pliku dockerfile. W przeciwnym razie usługa Azure Machine Learning podejmie próbę skompilowania nowego obrazu platformy Docker i zakończy się niepowodzeniem, ponieważ tylko klaster obliczeniowy ma dostęp do ściągania obrazu podstawowego z ACR.

### <a name="build-azure-machine-learning-managed-environment-into-base-image-from-private-acr-for-training-or-inference"></a>Kompiluj Azure Machine Learning środowisko zarządzane do obrazu podstawowego z prywatnego ACR na potrzeby szkolenia lub wnioskowania

W tym scenariuszu usługa Azure Machine Learning kompiluje środowisko szkoleniowe lub wnioskowania na podstawie obrazu podstawowego dostarczanego z ACR prywatnego. Ponieważ zadanie kompilacji obrazu odbywa się w obszarze roboczym ACR przy użyciu zadań ACR, należy wykonać dodatkowe czynności, aby zezwolić na dostęp.

1. Utwórz __tożsamość zarządzaną przypisaną przez użytkownika__ i Udziel tożsamości ACRPull dostęp do __prywatnego ACR__.  
1. Przyznaj tożsamości zarządzanej przez __system__ obszaru roboczego rolę operatora tożsamości zarządzanej na __zarządzanej tożsamości przypisanej przez użytkownika__ z poprzedniego kroku. Ta rola umożliwia obszarowi roboczemu przypisanie tożsamości zarządzanej przypisanej przez użytkownika do ACR zadania tworzenia środowiska zarządzanego. 

    1. Uzyskaj identyfikator podmiotu zabezpieczeń dla tożsamości zarządzanej przypisanej przez system:

        ```azurecli-interactive
        az ml workspace show -w <workspace name> -g <resource group> --query identityPrincipalId
        ```

    1. Udziel roli operatora tożsamości zarządzanej:

        ```azurecli-interactive
        az role assignment create --assignee <principal ID> --role managedidentityoperator --scope <UAI resource ID>
        ```

        Identyfikator zasobu UAI jest IDENTYFIKATORem zasobu platformy Azure dla tożsamości przypisanej do użytkownika w formacie `/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAI name>` .

1. Określ zewnętrzny ACR i identyfikator klienta __zarządzanej tożsamości przypisanej przez użytkownika__ w połączeniach obszaru roboczego za pomocą [metody Workspace.set_connection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-):

    ```python
    workspace.set_connection(
        name="privateAcr", 
        category="ACR", 
        target = "<acr url>", 
        authType = "RegistryConnection", 
        value={"ResourceId": "<UAI resource id>", "ClientId": "<UAI client ID>"})
    ```

Po zakończeniu konfiguracji można użyć obrazów podstawowych z prywatnego ACR podczas kompilowania środowisk do uczenia lub wnioskowania. Poniższy fragment kodu ilustruje sposób określania ACR obrazu podstawowego i nazwy obrazu w definicji środowiska:

```python
from azureml.core import Environment

env = Environment(name="my-env")
env.docker.base_image = "<acr url>/my-repo/my-image:latest"
```

Opcjonalnie można określić adres URL zasobu tożsamości zarządzanej i identyfikator klienta w samej definicji środowiska przy użyciu [RegistryIdentity](https://docs.microsoft.com/python/api/azureml-core/azureml.core.container_registry.registryidentity?view=azure-ml-py). Jeśli używasz tożsamości rejestru jawnie, zastępuje wszystkie określone wcześniej połączenia obszaru roboczego:

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>”
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="access-training-data"></a>Dostęp do danych szkoleniowych

Po utworzeniu klastra obliczeniowego uczenia maszynowego z tożsamością zarządzaną zgodnie z wcześniejszym opisem można użyć tej tożsamości w celu uzyskania dostępu do danych szkoleniowych bez kluczy konta magazynu. W tym scenariuszu można użyć zarządzanej tożsamości przypisanej do systemu lub użytkownika.

### <a name="grant-compute-managed-identity-access-to-storage-account"></a>Przyznaj dostęp do tożsamości zarządzanej przez obliczenia do konta magazynu

[Nadaj zarządzanej tożsamości rolę czytelnik](https://docs.microsoft.com/azure/storage/common/storage-auth-aad#assign-azure-roles-for-access-rights) na koncie magazynu, w którym przechowywane są dane szkoleniowe.

### <a name="register-data-store-with-workspace"></a>Rejestrowanie magazynu danych za pomocą obszaru roboczego

Po przypisaniu tożsamości zarządzanej można utworzyć magazyn danych bez konieczności określania poświadczeń magazynu.

```python
from azureml.core import Datastore

blob_dstore = Datastore.register_azure_blob_container(workspace=workspace,
                                                      datastore_name='my-datastore',
                                                      container_name='my-container',
                                                      account_name='my-storage-account')
```

### <a name="submit-training-run"></a>Przesyłanie przebiegu trenowania

Po przesłaniu szkolenia przy użyciu magazynu danych w ramach usługi obliczeniowej Uczenie maszynowe do uzyskiwania dostępu do danych wykorzystuje swoją zarządzaną tożsamość.

## <a name="use-docker-images-for-inference"></a>Używanie obrazów platformy Docker do wnioskowania

Po skonfigurowaniu ACR bez użytkownika administracyjnego zgodnie z wcześniejszym opisem można uzyskać dostęp do obrazów platformy Docker na potrzeby wnioskowania bez kluczy administracyjnych z usługi Azure Kubernetes Service (AKS). Podczas tworzenia lub dołączania AKS do obszaru roboczego, nazwa główna usługi klastra jest automatycznie przypisana ACRPull dostępu do obszaru roboczego ACR.

> [!NOTE]
> Jeśli przeniesiesz własny klaster AKS, klaster musi mieć włączoną jednostkę usługi zamiast tożsamości zarządzanej.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zabezpieczeniach korporacyjnych w Azure Machine Learning](concept-enterprise-security.md).
