---
title: Wdrażanie grupy kontenerów w usłudze Azure Virtual Network
description: Dowiedz się, jak wdrożyć grupę kontenerów w nowej lub istniejącej sieci wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
ms.topic: article
ms.date: 07/02/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: b791d3f37809c2eca53f5a3cd34f7c44dd11ce40
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028883"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Wdrażanie wystąpień kontenerów w sieci wirtualnej platformy Azure

[Usługa azure Virtual Network](../virtual-network/virtual-networks-overview.md) zapewnia bezpieczną i prywatną sieć dla zasobów platformy Azure i lokalnych. Wdrażając grupy kontenerów w sieci wirtualnej platformy Azure, kontenery mogą bezpiecznie komunikować się z innymi zasobami w sieci wirtualnej.

W tym artykule pokazano, jak za pomocą polecenia [AZ Container Create][az-container-create] w interfejsie CLI platformy Azure wdrożyć grupy kontenerów w nowej sieci wirtualnej lub istniejącej sieci wirtualnej. 

Aby poznać scenariusze i ograniczenia dotyczące sieci, zobacz [scenariusze i zasoby sieci wirtualnej dla Azure Container Instances](container-instances-virtual-network-concepts.md).

> [!IMPORTANT]
> Wdrożenie grupy kontenerów w sieci wirtualnej jest ogólnie dostępne dla kontenerów systemu Linux w większości regionów, w których Azure Container Instances jest dostępna. Aby uzyskać szczegółowe informacje, zobacz [regiony i dostępność zasobów][container-regions]. 

Przykłady w tym artykule są sformatowane dla powłoki bash. Jeśli wolisz inną powłokę, taką jak program PowerShell lub wiersz polecenia, Dostosuj odpowiednio znaki kontynuacji wiersza.


## <a name="deploy-to-new-virtual-network"></a>Wdróż w nowej sieci wirtualnej

Aby wdrożyć w nowej sieci wirtualnej i automatycznie utworzyć zasoby sieciowe na platformie Azure, określ następujące elementy podczas wykonywania [AZ Container Create][az-container-create]:

* Nazwa sieci wirtualnej
* Prefiks adresu sieci wirtualnej w formacie CIDR
* Nazwa podsieci
* Prefiks adresu podsieci w formacie CIDR

Prefiksy adresów sieci wirtualnej i podsieci określają odpowiednio przestrzenie adresowe dla sieci wirtualnej i podsieci. Te wartości są reprezentowane w notacji CIDR (Classless Inter-Domain Routing), na przykład `10.0.0.0/16` . Aby uzyskać więcej informacji na temat pracy z podsieciami, zobacz [Dodawanie, zmienianie lub usuwanie podsieci sieci wirtualnej](../virtual-network/virtual-network-manage-subnet.md).

Po wdrożeniu pierwszej grupy kontenerów za pomocą tej metody można wdrożyć ją w tej samej podsieci, określając nazwę sieci wirtualnej i podsieci, lub profil sieciowy, który zostanie automatycznie utworzony przez platformę Azure. Ponieważ platforma Azure deleguje podsieć do Azure Container Instances, można wdrożyć *tylko* grupy kontenerów w podsieci.

### <a name="example"></a>Przykład

Następujące polecenie [AZ Container Create][az-container-create] określa ustawienia dla nowej sieci wirtualnej i podsieci. Podaj nazwę grupy zasobów, która została utworzona w regionie, w którym są [dostępne](container-instances-region-availability.md)wdrożenia grup kontenerów w sieci wirtualnej. To polecenie wdraża publiczny kontener Microsoft [ACI-HelloWorld][aci-helloworld] , który uruchamia mały Node.js WebServer obsługującego statyczną stronę sieci Web. W następnej sekcji zostanie wdrożona druga grupa kontenerów w tej samej podsieci i przetestowana zostanie komunikacja między dwoma wystąpieniami kontenerów.

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

Po wdrożeniu do nowej sieci wirtualnej za pomocą tej metody wdrożenie może potrwać kilka minut podczas tworzenia zasobów sieciowych. Po wdrożeniu początkowym kolejne wdrożenia grup kontenerów w tej samej podsieci są szybsze.

## <a name="deploy-to-existing-virtual-network"></a>Wdróż w istniejącej sieci wirtualnej

Aby wdrożyć grupę kontenerów w istniejącej sieci wirtualnej:

1. Utwórz podsieć w istniejącej sieci wirtualnej, Użyj istniejącej podsieci, w której Grupa kontenerów została już wdrożona, lub Użyj istniejącej podsieci z opróżnieniem *wszystkich* innych zasobów
1. Wdróż grupę kontenerów za pomocą [AZ Container Create][az-container-create] i Określ jedną z następujących wartości:
   * Nazwa sieci wirtualnej i nazwa podsieci
   * Identyfikator zasobu sieci wirtualnej i identyfikator zasobu podsieci, który umożliwia korzystanie z sieci wirtualnej z innej grupy zasobów
   * Nazwa lub identyfikator profilu sieciowego, który można uzyskać za pomocą polecenia [AZ Network profile list][az-network-profile-list]

### <a name="example"></a>Przykład

