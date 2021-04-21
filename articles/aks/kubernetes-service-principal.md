---
title: Jednostki usługi dla usługi Azure Kubernetes Service (AKS)
description: Tworzenie jednostki usługi Azure Active Directory dla klastra w usłudze Azure Kubernetes Service (AKS) i zarządzanie nią
services: container-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 2f32ce96097e008ac1100c62c04b6bb7001ae972
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779637"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Jednostki usługi w usłudze Azure Kubernetes Service (AKS)

Do interakcji z interfejsami API platformy Azure klaster usługi AKS wymaga jednostki [usługi Azure Active Directory (AD)][aad-service-principal] lub tożsamości [zarządzanej.](use-managed-identity.md) Do dynamicznego tworzenia innych zasobów platformy Azure, takich jak usługa Azure Load Balancer lub rejestr kontenerów (ACR), potrzebna jest główna usługa lub tożsamość zarządzana.

W tym artykule przedstawiono sposób tworzenia jednostki usługi dla klastra usługi AKS i zarządzania nią.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby utworzyć jednostkę usługi Azure AD, musisz mieć uprawnienia do zarejestrowania aplikacji w swojej dzierżawie usługi Azure AD i przypisania aplikacji do roli w swojej subskrypcji. Jeśli nie masz niezbędnych uprawnień, może być konieczne zwrócenie się z prośbą do administratora usługi Azure AD lub subskrypcji o przyznanie niezbędnych uprawnień lub wstępne utworzenie jednostki usługi do użycia z klastrem usługi AKS.

