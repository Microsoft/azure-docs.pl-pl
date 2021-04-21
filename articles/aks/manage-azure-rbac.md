---
title: Zarządzanie RBAC platformy Azure na platformie Kubernetes z platformy Azure
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak używać kontroli RBAC platformy Azure na użytek autoryzacji kubernetes z Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: c708a577a1c2e4bb8f7ddff90f458afd0d9e566f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783003"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>Autoryzacja na platformie Kubernetes przy użyciu kontroli dostępu opartej na rolach platformy Azure (wersja zapoznawcza)

Obecnie można już korzystać ze zintegrowanego uwierzytelniania między [usługami Azure Active Directory (Azure AD) i AKS.](managed-aad.md) Po włączeniu tej integracji klienci mogą używać użytkowników, grup lub jednostki usługi Azure AD jako tematów w kontroli RBAC na platformie Kubernetes. Więcej informacji można znaleźć [tutaj.](azure-ad-rbac.md)
Dzięki tej funkcji nie trzeba oddzielnie zarządzać tożsamościami użytkowników i poświadczeniami dla usługi Kubernetes. Jednak nadal trzeba skonfigurować RBAC platformy Azure i kubernetes RBAC oddzielnie oraz zarządzać nimi. Aby uzyskać więcej informacji na temat uwierzytelniania i autoryzacji przy użyciu kontroli dostępu na podstawie ról w u usługi AKS, [zobacz tutaj](concepts-identity.md).

