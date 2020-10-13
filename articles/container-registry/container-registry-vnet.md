---
title: Ograniczanie dostępu przy użyciu punktu końcowego usługi
description: Ograniczanie dostępu do usługi Azure Container Registry przy użyciu punktu końcowego usług w sieci wirtualnej platformy Azure. Dostęp do punktu końcowego usługi jest funkcją warstwy Premium usługi.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 1fc8d54d677112a9c934f9079e953a7389939bde
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89488675"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Ograniczanie dostępu do rejestru kontenerów przy użyciu punktu końcowego usługi w sieci wirtualnej platformy Azure

[Usługa azure Virtual Network](../virtual-network/virtual-networks-overview.md) zapewnia bezpieczną i prywatną sieć dla zasobów platformy Azure i lokalnych. [Punkt końcowy usługi](../virtual-network/virtual-network-service-endpoints-overview.md) pozwala zabezpieczyć publiczny adres IP w rejestrze kontenerów tylko do sieci wirtualnej. Ten punkt końcowy zapewnia ruch optymalnie kierowany do zasobu za pośrednictwem sieci szkieletowej platformy Azure. Tożsamości sieci wirtualnej i podsieci są również przesyłane z każdym żądaniem.

W tym artykule pokazano, jak skonfigurować punkt końcowy usługi rejestru kontenerów (wersja zapoznawcza) w sieci wirtualnej. 

> [!IMPORTANT]
> Azure Container Registry teraz obsługuje [łącze prywatne platformy Azure](container-registry-private-link.md), umożliwiając prywatne punkty końcowe z sieci wirtualnej, która ma zostać umieszczona w rejestrze. Prywatne punkty końcowe są dostępne z poziomu sieci wirtualnej przy użyciu prywatnych adresów IP. Zalecamy używanie prywatnych punktów końcowych zamiast punktów końcowych usługi w większości scenariuszy sieciowych.

Konfigurowanie punktu końcowego usługi Registry Service jest dostępne w warstwie usługi Registry Container — wersja **Premium** . Aby uzyskać informacje o warstwach i ograniczeniach usługi Registry, zobacz [Azure Container Registry warstwy usług](container-registry-skus.md).

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Przyszłe Tworzenie punktów końcowych usługi dla Azure Container Registry nie jest obecnie planowane. Zalecamy używanie [prywatnych punktów końcowych](container-registry-private-link.md) .
* Nie można użyć Azure Portal w celu skonfigurowania punktów końcowych usługi w rejestrze.
* Tylko klaster [usługi Azure Kubernetes](../aks/intro-kubernetes.md) lub [maszyna wirtualna](../virtual-machines/linux/overview.md) platformy Azure może być używany jako host do uzyskiwania dostępu do rejestru kontenerów za pomocą punktu końcowego usługi. *Inne usługi platformy Azure, w tym Azure Container Instances nie są obsługiwane.*
* Każdy rejestr obsługuje maksymalnie 100 reguł dostępu do sieci.
* Punkty końcowe usługi dla Azure Container Registry nie są obsługiwane w chmurze platformy Azure dla instytucji rządowych lub platformy Azure w Chinach.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aby skorzystać z kroków interfejsu wiersza polecenia platformy Azure w tym artykule, wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.58 lub nowszej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

* Jeśli nie masz jeszcze rejestru kontenerów, utwórz go (wymagana warstwa Premium) i wypchnij przykładowy obraz, taki jak `hello-world` z usługi Docker Hub. Na przykład użyj [Azure Portal][quickstart-portal] lub [interfejsu wiersza polecenia platformy Azure][quickstart-cli] , aby utworzyć rejestr. 

* Jeśli chcesz ograniczyć dostęp do rejestru przy użyciu punktu końcowego usługi w innej subskrypcji platformy Azure, zarejestruj dostawcę zasobów dla Azure Container Registry w tej subskrypcji. Na przykład:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>Konfigurowanie dostępu do sieci dla rejestru

W tej sekcji Skonfiguruj rejestr kontenerów w taki sposób, aby zezwalał na dostęp z podsieci w sieci wirtualnej platformy Azure. Dostępne są równoważne kroki przy użyciu interfejsu wiersza polecenia platformy Azure i Azure Portal.

### <a name="allow-access-from-a-virtual-network---cli"></a>Zezwalanie na dostęp z sieci wirtualnej — interfejs wiersza polecenia

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Dodawanie punktu końcowego usługi do podsieci

Podczas tworzenia maszyny wirtualnej platforma Azure domyślnie tworzy sieć wirtualną w tej samej grupie zasobów. Nazwa sieci wirtualnej jest oparta na nazwie maszyny wirtualnej. Na przykład jeśli nazwasz maszyny wirtualnej *myDockerVM*, domyślną nazwą sieci wirtualnej jest *myDockerVMVNET*, z podsiecią o nazwie *myDockerVMSubnet*. Sprawdź to w Azure Portal lub przy użyciu polecenia [AZ Network VNET list][az-network-vnet-list] :

