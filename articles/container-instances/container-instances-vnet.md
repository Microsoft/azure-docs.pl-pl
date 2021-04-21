---
title: Wdrażanie grupy kontenerów w sieci wirtualnej platformy Azure
description: Dowiedz się, jak wdrożyć grupę kontenerów w nowej lub istniejącej sieci wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.topic: article
ms.date: 07/02/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 44be66957aa745179ffe4cd00db75f1d47237dfc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771051"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Wdrażanie wystąpień kontenerów w sieci wirtualnej platformy Azure

[Usługa Azure Virtual Network](../virtual-network/virtual-networks-overview.md) zapewnia bezpieczną, prywatną sieć dla platformy Azure i zasobów lokalnych. Wdrażając grupy kontenerów w sieci wirtualnej platformy Azure, kontenery mogą bezpiecznie komunikować się z innymi zasobami w sieci wirtualnej.

W tym artykule pokazano, jak za pomocą [polecenia az container create][az-container-create] w interfejsie wiersza polecenia platformy Azure wdrożyć grupy kontenerów w nowej sieci wirtualnej lub istniejącej sieci wirtualnej. 

Aby uzyskać informacje o scenariuszach i ograniczeniach dotyczących sieci, zobacz Virtual network scenarios and resources for Azure Container Instances (Scenariusze i zasoby dotyczące sieci [Azure Container Instances).](container-instances-virtual-network-concepts.md)

> [!IMPORTANT]
> Wdrożenie grupy kontenerów w sieci wirtualnej jest ogólnie dostępne dla kontenerów systemu Linux w większości regionów, Azure Container Instances jest dostępna. Aby uzyskać szczegółowe informacje, [zobacz Regions and resource availability (Regiony i dostępność zasobów).][container-regions] 

Przykłady w tym artykule są sformatowane dla powłoki Bash. Jeśli wolisz inną powłokę, taką jak program PowerShell lub wiersz polecenia, odpowiednio dostosuj znaki kontynuacji wiersza.


## <a name="deploy-to-new-virtual-network"></a>Wdrażanie w nowej sieci wirtualnej

Aby wdrożyć w nowej sieci wirtualnej i automatycznie utworzyć zasoby sieciowe na platformie Azure, podczas wykonywania polecenia az container create określ następujące [elementy:][az-container-create]

* Nazwa sieci wirtualnej
* Prefiks adresu sieci wirtualnej w formacie CIDR
* Nazwa podsieci
* Prefiks adresu podsieci w formacie CIDR

Prefiksy adresów sieci wirtualnej i podsieci określają odpowiednio przestrzenie adresowe dla sieci wirtualnej i podsieci. Te wartości są reprezentowane w notacji Inter-Domain routingu bez klas (CIDR), na przykład `10.0.0.0/16` . Aby uzyskać więcej informacji na temat pracy z podsieciami, zobacz [Dodawanie, zmienianie lub](../virtual-network/virtual-network-manage-subnet.md)usuwanie podsieci sieci wirtualnej .

Po wdrożeniu pierwszej grupy kontenerów za pomocą tej metody możesz wdrożyć ją w tej samej podsieci, określając sieć wirtualną i nazwy podsieci lub profil sieci, który platforma Azure automatycznie utworzy dla Ciebie. Ponieważ platforma Azure deleguje podsieć do  Azure Container Instances, w tej podsieci można wdrożyć tylko grupy kontenerów.

### <a name="example"></a>Przykład

Następujące polecenie [az container create][az-container-create] określa ustawienia dla nowej sieci wirtualnej i podsieci. Podaj nazwę grupy zasobów utworzonej w regionie, w którym są dostępne wdrożenia grup kontenerów w sieci [wirtualnej.](container-instances-region-availability.md) To polecenie wdraża publiczny kontener [aci-helloworld][aci-helloworld] firmy Microsoft, który uruchamia mały Node.js serwer internetowy obsługujący statyczną stronę internetową. W następnej sekcji wdrożysz drugą grupę kontenerów w tej samej podsieci i przetestujemy komunikację między dwoma wystąpieniami kontenera.

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

W przypadku wdrożenia w nowej sieci wirtualnej przy użyciu tej metody wdrożenie może potrwać kilka minut, gdy zasoby sieciowe zostaną utworzone. Po początkowym wdrożeniu dodatkowe wdrożenia grup kontenerów w tej samej podsieci są szybciej ukończone.

## <a name="deploy-to-existing-virtual-network"></a>Wdrażanie w istniejącej sieci wirtualnej

Aby wdrożyć grupę kontenerów w istniejącej sieci wirtualnej:

1. Utwórz podsieć w ramach istniejącej sieci wirtualnej, użyj istniejącej podsieci, w której grupa kontenerów została już wdrożona, lub użyj istniejącej podsieci opróżnionej ze *wszystkich* innych zasobów
1. Wd wdrażaj grupę kontenerów za pomocą az [container create][az-container-create] i określ jedną z następujących wartości:
   * Nazwa sieci wirtualnej i nazwa podsieci
   * Identyfikator zasobu sieci wirtualnej i identyfikator zasobu podsieci, co umożliwia korzystanie z sieci wirtualnej z innej grupy zasobów
   * Nazwa lub identyfikator profilu sieciowego, które można uzyskać za pomocą [narzędzia az network profile list][az-network-profile-list]

### <a name="example"></a>Przykład

Poniższy przykład wdraża drugą grupę kontenerów w tej samej podsieci, która została wcześniej utworzona, i weryfikuje komunikację między dwoma wystąpieniami kontenerów.