Ten dokument zawiera nowe podejście, które umożliwia ujednolicone zarządzanie zasobami platformy Azure, usługami AKS i platformą Kubernetes oraz kontrolę dostępu do nich.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Możliwość zarządzania RBAC dla zasobów platformy Kubernetes z platformy Azure umożliwia zarządzanie RBAC dla zasobów klastra przy użyciu platformy Azure lub natywnych mechanizmów kubernetes. Po włączeniu podmioty zabezpieczeń usługi Azure AD będą weryfikowane wyłącznie przez funkcję RBAC platformy Azure, a zwykłe konta usługi i użytkowników platformy Kubernetes są weryfikowane wyłącznie przez funkcję RBAC platformy Kubernetes. Aby uzyskać więcej informacji na temat uwierzytelniania i autoryzacji przy użyciu kontroli dostępu na podstawie ról w u usługi AKS, [zobacz tutaj](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>Wymagania wstępne 
- Upewnij się, że masz interfejs wiersza polecenia platformy Azure w wersji 2.9.0 lub nowszej
- Upewnij się, że `EnableAzureRBACPreview` flaga funkcji jest włączona.
- Upewnij się, że masz `aks-preview` [zainstalowane rozszerzenie interfejsu wiersza][az-extension-add] polecenia w wersji 0.4.55 lub wyższej
- Upewnij się, że [zainstalowano program kubectl w wersji 1.18.3+][az-aks-install-cli].

#### <a name="register-enableazurerbacpreview-preview-feature"></a>Rejestrowanie `EnableAzureRBACPreview` funkcji w wersji zapoznawczej

Aby utworzyć klaster usługi AKS, który używa kontroli dostępu na podstawie ról platformy Azure dla autoryzacji Kubernetes, należy włączyć `EnableAzureRBACPreview` flagę funkcji w subskrypcji.

Zarejestruj `EnableAzureRBACPreview` flagę funkcji za pomocą [polecenia az feature register,][az-feature-register] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

 Stan rejestracji można sprawdzić za pomocą [polecenia az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, odśwież rejestrację dostawcy *zasobów Microsoft.ContainerService* za pomocą [polecenia az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby utworzyć klaster usługi AKS, który używa kontroli RBAC platformy Azure, potrzebne jest rozszerzenie interfejsu wiersza polecenia *aks-preview* w wersji 0.4.55 lub wyższej. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure *aks-preview* za pomocą [polecenia az extension add][az-extension-add] lub zainstaluj wszystkie dostępne aktualizacje za pomocą polecenia az extension [update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Ograniczenia

- Wymaga [integracji zarządzanej z usługą Azure AD.](managed-aad.md)
- Podczas wersji zapoznawczej nie można zintegrować funkcji RBAC platformy Azure dla autoryzacji platformy Kubernetes z istniejącymi klastrami, ale będzie to możliwe w wersji ogólnie dostępnej.
- Użyj [kubectl v1.18.3+][az-aks-install-cli].
- Jeśli masz definicje CLD i tworzyć niestandardowe definicje ról, jedynym sposobem, aby je obecnie objąć, jest zapewnienie `Microsoft.ContainerService/managedClusters/*/read` . AKS pracuje nad zapewnieniem bardziej szczegółowych uprawnień dla CLD. Dla pozostałych obiektów można użyć określonych grup interfejsu API, na przykład: `Microsoft.ContainerService/apps/deployments/read` .
- Propagacja nowych przypisań ról może potrwać do 5 minut i może zostać zaktualizowana przez serwer autoryzacji.
- Wymaga, aby dzierżawa usługi Azure AD skonfigurowana do uwierzytelniania było taka sama jak dzierżawa dla subskrypcji, która zawiera klaster usługi AKS. 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Tworzenie nowego klastra przy użyciu kontroli RBAC platformy Azure i zarządzanej integracji z usługą Azure AD

Utwórz klaster usługi AKS przy użyciu następujących poleceń interfejsu wiersza polecenia.

Utwórz grupę zasobów platformy Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Utwórz klaster usługi AKS z zarządzaną integracją usługi Azure AD i usługą Azure RBAC dla autoryzacji kubernetes.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

Pomyślne utworzenie klastra z integracją usługi Azure AD i autoryzacją RBAC platformy Azure dla platformy Kubernetes zawiera następującą sekcję w treści odpowiedzi:

```json
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "enableAzureRbac": true,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "****-****-****-****-****"
  }
```

## <a name="create-role-assignments-for-users-to-access-cluster"></a>Tworzenie przypisań ról dla użytkowników w celu uzyskania dostępu do klastra

AKS udostępnia następujące cztery wbudowane role:


| Rola                                | Opis  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC Reader  | Zezwala na dostęp tylko do odczytu, aby wyświetlić większość obiektów w przestrzeni nazw. Nie zezwala na wyświetlanie ról ani powiązań ról. Ta rola nie zezwala na wyświetlanie elementu , ponieważ odczytywanie zawartości wpisów tajnych umożliwia dostęp do poświadczeń konta usługi w przestrzeni nazw, co umożliwia dostęp do interfejsu API jako dowolnemu użytkownikowi ServiceAccount w przestrzeni nazw (w formie eskalacji `Secrets` uprawnień)  |
| Azure Kubernetes Service RBAC Writer | Umożliwia dostęp do odczytu/zapisu do większości obiektów w przestrzeni nazw. Ta rola nie zezwala na wyświetlanie ani modyfikowanie ról ani powiązań ról. Jednak ta rola umożliwia uzyskiwanie dostępu do zasobników i uruchamianie ich jako dowolnego konta usługi w przestrzeni nazw, więc może służyć do uzyskiwania poziomów dostępu do interfejsu API dowolnego konta usługi w przestrzeni `Secrets` nazw. |
| Azure Kubernetes Service RBAC Admin  | Zezwala na dostęp administratora, który ma zostać przyznany w przestrzeni nazw. Umożliwia dostęp do odczytu/zapisu do większości zasobów w przestrzeni nazw (lub zakresie klastra), w tym na możliwość tworzenia ról i powiązań ról w przestrzeni nazw. Ta rola nie zezwala na dostęp do zapisu do limitu przydziału zasobów ani do samej przestrzeni nazw. |
| Azure Kubernetes Service RBAC Cluster Admin  | Umożliwia superuzyskonom dostęp do wykonywania dowolnych akcji na dowolnym zasobie. Zapewnia pełną kontrolę nad każdym zasobem w klastrze i we wszystkich przestrzeniach nazw. |


Przypisania ról w zakresie całego klastra **usługi AKS** można wykonać w bloku Access Control (IAM) zasobu klastra w usłudze Azure Portal lub za pomocą poleceń interfejsu wiersza polecenia platformy Azure, jak pokazano poniżej:

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

gdzie może być nazwą użytkownika (na przykład ), a `<AAD-ENTITY-ID>` nawet user@contoso.com clientID jednostki usługi.

Można również tworzyć przypisania ról w zakresie określonej przestrzeni **nazw** w klastrze:

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

Obecnie przypisania ról w zakresie przestrzeni nazw należy skonfigurować za pomocą interfejsu wiersza polecenia platformy Azure.


### <a name="create-custom-roles-definitions"></a>Tworzenie definicji ról niestandardowych

Opcjonalnie możesz utworzyć własną definicję roli, a następnie przypisać rolę zgodnie z powyższymi informacjami.

Poniżej znajduje się przykład definicji roli, która umożliwia użytkownikowi tylko odczytywanie wdrożeń i nic innego. Pełną listę możliwych akcji można sprawdzić [tutaj.](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)


Skopiuj poniższy plik json do pliku o nazwie `deploy-view.json` .

```json
{
    "Name": "AKS Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.ContainerService/managedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<YOUR SUBSCRIPTION ID>"
    ]
}
```

Zastąp `<YOUR SUBSCRIPTION ID>` identyfikatorem subskrypcji, który możesz uzyskać, uruchamiając:

```azurecli-interactive
az account show --query id -o tsv
```


Teraz możemy utworzyć definicję roli, uruchamiając poniższe polecenie w folderze, w którym zapisano polecenie `deploy-view.json` :

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

Teraz, gdy masz już definicję roli, możesz przypisać ją do użytkownika lub innej tożsamości, uruchamiając:

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>Używanie kontroli RBAC platformy Azure na użytek autoryzacji Kubernetes za pomocą `kubectl`

> [!NOTE]
> Upewnij się, że masz najnowszą wersję polecenia kubectl, uruchamiając poniższe polecenie:
>
> ```azurecli-interactive
> az aks install-cli
> ```
> Może być konieczne uruchomienie go z `sudo` uprawnieniami. 

Teraz, gdy masz przypisaną żądaną rolę i uprawnienia. Możesz rozpocząć wywoływanie interfejsu API Kubernetes, na przykład z `kubectl` .

W tym celu najpierw pobierzmy konfigurację kubeconfig klastra przy użyciu poniższego polecenia:

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> Aby wykonać powyższy [krok, Azure Kubernetes Service](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) wbudowaną rolę użytkownika klastra klastra.

Teraz możesz użyć kubectl, aby na przykład wyświetlić listę węzłów w klastrze. Przy pierwszym uruchomieniu będzie konieczne zalogowanie się, a kolejne polecenia będą używać odpowiedniego tokenu dostępu.

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>Używanie kontroli RBAC platformy Azure na użytek autoryzacji Kubernetes za pomocą `kubelogin`

Aby odblokować dodatkowe scenariusze, takie jak logowania nieinterakcyjne, starsze wersje lub korzystanie z logowania jednokrotnego w wielu klastrach bez konieczności logowania się do nowego klastra, przy przyznaniu, że token jest nadal prawidłowy, usługa AKS utworzyła wtyczkę exec o `kubectl` nazwie [`kubelogin`](https://github.com/Azure/kubelogin) .

Można go użyć, uruchamiając:

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

Za pierwszym razem musisz zalogować się interaktywnie, tak jak przy użyciu zwykłego narzędzia kubectl, ale później nie będzie już konieczne, nawet w przypadku nowych klastrów usługi Azure AD (o ile token jest nadal ważny).

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>Czyszczenie

### <a name="clean-role-assignment"></a>Przypisanie czystej roli

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
Skopiuj identyfikator lub identyfikatory ze wszystkich przypisań, a następnie.

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>Czyszczenie definicji roli

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>Usuwanie klastra i grupy zasobów

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

- Więcej informacji na temat uwierzytelniania, autoryzacji, kontroli RBAC na platformie Kubernetes i kontroli dostępu na platformie Azure znajdziesz [tutaj.](concepts-identity.md)
- Więcej informacji na temat kontroli RBAC na platformie Azure [znajdziesz tutaj.](../role-based-access-control/overview.md)
- Więcej informacji o wszystkich akcjach, których można użyć do szczegółowego definiowania niestandardowych ról platformy Azure na użytek autoryzacji kubernetes, [znajdziesz tutaj.](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
