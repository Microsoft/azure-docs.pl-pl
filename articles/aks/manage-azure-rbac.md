---
title: Zarządzanie RBAC platformy Azure w programie Kubernetes z platformy Azure
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak za pomocą usługi Azure Kubernetes Service (AKS) używać uwierzytelniania RBAC platformy Azure dla Kubernetes.
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: af8d0300b533d9f25cddf225f4ffbe78ca6bf2cb
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98249639"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>Autoryzacja na platformie Kubernetes przy użyciu kontroli dostępu opartej na rolach platformy Azure (wersja zapoznawcza)

Obecnie można już korzystać ze [zintegrowanego uwierzytelniania między Azure Active Directory (Azure AD) i AKS](managed-aad.md). Po włączeniu ta Integracja umożliwia klientom korzystanie z użytkowników usługi Azure AD, grup lub jednostek usługi jako tematów w Kubernetes RBAC, zobacz [tutaj](azure-ad-rbac.md).
Ta funkcja zwalnia z konieczności oddzielnego zarządzania tożsamościami i poświadczeniami użytkowników w celu Kubernetes. Jednak nadal trzeba skonfigurować i osobno zarządzać usługą Azure RBAC oraz Kubernetes RBAC. Aby uzyskać więcej informacji na temat uwierzytelniania i autoryzacji za pomocą RBAC w systemie AKS, zobacz [tutaj](concepts-identity.md).

