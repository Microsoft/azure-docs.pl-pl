---
title: Korzystanie ze standardowego modułu równoważenia obciążenia jednostki SKU
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak używać modułu równoważenia obciążenia ze standardową jednostką SKU do udostępnienia usług za pomocą usługi Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: c1d2c0e48394fbde1b595ae4b405d84f437dc5e4
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392808"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Użyj standardowego modułu równoważenia obciążenia usługi Azure Kubernetes (AKS)

Aby zapewnić dostęp do aplikacji za pośrednictwem `LoadBalancer` usług Kubernetes typu w usłudze Azure Kubernetes Service (AKS), można użyć modułu Azure Load Balancer. Moduł równoważenia obciążenia uruchomiony w u usługi AKS może być używany jako wewnętrzny lub zewnętrzny moduł równoważenia obciążenia. Wewnętrzny moduł równoważenia obciążenia sprawia, że usługa Kubernetes jest dostępna tylko dla aplikacji działających w tej samej sieci wirtualnej co klaster AKS. Zewnętrzny moduł równoważenia obciążenia odbiera jeden lub więcej publicznych usług IP dla transferu danych przychodzących i sprawia, że usługa Kubernetes jest dostępna zewnętrznie przy użyciu publicznych usług IP.

Moduł równoważenia obciążenia platformy Azure jest dostępny w dwóch jednostkach SKU — *Basic* i *Standard*. Domyślnie *standardowa* jednostka SKU jest używana podczas tworzenia klastra AKS. Za pomocą modułu równoważenia obciążenia *standardowej* jednostki SKU zapewnia dodatkowe funkcje i funkcje, takie jak większy rozmiar puli wewnętrznej bazy danych i strefy dostępności. Przed wybraniem, które mają być używane, należy zrozumieć różnice między modułami równoważenia obciążenia *standardowego* i *podstawowego.* Po utworzeniu klastra AKS nie można zmienić jednostki SKU modułu równoważenia obciążenia dla tego klastra. Aby uzyskać więcej informacji na temat jednostek SKU *podstawowych* i *standardowych,* zobacz [porównanie jednostek SKU modułu równoważenia obciążenia platformy Azure][azure-lb-comparison].

W tym artykule przyjęto podstawową wiedzę o pojęciach platformy Kubernetes i Azure Load Balancer. Aby uzyskać więcej informacji, zobacz [Podstawowe pojęcia kubernetes dla usługi Azure Kubernetes Service (AKS)][kubernetes-concepts] i [Co to jest moduł równoważenia obciążenia platformy Azure?][azure-lb].

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.81 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz klaster AKS ze *standardowym* modułem równoważenia obciążenia usługi Azure SKU. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

Podmiot zabezpieczeń usługi klastrowania usługi AKS wymaga również uprawnień do zarządzania zasobami sieciowymi, jeśli używasz istniejącej podsieci lub grupy zasobów. Ogólnie rzecz biorąc przypisz rolę *współautora sieci* do jednostki usługi na delegowanych zasobów. Zamiast jednostki usługi można również użyć systemu przypisanego tożsamości zarządzanej dla uprawnień. Aby uzyskać więcej informacji, zobacz [Używanie tożsamości zarządzanych](use-managed-identity.md). Aby uzyskać więcej informacji na temat uprawnień, zobacz [Delegowanie dostępu AKS do innych zasobów platformy Azure][aks-sp].

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Przejście z podstawowego modułu równoważenia obciążenia jednostki SKU do standardowej jednostki SKU

Jeśli masz istniejący klaster z podstawowym modułem równoważenia obciążenia jednostki SKU, istnieją ważne różnice behawioralne, które należy zwrócić uwagę podczas migracji w celu użycia klastra ze standardowym modułem równoważenia obciążenia jednostki SKU.