Poniższy przykład służy do wdrażania drugiej grupy kontenerów w tej samej podsieci, która została utworzona wcześniej, i weryfikuje komunikację między dwoma wystąpieniami kontenerów.

Najpierw Pobierz adres IP pierwszej wdrożonej grupy kontenerów *:*

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

W danych wyjściowych zostanie wyświetlony adres IP grupy kontenerów w podsieci prywatnej. Na przykład:

```console
10.0.0.4
```

Teraz ustaw `CONTAINER_GROUP_IP` adres IP pobrany z `az container show` poleceniem i wykonaj następujące `az container create` polecenie. Ten drugi kontener, *commchecker*, uruchamia obraz oparty na systemie Linux i jest wykonywany `wget` w odniesieniu do pierwszego adresu IP podsieci prywatnej grupy kontenerów.

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

Po zakończeniu tego wdrożenia drugiego kontenera Pobierz jego dzienniki, aby wyświetlić dane wyjściowe `wget` wykonywane przez niego polecenie:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Jeśli drugi kontener został pomyślnie przekazany do pierwszego, dane wyjściowe są podobne do:

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

W danych wyjściowych dziennika powinna być wyświetlana wartość, która `wget` była w stanie połączyć i pobrać plik indeksu z pierwszego kontenera przy użyciu jego prywatnego adresu IP w podsieci lokalnej. Ruch sieciowy między dwiema grupami kontenerów pozostał w sieci wirtualnej.

### <a name="example---yaml"></a>Przykład — YAML

Możesz również wdrożyć grupę kontenerów w istniejącej sieci wirtualnej przy użyciu pliku YAML, [szablonu Menedżer zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)lub innej metody programistycznej, takiej jak z zestawem SDK języka Python. 

Na przykład podczas korzystania z pliku YAML można wdrożyć w sieci wirtualnej z podsiecią delegowaną do Azure Container Instances. Określ następujące właściwości:

* `ipAddress`: Ustawienia prywatnego adresu IP dla grupy kontenerów.
  * `ports`: Porty do otwarcia (jeśli istnieją).
  * `protocol`: Protokół (TCP lub UDP) dla otwartego portu.
* `networkProfile`: Ustawienia sieci dla sieci wirtualnej i podsieci.
  * `id`: Pełny identyfikator zasobu Menedżer zasobów `networkProfile` .

Aby uzyskać identyfikator profilu sieciowego, uruchom polecenie [AZ Network profile list][az-network-profile-list] , określając nazwę grupy zasobów zawierającej sieć wirtualną i delegowaną podsieć.

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

Przykładowe dane wyjściowe:

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Gdy masz identyfikator profilu sieciowego, Skopiuj następujący YAML do nowego pliku o nazwie *VNET-Deploy-ACI. YAML*. W obszarze `networkProfile` , Zastąp `id` wartość identyfikatorem, który właśnie został pobrany, a następnie Zapisz plik. Ta YAML tworzy grupę kontenerów o nazwie *appcontaineryaml* w sieci wirtualnej.

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

Wdróż grupę kontenerów za pomocą polecenia [AZ Container Create][az-container-create] , określając nazwę pliku YAML dla `--file` parametru:

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

Po zakończeniu wdrożenia Uruchom polecenie [AZ Container show][az-container-show] , aby wyświetlić jego stan. Przykładowe dane wyjściowe:

```console
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

### <a name="delete-container-instances"></a>Usuwanie wystąpień kontenerów

Po zakończeniu pracy z utworzonymi wystąpieniami kontenerów usuń je za pomocą następujących poleceń:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Usuwanie zasobów sieciowych

Ta funkcja wymaga obecnie kilku dodatkowych poleceń w celu usunięcia utworzonych wcześniej zasobów sieciowych. Jeśli użyto przykładowych poleceń w poprzednich sekcjach tego artykułu, aby utworzyć sieć wirtualną i podsieć, można użyć następującego skryptu, aby usunąć te zasoby sieciowe. Skrypt zakłada, że grupa zasobów zawiera pojedynczą sieć wirtualną z pojedynczym profilem sieciowym.

Przed wykonaniem skryptu Ustaw `RES_GROUP` zmienną na nazwę grupy zasobów zawierającej sieć wirtualną i podsieć, która ma zostać usunięta. Zaktualizuj nazwę sieci wirtualnej, jeśli nie używasz `aci-vnet` sugerowanej wcześniej nazwy. Skrypt jest sformatowany dla powłoki bash. Jeśli wolisz innej powłoki, takiej jak PowerShell lub wiersz polecenia, musisz odpowiednio dostosować przypisanie zmiennych i metody dostępu.

> [!WARNING]
> Ten skrypt usuwa zasoby! Usuwa sieć wirtualną i wszystkie podsieci, które zawiera. Upewnij się, że nie potrzebujesz już *żadnych* zasobów w sieci wirtualnej, w tym wszystkich podsieci, które zawiera, przed uruchomieniem tego skryptu. Po usunięciu **te zasoby są nieodwracalne**.

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

Aby wdrożyć nową sieć wirtualną, podsieć, profil sieci i grupę kontenerów przy użyciu szablonu Menedżer zasobów, zobacz [Tworzenie grupy kontenerów platformy Azure przy](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)użyciu sieci wirtualnej.

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
[container-regions]: container-instances-region-availability.md
