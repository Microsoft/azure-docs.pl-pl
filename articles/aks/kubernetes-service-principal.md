---
title: Jednostki usługi dla usługi Azure Kubernetes Service (AKS)
description: Tworzenie jednostki usługi Azure Active Directory dla klastra w usłudze Azure Kubernetes Service (AKS) i zarządzanie nią
services: container-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: b4b5b3eedb2e63686e1bb26580ea653e3a50a910
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507827"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Jednostki usługi w usłudze Azure Kubernetes Service (AKS)

Aby można było korzystać z interfejsów API platformy Azure, klaster AKS wymaga jednostki [usługi Azure Active Directory (AD)][aad-service-principal] lub [tożsamości zarządzanej](use-managed-identity.md). Jednostka usługi lub zarządzana tożsamość jest wymagana do dynamicznego tworzenia i zarządzania innymi zasobami platformy Azure, takimi jak moduł równoważenia obciążenia platformy Azure lub rejestr kontenerów (ACR).

W tym artykule przedstawiono sposób tworzenia jednostki usługi dla klastra usługi AKS i zarządzania nią.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby utworzyć jednostkę usługi Azure AD, musisz mieć uprawnienia do zarejestrowania aplikacji w swojej dzierżawie usługi Azure AD i przypisania aplikacji do roli w swojej subskrypcji. Jeśli nie masz niezbędnych uprawnień, może być konieczne zwrócenie się z prośbą do administratora usługi Azure AD lub subskrypcji o przyznanie niezbędnych uprawnień lub wstępne utworzenie jednostki usługi do użycia z klastrem usługi AKS.

Jeśli używasz jednostki usługi z innej dzierżawy usługi Azure AD, istnieją dodatkowe zagadnienia dotyczące uprawnień dostępnych podczas wdrażania klastra. Licencjobiorca może nie mieć odpowiednich uprawnień do odczytu i zapisu informacji katalogowych. Aby uzyskać więcej informacji, zobacz [co to są domyślne uprawnienia użytkownika w Azure Active Directory?][azure-ad-permissions]

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

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
> Jeśli używasz istniejącej jednostki usługi z dostosowanym kluczem tajnym, upewnij się, że wpis tajny nie jest dłuższy niż 190 bajtów.

W przypadku wdrażania klastra usługi AKS przy użyciu witryny Azure Portal na stronie *Uwierzytelnianie* okna dialogowego **Tworzenie klastra Kubernetes**, wybierz opcję **Konfigurowanie jednostki usługi**. Wybierz pozycję **Użyj istniejącej**, a następnie określ następujące wartości:

- **Identyfikator klienta jednostki usługi** to Twój *appId*
- **Klucz tajny klienta jednostki usługi** to wartość *hasła*