W tym dokumencie opisano nowe podejście, które pozwala na ujednolicone zarządzanie i kontrolę dostępu między zasobami platformy Azure, AKS i zasobami Kubernetes.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Możliwość zarządzania RBAC dla zasobów Kubernetes z platformy Azure umożliwia zarządzanie RBAC dla zasobów klastra przy użyciu platformy Azure lub natywnych mechanizmów Kubernetes. Po włączeniu nazwy główne usługi Azure AD będą weryfikowane wyłącznie przez funkcję RBAC platformy Azure, podczas gdy regularni Użytkownicy i konta usług Kubernetes są weryfikowane wyłącznie przez Kubernetes RBAC. Aby uzyskać więcej informacji na temat uwierzytelniania i autoryzacji za pomocą RBAC w systemie AKS, zobacz [tutaj](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>Wymagania wstępne 
- Upewnij się, że masz interfejs wiersza polecenia platformy Azure w wersji 2.9.0 lub nowszej
- Upewnij się, że `EnableAzureRBACPreview` Flaga funkcji jest włączona.
- Upewnij się, że jest `aks-preview` zainstalowany [interfejs wiersza polecenia][az-extension-add] w wersji 0.4.55 lub nowszej
- Upewnij się, że zainstalowano [polecenia kubectl v 1.18.3 +][az-aks-install-cli].

#### <a name="register-enableazurerbacpreview-preview-feature"></a>Funkcja rejestracji w `EnableAzureRBACPreview` wersji zapoznawczej

Aby utworzyć klaster AKS, który używa usługi Azure RBAC do autoryzacji Kubernetes, należy włączyć `EnableAzureRBACPreview` flagę funkcji w ramach subskrypcji.

Zarejestruj `EnableAzureRBACPreview` flagę funkcji za pomocą polecenia [AZ Feature Register][az-feature-register] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

 Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów *Microsoft. ContainerService* za pomocą polecenia [AZ Provider Register] [AZ-Provider-Register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby utworzyć klaster AKS, który korzysta z usługi Azure RBAC, potrzebujesz rozszerzenia interfejsu wiersza polecenia *AKS-Preview* w wersji 0.4.55 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji zapoznawczej* przy użyciu poleceń [AZ Extension Add][az-extension-add] lub zainstaluj wszystkie dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Ograniczenia

- Wymaga [zarządzanej integracji z usługą Azure AD](managed-aad.md).
- Nie możesz zintegrować usługi Azure RBAC z autoryzacją Kubernetes w istniejących klastrach w trakcie okresu zapoznawczego, ale będziesz mieć dostęp do ogólnej dostępności.
- Użyj [polecenia kubectl v 1.18.3 +][az-aks-install-cli].
- Jeśli masz CRDs i tworzysz niestandardowe definicje ról, jedynym sposobem na pokrycie CRDs dzisiaj jest zapewnienie `Microsoft.ContainerService/managedClusters/*/read` . AKS pracuje nad zapewnieniem bardziej szczegółowych uprawnień dla CRDs. Dla pozostałych obiektów można użyć określonych grup interfejsów API, na przykład: `Microsoft.ContainerService/apps/deployments/read` .
- Nowe przypisania ról mogą trwać do 5 min i aktualizować je na serwerze autoryzacji.
- Wymaga, aby dzierżawa usługi Azure AD była taka sama jak dzierżawa dla subskrypcji zawierającej klaster AKS. 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Tworzenie nowego klastra przy użyciu funkcji RBAC platformy Azure i zarządzanej integracji usługi Azure AD

Utwórz klaster AKS przy użyciu następujących poleceń interfejsu wiersza polecenia.

Utwórz grupę zasobów platformy Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Utwórz klaster AKS z zarządzaną integracją usługi Azure AD i usługą Azure RBAC na potrzeby autoryzacji Kubernetes.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

Pomyślne utworzenie klastra z integracją z usługą Azure AD i użyciem usługi Azure RBAC na potrzeby autoryzacji Kubernetes ma następującą sekcję w treści odpowiedzi:

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
| Czytnik RBAC usługi Azure Kubernetes Service  | Zezwala na dostęp tylko do odczytu do wyświetlania większości obiektów w przestrzeni nazw. Nie zezwala na wyświetlanie ról ani powiązań ról. Ta rola nie zezwala na przeglądanie `Secrets` , ponieważ odczytywanie zawartości wpisów tajnych umożliwia dostęp do poświadczeń usługi ServiceAccount w przestrzeni nazw, co umożliwia dostęp do interfejsu API jako dowolne ServiceAccount w przestrzeni nazw (w formie eskalacji uprawnień).  |
| Składnik zapisywania RBAC usługi Azure Kubernetes Service | Zezwala na dostęp do odczytu/zapisu do większości obiektów w przestrzeni nazw. Ta rola nie zezwala na przeglądanie ani modyfikowanie ról ani powiązań ról. Jednak ta rola pozwala uzyskać dostęp do `Secrets` i uruchamiać zasobniki jako dowolne ServiceAccount w przestrzeni nazw, dzięki czemu można je wykorzystać do uzyskania poziomów dostępu interfejsu API dowolnego konta ServiceAccount w przestrzeni nazw. |
| Administrator RBAC usługi Azure Kubernetes Service  | Zezwala na dostęp administratora, który ma być przyznany w przestrzeni nazw. Zezwala na dostęp do odczytu/zapisu do większości zasobów w przestrzeni nazw (lub zakresie klastra), w tym możliwość tworzenia ról i powiązań ról w przestrzeni nazw. Ta rola nie zezwala na dostęp do zapisu do przydziału zasobów ani do samego obszaru nazw. |
| Administrator klastra RBAC usługi Azure Kubernetes Service  | Zezwala na dostęp administratora do wykonywania dowolnych akcji względem dowolnego zasobu. Zapewnia pełną kontrolę nad każdym zasobem w klastrze i we wszystkich przestrzeniach nazw. |


Przypisania ról należące do **całego klastra AKS** można wykonać w bloku Access Control (IAM) zasobu klastra na Azure Portal lub przy użyciu poleceń interfejsu wiersza polecenia platformy Azure, jak pokazano poniżej:

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

gdzie `<AAD-ENTITY-ID>` może być nazwą użytkownika (na przykład), a user@contoso.com nawet ClientID nazwy głównej usługi.

Można również utworzyć przypisania ról do zakresu określonej **przestrzeni nazw** w klastrze:

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

Dzisiaj przypisania ról należące do zakresu przestrzeni nazw muszą zostać skonfigurowane za pomocą interfejsu wiersza polecenia platformy Azure.


### <a name="create-custom-roles-definitions"></a>Tworzenie definicji ról niestandardowych

Opcjonalnie możesz utworzyć własną definicję roli, a następnie przypisać ją jak powyżej.

Poniżej znajduje się przykład definicji roli, która umożliwia użytkownikowi tylko odczytywanie wdrożeń i nic innego. Pełną listę możliwych akcji można sprawdzić [tutaj](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice).


Skopiuj poniższy kod JSON do pliku o nazwie `deploy-view.json` .

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

Zastąp ciąg `<YOUR SUBSCRIPTION ID>` identyfikatorem z subskrypcji, którą możesz uzyskać, uruchamiając polecenie:

```azurecli-interactive
az account show --query id -o tsv
```


Teraz możemy utworzyć definicję roli, uruchamiając poniższe polecenie z folderu, w którym zapisano `deploy-view.json` :

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

Teraz, gdy masz definicję roli, możesz ją przypisać do użytkownika lub innej tożsamości, uruchamiając następujące polecenie:

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>Korzystanie z usługi Azure RBAC na potrzeby autoryzacji Kubernetes przy użyciu `kubectl`

> [!NOTE]
> Upewnij się, że masz najnowszą polecenia kubectl, uruchamiając następujące polecenie:
>
> ```azurecli-interactive
> az aks install-cli
> ```
> Może być konieczne uruchomienie go z `sudo` uprawnieniami. 

Teraz masz przypisaną żądaną rolę i uprawnienia. Możesz rozpocząć wywoływanie interfejsu API Kubernetes, na przykład z `kubectl` .

W tym celu należy najpierw pobrać kubeconfig klastra przy użyciu poniższego polecenia:

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> Do wykonania powyższych kroków będzie potrzebna wbudowana rola [użytkownika klastra usługi Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) .

Teraz możesz użyć polecenia kubectl, aby na przykład wyświetlić listę węzłów w klastrze. Przy pierwszym uruchomieniu należy się zalogować, a kolejne polecenia będą używały odpowiedniego tokenu dostępu.

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>Korzystanie z usługi Azure RBAC na potrzeby autoryzacji Kubernetes przy użyciu `kubelogin`

Aby odblokować dodatkowe scenariusze, takie jak logowania nieinterakcyjne, starsze `kubectl` wersje lub korzystające z logowania JEDNOkrotnego w wielu klastrach bez konieczności logowania się do nowego klastra, nadano, że token jest nadal ważny, AKS utworzyć wtyczkę exec o nazwie [`kubelogin`](https://github.com/Azure/kubelogin) .

Można go użyć, uruchamiając:

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

Po raz pierwszy trzeba będzie zalogować się interaktywnie, podobnie jak w przypadku zwykłych polecenia kubectl, ale następnie nie będzie już potrzebne, nawet w przypadku nowych klastrów usługi Azure AD (o ile token jest nadal ważny).

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>Czyszczenie

### <a name="clean-role-assignment"></a>Wyczyść przypisanie roli

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
Skopiuj identyfikator lub identyfikatory ze wszystkich przypisań, a następnie.

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>Wyczyść definicję roli

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>Usuń klaster i grupę zasobów

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej na temat uwierzytelniania AKS, autoryzacji, Kubernetes RBAC i usługi Azure RBAC [tutaj](concepts-identity.md).
- Więcej informacji na temat usługi Azure RBAC można znaleźć [tutaj](../role-based-access-control/overview.md).
- Przeczytaj więcej na temat wszystkich akcji, których można użyć, aby szczegółowo zdefiniować niestandardowe role platformy Azure na [potrzeby autoryzacji Kubernetes](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice).


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli&preserve-view=true
