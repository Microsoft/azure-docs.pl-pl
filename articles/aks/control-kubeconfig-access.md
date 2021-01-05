---
title: Ograniczanie dostępu do kubeconfig w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak kontrolować dostęp do pliku Kubernetes Configuration (kubeconfig) dla administratorów klastrów i użytkowników klastrów
services: container-service
ms.topic: article
ms.date: 05/06/2020
ms.openlocfilehash: 77b9988557106ef460d3b222ef85eb29e08f31c8
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693992"
---
# <a name="use-azure-role-based-access-control-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Użycie kontroli dostępu opartej na rolach na platformie Azure w celu zdefiniowania dostępu do pliku konfiguracji Kubernetes w usłudze Azure Kubernetes Service (AKS)

Za pomocą narzędzia można korzystać z klastrów Kubernetes `kubectl` . Interfejs wiersza polecenia platformy Azure udostępnia łatwy sposób uzyskiwania poświadczeń dostępu i informacji o konfiguracji w celu łączenia się z klastrami AKS przy użyciu programu `kubectl` . Aby ograniczyć liczbę użytkowników, którzy mogą uzyskać informacje o konfiguracji usługi Kubernetes (*kubeconfig*) i ograniczyć uprawnienia do nich, możesz użyć kontroli dostępu opartej na rolach (Azure RBAC).

W tym artykule opisano sposób przypisywania ról platformy Azure, które ograniczają, kto może uzyskać informacje o konfiguracji dla klastra AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Ten artykuł wymaga również uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Dostępne uprawnienia ról klastra

Podczas pracy z klastrem AKS przy użyciu `kubectl` narzędzia jest używany plik konfiguracji, który definiuje informacje o połączeniu z klastrem. Ten plik konfiguracji jest zwykle przechowywany w *~/.Kube/config*. W tym pliku *kubeconfig* można zdefiniować wiele klastrów. Można przełączać się między klastrami przy użyciu polecenia [polecenia kubectl config use-Context][kubectl-config-use-context] .

Polecenie [AZ AKS Get-Credentials][az-aks-get-credentials] pozwala uzyskać poświadczenia dostępu do klastra AKS i scalić je z plikiem *kubeconfig* . Aby kontrolować dostęp do tych poświadczeń, można użyć kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure. Te role platformy Azure umożliwiają zdefiniowanie osób, które mogą pobrać plik *kubeconfig* i jakie mają uprawnienia w klastrze.

Dwie wbudowane role to:

* **Rola administratora klastra usługi Kubernetes platformy Azure**  
  * Zezwala na dostęp do wywołania interfejsu API *Microsoft. ContainerService/managedClusters/listClusterAdminCredential/Action* . To wywołanie interfejsu API [wyświetla listę poświadczeń administratora klastra][api-cluster-admin].
  * Pobiera *kubeconfig* dla roli *clusterAdmin* .
* **Rola użytkownika klastra usługi Azure Kubernetes Service**
  * Zezwala na dostęp do wywołania interfejsu API *Microsoft. ContainerService/managedClusters/listClusterUserCredential/Action* . To wywołanie interfejsu API [wyświetla listę poświadczeń użytkownika klastra][api-cluster-user].
  * Pobiera *kubeconfig* dla roli *clusterUser* .

Te role platformy Azure można stosować do użytkownika lub grupy Azure Active Directory (AD).

> [!NOTE]
> W klastrach korzystających z usługi Azure AD użytkownicy z rolą *clusterUser* mają pusty plik *kubeconfig* , który będzie monitował o logowanie. Po zalogowaniu użytkownicy będą mieli dostęp na podstawie ustawień użytkownika lub grupy usługi Azure AD. Użytkownicy z rolą *clusterAdmin* mają dostęp administratora.
>
> W klastrach, które nie korzystają z usługi Azure AD, należy używać tylko roli *clusterAdmin* .

## <a name="assign-role-permissions-to-a-user-or-group"></a>Przypisywanie uprawnień roli użytkownikowi lub grupie

Aby przypisać jedną z dostępnych ról, należy uzyskać identyfikator zasobu klastra AKS oraz identyfikator konta użytkownika lub grupy usługi Azure AD. Następujące przykładowe polecenia:

* Pobierz identyfikator zasobu klastra przy użyciu polecenia [AZ AKS show][az-aks-show] dla klastra o nazwie *myAKSCluster* w grupie zasobów *WebResource* . Podaj własny klaster i nazwę grupy zasobów zgodnie z potrzebami.
* Użyj polecenia [AZ Account show][az-account-show] i [AZ AD User show][az-ad-user-show] , aby uzyskać identyfikator użytkownika.
* Na koniec Przypisz rolę za pomocą polecenia [AZ role Assign Create][az-role-assignment-create] .

Poniższy przykład przypisuje *rolę administratora klastra usługi Azure Kubernetes* do indywidualnego konta użytkownika:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!IMPORTANT]
> W niektórych przypadkach *User.Name* na koncie różni się od *userPrincipalName*, takiego jak użytkownicy-Goście usługi Azure AD:
>
> ```output
> $ az account show --query user.name -o tsv
> user@contoso.com
> $ az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv
> user_contoso.com#EXT#@contoso.onmicrosoft.com
> ```
>
> W takim przypadku należy ustawić wartość *ACCOUNT_UPN* na *userPrincipalName* od użytkownika usługi Azure AD. Na przykład jeśli Twoje konto *User.Name* to *User \@ contoso.com*:
> 
> ```azurecli-interactive
> ACCOUNT_UPN=$(az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv)
> ```

> [!TIP]
> Jeśli chcesz przypisać uprawnienia do grupy usługi Azure AD, zaktualizuj `--assignee` parametr wyświetlany w poprzednim przykładzie przy użyciu identyfikatora obiektu dla *grupy* , a nie *użytkownika*. Aby uzyskać identyfikator obiektu dla grupy, użyj polecenia [AZ AD Group Show][az-ad-group-show] . Poniższy przykład pobiera identyfikator obiektu dla grupy usługi Azure AD o nazwie *appdev*: `az ad group show --group appdev --query objectId -o tsv`

W razie potrzeby można zmienić poprzednie przypisanie do *roli użytkownika klastra* .

Następujące przykładowe dane wyjściowe pokazują, że przypisanie roli zostało pomyślnie utworzone:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>Pobieranie i weryfikowanie informacji o konfiguracji

Przy użyciu przypisanych ról platformy Azure Użyj polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] , aby uzyskać definicję *KUBECONFIG* dla klastra AKS. Poniższy przykład pobiera poświadczenia *--administratora* , które działają poprawnie, jeśli użytkownikowi udzielono *roli administratora klastra*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Następnie można użyć polecenia [View config polecenia kubectl][kubectl-config-view] , aby sprawdzić, czy *kontekst* klastra pokazuje, że zostały zastosowane informacje o konfiguracji administratora:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Usuń uprawnienia roli

Aby usunąć przypisania ról, użyj polecenia [AZ role przypisanie Delete][az-role-assignment-delete] . Określ identyfikator konta i identyfikator zasobu klastra, jak uzyskano w poprzednich poleceniach. Jeśli rola została przypisana do grupy, a nie jako użytkownik, określ odpowiedni identyfikator obiektu grupy zamiast identyfikatora obiektu konta dla `--assignee` parametru:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Następne kroki

Aby zwiększyć bezpieczeństwo dostępu do klastrów AKS, należy [zintegrować uwierzytelnianie Azure Active Directory][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: ./azure-ad-integration-cli.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
