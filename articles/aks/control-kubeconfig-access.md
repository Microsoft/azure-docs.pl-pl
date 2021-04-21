---
title: Ograniczanie dostępu do aplikacji kubeconfig w Azure Kubernetes Service (AKS)
description: Dowiedz się, jak kontrolować dostęp do pliku konfiguracji Kubernetes (kubeconfig) dla administratorów klastra i użytkowników klastra
services: container-service
ms.topic: article
ms.date: 05/06/2020
ms.openlocfilehash: 279ca9800d7d721cc2e77d269cb577d8bd166d41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765561"
---
# <a name="use-azure-role-based-access-control-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Używanie kontroli dostępu opartej na rolach platformy Azure do definiowania dostępu do pliku konfiguracji platformy Kubernetes w usłudze Azure Kubernetes Service (AKS)

Za pomocą narzędzia można wchodzić w interakcje z klastrami `kubectl` Kubernetes. Interfejs wiersza polecenia platformy Azure zapewnia łatwy sposób uzyskiwania poświadczeń dostępu i informacji o konfiguracji w celu nawiązania połączenia z klastrami usługi AKS przy użyciu polecenia `kubectl` . Aby ograniczyć to, kto może uzyskać te informacje o konfiguracji *kubernetes (kubeconfig)* i ograniczyć uprawnienia, które mają, możesz użyć kontroli dostępu opartej na rolach (RBAC) platformy Azure.

W tym artykule przedstawiono sposób przypisywania ról platformy Azure, które ograniczają, kto może uzyskać informacje o konfiguracji klastra usługi AKS.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra usługi AKS, zobacz przewodnik Szybki start usługi AKS przy użyciu interfejsu wiersza polecenia platformy [Azure][aks-quickstart-cli] lub [interfejsu Azure Portal][aks-quickstart-portal].

Ten artykuł wymaga również interfejsu wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Dostępne uprawnienia ról klastra

Podczas interakcji z klastrem usługi AKS za pomocą narzędzia jest używany plik konfiguracji, który `kubectl` definiuje informacje o połączeniu klastra. Ten plik konfiguracji jest zwykle przechowywany w *pliku ~/.kube/config.* W tym pliku *kubeconfig* można zdefiniować wiele klastrów. Przełączasz się między klastrami za pomocą [polecenia kubectl config use-context.][kubectl-config-use-context]

Polecenie [az aks get-credentials][az-aks-get-credentials] umożliwia uzyskanie poświadczeń dostępu dla klastra usługi AKS i scalenie ich z *plikiem kubeconfig.* Aby kontrolować dostęp do tych poświadczeń, możesz użyć kontroli dostępu opartej na rolach (RBAC) platformy Azure. Te role platformy Azure umożliwiają zdefiniowanie, kto może pobrać plik *kubeconfig* i jakie uprawnienia ma w klastrze.

Dwie wbudowane role to:

* **Azure Kubernetes Service roli administratora klastra**  
  * Umożliwia dostęp do interfejsu API *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action.* To wywołanie interfejsu API [wyświetla poświadczenia administratora klastra][api-cluster-admin].
  * Pobiera *plik kubeconfig* dla *roli clusterAdmin.*
* **Azure Kubernetes Service roli użytkownika klastra**
  * Umożliwia dostęp do wywołania interfejsu API *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action.* To wywołanie interfejsu API [wyświetla poświadczenia użytkownika klastra][api-cluster-user].
  * Pobiera *plik kubeconfig dla* roli *clusterUser.*

Te role platformy Azure można zastosować do Azure Active Directory (AD) lub grupy.

> [!NOTE]
> W klastrach korzystających z usługi Azure AD użytkownicy z rolą *clusterUser* mają pusty plik *kubeconfig,* który monituje o zalogowanie. Po zalogowaniu użytkownicy mają dostęp na podstawie ustawień użytkownika lub grupy usługi Azure AD. Użytkownicy z rolą *clusterAdmin mają* dostęp administratora.
>
> Klastry, które nie korzystają z usługi Azure AD, używają tylko *roli clusterAdmin.*

