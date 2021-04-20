---
title: Tworzenie prywatnego Azure Kubernetes Service klastra
description: Dowiedz się, jak utworzyć klaster Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/31/2021
ms.openlocfilehash: 339bb41aed5ead3d7ee7d1217bfbc771cf068832
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719119"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Tworzenie prywatnego Azure Kubernetes Service klastra

W klastrze prywatnym płaszczyzna sterowania lub serwer interfejsu API ma wewnętrzne adresy IP zdefiniowane w dokumencie [RFC1918 — Alokacja](https://tools.ietf.org/html/rfc1918) adresów dla prywatnego Internetu. Korzystając z klastra prywatnego, można zapewnić, że ruch sieciowy między serwerem interfejsu API i pulami węzłów pozostanie tylko w sieci prywatnej.

Płaszczyzna sterowania lub serwer interfejsu API znajduje się w subskrypcji platformy Azure zarządzanej Azure Kubernetes Service (AKS). Klaster lub pula węzłów klienta znajduje się w subskrypcji klienta. Serwer i klaster lub pula węzłów mogą komunikować się ze sobą za pośrednictwem usługi [Azure Private Link][private-link-service] w sieci wirtualnej serwera interfejsu API i prywatnego punktu końcowego, który jest ujawniony w podsieci klastra AKS klienta.

## <a name="region-availability"></a>Dostępność w danym regionie

Klaster prywatny jest dostępny w regionach publicznych, Azure Government i Azure (Chiny) — 21Vianet, w których usługa [AKS jest obsługiwana.](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)

> [!NOTE]
> Azure Government lokacje są obsługiwane, US Gov Teksas nie są obecnie obsługiwane z powodu Private Link technicznej.

## <a name="prerequisites"></a>Wymagania wstępne

* Interfejs wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej
* Usługa Private Link jest obsługiwana tylko w Azure Load Balancer Standardowa. Podstawowe Azure Load Balancer nie są obsługiwane.  
* Aby użyć niestandardowego serwera DNS, dodaj adres IP Azure DNS 168.63.129.16 jako nadrzędny serwer DNS na niestandardowym serwerze DNS.

## <a name="create-a-private-aks-cluster"></a>Tworzenie prywatnego klastra usługi AKS

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów lub użyj istniejącej grupy zasobów dla klastra usługi AKS.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Domyślna sieć podstawowa 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Gdzie `--enable-private-cluster` jest flagą obowiązkową dla klastra prywatnego. 

### <a name="advanced-networking"></a>Sieć zaawansowana  

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
Gdzie `--enable-private-cluster` jest flagą obowiązkową dla klastra prywatnego. 

> [!NOTE]
> Jeśli adres mostka platformy Docker CIDR (172.17.0.1/16) koliduje z podsiecią CIDR, zmień odpowiednio adres mostka platformy Docker.

## <a name="configure-private-dns-zone"></a>Konfigurowanie Prywatna strefa DNS strefie 

Następujące parametry można wykorzystać do skonfigurowania Prywatna strefa DNS strefie.

- Wartość domyślna to "System". Jeśli argument --private-dns-zone zostanie pominięty, usługa AKS utworzy strefę Prywatna strefa DNS w grupie zasobów węzła.
- "Brak" oznacza, że nie utworzy ona strefy Prywatna strefa DNS AKS.  Wymaga to użycia własnego serwera DNS i skonfigurowania rozpoznawania nazw DNS dla prywatnej nazwy FQDN.  Jeśli nie skonfigurujesz rozpoznawania nazw DNS, usługa DNS będzie rozpoznawana tylko w węzłach agenta i spowoduje problemy z klastrem po wdrożeniu. 
- "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" wymaga utworzenia strefy Prywatna strefa DNS w tym formacie dla globalnej chmury platformy Azure: `privatelink.<region>.azmk8s.io` . Będziesz potrzebować identyfikatora zasobu tej strefy Prywatna strefa DNS przyszłości.  Ponadto będziesz potrzebować tożsamości przypisanej przez użytkownika lub jednostki usługi z co najmniej rolami `private dns zone contributor` `vnet contributor` i .
- "fqdn-subdomain" może być używane z "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" tylko w celu zapewnienia możliwości poddomeny `privatelink.<region>.azmk8s.io`

### <a name="prerequisites"></a>Wymagania wstępne

* AKS (wersja zapoznawcza) w wersji 0.5.7 lub nowszej
* Interfejs API w wersji 2020-11-01 lub nowszej

### <a name="create-a-private-aks-cluster-with-private-dns-zone"></a>Tworzenie prywatnego klastra AKS z Prywatna strefa DNS strefą

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [system|none]
```

### <a name="create-a-private-aks-cluster-with-a-custom-private-dns-zone"></a>Tworzenie prywatnego klastra AKS z niestandardową strefą Prywatna strefa DNS strefie

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId> --fqdn-subdomain <subdomain-name>
```

## <a name="options-for-connecting-to-the-private-cluster"></a>Opcje nawiązywania połączenia z klastrem prywatnym

Punkt końcowy serwera interfejsu API nie ma publicznego adresu IP. Aby zarządzać serwerem interfejsu API, musisz użyć maszyny wirtualnej, która ma dostęp do usługi Azure Virtual Network (VNet) klastra AKS. Istnieje kilka opcji ustanawiania łączności sieciowej z klastrem prywatnym.

* Utwórz maszynę wirtualną w tej samej sieci Virtual Network Azure jako klaster usługi AKS.
* Użyj maszyny wirtualnej w oddzielnej sieci i skonfiguruj wirtualne [sieci równorzędne][virtual-network-peering].  Więcej informacji na temat tej opcji znajduje się w poniższej sekcji.
* Użyj połączenia [usługi Express Route lub sieci VPN.][express-route-or-VPN]
* Użyj funkcji [Uruchamianie polecenia AKS.](#aks-run-command-preview)

Najłatwiejszą opcją jest utworzenie maszyny wirtualnej w tej samej sieci wirtualnej co klaster usługi AKS.  Express Route i sieci VPN dodają koszty i wymagają dodatkowej złożoności sieci.  Komunikacja równorzędna sieci wirtualnych wymaga zaplanowania zakresów CIDR sieci w celu zapewnienia, że nie ma nakładających się zakresów.

### <a name="aks-run-command-preview"></a>AKS Uruchamianie polecenia (wersja zapoznawcza)

Obecnie, gdy musisz uzyskać dostęp do klastra prywatnego, musisz to zrobić w sieci wirtualnej klastra, sieci równorzędnej lub maszynie klienckiej. Zwykle wymaga to połączenia maszyny za pośrednictwem sieci VPN lub usługi Express Route z siecią wirtualną klastra lub serwera przeskoku w sieci wirtualnej klastra. Polecenie uruchomienia usługi AKS umożliwia zdalne wywoływanie poleceń w klastrze usługi AKS za pośrednictwem interfejsu API usługi AKS. Ta funkcja udostępnia interfejs API, który umożliwia na przykład wykonywanie poleceń just in time ze zdalnego komputera przenośnego dla klastra prywatnego. Może to znacznie pomóc w szybkim dostępie just in time do klastra prywatnego, gdy komputer kliencki nie znajduje się w sieci prywatnej klastra, zachowując i wymuszając te same kontrolki RBAC i prywatny serwer interfejsu API.

### <a name="register-the-runcommandpreview-preview-feature"></a>Rejestrowanie funkcji w `RunCommandPreview` wersji zapoznawczej

Aby korzystać z nowego Uruchamianie polecenia API, należy włączyć `RunCommandPreview` flagę funkcji w subskrypcji.

Zarejestruj `RunCommandPreview` flagę funkcji za pomocą polecenia [az feature register][az-feature-register], jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "RunCommandPreview"
```

Wyświetlanie zarejestrowanego stanu może potrwać *kilka minut.* Sprawdź stan rejestracji za pomocą [polecenia az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/RunCommandPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, odśwież rejestrację dostawcy *zasobów Microsoft.ContainerService* za pomocą [polecenia az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-run-command"></a>Korzystanie z Uruchamianie polecenia AKS

Proste polecenie

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl get pods -n kube-system"
```

Wdrażanie manifestu przez dołączenie określonego pliku

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f deployment.yaml
```

Wdrażanie manifestu przez dołączenie całego folderu

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f .
```

Przeprowadzanie instalacji programu Helm i przekaż manifest określonych wartości

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "helm repo add bitnami https://charts.bitnami.com/bitnami && helm repo update && helm install my-release -f values.yaml bitnami/nginx" -f values.yaml
```

## <a name="virtual-network-peering"></a>Komunikacja równorzędna sieci wirtualnej

Jak wspomniano, komunikacja równorzędna sieci wirtualnych jest jednym ze sposobów uzyskiwania dostępu do klastra prywatnego. Aby korzystać z komunikacji równorzędnej sieci wirtualnych, należy skonfigurować połączenie między siecią wirtualną a prywatną strefą DNS.
    
1. Przejdź do grupy zasobów węzła w Azure Portal.  
2. Wybierz prywatną strefę DNS.   
3. W okienku po lewej stronie wybierz link **Sieć wirtualna.**  
4. Utwórz nowy link, aby dodać sieć wirtualną maszyny wirtualnej do prywatnej strefy DNS. Po kilku minutach łącze strefy DNS stanie się dostępne.  
5. W Azure Portal przejdź do grupy zasobów zawierającej sieć wirtualną klastra.  
6. W okienku po prawej stronie wybierz sieć wirtualną. Nazwa sieci wirtualnej ma postać *aks-vnet-. \**  
7. W okienku po lewej stronie wybierz pozycję **Komunikacja równorzędna.**  
8. Wybierz **pozycję** Dodaj , dodaj sieć wirtualną maszyny wirtualnej, a następnie utwórz połączenie komunikacji równorzędnej.  
9. Przejdź do sieci wirtualnej, w której masz maszynę wirtualną, wybierz pozycję **Komunikacja** równorzędna, wybierz sieć wirtualną usługi AKS, a następnie utwórz połączenie komunikacji równorzędnej. Jeśli zakresy adresów w sieci wirtualnej usługi AKS i konflikty sieci wirtualnej maszyny wirtualnej, komunikacja równorzędna zakończy się niepowodzeniem. Aby uzyskać więcej informacji, zobacz [Virtual network peering (Komunikacja równorzędna sieci wirtualnych).][virtual-network-peering]

## <a name="hub-and-spoke-with-custom-dns"></a>Piasta i szprychy z niestandardowym systemem DNS

[Architektury piasty i szprych](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) są często używane do wdrażania sieci na platformie Azure. W wielu z tych wdrożeń ustawienia DNS w sieciach wirtualnych szprych są konfigurowane tak, aby odwoływały się do centralnej usługi przesyłania dalej DNS w celu umożliwienia lokalnego rozpoznawania nazw DNS na platformie Azure. Podczas wdrażania klastra usługi AKS w takim środowisku sieciowym należy wziąć pod uwagę pewne specjalne zagadnienia.

![Koncentrator i szprycha klastra prywatnego](media/private-clusters/aks-private-hub-spoke.png)

1. Domyślnie podczas aprowizowania klastra prywatnego prywatny punkt końcowy (1) i prywatna strefa DNS (2) są tworzone w grupie zasobów zarządzanej przez klaster. Klaster używa rekordu A w strefie prywatnej, aby rozpoznać adres IP prywatnego punktu końcowego do komunikacji z serwerem interfejsu API.

2. Prywatna strefa DNS jest połączona tylko z siecią wirtualną, do których są dołączone węzły klastra (3). Oznacza to, że prywatny punkt końcowy może zostać rozpoznany tylko przez hosty w tej połączonej sieci wirtualnej. W scenariuszach, w których w sieci wirtualnej nie skonfigurowano niestandardowego systemu DNS (ustawienie domyślne), działa to bez problemu jako punkt hostów 168.63.129.16 dla systemu DNS, który może rozpoznać rekordy w prywatnej strefie DNS z powodu łącza.

3. W scenariuszach, w których sieć wirtualna zawierająca klaster ma niestandardowe ustawienia DNS (4), wdrażanie klastra kończy się niepowodzeniem, chyba że prywatna strefa DNS jest połączona z siecią wirtualną zawierającą niestandardowe program rozpoznawania nazw DNS (5). Ten link można utworzyć ręcznie po utworzeniu strefy prywatnej podczas aprowizowania klastra lub za pośrednictwem automatyzacji po wykryciu utworzenia strefy przy użyciu mechanizmów wdrażania opartych na zdarzeniach (na przykład Azure Event Grid i Azure Functions).

> [!NOTE]
> Jeśli używasz funkcji Bring Your Own Route Table z usługą [kubenet](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) i usługi Bring Your Own DNS z klastrem prywatnym, tworzenie klastra nie powiedzie się. Aby tworzenie zakończyło się pomyślnie, należy skojarzyć tabelę [RouteTable](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) w grupie zasobów węzła z podsiecią po awarii klastra.

## <a name="limitations"></a>Ograniczenia 
* Autoryzowanych zakresów adresów IP nie można stosować do prywatnego punktu końcowego serwera interfejsów API. Dotyczą one tylko publicznego serwera interfejsu API
* [Azure Private Link usługi mają][private-link-service] zastosowanie do klastrów prywatnych.
* Brak obsługi Azure DevOps hostowanych przez firmę Microsoft z klastrami prywatnymi. Rozważ użycie [agentów hostowanych samodzielnie.](/azure/devops/pipelines/agents/agents?tabs=browser) 
* W przypadku klientów, którzy muszą Azure Container Registry pracy z prywatną usługą AKS, sieć wirtualna Container Registry musi być równorzędna z siecią wirtualną klastra agentów.
* Brak obsługi konwertowania istniejących klastrów usługi AKS na klastry prywatne
* Usunięcie lub zmodyfikowanie prywatnego punktu końcowego w podsieci klienta spowoduje, że klaster przestanie działać. 
* Po zaktualizowaniu rekordu A na własnych serwerach DNS klienci nadal rozpoznają nazwę FQDN apiserver jako starszy adres IP po migracji, dopóki nie zostaną ponownie uruchomione. Po migracji płaszczyzny sterowania klienci muszą ponownie uruchomić zasobniki hostNetwork i zasobniki default-DNSPolicy.
* W przypadku konserwacji na płaszczyźnie sterowania adres [IP usługi AKS](./limit-egress-traffic.md) może ulec zmianie. W takim przypadku należy zaktualizować rekord A, który wskaże prywatny adres IP serwera interfejsu API na niestandardowym serwerze DNS, i ponownie uruchomić wszystkie niestandardowe zasobniki lub wdrożenia przy użyciu hostNetwork.

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents
[availability-zones]: availability-zones.md