![Obraz przedstawiający przechodzenie do aplikacji Azure Vote](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Delegowanie dostępu do innych zasobów platformy Azure

Nazwa główna usługi klastra AKS może służyć do dostępu do innych zasobów. Jeśli na przykład chcesz wdrożyć klaster AKS w istniejącej podsieci sieci wirtualnej platformy Azure lub połączyć się z usługą Azure Container Registry (ACR), musisz delegować dostęp do tych zasobów do nazwy głównej usługi.

Aby delegować uprawnienia, Utwórz przypisanie roli za pomocą polecenia [AZ role przypisanie Create][az-role-assignment-create] . Przypisz `appId` do określonego zakresu, na przykład grupy zasobów lub zasobu sieci wirtualnej. Rola następnie definiuje uprawnienia, które jednostka usługi ma względem zasobu, jak pokazano w poniższym przykładzie:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

`--scope`Dla zasobu musi być pełny identyfikator zasobu, taki jak */subscriptions/ \<guid\> /ResourceGroups/myResourceGroup* lub */subscriptions/ \<guid\> /resourceGroups/myResourceGroupVnet/Providers/Microsoft.Network/virtualNetworks/myVnet*

> [!NOTE]
> Jeśli usunięto przypisanie roli współautor z grupy zasobów węzła, operacje poniżej mogą zakończyć się niepowodzeniem.  

W poniższych sekcjach opisano typowe delegacje, które należy wykonać.

### <a name="azure-container-registry"></a>Azure Container Registry

Jeśli używasz Azure Container Registry (ACR) jako magazynu obrazów kontenerów, musisz udzielić uprawnień do jednostki usługi dla klastra AKS w celu odczytywania i ściągania obrazów. Obecnie zalecaną konfiguracją jest użycie polecenia [AZ AKS Create][az-aks-create] lub [AZ AKS Update][az-aks-update] do integracji z rejestrem i przypisanie odpowiedniej roli dla jednostki usługi. Aby uzyskać szczegółowe instrukcje, zobacz [uwierzytelnianie za pomocą Azure Container Registry z usługi Azure Kubernetes][aks-to-acr].

### <a name="networking"></a>Sieć

Możesz użyć zaawansowanych funkcji sieciowych, w przypadku których sieć wirtualna i podsieć lub publiczne adresy IP znajdują się w innej grupie zasobów. Przypisz wbudowaną rolę [współautor sieci][rbac-network-contributor] w podsieci w sieci wirtualnej. Alternatywnie można utworzyć [rolę niestandardową][rbac-custom-role] z uprawnieniami dostępu do zasobów sieciowych w danej grupie zasobów. Aby uzyskać więcej informacji, zobacz [uprawnienia usługi AKS][aks-permissions] .

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
- Domyślnie poświadczenia jednostki usługi są ważne przez jeden rok. [Poświadczenia nazwy głównej usługi można zaktualizować lub obrócić][update-credentials] w dowolnym momencie.
- Każda jednostka usługi jest skojarzona z aplikacją usługi Azure AD. Nazwa główna usługi dla klastra Kubernetes może być skojarzona z dowolną prawidłową nazwą aplikacji usługi Azure AD (na przykład: *https://www.contoso.org/example* ). Adres URL dla aplikacji nie musi być rzeczywistym punktem końcowym.
- Podczas określania **identyfikatora klienta** jednostki usługi użyj wartości `appId`.
- Na maszynach wirtualnych węzłów agenta w klastrze Kubernetes poświadczenia jednostki usługi są przechowywane w pliku `/etc/kubernetes/azure.json`
- Gdy używasz polecenia [az aks create][az-aks-create], aby automatycznie wygenerować jednostkę usługi, poświadczenia jednostki usługi są zapisywane w pliku `~/.azure/aksServicePrincipal.json` na maszynie użytej do uruchomienia polecenia.
- Jeśli nie przekażesz podmiotu usługi do dodatkowych poleceń interfejsu wiersza polecenia AKS, zostanie użyta domyślna nazwa główna usługi `~/.azure/aksServicePrincipal.json` .  
- Opcjonalnie można również usunąć aksServicePrincipal.jsw pliku, a AKS utworzy nową nazwę główną usługi.
- Usunięcie klastra AKS utworzonego za pomocą polecenia [az aks create][az-aks-create] nie powoduje usunięcia utworzonej automatycznie jednostki usługi.
    - Aby usunąć nazwę główną usługi, wykonaj zapytanie dotyczące klastra *servicePrincipalProfile. clientId* , a następnie usuń za pomocą [AZ AD Sp Delete][az-ad-sp-delete]. Zastąp następujące nazwy klastra i grupy zasobów własnymi wartościami:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Poświadczenia nazwy głównej usługi dla klastra AKS są buforowane przez interfejs wiersza polecenia platformy Azure. Jeśli te poświadczenia wygasną, wystąpią błędy podczas wdrażania klastrów AKS. Następujący komunikat o błędzie podczas uruchamiania [AZ AKS Create][az-aks-create] może wskazywać na problem z poświadczeniami jednostki usługi w pamięci podręcznej:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Sprawdź wiek pliku poświadczeń przy użyciu następującego polecenia:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

Domyślny czas wygaśnięcia poświadczeń jednostki usługi wynosi jeden rok. Jeśli *aksServicePrincipal.jsw* pliku jest starsza niż rok, Usuń ten plik i spróbuj ponownie WDROŻYĆ klaster AKS.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Active Directory jednostek usługi, zobacz [obiekty główne aplikacji i usługi][service-principal].

Aby uzyskać informacje na temat aktualizowania poświadczeń, zobacz [Aktualizowanie lub obracanie poświadczeń dla jednostki usługi w AKS][update-credentials].

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-delete]: /cli/azure/ad/sp#az_ad_sp_delete
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
[aks-permissions]: concepts-identity.md#aks-service-permissions