Na przykład tworzenie niebieskich/zielonych wdrożeń w celu `load-balancer-sku` migracji klastrów jest powszechną praktyką, biorąc pod uwagę, że typ klastra można zdefiniować tylko w czasie tworzenia klastra. Podstawowe moduły równoważenia obciążenia *jednostek SKU* używają jednak podstawowych adresów IP *jednostki SKU,* które nie są zgodne ze standardowymi modułami równoważenia obciążenia *jednostki SKU,* ponieważ wymagają standardowych adresów IP *jednostki SKU.* Podczas migracji klastrów w celu uaktualnienia jednostek SKU modułu równoważenia obciążenia wymagany będzie nowy adres IP ze zgodną jednostką SKU adresu IP.

Aby uzyskać więcej informacji na temat migracji klastrów, odwiedź [naszą dokumentację dotyczącą zagadnień związanych z migracją,](aks-migration.md) aby wyświetlić listę ważnych tematów, które należy wziąć pod uwagę podczas migracji. Poniższe ograniczenia są również ważne różnice behawioralne, aby pamiętać, podczas korzystania ze standardowych modułów równoważenia obciążenia SKU w AKS.

### <a name="limitations"></a>Ograniczenia

Podczas tworzenia klastrów AKS i zarządzania nimi, które obsługują moduł równoważenia obciążenia przy *standardowym* poziomie SKU, obowiązują następujące ograniczenia:

* Co najmniej jeden publiczny prefiks IP lub IP jest wymagany do zezwalania na ruch wychodzący z klastra AKS. Publiczny prefiks ip lub IP jest również wymagany do utrzymania łączności między płaszczyzną sterowania a węzłami agenta, a także do zachowania zgodności z poprzednimi wersjami usługi AKS. Dostępne są następujące opcje określania publicznych adresów IP lub prefiksów IP za pomocą *standardowego* modułu równoważenia obciążenia jednostki SKU:
    * Podaj własne publiczne wiadomości IP.
    * Podaj własne publiczne prefiksy IP.
    * Określ liczbę do 100, aby umożliwić klastrowi AKS utworzenie wielu publicznych adresów IP *usługi SKU standardu* w tej samej grupie zasobów utworzonej jako klaster AKS, który zwykle nosi *nazwę MC_* na początku. Usługa AKS przypisuje publiczny adres IP do *standardowego* modułu równoważenia obciążenia jednostki SKU. Domyślnie jeden publiczny adres IP zostanie automatycznie utworzony w tej samej grupie zasobów co klaster AKS, jeśli nie określono publicznego adresu IP, publicznego prefiksu IP lub liczby adresów IP. Należy również zezwolić na adresy publiczne i uniknąć tworzenia dowolnej zasady platformy Azure, która zakazuje tworzenia adresów IP.
* W przypadku korzystania ze *standardowej* jednostki SKU dla modułu równoważenia obciążenia należy użyć programu Kubernetes w wersji *1.13 lub nowszej*.
* Definiowanie jednostki SKU modułu równoważenia obciążenia można wykonać tylko podczas tworzenia klastra AKS. Po utworzeniu klastra AKS nie można zmienić jednostki SKU modułu równoważenia obciążenia.
* W jednym klastrze można używać tylko jednego typu jednostki SKU równoważenia obciążenia (podstawowej lub standardowej).
* *Standard* Moduły równoważenia obciążenia SKU obsługują tylko *standardowe* adresy IP jednostki SKU.

## <a name="use-the-standard-sku-load-balancer"></a>Użyj *standardowego* modułu równoważenia obciążenia SKU

Podczas tworzenia klastra AKS domyślnie *standardowy* moduł równoważenia obciążenia jednostki SKU jest używany podczas uruchamiania usług w tym klastrze. Na przykład [szybki start przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] wdraża przykładową aplikację, która używa *standardowego* modułu równoważenia obciążenia jednostki SKU.

> [!IMPORTANT]
> Publicznych adresów IP można uniknąć, dostosowując trasę zdefiniowaną przez użytkownika (UDR). Określanie typu wychodzącego klastra AKS jako UDR można pominąć aprowizacji adresów IP i konfiguracji puli wewnętrznej bazy danych dla AKS utworzonego modułu równoważenia obciążenia platformy Azure. Zobacz [ustawienie klastra `outboundType` na "userDedefedRouting"](egress-outboundtype.md).

