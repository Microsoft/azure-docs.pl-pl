---
title: Ograniczanie dostępu przy użyciu punktu końcowego usługi
description: Ograniczanie dostępu do rejestru kontenerów platformy Azure przy użyciu punktu końcowego usługi w sieci wirtualnej platformy Azure. Dostęp do punktu końcowego usługi jest funkcją warstwy usługi Premium.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 8a67a011c75a192df9ad3460458fd766b5ec1ec1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773474"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Ograniczanie dostępu do rejestru kontenerów przy użyciu punktu końcowego usługi w sieci wirtualnej platformy Azure

[Usługa Azure Virtual Network](../virtual-network/virtual-networks-overview.md) zapewnia bezpieczną, prywatną sieć dla platformy Azure i zasobów lokalnych. Punkt [końcowy usługi](../virtual-network/virtual-network-service-endpoints-overview.md) umożliwia zabezpieczenie publicznego adresu IP rejestru kontenerów tylko do sieci wirtualnej. Ten punkt końcowy zapewnia ruchowi optymalną trasę do zasobu za pośrednictwem sieci szkieletowej platformy Azure. Tożsamości sieci wirtualnej i podsieci są również przesyłane z każdym żądaniem.

W tym artykule pokazano, jak skonfigurować punkt końcowy usługi rejestru kontenerów (wersja zapoznawcza) w sieci wirtualnej. 

> [!IMPORTANT]
> Azure Container Registry obsługuje teraz [Azure Private Link](container-registry-private-link.md), umożliwiając umieszczanie prywatnych punktów końcowych z sieci wirtualnej w rejestrze. Prywatne punkty końcowe są dostępne z poziomu sieci wirtualnej przy użyciu prywatnych adresów IP. W większości scenariuszy sieciowych zalecamy używanie prywatnych punktów końcowych zamiast punktów końcowych usługi.

Konfigurowanie punktu końcowego usługi rejestru jest dostępne w warstwie usługi rejestru kontenerów w warstwie **Premium.** Aby uzyskać informacje o warstwach i limitach usługi rejestru, [zobacz Azure Container Registry usługi](container-registry-skus.md).

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Przyszłe opracowywanie punktów końcowych usługi dla Azure Container Registry nie jest obecnie planowane. Zamiast tego zalecamy [używanie prywatnych punktów końcowych.](container-registry-private-link.md)
* Nie można użyć tej Azure Portal do konfigurowania punktów końcowych usługi w rejestrze.
* Tylko [klaster Azure Kubernetes Service](../aks/intro-kubernetes.md) lub maszyna wirtualna platformy [Azure](../virtual-machines/linux/overview.md) mogą być używane jako host w celu uzyskania dostępu do rejestru kontenerów przy użyciu punktu końcowego usługi. *Inne usługi platformy Azure Azure Container Instances w tym usługi, nie są obsługiwane.*
* Punkty końcowe usługi dla Azure Container Registry nie są obsługiwane w chmurze Platformy Azure dla instytucji rządowych USA ani w chmurze Azure China Cloud.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aby wykonać kroki interfejsu wiersza polecenia platformy Azure opisane w tym artykule, wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.58 lub nowszej. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

* Jeśli nie masz jeszcze rejestru kontenerów, utwórz go (wymagana warstwa Premium) i wypchń przykładowy obraz, taki jak z `hello-world` Docker Hub. Aby na przykład utworzyć [rejestr, Azure Portal][quickstart-portal] interfejsu wiersza polecenia platformy [Azure][quickstart-cli] lub interfejsu wiersza polecenia platformy Azure. 

* Jeśli chcesz ograniczyć dostęp do rejestru przy użyciu punktu końcowego usługi w innej subskrypcji platformy Azure, zarejestruj dostawcę zasobów dla Azure Container Registry tej subskrypcji. Na przykład:

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>Konfigurowanie dostępu sieciowego dla rejestru

W tej sekcji skonfigurujesz rejestr kontenerów, aby zezwolić na dostęp z podsieci w sieci wirtualnej platformy Azure. Kroki można wykonać przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="add-a-service-endpoint-to-a-subnet"></a>Dodawanie punktu końcowego usługi do podsieci

Podczas tworzenia maszyny wirtualnej platforma Azure domyślnie tworzy sieć wirtualną w tej samej grupie zasobów. Nazwa sieci wirtualnej jest oparta na nazwie maszyny wirtualnej. Jeśli na przykład nadasz maszynie wirtualnej nazwę *myDockerVM,* domyślną nazwą sieci wirtualnej jest *myDockerVMVNET,* z podsiecią o nazwie *myDockerVMSubnet.* Sprawdź to za pomocą [polecenia az network vnet list:][az-network-vnet-list]

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