Najpierw pobierz adres IP pierwszej wdrożonej grupy kontenerów, *appcontainer*:

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

W danych wyjściowych zostanie wyświetlony adres IP grupy kontenerów w podsieci prywatnej. Na przykład:

```console
10.0.0.4
```

Teraz ustaw `CONTAINER_GROUP_IP` na adres IP pobrany za pomocą polecenia i wykonaj następujące `az container show` `az container create` polecenie. Ten drugi kontener, *commchecker,* uruchamia obraz oparty na systemie Alpine Linux i wykonuje względem adresu IP podsieci prywatnej `wget` pierwszej grupy kontenerów.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-address>

az container create \
  --resource-group myResourceGroup \
  --name commchecker \
  --image alpine:3.5 \
  --command-line "wget $CONTAINER_GROUP_IP" \
  --restart-policy never \
  --vnet aci-vnet \
  --subnet aci-subnet
```

Po ukończeniu drugiego wdrożenia kontenera ściągnij jego dzienniki, aby wyświetlić dane wyjściowe `wget` wykonanego polecenia:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Jeśli drugi kontener pomyślnie komunikował się z pierwszym, dane wyjściowe będą podobne do:

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

Dane wyjściowe dziennika powinny pokazywać, że można było nawiązać połączenie i pobrać plik indeksu z pierwszego kontenera przy użyciu jego prywatnego adresu `wget` IP w podsieci lokalnej. Ruch sieciowy między dwiema grupami kontenerów pozostał w sieci wirtualnej.

### <a name="example---yaml"></a>Przykład — YAML

Grupę kontenerów można również wdrożyć w istniejącej sieci [](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)wirtualnej przy użyciu pliku YAML, szablonu Resource Manager lub innej metody programowej, takiej jak zestaw PYTHON SDK. 

Na przykład w przypadku korzystania z pliku YAML można wdrożyć w sieci wirtualnej z podsiecią delegowaną do Azure Container Instances. Określ następujące właściwości:

* `ipAddress`: ustawienia prywatnego adresu IP dla grupy kontenerów.
  * `ports`: porty do otwarcia, jeśli są otwarte.
  * `protocol`: protokół (TCP lub UDP) dla otwartego portu.
* `networkProfile`: ustawienia sieci dla sieci wirtualnej i podsieci.
  * `id`: pełny Resource Manager identyfikatora zasobu zasobu `networkProfile` .

Aby uzyskać identyfikator profilu sieciowego, uruchom polecenie [az network profile list,][az-network-profile-list] określając nazwę grupy zasobów zawierającej sieć wirtualną i delegowaną podsieć.

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

Przykładowe dane wyjściowe:

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Gdy masz już identyfikator profilu sieciowego, skopiuj następujący kod YAML do nowego pliku o nazwie *vnet-deploy-aci.yaml.* W obszarze zastąp wartość właśnie pobranym identyfikatorem, a `networkProfile` `id` następnie zapisz plik. Ten plik YAML tworzy grupę kontenerów o *nazwie appcontaineryaml* w sieci wirtualnej.

```YAML
apiVersion: '2019-12-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Wdróż grupę kontenerów za pomocą polecenia [az container create,][az-container-create] określając nazwę pliku YAML dla `--file` parametru :

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

Po zakończeniu wdrażania uruchom polecenie [az container show,][az-container-show] aby wyświetlić jego stan. Przykładowe dane wyjściowe:

```console
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

### <a name="delete-container-instances"></a>Usuwanie wystąpień kontenerów

Po pracy z utworzonymi wystąpieniami kontenerów usuń je za pomocą następujących poleceń:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Usuwanie zasobów sieciowych

Ta funkcja wymaga obecnie kilku dodatkowych poleceń do usunięcia utworzonych wcześniej zasobów sieciowych. Jeśli przykładowe polecenia w poprzednich sekcjach tego artykułu były używane do tworzenia sieci wirtualnej i podsieci, możesz użyć następującego skryptu, aby usunąć te zasoby sieciowe. Skrypt zakłada, że grupa zasobów zawiera pojedynczą sieć wirtualną z pojedynczym profilem sieciowym.

Przed wykonaniem skryptu ustaw zmienną na nazwę grupy zasobów zawierającej sieć wirtualną i podsieć, `RES_GROUP` które powinny zostać usunięte. Zaktualizuj nazwę sieci wirtualnej, jeśli nie została wcześniej `aci-vnet` zasugerowana nazwa. Skrypt jest sformatowany dla powłoki Bash. Jeśli wolisz inną powłokę, taką jak program PowerShell lub wiersz polecenia, musisz odpowiednio dostosować przypisanie zmiennych i dostęp do nich.

> [!WARNING]
> Ten skrypt usuwa zasoby! Spowoduje to usunięcie sieci wirtualnej i wszystkich podsieci, które zawiera. Przed uruchomieniem tego  skryptu upewnij się, że nie są już potrzebne żadne zasoby w sieci wirtualnej, w tym wszystkie podsieci, które zawiera. Po usunięciu **te zasoby są nieodwracalne.**

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Następne kroki

Aby wdrożyć nową sieć wirtualną, podsieć, profil sieci i grupę kontenerów przy użyciu szablonu Resource Manager, zobacz Tworzenie grupy kontenerów platformy [Azure za pomocą sieci wirtualnej](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-network-vnet-create]: /cli/azure/network/vnet#az_network_vnet_create
[az-network-profile-list]: /cli/azure/network/profile#az_network_profile_list
[container-regions]: container-instances-region-availability.md