## <a name="assign-role-permissions-to-a-user-or-group"></a>Przypisywanie uprawnień roli do użytkownika lub grupy

Aby przypisać jedną z dostępnych ról, musisz uzyskać identyfikator zasobu klastra usługi AKS i identyfikator konta użytkownika lub grupy usługi Azure AD. Następujące przykładowe polecenia:

* Pobierz identyfikator zasobu klastra za pomocą polecenia [az aks show][az-aks-show] dla klastra o nazwie *myAKSCluster* w grupie zasobów *myResourceGroup.* W razie potrzeby podaj własną nazwę klastra i grupy zasobów.
* Użyj poleceń [az account show i][az-account-show] az ad user [show,][az-ad-user-show] aby uzyskać identyfikator użytkownika.
* Na koniec przypisz rolę za pomocą [polecenia az role assignment create.][az-role-assignment-create]

W poniższym przykładzie przypisano *Azure Kubernetes Service administratora klastra* do pojedynczego konta użytkownika:

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
> W niektórych przypadkach konto *user.name* inne niż *userPrincipalName,* na przykład w przypadku użytkowników-gości usługi Azure AD:
>
> ```output
> $ az account show --query user.name -o tsv
> user@contoso.com
> $ az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv
> user_contoso.com#EXT#@contoso.onmicrosoft.com
> ```
>
> W takim przypadku ustaw wartość parametru *ACCOUNT_UPN* *userPrincipalName* użytkownika usługi Azure AD. Jeśli na przykład  Twoje konto user.name *\@ użytkownikiem, contoso.com*:
> 
> ```azurecli-interactive
> ACCOUNT_UPN=$(az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv)
> ```

> [!TIP]
> Jeśli chcesz przypisać uprawnienia do grupy usługi Azure AD, zaktualizuj parametr pokazany w poprzednim przykładzie przy użyciu identyfikatora obiektu dla grupy, `--assignee` a nie *użytkownika*.  Aby uzyskać identyfikator obiektu dla grupy, użyj [polecenia az ad group show.][az-ad-group-show] Poniższy przykład pobiera identyfikator obiektu dla grupy usługi Azure AD o nazwie *appdev:*`az ad group show --group appdev --query objectId -o tsv`

W razie potrzeby można zmienić poprzednie przypisanie do roli *użytkownika* klastra.

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

## <a name="get-and-verify-the-configuration-information"></a>Uzyskiwanie i weryfikowanie informacji o konfiguracji

Po przypisaniu ról platformy Azure użyj [polecenia az aks get-credentials,][az-aks-get-credentials] aby pobrać *definicję kubeconfig* dla klastra usługi AKS. Poniższy przykład pobiera poświadczenia *--admin,* które działają prawidłowo, jeśli użytkownik ma przyznaną rolę *administratora klastra:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Następnie możesz użyć polecenia [kubectl config view,][kubectl-config-view] aby sprawdzić, czy kontekst klastra pokazuje, że zastosowano informacje o konfiguracji administratora: 

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

## <a name="remove-role-permissions"></a>Usuwanie uprawnień roli

Aby usunąć przypisania ról, użyj [polecenia az role assignment delete.][az-role-assignment-delete] Określ identyfikator konta i identyfikator zasobu klastra uzyskany w poprzednich poleceniach. Jeśli przypisano rolę do grupy, a nie do użytkownika, określ odpowiedni identyfikator obiektu grupy, a nie identyfikator obiektu konta dla `--assignee` parametru:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Następne kroki

Aby zwiększyć bezpieczeństwo dostępu do klastrów usługi AKS, [zintegruj Azure Active Directory uwierzytelniania.][aad-integration]

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-account-show]: /cli/azure/account#az_account_show
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-role-assignment-delete]: /cli/azure/role/assignment#az_role_assignment_delete
[aad-integration]: ./azure-ad-integration-cli.md
[az-ad-group-show]: /cli/azure/ad/group#az_ad_group_show