## <a name="configure-the-load-balancer-to-be-internal"></a>Skonfiguruj moduł równoważenia obciążenia jako wewnętrzny

Można również skonfigurować moduł równoważenia obciążenia jako wewnętrzny i nie uwidaczniać publicznego adresu IP. Aby skonfigurować moduł równoważenia `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` obciążenia jako wewnętrzny, dodaj jako adnotację do usługi *LoadBalancer.* W tym miejscu można zobaczyć przykładowy manifest yaml, [here][internal-lb-yaml]a także więcej szczegółów na temat wewnętrznego modułu równoważenia obciążenia .

## <a name="scale-the-number-of-managed-public-ips"></a>Skalowanie liczby zarządzanych publicznych usług IP

W przypadku korzystania ze *standardowego* modułu równoważenia obciążenia jednostki SKU z zarządzanymi wychodzącymi publicznymi adresami IP, które są tworzone domyślnie, można skalować liczbę zarządzanych wychodzących publicznych adresów IP przy użyciu *parametru load-balancer-managed-ip-count.*

Aby zaktualizować istniejący klaster, uruchom następujące polecenie. Ten parametr można również ustawić w czasie tworzenia klastra, aby mieć wiele zarządzanych wychodzących publicznych adresów IP.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

W powyższym przykładzie ustawia liczbę zarządzanych wychodzących publicznych adresów IP na *2* dla klastra *myAKSCluster* w *myResourceGroup*. 

Można również użyć parametru *load-balance-managed-ip-count,* aby ustawić początkową liczbę zarządzanych wychodzących publicznych adresów IP podczas tworzenia klastra przez dołączenie parametru `--load-balancer-managed-outbound-ip-count` i ustawienie go do żądanej wartości. Domyślna liczba zarządzanych wychodzących publicznych usług IP to 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Podaj własne publiczne adresy IP lub prefiksy dla wyjścia

W przypadku korzystania ze *standardowego* modułu równoważenia obciążenia jednostki SKU klaster AKS automatycznie tworzy publiczny adres IP w tej samej grupie zasobów, która została utworzona dla klastra AKS i przypisuje publiczny adres IP do *standardowego* modułu równoważenia obciążenia jednostki SKU. Alternatywnie można przypisać własny publiczny adres IP w czasie tworzenia klastra lub można zaktualizować właściwości modułu równoważenia obciążenia istniejącego klastra.

Przynosząc wiele adresów IP lub prefiksów, można zdefiniować wiele usług kopii zapasowej podczas definiowania adresu IP za pojedynczym obiektem modułu równoważenia obciążenia. Punkt końcowy wyjścia określonych węzłów będzie zależeć od usługi, z którą są skojarzone.

> [!IMPORTANT]
> Należy użyć *standardowych* adresów IP jednostek SKU dla wyjścia ze *standardową* jednostką SKU modułu równoważenia obciążenia. Jednostkę SKU publicznych adresów IP można zweryfikować za pomocą polecenia [az network public-ip show:][az-network-public-ip-show]
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Użyj polecenia [az network public-ip show,][az-network-public-ip-show] aby wyświetlić listę identyfikatorów publicznych adresów IP.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Powyższe polecenie pokazuje identyfikator publicznego adresu IP *myPublicIP* w grupie zasobów *myResourceGroup.*

Użyj polecenia *az aks update* z parametrem *load-balance-outbound-ips,* aby zaktualizować klaster za pomocą publicznych adresów IP.

W poniższym przykładzie użyto parametru *load-balancer-outbound-ips* z identyfikatorami z poprzedniego polecenia.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Można również użyć publicznych prefiksów IP dla wyjścia ze *standardowym* modułem równoważenia obciążenia jednostki SKU. W poniższym przykładzie użyto polecenia [az network public-ip prefix show][az-network-public-ip-prefix-show] do listy identyfikatorów publicznych prefiksów IP:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Powyższe polecenie pokazuje identyfikator publicznego prefiksu IP *myPublicIpPrefix* w grupie zasobów *myResourceGroup.*

