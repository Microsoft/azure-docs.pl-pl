---
title: Używanie tożsamości usługi Azure AD z usługą sieci Web
titleSuffix: Azure Machine Learning
description: Użyj tożsamości usługi Azure AD z usługą sieci Web w usłudze Azure Kubernetes, aby uzyskać dostęp do zasobów w chmurze podczas oceniania.
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b6e6288f125da2a29a8eff56b64f327914f90cb4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520476"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Używanie tożsamości usługi Azure AD z usługą internetową uczenia maszynowego w usłudze Azure Kubernetes Service

W tym instruktażu dowiesz się, jak przypisać tożsamość usługi Azure Active Directory (Azure AD) do wdrożonego modelu uczenia maszynowego w usłudze Azure Kubernetes. Projekt [tożsamości usługi Azure AD pod](https://github.com/Azure/aad-pod-identity) , umożliwia aplikacjom bezpieczne uzyskiwanie dostępu do zasobów w chmurze przy użyciu [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) i Kubernetes podstawowych. Dzięki temu usługa sieci Web może bezpiecznie uzyskiwać dostęp do zasobów platformy Azure bez konieczności osadzania poświadczeń ani zarządzania tokenami bezpośrednio w `score.py` skrypcie. W tym artykule opisano kroki umożliwiające utworzenie i zainstalowanie tożsamości platformy Azure w klastrze usługi Azure Kubernetes i przypisanie tożsamości do wdrożonej usługi sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning](reference-azure-machine-learning-cli.md), [zestawu Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/intro)lub [rozszerzenia Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Dostęp do klastra AKS przy użyciu `kubectl` polecenia. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z klastrem](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)

- Usługa sieci Web Azure Machine Learning wdrożona w klastrze AKS.

## <a name="create-and-install-an-azure-identity"></a>Utwórz i zainstaluj tożsamość platformy Azure

1. Aby określić, czy klaster AKS ma włączoną funkcję RBAC Kubernetes, użyj następującego polecenia:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    To polecenie zwraca wartość, `true` Jeśli KUBERNETES RBAC jest włączona. Ta wartość Określa polecenie do użycia w następnym kroku.

1. Zainstaluj [tożsamość usługi Azure AD pod](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/) w klastrze AKS.

1. [Utwórz tożsamość na platformie Azure](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#2-create-an-identity-on-azure) , postępując zgodnie z instrukcjami wyświetlanymi na stronie projektu tożsamości usługi Azure AD pod.

1. [Wdróż AzureIdentity](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#3-deploy-azureidentity) , postępując zgodnie z instrukcjami wyświetlanymi na stronie projektu tożsamości usługi Azure AD pod.

1. [Wdróż AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding) , postępując zgodnie z instrukcjami wyświetlanymi na stronie projektu tożsamości usługi Azure AD pod.

1. Jeśli tożsamość platformy Azure utworzona w poprzednim kroku nie znajduje się w tej samej grupie zasobów węzła dla klastra AKS, postępuj zgodnie z instrukcjami [przypisywania roli](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/#user-assigned-identities-that-are-not-within-the-node-resource-group) przedstawionymi na stronie projektu tożsamości usługi Azure AD pod.

## <a name="assign-azure-identity-to-web-service"></a>Przypisywanie tożsamości platformy Azure do usługi sieci Web

W poniższych krokach użyto tożsamości platformy Azure utworzonej w poprzedniej sekcji i przypiszesz ją do usługi sieci Web AKS za pomocą **etykiety selektora**.

Najpierw zidentyfikuj nazwę i przestrzeń nazw wdrożenia w klastrze AKS, do którego chcesz przypisać tożsamość platformy Azure. Te informacje można uzyskać, uruchamiając następujące polecenie. Przestrzenie nazw powinny być nazwami obszaru roboczego Azure Machine Learning, a nazwa wdrożenia powinna być nazwą punktu końcowego, jak pokazano w portalu.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Dodaj etykietę selektora tożsamości platformy Azure do wdrożenia, edytując specyfikację wdrażania. Wartość selektora powinna być taka, którą zdefiniowano w kroku 5 [wdrożenia AzureIdentityBinding](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding).

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Edytuj wdrożenie, aby dodać etykietę selektora tożsamości platformy Azure. Przejdź do poniższej sekcji w temacie `/spec/template/metadata/labels` . Powinny być widoczne wartości, takie jak `isazuremlapp: “true”` . Dodaj etykietę usługi AAD-pod-Identity, jak pokazano poniżej.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Aby sprawdzić, czy etykieta została prawidłowo dodana, uruchom następujące polecenie. Należy również sprawdzić stan nowo utworzonych zasobników.

```azurecli-interactive
   kubectl get pod -n azureml-<name of workspace> --show-labels
```

Usługi sieci Web dla tego wdrożenia mogą teraz uzyskiwać dostęp do zasobów platformy Azure za pomocą tożsamości platformy Azure bez konieczności osadzania poświadczeń w kodzie.

## <a name="assign-roles-to-your-azure-identity"></a>Przypisywanie ról do tożsamości platformy Azure

[Przypisz swoją tożsamość zarządzaną platformy Azure z odpowiednimi rolami](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) , aby uzyskać dostęp do innych zasobów platformy Azure. Upewnij się, że przypisywane role mają poprawne **akcje dotyczące danych**. Na przykład [rola czytnika danych obiektów blob magazynu](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) będzie miała uprawnienia do odczytu obiektu blob magazynu, podczas gdy ogólna [rola czytnika](../role-based-access-control/built-in-roles.md#reader) może nie być.

## <a name="use-azure-identity-with-your-web-service"></a>Używanie tożsamości platformy Azure z usługą sieci Web

Wdróż model w klastrze AKS. `score.py`Skrypt może zawierać operacje wskazujące zasoby platformy Azure, do których ma dostęp Twoja tożsamość platformy Azure. Upewnij się, że zainstalowano wymagane zależności biblioteki klienta dla zasobu, do którego próbujesz uzyskać dostęp. Poniżej przedstawiono kilka przykładów użycia tożsamości platformy Azure do uzyskiwania dostępu do różnych zasobów platformy Azure z usługi.

### <a name="access-key-vault-from-your-web-service"></a>Dostęp do Key Vault z usługi sieci Web

Jeśli masz uprawnienia do odczytu tożsamości platformy Azure do wpisu tajnego w ramach **Key Vault**, `score.py` możesz uzyskać do niego dostęp przy użyciu następującego kodu.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name)
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

> [!IMPORTANT]
> Ten przykład używa DefaultAzureCredential. Aby udzielić dostępu do tożsamości przy użyciu określonych zasad dostępu, zobacz [przypisywanie zasad dostępu Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure](../key-vault/general/assign-access-policy-cli.md).

### <a name="access-blob-from-your-web-service"></a>Dostęp do obiektu BLOB z usługi sieci Web

Jeśli masz dostęp do odczytu tożsamości platformy Azure do danych wewnątrz **obiektu blob magazynu**, `score.py` możesz uzyskać do niego dostęp przy użyciu następującego kodu.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat używania biblioteki klienta Azure Identity platform w języku Python, zobacz [repozytorium](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) w witrynie GitHub.
* Szczegółowy przewodnik dotyczący wdrażania modeli w klastrach usługi Azure Kubernetes Service znajduje się w temacie [how](how-to-deploy-azure-kubernetes-service.md)to.