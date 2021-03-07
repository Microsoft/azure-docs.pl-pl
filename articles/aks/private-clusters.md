---
title: Tworzenie prywatnego klastra usługi Azure Kubernetes Service
description: Dowiedz się, jak utworzyć prywatny klaster usługi Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/5/2021
ms.openlocfilehash: d5f39460ad821265aed2c21d7426aa894f7cc933
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425111"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Tworzenie prywatnego klastra usługi Azure Kubernetes Service

W klastrze prywatnym płaszczyzna kontroli lub serwer interfejsu API ma wewnętrzne adresy IP, które są zdefiniowane w [alokacji RFC1918-Address dla prywatnego dokumentu internetowego](https://tools.ietf.org/html/rfc1918) . Za pomocą klastra prywatnego można zapewnić, że ruch sieciowy między serwerem interfejsu API a pulami węzłów pozostanie tylko w sieci prywatnej.

Płaszczyzna kontroli lub serwer interfejsu API znajduje się w subskrypcji platformy Azure zarządzanej przez usługę Azure Kubernetes Service (AKS). Klaster klienta lub Pula węzłów znajduje się w subskrypcji klienta. Serwer i Pula węzłów mogą komunikować się ze sobą za pomocą [usługi Azure Private link][private-link-service] w sieci wirtualnej serwera interfejsu API i prywatnego punktu końcowego, który jest udostępniany w podsieci klastra AKS klienta.

## <a name="region-availability"></a>Dostępność w danym regionie

Klaster prywatny jest dostępny w regionach publicznych, Azure Government i w Chinach, w których [są obsługiwane AKS](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).

> [!NOTE]
> Obsługiwane są Azure Government witryny, ale US Gov Teksas nie są obecnie obsługiwane z powodu braku obsługi linku prywatnego.

## <a name="prerequisites"></a>Wymagania wstępne

* Interfejs wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej
* Usługa link prywatny jest obsługiwana tylko w przypadku standardowej Azure Load Balancer. Podstawowa Azure Load Balancer nie jest obsługiwana.  
* Aby użyć niestandardowego serwera DNS, należy dodać Azure DNS 168.63.129.16 IP jako nadrzędny Serwer DNS na niestandardowym serwerze DNS.

## <a name="create-a-private-aks-cluster"></a>Tworzenie prywatnego klastra AKS

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów lub Użyj istniejącej grupy zasobów dla klastra AKS.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Domyślna sieć podstawowa 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Gdzie `--enable-private-cluster` jest obowiązkową flagą dla klastra prywatnego. 

### <a name="advanced-networking"></a>Zaawansowane sieci  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Gdzie `--enable-private-cluster` jest obowiązkową flagą dla klastra prywatnego. 

> [!NOTE]
> Jeśli adres CIDR (172.17.0.1/16) mostka platformy Docker koliduje z maską CIDR podsieci, należy odpowiednio zmienić adres mostka platformy Docker.

## <a name="configure-private-dns-zone"></a>Konfigurowanie strefy Prywatna strefa DNS 

Aby skonfigurować strefę Prywatna strefa DNS, można użyć następujących parametrów.

- Wartość domyślna to "system". W przypadku pominięcia argumentu--Private-DNS-strefy AKS utworzy strefę Prywatna strefa DNS w grupie zasobów węzła.
- "Brak" oznacza, że AKS nie utworzy strefy Prywatna strefa DNS.  Wymaga to przeniesienia własnego serwera DNS i skonfigurowania rozpoznawania nazw DNS dla prywatnej nazwy FQDN.  Jeśli nie skonfigurujesz rozpoznawania nazw DNS, usługa DNS jest rozpoznawana tylko w węzłach agenta i spowoduje problemy z klastrem po wdrożeniu. 
- "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" wymaga utworzenia strefy Prywatna strefa DNS w tym formacie dla chmury globalnej platformy Azure: `privatelink.<region>.azmk8s.io` . Do przechodzenia do przodu będzie potrzebny identyfikator zasobu tego Prywatna strefa DNS.  Ponadto potrzebna jest tożsamość lub jednostka usługi przypisana przez użytkownika z co najmniej `private dns zone contributor` rolą.
- "FQDN-subdomene" można użyć z "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" tylko w celu zapewnienia możliwości poddomeny `privatelink.<region>.azmk8s.io`

### <a name="prerequisites"></a>Wymagania wstępne

* AKS wersja zapoznawcza 0.5.3 lub nowsza
* Interfejs API w wersji 2020-11-01 lub nowszej

### <a name="create-a-private-aks-cluster-with-private-dns-zone-preview"></a>Tworzenie prywatnego klastra AKS z strefą Prywatna strefa DNS (wersja zapoznawcza)

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [system|none]
```

### <a name="create-a-private-aks-cluster-with-a-custom-private-dns-zone-preview"></a>Tworzenie prywatnego klastra AKS za pomocą niestandardowej strefy Prywatna strefa DNS (wersja zapoznawcza)

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId> --fqdn-subdomain <subdomain-name>
```
## <a name="options-for-connecting-to-the-private-cluster"></a>Opcje łączenia się z klastrem prywatnym

Punkt końcowy serwera interfejsu API nie ma publicznego adresu IP. Aby zarządzać serwerem interfejsu API, należy użyć maszyny wirtualnej, która ma dostęp do Virtual Network platformy Azure klastra AKS. Istnieje kilka opcji ustanawiania łączności sieciowej z klastrem prywatnym.

* Utwórz maszynę wirtualną w tej samej usłudze Azure Virtual Network (VNet) jako klaster AKS.
* Użyj maszyny wirtualnej w oddzielnym sieci i skonfiguruj [komunikację równorzędną sieci wirtualnej][virtual-network-peering].  Zapoznaj się z sekcją poniżej, aby uzyskać więcej informacji na temat tej opcji.
* Użyj usługi [Express Route lub połączenia sieci VPN][express-route-or-VPN] .

Najłatwiej jest utworzyć maszynę wirtualną w tej samej sieci wirtualnej, co klaster AKS.  Funkcja Express Route i sieci VPN zwiększa koszty i wymaga dodatkowej złożoności sieci.  Komunikacja równorzędna sieci wirtualnych wymaga zaplanowania zakresów CIDR sieci, aby upewnić się, że nie ma nakładających się zakresów.

## <a name="virtual-network-peering"></a>Komunikacja równorzędna sieci wirtualnej

Jak wspomniano, Komunikacja równorzędna sieci wirtualnej jest jednym ze sposobów uzyskiwania dostępu do klastra prywatnego. Aby użyć komunikacji równorzędnej sieci wirtualnej, należy skonfigurować połączenie między siecią wirtualną i prywatną strefą DNS.
    
1. Przejdź do grupy zasobów węzła w Azure Portal.  
2. Wybierz prywatną strefę DNS.   
3. W lewym okienku wybierz łącze **Sieć wirtualna** .  
4. Utwórz nowy link, aby dodać sieć wirtualną maszyny wirtualnej do prywatnej strefy DNS. Udostępnienie linku strefy DNS może potrwać kilka minut.  
5. W Azure Portal przejdź do grupy zasobów zawierającej sieć wirtualną klastra.  
6. W prawym okienku wybierz sieć wirtualną. Nazwa sieci wirtualnej ma postać *AKS-VNET- \**.  
7. W lewym okienku wybierz pozycję **Komunikacja równorzędna**.  
8. Wybierz pozycję **Dodaj**, Dodaj sieć wirtualną maszyny wirtualnej, a następnie utwórz komunikację równorzędną.  
9. Przejdź do sieci wirtualnej, w której znajduje się maszyna wirtualna, wybierz pozycję **Komunikacja równorzędna**, wybierz sieć wirtualną AKS, a następnie utwórz komunikację równorzędną. Jeśli zakresy adresów w sieci wirtualnej AKS i konflikty sieci wirtualnej maszyn wirtualnych są niepowodzeniem, Komunikacja równorzędna nie powiedzie się. Aby uzyskać więcej informacji, zobacz  [wirtualne sieci równorzędne][virtual-network-peering].

## <a name="hub-and-spoke-with-custom-dns"></a>Koncentrator i szprycha z niestandardowym systemem DNS

[Architektury](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) gwiazdy są często używane do wdrażania sieci na platformie Azure. W wielu z tych wdrożeń ustawienia DNS w szprychie sieci wirtualnych są skonfigurowane tak, aby odwoływać się do centralnej usługi przesyłania dalej w systemie DNS w celu umożliwienia lokalnego rozpoznawania nazw DNS opartych na platformie Azure. W przypadku wdrażania klastra AKS do takiego środowiska sieciowego należy wziąć pod uwagę pewne szczególne kwestie.

![Prywatny koncentrator klastra i szprycha](media/private-clusters/aks-private-hub-spoke.png)

1. Domyślnie po zainicjowaniu obsługi klastra prywatnego w grupie zasobów zarządzanej przez klaster jest tworzony prywatny punkt końcowy (1) i prywatna strefa DNS (2). Klaster używa rekordu A w strefie prywatnej w celu rozpoznania adresu IP prywatnego punktu końcowego na potrzeby komunikacji z serwerem interfejsu API.

2. Prywatna strefa DNS jest połączona tylko z siecią wirtualną, do której są dołączone węzły klastra (3). Oznacza to, że prywatny punkt końcowy może być rozpoznany tylko przez hosty w połączonej sieci wirtualnej. W scenariuszach, w których w sieci wirtualnej nie skonfigurowano żadnych niestandardowych nazw DNS (domyślnie), to działa bez problemu jako hosty w 168.63.129.16 dla systemu DNS, który może rozpoznawać rekordy w prywatnej strefie DNS ze względu na link.

3. W scenariuszach, w których sieć wirtualna zawierająca klaster ma niestandardowe ustawienia DNS (4), wdrożenie klastra kończy się niepowodzeniem, chyba że prywatna strefa DNS jest połączona z siecią wirtualną, która zawiera niestandardowe resolvery DNS (5). Ten link można utworzyć ręcznie po utworzeniu strefy prywatnej podczas aprowizacji klastra lub za pośrednictwem automatyzacji podczas wykrywania tworzenia strefy przy użyciu mechanizmów wdrażania opartych na zdarzeniach (na przykład Azure Event Grid i Azure Functions).

> [!NOTE]
> Jeśli używasz funkcji [Przenieś własną tabelę tras z korzystającą wtyczki kubenet](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) i przeniesiesz swój własny klaster DNS z prywatnym klastrem, tworzenie klastra zakończy się niepowodzeniem. W celu pomyślnego utworzenia klastra konieczne będzie skojarzenie [w grupie](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) zasobów węzła z podsiecią.

## <a name="limitations"></a>Ograniczenia 
* Nie można zastosować dozwolonych zakresów adresów IP do punktu końcowego serwera prywatnego interfejsu API, są one stosowane tylko do publicznego serwera interfejsu API
* [Ograniczenia usługi Azure Private link][private-link-service] są stosowane do klastrów prywatnych.
* Brak obsługi dla agentów hostowanych przez firmę Microsoft dla platformy Azure DevOps z klastrami prywatnymi. Rozważ użycie [agentów samoobsługowych](/azure/devops/pipelines/agents/agents?tabs=browser). 
* W przypadku klientów, którzy muszą umożliwić Azure Container Registry pracy z prywatnym AKS, Container Registry sieci wirtualnej musi być połączona z siecią wirtualną klastra agentów.
* Brak obsługi konwertowania istniejących klastrów AKS do klastrów prywatnych
* Usunięcie lub zmodyfikowanie prywatnego punktu końcowego w podsieci klienta spowoduje, że klaster przestanie działać. 
* Po zaktualizowaniu rekordu A klienta na własnych serwerach DNS te zasobniki nadal rozwiązują apiserverą nazwę FQDN do starszego adresu IP po migracji do momentu ponownego uruchomienia. Po migracji płaszczyzny kontroli klienci muszą ponownie uruchomić hostNetworky i DNSPolicye.
* W przypadku konserwacji na płaszczyźnie kontroli [AKS IP](./limit-egress-traffic.md) może ulec zmianie. W takim przypadku należy zaktualizować rekord A wskazujący prywatny adres IP serwera interfejsu API na niestandardowym serwerze DNS i ponownie uruchomić wszystkie niestandardowe zasobniki lub wdrożenia przy użyciu hostNetwork.

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents?view=azure-devops
[availability-zones]: availability-zones.md