Jeśli używasz jednostki usługi z innej dzierżawy usługi Azure AD, istnieją dodatkowe zagadnienia dotyczące uprawnień dostępnych podczas wdrażania klastra. Być może nie masz odpowiednich uprawnień do odczytu i zapisu informacji o katalogu. Aby uzyskać więcej informacji, [zobacz What are the default user permissions in Azure Active Directory? (Jakie][azure-ad-permissions] są domyślne uprawnienia użytkownika w u Azure Active Directory?

Musisz również zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Automatyczne tworzenie i używanie jednostki usługi

Podczas tworzenia klastra usługi AKS w witrynie Azure Portal lub przy użyciu polecenia [az aks create][az-aks-create] platforma Azure może automatycznie generować jednostkę usługi.

W poniższym przykładzie dotyczącym interfejsu wiersza polecenia platformy Azure nie została określona jednostka usługi. W tym scenariuszu interfejs wiersza polecenia platformy Azure tworzy jednostkę usługi dla klastra usługi AKS. Aby można było pomyślnie ukończyć tę operację, Twoje konto platformy Azure musi mieć odpowiednie uprawnienia do tworzenia jednostki usługi.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Ręczne tworzenie jednostki usługi

Aby ręcznie utworzyć jednostkę usługi przy użyciu interfejsu wiersza polecenia platformy Azure, użyj polecenia [az ad sp create-for-rbac][az-ad-sp-create]. W poniższym przykładzie parametr `--skip-assignment` zapobiega przypisaniu jakichkolwiek dodatkowych przypisań:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

Dane wyjściowe będą podobne do poniższego przykładu. Zanotuj własne wartości `appId` i `password`. Te wartości będą używane podczas tworzenia klastra usługi AKS w następnej sekcji.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Określanie jednostki usługi dla klastra usługi AKS

Aby użyć istniejącej jednostki usługi podczas tworzenia klastra usługi AKS za pomocą polecenia [az aks create][az-aks-create], użyj parametrów `--service-principal` i `--client-secret` w celu określenia właściwości `appId` i `password` z danych wyjściowych polecenia [az ad sp create-for-rbac][az-ad-sp-create]:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

> [!NOTE]
> Jeśli używasz istniejącej jednostki usługi z dostosowanym kluczem tajnym, upewnij się, że nie jest on dłuższy niż 190 bajtów.

W przypadku wdrażania klastra usługi AKS przy użyciu witryny Azure Portal na stronie *Uwierzytelnianie* okna dialogowego **Tworzenie klastra Kubernetes**, wybierz opcję **Konfigurowanie jednostki usługi**. Wybierz pozycję **Użyj istniejącej**, a następnie określ następujące wartości:

- **Identyfikator klienta jednostki usługi** to Twój *appId*
- **Klucz tajny klienta jednostki usługi** to wartość *hasła*

![Obraz przedstawiający przechodzenie do aplikacji Azure Vote](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Delegowanie dostępu do innych zasobów platformy Azure

Nazwa główna usługi klastra AKS może służyć do dostępu do innych zasobów. Jeśli na przykład chcesz wdrożyć klaster usługi AKS w istniejącej podsieci sieci wirtualnej platformy Azure lub połączyć się z usługą Azure Container Registry (ACR), musisz delegować dostęp do tych zasobów do jednostki usługi.

Aby delegować uprawnienia, utwórz przypisanie roli za pomocą [polecenia az role assignment create.][az-role-assignment-create] Przypisz `appId` do określonego zakresu, takiego jak grupa zasobów lub zasób sieci wirtualnej. Rola następnie definiuje uprawnienia, które jednostka usługi ma względem zasobu, jak pokazano w poniższym przykładzie:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

Identyfikator zasobu musi być pełnym identyfikatorem zasobu, takim jak `--scope` *\<guid\> /subscriptions/ /resourceGroups/myResourceGroup* lub */subscriptions/ \<guid\> /resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

> [!NOTE]
> Jeśli usunięto przypisanie roli Współautor z grupy zasobów węzła, poniższe operacje mogą się nie powieść.  

W poniższych sekcjach opisano typowe delegacje, które należy wykonać.

### <a name="azure-container-registry"></a>Azure Container Registry

Jeśli używasz usługi Azure Container Registry (ACR) jako magazynu obrazów kontenera, musisz udzielić uprawnień do jednostki usługi dla klastra AKS w celu odczytu i ściągania obrazów. Obecnie zalecaną konfiguracją jest użycie [polecenia az aks create][az-aks-create] lub az [aks update][az-aks-update] w celu integracji z rejestrem i przypisania odpowiedniej roli dla jednostki usługi. Aby uzyskać szczegółowe instrukcje, zobacz [Authenticate with Azure Container Registry from Azure Kubernetes Service (Uwierzytelnianie za pomocą Azure Kubernetes Service][aks-to-acr]).

### <a name="networking"></a>Sieć

Możesz użyć zaawansowanych funkcji sieciowych, w przypadku których sieć wirtualna i podsieć lub publiczne adresy IP znajdują się w innej grupie zasobów. Przypisz [wbudowaną][rbac-network-contributor] rolę Współautor sieci w podsieci w sieci wirtualnej. Alternatywnie możesz utworzyć rolę [niestandardową z uprawnieniami][rbac-custom-role] dostępu do zasobów sieciowych w tej grupie zasobów. Aby uzyskać więcej informacji, zobacz Uprawnienia usługi [AKS.][aks-permissions]

### <a name="storage"></a>Storage

Konieczne może być uzyskanie dostępu do istniejących zasobów dysku w innej grupie zasobów. Przypisz jeden z następujących zestawów uprawnień ról:

- Utwórz [rolę niestandardową][rbac-custom-role] i zdefiniuj następujące uprawnienia roli:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Innym rozwiązaniem jest przypisanie wbudowanej roli [Współautor konta magazynu][rbac-storage-contributor] do grupy zasobów

### <a name="azure-container-instances"></a>Azure Container Instances

Jeśli do integracji z usługą AKS używasz rozwiązania Virtual Kubelet i decydujesz się na uruchamianie usługi Azure Container Instances (ACI) w grupie zasobów niezależnie od klastra AKS, jednostka usługi AKS musi otrzymać uprawnienia *Współautor* w grupie zasobów usługi ACI.

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

Podczas korzystania z jednostek usług AKS i Azure AD należy pamiętać o następujących kwestiach.

- Jednostka usługi dla rozwiązania Kubernetes jest częścią konfiguracji klastra. Nie należy jednak używać tożsamości do wdrażania klastra.
- Domyślnie poświadczenia jednostki usługi są ważne przez jeden rok. Poświadczenia [jednostki usługi można zaktualizować lub obrócić w][update-credentials] dowolnym momencie.
- Każda jednostka usługi jest skojarzona z aplikacją usługi Azure AD. Jednostkę usługi dla klastra Kubernetes można skojarzyć z dowolną prawidłową nazwą aplikacji usługi Azure AD (na przykład *https://www.contoso.org/example* : ). Adres URL dla aplikacji nie musi być rzeczywistym punktem końcowym.
- Podczas określania **identyfikatora klienta** jednostki usługi użyj wartości `appId`.
- W przypadku maszyn wirtualnych węzła agenta w klastrze Kubernetes poświadczenia jednostki usługi są przechowywane w pliku `/etc/kubernetes/azure.json`
- Gdy używasz polecenia [az aks create][az-aks-create], aby automatycznie wygenerować jednostkę usługi, poświadczenia jednostki usługi są zapisywane w pliku `~/.azure/aksServicePrincipal.json` na maszynie użytej do uruchomienia polecenia.
- Jeśli nie przekażemy jednostki usługi w dodatkowych poleceniach interfejsu wiersza polecenia usługi AKS, zostanie użyta domyślna jednostka `~/.azure/aksServicePrincipal.json` usługi znajdująca się w .  
- Opcjonalnie możesz również usunąć plik aksServicePrincipal.js, a usługa AKS utworzy nową jednostkę usługi.
- Usunięcie klastra AKS utworzonego za pomocą polecenia [az aks create][az-aks-create] nie powoduje usunięcia utworzonej automatycznie jednostki usługi.
    - Aby usunąć jednostkę usługi, należy zapytanie dotyczące klastra *servicePrincipalProfile.clientId,* a następnie usunąć za pomocą [az ad sp delete][az-ad-sp-delete]. Zastąp następujące nazwy klastra i grupy zasobów własnymi wartościami:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Poświadczenia jednostki usługi dla klastra usługi AKS są buforowane przez interfejs wiersza polecenia platformy Azure. Jeśli te poświadczenia wygasły, podczas wdrażania klastrów usługi AKS występują błędy. Następujący komunikat o błędzie podczas uruchamiania [az aks create][az-aks-create] może wskazywać problem z poświadczeniami jednostki usługi w pamięci podręcznej:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Sprawdź wiek pliku poświadczeń przy użyciu następującego polecenia:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

Domyślny czas wygaśnięcia poświadczeń jednostki usługi wynosi jeden rok. Jeśli plik *aksServicePrincipal.js* jest starszy niż jeden rok, usuń plik i spróbuj ponownie wdrożyć klaster usługi AKS.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Active Directory jednostki usługi, zobacz [Application and service principal objects (Obiekty aplikacji i jednostki usługi).][service-principal]

Aby uzyskać informacje na temat aktualizowania poświadczeń, zobacz Aktualizowanie lub obracanie poświadczeń dla jednostki usługi w [usłudze AKS.][update-credentials]

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-ad-sp-delete]: /cli/azure/ad/sp#az_ad_sp_delete
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az_ad_app_list
[az-ad-app-delete]: /cli/azure/ad/app#az_ad_app_delete
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
[aks-permissions]: concepts-identity.md#aks-service-permissions