W poniższym przykładzie użyto *parametru prefiksów load-balancer-outbound-ip* z identyfikatorami z poprzedniego polecenia.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> Publiczne adresy IP i prefiksy IP muszą znajdować się w tym samym regionie i części tej samej subskrypcji co klaster AKS. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Definiowanie własnego publicznego adresu IP lub prefiksów w czasie tworzenia klastra

Możesz chcieć przynieść własne adresy IP lub prefiksy IP dla wyjścia w czasie tworzenia klastra do obsługi scenariuszy, takich jak białe listy wychodzących punktów końcowych. Dołącz te same parametry pokazane powyżej do kroku tworzenia klastra, aby zdefiniować własne publiczne adresy IP i prefiksy IP na początku cyklu życia klastra.

Polecenie *az aks create* z parametrem *load-balance-outbound-ips* umożliwia utworzenie nowego klastra z publicznymi adresami IP na początku.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Polecenie *az aks create* z parametrem *load-balance-outbound-ip-prefixes* umożliwia utworzenie nowego klastra z publicznymi prefiksami IP na początku.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="configure-outbound-ports-and-idle-timeout"></a>Konfigurowanie portów wychodzących i limitu czasu bezczynnego

> [!WARNING]
> Poniższa sekcja jest przeznaczona dla zaawansowanych scenariuszy sieci na większą skalę lub rozwiązywania problemów wyczerpania SNAT z domyślnymi konfiguracjami. Przed zmianą *allocatedOutboundPorts* lub *IdleTimeoutInMinutes* należy mieć dokładny spis dostępnego przydziału dla maszyn wirtualnych i adresów IP, aby zachować klastry w dobrej kondycji.
> 
> Zmiana wartości *allocatedOutboundPorts* i *IdleTimeoutInMinutes* może znacznie zmienić zachowanie reguły ruchu wychodzącego dla modułu równoważenia obciążenia. Przejrzyj [reguły ruchu wychodzącego modułu równoważenia obciążenia,][azure-lb-outbound-rules-overview] [reguły ruchu wychodzącego modułu równoważenia obciążenia][azure-lb-outbound-rules]i [połączenia wychodzące na platformie Azure][azure-lb-outbound-connections] przed zaktualizowaniem tych wartości, aby w pełni zrozumieć wpływ zmian.

Porty przydzielone wychodzące i ich limity czasu bezczynnego są używane dla [SNAT][azure-lb-outbound-connections]. Domyślnie moduł równoważenia obciążenia *standardowej* jednostki SKU używa [automatycznego przypisania dla liczby portów wychodzących na podstawie rozmiaru puli zaplecza][azure-lb-outbound-preallocatedports] i 30-minutowego limitu czasu bezczynności dla każdego portu. Aby wyświetlić te wartości, użyj [listy reguł wychodzących az lb,][az-network-lb-outbound-rule-list] aby wyświetlić regułę ruchu wychodzącego dla modułu równoważenia obciążenia:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Poprzednie polecenia będą na przykład wymieniać regułę ruchu wychodzącego dla modułu równoważenia obciążenia:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Przykładowy wynik przedstawia wartość domyślną dla *AllocatedOutboundPorts* i *IdleTimeoutInMinutes*. Wartość 0 dla *AllocatedOutboundPorts* ustawia liczbę portów wychodzących przy użyciu automatycznego przypisywania liczby portów wychodzących na podstawie rozmiaru puli wewnętrznej bazy danych. Na przykład jeśli klaster ma 50 lub mniej węzłów, 1024 porty dla każdego węzła są przydzielane.

Należy rozważyć zmianę ustawienia *allocatedOutboundPorts* lub *IdleTimeoutInMinutes,* jeśli spodziewasz się zmierzyć wyczerpania SNAT na podstawie powyższej konfiguracji domyślnej. Każdy dodatkowy adres IP umożliwia 64 000 dodatkowych portów do alokacji, jednak moduł równoważenia obciążenia standardowego platformy Azure nie zwiększa automatycznie portów na węzeł po dodaniu większej liczby adresów IP. Wartości te można zmienić, ustawiając parametry *limitu czasu modułu równoważenia obciążenia-balancer-outbound* i *parametrów limitu czasu i równoważenia obciążenia.* Przykład:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> Należy [obliczyć wymagany przydział][calculate-required-quota] przed dostosowaniem *allocatedOutboundPorts,* aby uniknąć problemów z łącznością lub skalowaniem. Wartość określona dla *allocatedOutboundPorts* musi być również wielokrotnością 8.

