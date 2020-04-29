---
title: Wdrażanie grupy kontenerów w usłudze Azure Virtual Network
description: Dowiedz się, jak wdrażać grupy kontenerów w nowej lub istniejącej sieci wirtualnej platformy Azure.
ms.topic: article
ms.date: 01/06/2020
ms.author: danlep
ms.openlocfilehash: 318576e9b5c5b32bbc993ea16494c938b74bd2f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77200065"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Wdrażanie wystąpień kontenerów w sieci wirtualnej platformy Azure

[Usługa azure Virtual Network](../virtual-network/virtual-networks-overview.md) zapewnia bezpieczną i prywatną sieć dla zasobów platformy Azure i lokalnych. Wdrażając grupy kontenerów w sieci wirtualnej platformy Azure, kontenery mogą bezpiecznie komunikować się z innymi zasobami w sieci wirtualnej.

Grupy kontenerów wdrożone w usłudze Azure Virtual Network umożliwiają włączanie scenariuszy, takich jak:

* Bezpośrednia komunikacja między grupami kontenerów w tej samej podsieci
* Wysyłaj dane wyjściowe obciążenia [opartego na zadaniach](container-instances-restart-policy.md) z wystąpień kontenerów do bazy danych w sieci wirtualnej
* Pobieranie zawartości dla wystąpień kontenerów z [punktu końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) w sieci wirtualnej
* Komunikacja kontenerów z maszynami wirtualnymi w sieci wirtualnej
* Komunikacja kontenerów z zasobami lokalnymi za pośrednictwem [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [ExpressRoute](../expressroute/expressroute-introduction.md)

> [!IMPORTANT]
> Wdrożenia grup kontenerów w sieci wirtualnej są ogólnie dostępne w przypadku obciążeń produkcyjnych tylko w następujących regionach: **Wschodnie stany USA, Południowo-środkowe stany USA i zachodnie stany USA 2**. W innych regionach, w których ta funkcja jest dostępna, wdrożenia sieci wirtualnych są obecnie w wersji zapoznawczej, a ogólnie dostępna dostępność jest planowana w najbliższej przyszłości. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. 


## <a name="virtual-network-deployment-limitations"></a>Ograniczenia wdrożenia sieci wirtualnej

Niektóre ograniczenia mają zastosowanie w przypadku wdrażania grup kontenerów w sieci wirtualnej.

* Aby wdrożyć grupy kontenerów w podsieci, podsieć nie może zawierać żadnych innych typów zasobów. Usuń wszystkie istniejące zasoby z istniejącej podsieci przed wdrożeniem w niej grup kontenerów lub Utwórz nową podsieć.
* Nie można użyć [tożsamości zarządzanej](container-instances-managed-identity.md) w grupie kontenerów wdrożonej w sieci wirtualnej.
* Nie można włączyć [sondy na żywo](container-instances-liveness-probe.md) lub [sondy gotowości](container-instances-readiness-probe.md) w grupie kontenerów wdrożonej w sieci wirtualnej.
* Ze względu na dodatkowe zasoby sieciowe, wdrażanie grupy kontenerów w sieci wirtualnej jest zwykle wolniejsze niż wdrożenie standardowego wystąpienia kontenera.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

Limity zasobów kontenera mogą różnić się od ograniczeń dla wystąpień kontenerów nienależących do sieci w tych regionach. W przypadku tej funkcji obsługiwane są obecnie tylko kontenery systemu Linux. Planowane jest wprowadzenie obsługi w systemie Windows.

### <a name="unsupported-networking-scenarios"></a>Nieobsługiwane scenariusze sieci 

* **Azure Load Balancer** — umieszczenie Azure Load Balancer przed wystąpieniami kontenerów w grupie kontenerów sieciowych nie jest obsługiwane
* **Wirtualne sieci równorzędne**
  * Wirtualne sieci równorzędne nie będą działały w przypadku usługi ACI, jeśli sieć wirtualna ACI jest wykorzystywana do korzystania z publicznej przestrzeni adresów IP. Aby wirtualne sieci równorzędne działały, Sieć równorzędna musi mieć prywatną przestrzeń IP RFC 1918. 
  * Możesz tylko połączyć sieć wirtualną z jedną inną siecią wirtualną
* **Routing ruchu w sieci wirtualnej** — nie można skonfigurować tras niestandardowych między publicznymi adresami IP. Trasy można skonfigurować w ramach prywatnego obszaru adresów IP delegowanej podsieci, w której są wdrażane zasoby ACI 
* **Sieciowe grupy zabezpieczeń** — reguły zabezpieczeń dla ruchu wychodzącego w sieciowych grup zabezpieczeń zastosowane do podsieci delegowanej do Azure Container Instances nie są obecnie wymuszane 
* **Public IP lub etykieta DNS** — grupy kontenerów wdrożone w sieci wirtualnej nie obsługują obecnie udostępniania kontenerów bezpośrednio w Internecie za pomocą publicznego adresu IP lub w pełni kwalifikowanej nazwy domeny
* **Wewnętrzne rozpoznawanie nazw** — rozpoznawanie nazw dla zasobów platformy Azure w sieci wirtualnej za pośrednictwem wewnętrznego Azure DNS nie jest obsługiwane

**Usunięcie zasobów sieciowych** wymaga wykonania [dodatkowych czynności](#delete-network-resources) po wdrożeniu grup kontenerów do sieci wirtualnej.

## <a name="required-network-resources"></a>Wymagane zasoby sieciowe

Do wdrożenia grup kontenerów w sieci wirtualnej są wymagane trzy zasoby Virtual Network platformy Azure: [Sieć wirtualna](#virtual-network) , [podsieć delegowana](#subnet-delegated) w ramach sieci wirtualnej i [profil sieciowy](#network-profile). 

### <a name="virtual-network"></a>Sieć wirtualna

Sieć wirtualna definiuje przestrzeń adresową, w której można utworzyć co najmniej jedną podsieć. Następnie należy wdrożyć zasoby platformy Azure (takie jak grupy kontenerów) w podsieciach w sieci wirtualnej.

### <a name="subnet-delegated"></a>Podsieć (delegowana)

Podsieci umożliwiają segmentację sieci wirtualnej na oddzielne przestrzenie adresowe, które są używane przez zasoby platformy Azure, które znajdują się w nich. Należy utworzyć co najmniej jedną podsieć w ramach sieci wirtualnej.

Podsieć używana dla grup kontenerów może zawierać tylko grupy kontenerów. Podczas pierwszego wdrażania grupy kontenerów w podsieci usługa Azure deleguje tę podsieć do Azure Container Instances. Po delegowaniu ta podsieć może być używana tylko dla grup kontenerów. Jeśli podjęto próbę wdrożenia zasobów innych niż grupy kontenerów w delegowanej podsieci, operacja zakończy się niepowodzeniem.

### <a name="network-profile"></a>Profil sieciowy

Profil sieci to szablon konfiguracji sieci dla zasobów platformy Azure. Określa pewne właściwości sieci dla zasobu, na przykład podsieć, w której ma zostać wdrożona. Po pierwszym użyciu polecenia [AZ Container Create][az-container-create] do wdrożenia grupy kontenerów w podsieci (i w ten sposób do sieci wirtualnej) platforma Azure tworzy profil sieciowy. Tego profilu sieciowego można następnie użyć do przyszłych wdrożeń w podsieci. 

Aby użyć szablonu Menedżer zasobów, pliku YAML lub metody programowej do wdrożenia grupy kontenerów w podsieci, należy podać pełny Menedżer zasobów identyfikator zasobu profilu sieciowego. Możesz użyć profilu utworzonego wcześniej przy użyciu polecenia [AZ Container Create][az-container-create]lub Create a profile przy użyciu szablonu Menedżer zasobów (zobacz [przykład szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) i [odwołanie](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). Aby uzyskać identyfikator utworzonego wcześniej profilu, użyj polecenia [AZ Network profile list][az-network-profile-list] . 

Na poniższym diagramie kilka grup kontenerów zostało wdrożonych w podsieci delegowanej do Azure Container Instances. Po wdrożeniu jednej grupy kontenerów w podsieci można wdrożyć do niej dodatkowe grupy kontenerów, określając ten sam profil sieci.

![Grupy kontenerów w ramach sieci wirtualnej][aci-vnet-01]

## <a name="deployment-scenarios"></a>Scenariusze wdrażania

Możesz użyć [AZ Container Create][az-container-create] , aby wdrożyć grupy kontenerów w nowej sieci wirtualnej i zezwolić platformie Azure na tworzenie wymaganych zasobów sieciowych lub wdrożyć ją w istniejącej sieci wirtualnej. 

### <a name="new-virtual-network"></a>Nowa sieć wirtualna

Aby wdrożyć w nowej sieci wirtualnej i automatycznie utworzyć zasoby sieciowe na platformie Azure, określ następujące elementy podczas wykonywania [AZ Container Create][az-container-create]:

* Nazwa sieci wirtualnej
* Prefiks adresu sieci wirtualnej w formacie CIDR
* Nazwa podsieci
* Prefiks adresu podsieci w formacie CIDR

Prefiksy adresów sieci wirtualnej i podsieci określają odpowiednio przestrzenie adresowe dla sieci wirtualnej i podsieci. Te wartości są reprezentowane w notacji CIDR (Classless Inter-Domain Routing), na przykład `10.0.0.0/16`. Aby uzyskać więcej informacji na temat pracy z podsieciami, zobacz [Dodawanie, zmienianie lub usuwanie podsieci sieci wirtualnej](../virtual-network/virtual-network-manage-subnet.md).

Po wdrożeniu pierwszej grupy kontenerów za pomocą tej metody można wdrożyć ją w tej samej podsieci, określając nazwę sieci wirtualnej i podsieci, lub profil sieciowy, który zostanie automatycznie utworzony przez platformę Azure. Ponieważ platforma Azure deleguje podsieć do Azure Container Instances, można wdrożyć *tylko* grupy kontenerów w podsieci.

### <a name="existing-virtual-network"></a>Istniejąca sieć wirtualna

Aby wdrożyć grupę kontenerów w istniejącej sieci wirtualnej:

1. Utwórz podsieć w istniejącej sieci wirtualnej, Użyj istniejącej podsieci, w której Grupa kontenerów została już wdrożona, lub Użyj istniejącej podsieci z opróżnieniem *wszystkich* innych zasobów
1. Wdróż grupę kontenerów za pomocą [AZ Container Create][az-container-create] i Określ jedną z następujących wartości:
   * Nazwa sieci wirtualnej i nazwa podsieci
   * Identyfikator zasobu sieci wirtualnej i identyfikator zasobu podsieci, który umożliwia korzystanie z sieci wirtualnej z innej grupy zasobów
   * Nazwa lub identyfikator profilu sieciowego, który można uzyskać za pomocą polecenia [AZ Network profile list][az-network-profile-list]

Po wdrożeniu pierwszej grupy kontenerów w istniejącej podsieci usługa Azure deleguje tę podsieć do Azure Container Instances. Nie można już wdrażać zasobów innych niż grupy kontenerów w tej podsieci.

## <a name="deployment-examples"></a>Przykłady wdrożeń

W poniższych sekcjach opisano sposób wdrażania grup kontenerów w sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure. Przykłady poleceń są sformatowane dla powłoki **bash** . Jeśli wolisz inną powłokę, taką jak program PowerShell lub wiersz polecenia, Dostosuj odpowiednio znaki kontynuacji wiersza.

### <a name="deploy-to-a-new-virtual-network"></a>Wdróż w nowej sieci wirtualnej

Najpierw należy wdrożyć grupę kontenerów i określić parametry nowej sieci wirtualnej i podsieci. Po określeniu tych parametrów platforma Azure tworzy sieć wirtualną i podsieć, deleguje podsieć do usługi Azure Container Instances, a także tworzy profil sieciowy. Po utworzeniu tych zasobów Grupa kontenerów zostanie wdrożona w podsieci.

Uruchom następujące polecenie [AZ Container Create][az-container-create] , które określa ustawienia dla nowej sieci wirtualnej i podsieci. Należy podać nazwę grupy zasobów, która została utworzona w regionie, w którym są [dostępne](#virtual-network-deployment-limitations)wdrożenia grup kontenerów w sieci wirtualnej. To polecenie wdraża publiczny kontener Microsoft [ACI-HelloWorld][aci-helloworld] , który uruchamia mały serwer webnode. js obsługujący statyczną stronę sieci Web. W następnej sekcji zostanie wdrożona druga grupa kontenerów w tej samej podsieci i przetestowana zostanie komunikacja między dwoma wystąpieniami kontenerów.

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

Po wdrożeniu do nowej sieci wirtualnej za pomocą tej metody wdrożenie może potrwać kilka minut podczas tworzenia zasobów sieciowych. Po wdrożeniu początkowym dodatkowe wdrożenia grup kontenerów są szybsze.

### <a name="deploy-to-existing-virtual-network"></a>Wdróż w istniejącej sieci wirtualnej

Teraz, gdy grupa kontenerów została wdrożona w nowej sieci wirtualnej, wdróż drugą grupę kontenerów w tej samej podsieci i Sprawdź komunikację między dwoma wystąpieniami kontenerów.

Najpierw Pobierz adres IP pierwszej wdrożonej grupy kontenerów *:*

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

W danych wyjściowych powinien być wyświetlany adres IP grupy kontenerów w podsieci prywatnej:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Teraz ustaw `CONTAINER_GROUP_IP` adres IP pobrany z `az container show` poleceniem i wykonaj następujące `az container create` polecenie. Ten drugi kontener, *commchecker*, uruchamia obraz oparty na systemie Linux i jest wykonywany `wget` w odniesieniu do pierwszego adresu IP podsieci prywatnej grupy kontenerów.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-here>

az container create \
    --resource-group myResourceGroup \
    --name commchecker \
    --image alpine:3.5 \
    --command-line "wget $CONTAINER_GROUP_IP" \
    --restart-policy never \
    --vnet aci-vnet \
    --subnet aci-subnet
```

Po zakończeniu tego wdrożenia drugiego kontenera Pobierz jego dzienniki, aby wyświetlić dane wyjściowe wykonywane przez `wget` niego polecenie:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Jeśli drugi kontener został pomyślnie przekazany do pierwszego, wynik powinien wyglądać podobnie do:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

W danych wyjściowych dziennika powinna `wget` być wyświetlana wartość, która była w stanie połączyć i pobrać plik indeksu z pierwszego kontenera przy użyciu jego prywatnego adresu IP w podsieci lokalnej. Ruch sieciowy między dwiema grupami kontenerów pozostał w sieci wirtualnej.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Wdróż w istniejącej sieci wirtualnej — YAML

Możesz również wdrożyć grupę kontenerów w istniejącej sieci wirtualnej przy użyciu pliku YAML, szablonu Menedżer zasobów lub innej metody programistycznej, takiej jak z zestawem SDK języka Python. Aby wdrożyć w podsieci w sieci wirtualnej, należy określić kilka dodatkowych właściwości w YAML:

* `ipAddress`: Ustawienia adresu IP dla grupy kontenerów.
  * `ports`: Porty do otwarcia (jeśli istnieją).
  * `protocol`: Protokół (TCP lub UDP) dla otwartego portu.
* `networkProfile`: Określa ustawienia sieci, takie jak sieć wirtualna i podsieć dla zasobu platformy Azure.
  * `id`: Pełny identyfikator zasobu Menedżer zasobów `networkProfile`.

Aby wdrożyć grupę kontenerów w sieci wirtualnej przy użyciu pliku YAML, należy najpierw uzyskać identyfikator profilu sieciowego. Wykonaj polecenie [AZ Network profile list][az-network-profile-list] , określając nazwę grupy zasobów zawierającej sieć wirtualną i delegowaną podsieć.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

Dane wyjściowe polecenia wyświetla pełny identyfikator zasobu dla profilu sieciowego:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Gdy masz identyfikator profilu sieciowego, Skopiuj następujący YAML do nowego pliku o nazwie *VNET-Deploy-ACI. YAML*. W `networkProfile`obszarze, Zastąp `id` wartość identyfikatorem, który właśnie został pobrany, a następnie Zapisz plik. Ta YAML tworzy grupę kontenerów o nazwie *appcontaineryaml* w sieci wirtualnej.

```YAML
apiVersion: '2018-09-01'
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
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Po zakończeniu wdrożenia Uruchom polecenie [AZ Container show][az-container-show] , aby wyświetlić jego stan:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

### <a name="delete-container-instances"></a>Usuwanie wystąpień kontenerów

Po zakończeniu pracy z utworzonymi wystąpieniami kontenerów usuń je za pomocą następujących poleceń:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Usuwanie zasobów sieciowych

Ta funkcja wymaga obecnie kilku dodatkowych poleceń w celu usunięcia utworzonych wcześniej zasobów sieciowych. Jeśli użyto przykładowych poleceń w poprzednich sekcjach tego artykułu, aby utworzyć sieć wirtualną i podsieć, można użyć następującego skryptu, aby usunąć te zasoby sieciowe. Skrypt zakłada, że grupa zasobów zawiera pojedynczą sieć wirtualną z pojedynczym profilem sieciowym.

Przed wykonaniem skryptu Ustaw `RES_GROUP` zmienną na nazwę grupy zasobów zawierającej sieć wirtualną i podsieć, która ma zostać usunięta. Zaktualizuj nazwę sieci wirtualnej, jeśli nie używasz sugerowanej wcześniej `aci-vnet` nazwy. Skrypt jest sformatowany dla powłoki bash. Jeśli wolisz innej powłoki, takiej jak PowerShell lub wiersz polecenia, musisz odpowiednio dostosować przypisanie zmiennych i metody dostępu.

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

Niektóre zasoby i funkcje sieci wirtualnej zostały omówione w tym artykule, ale krótko. Dokumentacja usługi Azure Virtual Network obejmuje wiele następujących zagadnień:

* [Sieć wirtualna](../virtual-network/manage-virtual-network.md)
* [Podsieci](../virtual-network/virtual-network-manage-subnet.md)
* [Punkty końcowe usługi](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