```azurecli
az network vnet list \
  --resource-group myResourceGroup \
  --query "[].{Name: name, Subnet: subnets[0].name}"
```

Dane wyjściowe:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

Użyj polecenia [AZ Network VNET Subnet Update][az-network-vnet-subnet-update] , aby dodać punkt końcowy usługi **Microsoft. ContainerRegistry** do podsieci. Zastąp nazwy sieci wirtualnej i podsieci w następującym poleceniu:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Użyj polecenia [AZ Network VNET Subnet show][az-network-vnet-subnet-show] , aby pobrać identyfikator zasobu podsieci. Jest to konieczne w późniejszym kroku, aby skonfigurować regułę dostępu do sieci.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Dane wyjściowe:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>Zmień domyślny dostęp sieciowy do rejestru

Domyślnie usługa Azure Container Registry umożliwia nawiązywanie połączeń między hostami w dowolnej sieci. Aby ograniczyć dostęp do wybranej sieci, Zmień domyślną akcję na Odmów dostępu. Zastąp nazwę rejestru następującym poleceniem [AZ ACR Update][az-acr-update] :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Dodawanie reguły sieci do rejestru

Użyj polecenia [AZ ACR Network-Rule Add][az-acr-network-rule-add] , aby dodać regułę sieci do rejestru, który umożliwia dostęp z podsieci maszyny wirtualnej. Zastąp nazwę rejestru kontenerów i identyfikator zasobu podsieci w następującym poleceniu: 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>Weryfikowanie dostępu do rejestru

Po odczekaniu kilku minut na aktualizację konfiguracji Sprawdź, czy maszyna wirtualna może uzyskać dostęp do rejestru kontenerów. Utwórz połączenie SSH z maszyną wirtualną i uruchom polecenie [AZ ACR login][az-acr-login] , aby zalogować się do rejestru. 

```bash
az acr login --name mycontainerregistry
```

Można wykonać operacje rejestru, takie jak Uruchom, `docker pull` Aby ściągnąć przykładowy obraz z rejestru. Zastąp wartość obrazu i tagu odpowiednią dla rejestru poprzedzoną prefiksem nazwy serwera logowania rejestru (wszystkie małe litery):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Platforma Docker pomyślnie ściąga obraz do maszyny wirtualnej.

Ten przykład pokazuje, że można uzyskać dostęp do prywatnego rejestru kontenerów za pomocą reguły dostępu do sieci. Nie można jednak uzyskać dostępu do rejestru z hosta logowania, który nie ma skonfigurowanej reguły dostępu do sieci. W przypadku próby zalogowania się z innego hosta za pomocą `az acr login` polecenia lub `docker login` polecenia dane wyjściowe są podobne do następujących:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Przywróć domyślny dostęp do rejestru

Aby przywrócić rejestr w celu zezwolenia na dostęp domyślnie, Usuń wszystkie skonfigurowane reguły sieciowe. Następnie ustaw domyślną akcję na Zezwalaj na dostęp. Dostępne są równoważne kroki przy użyciu interfejsu wiersza polecenia platformy Azure i Azure Portal.

### <a name="restore-default-registry-access---cli"></a>Przywróć domyślny dostęp do rejestru — interfejs wiersza polecenia

#### <a name="remove-network-rules"></a>Usuń reguły sieci

Aby wyświetlić listę reguł sieci skonfigurowanych dla rejestru, uruchom następujące polecenie [AZ ACR Network-Rule list][az-acr-network-rule-list] :

```azurecli
az acr network-rule list --name mycontainerregistry 
```

Dla każdej skonfigurowanej reguły Uruchom polecenie [AZ ACR Network-Rule Remove][az-acr-network-rule-remove] , aby je usunąć. Na przykład:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="allow-access"></a>Zezwól na dostęp

Zastąp nazwę rejestru następującym poleceniem [AZ ACR Update][az-acr-update] :
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli wszystkie zasoby platformy Azure zostały utworzone w tej samej grupie zasobów i nie będą już potrzebne, możesz opcjonalnie usunąć zasoby za pomocą jednego polecenia [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name myResourceGroup
```

Aby wyczyścić zasoby w portalu, przejdź do grupy zasobów zasoby. Po załadowaniu grupy zasobów kliknij pozycję **Usuń grupę zasobów** , aby usunąć grupę zasobów i przechowywane w niej zasoby.

## <a name="next-steps"></a>Następne kroki

* Aby ograniczyć dostęp do rejestru przy użyciu prywatnego punktu końcowego w sieci wirtualnej, zobacz [Konfigurowanie prywatnego linku platformy Azure dla usługi Azure Container Registry](container-registry-private-link.md).
* Jeśli konieczne jest skonfigurowanie reguł dostępu do rejestru za zaporą klienta, zobacz [Konfigurowanie reguł dostępu do usługi Azure Container Registry za zaporą](container-registry-firewall-access-rules.md).


<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png


<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