Podczas tworzenia klastra można również użyć *parametrów load-balancer-outbound-ports* i *load-balancer-idle-timeout,* ale należy również określić albo prefiksy *load-balancer-managed-outbound-ip-count,* *load-balancer-outbound-ips*lub *load-balancer-outbound-ip-prefixes.*  Przykład:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

Podczas zmiany *obciążenia-balancer-wychodzących portów* i *obciążenia-balancer-idle-timeout* parametry z ich domyślnego, wpływa na zachowanie profilu modułu równoważenia obciążenia, który ma wpływ na cały klaster.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>Wymagany przydział do dostosowywania allocatedOutboundPorts
Musi mieć wystarczającą pojemność wychodzącej pamięci IP na podstawie liczby maszyn wirtualnych węzła i żądanych przydzielonych portów wychodzących. Aby sprawdzić poprawność, masz wystarczającą pojemność wychodzącej własności IP, użyj następującej formuły: 
 
*outboundIPs* \* 64,000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*.
 
Na przykład, jeśli masz 3 *węzły VMs*i 50,000 *desiredAllocatedOutboundPorts*, musisz mieć co najmniej 3 *wychodzące rozwiązania.* Zaleca się włączenie dodatkowej pojemności wychodzącej usługi IP wykraczających poza to, czego potrzebujesz. Ponadto należy uwzględnić skalowanie automatyczne klastra i możliwość uaktualnień puli węzłów podczas obliczania wychodzącej pojemności IP. W przypadku skalowania automatycznego klastra przejrzyj bieżącą liczbę węzłów i maksymalną liczbę węzłów i użyj wyższej wartości. W przypadku uaktualniania należy uwzględnić dodatkową maszynę wirtualną węzła dla każdej puli węzłów, która umożliwia uaktualnienie.
 
Podczas *ustawiania IdleTimeoutInMinutes* do innej wartości niż domyślna 30 minut, należy wziąć pod uwagę, jak długo obciążenia będą potrzebować połączenia wychodzącego. Należy również wziąć pod uwagę domyślną wartość limitu czasu dla *standardowego* modułu równoważenia obciążenia SKU używane poza AKS jest 4 minut. *IdleTimeoutInMinutes* wartość, która dokładniej odzwierciedla określone obciążenie usługi AKS może pomóc zmniejszyć wyczerpanie SNAT spowodowane przez wiązanie połączeń nie są już używane.

## <a name="restrict-access-to-specific-ip-ranges"></a>Ograniczanie dostępu do określonych zakresów adresów IP

Network Security Group (NSG) skojarzona z siecią wirtualną dla modułu równoważenia obciążenia domyślnie ma regułę zezwalania na cały przychodzący ruch zewnętrzny. Tę regułę można zaktualizować, aby zezwalać tylko na określone zakresy adresów IP dla ruchu przychodzącego. Następujący manifest używa *loadBalancerSourceRanges* do określenia nowego zakresu adresów IP dla przychodzącego ruchu zewnętrznego:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

Powyższy przykład aktualizuje regułę, aby zezwalać tylko na przychodzący ruch zewnętrzny z zakresu *MY_EXTERNAL_IP_RANGE.* Więcej informacji na temat używania tej metody w celu ograniczenia dostępu do usługi równoważenia obciążenia można znaleźć w [dokumentacji programu Kubernetes.][kubernetes-cloud-provider-firewall]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach Kubernetes w [dokumentacji usług Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-cloud-provider-firewall]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-cloud-provider-firewall/#restrict-access-for-loadbalancer-service
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-rules-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[calculate-required-quota]: #required-quota-for-customizing-allocatedoutboundports