Użyj polecenia [az network vnet subnet update,][az-network-vnet-subnet-update] aby dodać punkt końcowy usługi **Microsoft.ContainerRegistry** do podsieci. Zastąp nazwy sieci wirtualnej i podsieci następującym poleceniem:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Użyj polecenia [az network vnet subnet show,][az-network-vnet-subnet-show] aby pobrać identyfikator zasobu podsieci. Będzie to potrzebne w późniejszym kroku, aby skonfigurować regułę dostępu do sieci.

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

### <a name="change-default-network-access-to-registry"></a>Zmienianie domyślnego dostępu sieciowego do rejestru

Domyślnie rejestr kontenerów platformy Azure zezwala na połączenia z hostów w dowolnej sieci. Aby ograniczyć dostęp do wybranej sieci, zmień akcję domyślną na odmów dostępu. Zastąp nazwę rejestru za pomocą następującego [polecenia az acr update:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Dodawanie reguły sieci do rejestru

Użyj [polecenia az acr network-rule add,][az-acr-network-rule-add] aby dodać do rejestru regułę sieci, która zezwala na dostęp z podsieci maszyny wirtualnej. Zastąp nazwę rejestru kontenerów i identyfikator zasobu podsieci w następującym poleceniu: 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>Weryfikowanie dostępu do rejestru

Po odczekieniu kilku minut na aktualizację konfiguracji sprawdź, czy maszyna wirtualna może uzyskać dostęp do rejestru kontenerów. Nawiązaniu połączenia SSH z maszyną wirtualną i uruchom polecenie [az acr login,][az-acr-login] aby zalogować się do rejestru. 

```bash
az acr login --name mycontainerregistry
```

Można wykonywać operacje rejestru, takie jak uruchamianie w `docker pull` celu ściągnięcie przykładowego obrazu z rejestru. Zastąp obraz i wartość tagu odpowiednią dla rejestru, poprzedzoną nazwą serwera logowania rejestru (wszystkie małe litery):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker pomyślnie ściąga obraz do maszyny wirtualnej.

W tym przykładzie pokazano, że dostęp do prywatnego rejestru kontenerów można uzyskać za pośrednictwem reguły dostępu do sieci. Nie można jednak uzyskać dostępu do rejestru z hosta logowania, który nie ma skonfigurowanej reguły dostępu do sieci. Jeśli spróbujemy zalogować się z innego hosta przy użyciu polecenia lub polecenia , dane wyjściowe `az acr login` `docker login` będą podobne do następujących:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Przywracanie domyślnego dostępu do rejestru

Aby przywrócić rejestr w celu domyślnego zezwalania na dostęp, usuń wszystkie skonfigurowane reguły sieciowe. Następnie ustaw akcję domyślną, aby zezwolić na dostęp. 

### <a name="remove-network-rules"></a>Usuwanie reguł sieci

Aby wyświetlić listę reguł sieci skonfigurowanych dla rejestru, uruchom następujące [polecenie az acr network-rule list:][az-acr-network-rule-list]

```azurecli
az acr network-rule list --name mycontainerregistry 
```

Dla każdej skonfigurowanej reguły uruchom [polecenie az acr network-rule remove,][az-acr-network-rule-remove] aby ją usunąć. Na przykład:

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

### <a name="allow-access"></a>Zezwól na dostęp

Zastąp nazwę rejestru następującym poleceniem [az acr update:][az-acr-update]
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli wszystkie zasoby platformy Azure zostały utworzone w tej samej grupie zasobów i nie są już potrzebne, możesz opcjonalnie usunąć zasoby za pomocą pojedynczego polecenia [az group delete:](/cli/azure/group)

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

* Aby ograniczyć dostęp do rejestru przy użyciu prywatnego punktu końcowego w sieci wirtualnej, zobacz [Azure Private Link dla rejestru kontenerów platformy Azure.](container-registry-private-link.md)
* Jeśli musisz skonfigurować reguły dostępu do rejestru zza zapory klienta, zobacz Configure rules to access an Azure container registry behind a firewall (Konfigurowanie reguł dostępu do rejestru kontenerów platformy [Azure za zaporą).](container-registry-firewall-access-rules.md)


<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png


<!-- LINKS - External -->
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
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az_acr_network_rule_add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az_acr_network_rule_remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az_acr_network_rule_list
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-list]: /cli/azure/network/vnet/#az_network_vnet_list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
