---
title: Korzystanie ze standardowego modułu równoważenia obciążenia jednostki SKU
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak używać modułu równoważenia obciążenia ze standardową jednostką SKU, aby udostępnić swoje usługi za pomocą usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 09/27/2019
ms.openlocfilehash: 9f75153868633079f24992955c7585f5954befc4
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465783"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Korzystanie ze standardowego modułu równoważenia obciążenia jednostki SKU w usłudze Azure Kubernetes Service (AKS)

Aby zapewnić dostęp do aplikacji za pośrednictwem usług Kubernetes Services typu `LoadBalancer` w usłudze Azure Kubernetes Service (AKS), możesz użyć Azure Load Balancer. Moduł równoważenia obciążenia uruchomiony w systemie AKS może być używany jako wewnętrzny lub zewnętrzny moduł równoważenia obciążenia. Wewnętrzny moduł równoważenia obciążenia sprawia, że usługa Kubernetes jest dostępna tylko dla aplikacji działających w tej samej sieci wirtualnej co klaster AKS. Zewnętrzny moduł równoważenia obciążenia otrzymuje co najmniej jeden publiczny adres IP dla ruchu przychodzącego, a usługa Kubernetes jest dostępna zewnętrznie przy użyciu publicznych adresów IP.

Azure Load Balancer jest dostępny w dwóch jednostkach SKU — *podstawowa* i *standardowa*. Domyślnie *standardowa* jednostka SKU jest używana podczas tworzenia klastra AKS. Użycie usługi równoważenia obciążenia ze *standardową* jednostką SKU zapewnia dodatkowe funkcje i funkcje, takie jak większy rozmiar puli zaplecza i strefy dostępności. Ważne jest, aby zrozumieć różnice między *standardowymi* i *podstawowymi* usługami równoważenia obciążenia przed wybraniem, który ma być używany. Po utworzeniu klastra AKS nie można zmienić jednostki SKU modułu równoważenia obciążenia dla tego klastra. Aby uzyskać więcej informacji na temat *podstawowych* i *standardowych* jednostek SKU, zobacz [porównanie jednostki SKU modułu równoważenia obciążenia platformy Azure][azure-lb-comparison].
Klaster AKS musi używać usługi równoważenia obciążenia ze standardową jednostką SKU do korzystania z wielu pul węzłów, ale ta funkcja nie jest obsługiwana w przypadku podstawowych modułów równoważenia obciążenia SKU, zobacz [Tworzenie i zarządzanie wieloma pulami węzłów klastra w AKS][use-multiple-node-pools].

W tym artykule założono podstawową wiedzę na temat koncepcji Kubernetes i Azure Load Balancer. Aby uzyskać więcej informacji, zobacz [Kubernetes podstawowe pojęcia dotyczące usługi Azure Kubernetes Service (AKS)][kubernetes-concepts] i [co to jest Azure Load Balancer?][azure-lb].

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.81 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz klaster AKS ze *standardową* jednostką SKU Azure Load Balancer. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Nazwa główna usługi klastra AKS musi również mieć uprawnienia do zarządzania zasobami sieci, jeśli używana jest istniejąca podsieć lub Grupa zasobów. Ogólnie rzecz biorąc Przypisz rolę *współautor sieci* do nazwy głównej usługi w odniesieniu do zasobów delegowanych. Zamiast nazwy głównej usługi można także użyć przypisanej tożsamości zarządzanej przez system do uprawnień. Aby uzyskać więcej informacji, zobacz [Korzystanie z tożsamości zarządzanych](use-managed-identity.md). Aby uzyskać więcej informacji o uprawnieniach, zobacz [delegowanie dostępu AKS do innych zasobów platformy Azure][aks-sp].

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Przeniesienie z podstawowej jednostki SKU Load Balancer do standardowej jednostki SKU

Jeśli istnieje klaster z podstawową jednostką SKU Load Balancer, istnieją istotne różnice w działaniu podczas migrowania do używania klastra ze standardową jednostką SKU Load Balancer.

Na przykład w przypadku wdrożeń Blue/Green do migracji klastrów jest powszechną metodą, w której `load-balancer-sku` Typ klastra można zdefiniować tylko w czasie tworzenia klastra. Jednak *podstawowe* usługi równoważenia obciążenia SKU używają podstawowych adresów IP *jednostki SKU* , które nie są zgodne ze *standardowymi* modułami równoważenia obciążenia jednostki SKU, ponieważ wymagają standardowych adresów IP *jednostki SKU* . W przypadku migrowania klastrów w celu uaktualnienia Load Balancer jednostek SKU będzie wymagane nowe adresy IP ze zgodną jednostką SKU adresu IP.

Aby uzyskać więcej informacji na temat migracji klastrów, zapoznaj się z [naszą dokumentacją dotyczącą zagadnień związanych z migracją](aks-migration.md) , aby wyświetlić listę ważnych tematów, które należy wziąć pod uwagę podczas migracji. Poniższe ograniczenia są również ważnymi różnicami w działaniu podczas korzystania ze standardowych modułów równoważenia obciążenia SKU w programie AKS.

### <a name="limitations"></a>Ograniczenia

Podczas tworzenia klastrów AKS i zarządzania nimi, które obsługują moduł równoważenia obciążenia ze *standardową* jednostką SKU, obowiązują następujące ograniczenia:

* Do zezwalania na ruch wychodzący z klastra AKS jest wymagany co najmniej jeden publiczny adres IP lub prefiks IP. Aby zachować zgodność z poprzednimi wersjami AKS, należy również podać publiczny adres IP lub adres IP. Dostępne są następujące opcje określania publicznych adresów IP lub prefiksów protokołu IPv4 za pomocą usługi równoważenia obciążenia w *warstwie Standardowa* :
    * Udostępnianie własnych publicznych adresów IP.
    * Podaj własne prefiksy publicznych adresów IP.
    * Określ liczbę do 100, aby umożliwić klastrowi AKS tworzenie wielu publicznych adresów IP *standardowej* jednostki SKU w tej samej grupie zasobów utworzonej jako klaster AKS, która zazwyczaj nazywa się *MC_* na początku. AKS przypisuje publiczny adres IP do modułu równoważenia obciążenia *standardowej* jednostki SKU. Domyślnie jeden publiczny adres IP zostanie automatycznie utworzony w tej samej grupie zasobów co klaster AKS, jeśli nie określono publicznego adresu IP, publicznego prefiksu adresu IP lub liczby adresów IP. Należy również zezwolić na publiczne adresy i uniknąć tworzenia Azure Policy, które zakazują tworzenie adresów IP.
* W przypadku korzystania z *standardowej* jednostki SKU dla modułu równoważenia obciążenia należy użyć Kubernetes w wersji *1,13 lub nowszej*.
* Definiowanie jednostki SKU modułu równoważenia obciążenia można wykonać tylko podczas tworzenia klastra AKS. Nie można zmienić jednostki SKU modułu równoważenia obciążenia po utworzeniu klastra AKS.
* W pojedynczym klastrze można używać tylko jednego typu jednostki SKU usługi równoważenia obciążenia (Basic lub standard).
* *Standard* Usługi równoważenia obciążenia jednostki SKU obsługują tylko adresy IP *standardowej* jednostki SKU.

## <a name="use-the-standard-sku-load-balancer"></a>Korzystanie ze *standardowego* modułu równoważenia obciążenia jednostki SKU

Podczas tworzenia klastra AKS domyślnie używany jest *Standardowy* moduł równoważenia obciążenia jednostki SKU w przypadku uruchamiania usług w tym klastrze. Na przykład [Przewodnik Szybki Start przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] wdraża przykładową aplikację korzystającą ze *standardowego* modułu równoważenia obciążenia jednostki SKU.

> [!IMPORTANT]
> Publiczne adresy IP można uniknąć przez dostosowanie trasy zdefiniowanej przez użytkownika (UDR). Określenie typu wychodzącego klastra AKS jako UDR może pominąć instalację protokołu IP i pulę zaplecza dla AKS utworzonego modułu równoważenia obciążenia platformy Azure. Zobacz [Ustawianie klastra `outboundType` na wartość "userDefinedRouting"](egress-outboundtype.md).

## <a name="configure-the-load-balancer-to-be-internal"></a>Konfigurowanie usługi równoważenia obciążenia jako wewnętrznej

Można również skonfigurować usługę równoważenia obciążenia jako wewnętrzną i nie ujawniać publicznego adresu IP. Aby skonfigurować moduł równoważenia obciążenia jako wewnętrzny, należy dodać `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` jako adnotację do usługi *modułu równoważenia* . Możesz zobaczyć przykład manifestu YAML oraz więcej szczegółów dotyczących wewnętrznego modułu równoważenia [obciążenia.][internal-lb-yaml]

## <a name="scale-the-number-of-managed-public-ips"></a>Skalowanie liczby zarządzanych publicznych adresów IP

W przypadku korzystania ze *standardowego* modułu równoważenia obciążenia z zarządzanymi wychodzącymi adresami IP, które są tworzone domyślnie, można skalować liczbę zarządzanych wychodzących adresów IP, używając parametru- *Managed-IP-Count* .

Aby zaktualizować istniejący klaster, uruchom następujące polecenie. Ten parametr można również ustawić podczas tworzenia klastra, aby miał wiele zarządzanych publicznych adresów IP.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

Powyższy przykład ustawia liczbę zarządzanych publicznych adresów IP z *2* dla klastra *MyAKSCluster* w ramach *zasobu*. 

Aby ustawić początkową liczbę zarządzanych publicznych adresów IP, które są używane podczas tworzenia klastra, można również użyć parametru *modułu równoważenia obciążenia-Managed-IP-Count* `--load-balancer-managed-outbound-ip-count` . Domyślna liczba zarządzanych publicznych adresów IP wychodzących to 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Podaj własne publiczne adresy IP lub prefiksy dla ruchu wychodzącego

W przypadku korzystania ze *standardowego* modułu równoważenia obciążenia jednostki SKU klaster AKS automatycznie tworzy publiczny adres IP w tej samej grupie zasobów utworzonej dla klastra AKS i przypisuje publiczny adres IP do modułu równoważenia obciążenia *standardowej* jednostki SKU. Alternatywnie można przypisać własny publiczny adres IP podczas tworzenia klastra lub zaktualizować właściwości modułu równoważenia obciążenia istniejącego klastra.

Przez umieszczenie wielu adresów IP lub prefiksów, można zdefiniować wiele usług zapasowych podczas definiowania adresu IP za pojedynczym obiektem modułu równoważenia obciążenia. Punkt końcowy danych wychodzących określonych węzłów będzie zależeć od tego, z jakim usługą są one skojarzone.

### <a name="pre-requisites-to-bring-your-own-ip-addresses-or-ip-prefixes"></a>Wymagania wstępne dotyczące przenoszenia własnych adresów IP lub prefiksów IP
1. Należy używać publicznych adresów IP jednostki SKU dla ruchu wychodzącego ze *standardową* jednostką *SKU modułu* równoważenia obciążenia. Można zweryfikować jednostki SKU publicznych adresów IP za pomocą polecenia [AZ Network Public-IP show][az-network-public-ip-show] :

   ```azurecli-interactive
   az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
   ```
 1. Publiczne adresy IP i prefiksy adresów IPv4 muszą znajdować się w tym samym regionie i części tej samej subskrypcji, co klaster AKS.
 1. Publiczne adresy IP i prefiksy protokołu IPSec nie mogą zostać adresami IP utworzonymi przez AKS jako zarządzany adres IP. Upewnij się, że adresy IP określone jako niestandardowe adresy IP zostały utworzone ręcznie, a nie jako usługa AKS.
 1. Nie można używać publicznych adresów IP i prefiksów adresów IPv4 przez inny zasób lub usługę.

 ### <a name="define-your-own-public-ip-or-prefixes-on-an-existing-cluster"></a>Definiowanie własnego publicznego adresu IP lub prefiksów w istniejącym klastrze

Użyj polecenia [AZ Network Public-IP show][az-network-public-ip-show] , aby wyświetlić listę identyfikatorów publicznych adresów IP.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Powyższe polecenie wyświetla identyfikator publicznego adresu IP *myPublicIP* w *grupie zasobów zasobu* .

Aby zaktualizować klaster przy użyciu publicznych adresów IP, użyj polecenia *AZ AKS Update* z parametrem *równoważenia obciążenia — wychodzące adresy IP* .

W poniższym przykładzie zastosowano parametr *równoważenia obciążenia — wychodzące adresy IP* z identyfikatorami z poprzedniego polecenia.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Możesz również użyć publicznych prefiksów IP dla ruchu wychodzącego w ramach usługi równoważenia obciążenia w *warstwie Standardowa* . Poniższy przykład używa polecenia [AZ Network Public-IP prefix show][az-network-public-ip-prefix-show] , aby wyświetlić listę identyfikatorów publicznych prefiksów IP:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Powyższe polecenie wyświetla identyfikator *myPublicIPPrefix* publicznego adresu IP w *grupie zasobów zasobu* .

Poniższy przykład używa parametru *równoważenia obciążenia-wychodzącego-IP prefiksów* z identyfikatorami z poprzedniego polecenia.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Definiowanie własnego publicznego adresu IP lub prefiksów podczas tworzenia klastra

Możesz chcieć wprowadzić własne adresy IP lub prefiksy IP dla ruchu wychodzącego w czasie tworzenia klastra, aby obsługiwać scenariusze takie jak listy dozwolonyche punkty końcowe. Dołącz te same parametry, które podano powyżej, do kroku tworzenia klastra, aby zdefiniować własne publiczne adresy IP i prefiksy adresów IPv4 na początku cyklu życia klastra.

Użyj polecenia *AZ AKS Create* z parametrem *Load-wychodzące-IP* , aby utworzyć nowy klaster z publicznymi adresami IP na początku.

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

Za pomocą polecenia *AZ AKS Create* z parametrem *równoważenia obciążenia wychodzącego IP prefiksów* Utwórz nowy klaster z prefiksami publicznych adresów IP na początku.

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

## <a name="configure-outbound-ports-and-idle-timeout"></a>Skonfiguruj porty wychodzące i limit czasu bezczynności

> [!WARNING]
> Poniższa sekcja jest przeznaczona dla zaawansowanych scenariuszy o większej skali sieci lub do rozwiązywania problemów z wyczerpaniem połączenia z konfiguracją domyślną. Aby zapewnić obsługę klastrów w dobrej kondycji, należy mieć dokładną inwentaryzację dostępnego przydziału *IdleTimeoutInMinutes* dla maszyn wirtualnych i adresów IP. *AllocatedOutboundPorts*
> 
> Zmiana wartości dla *AllocatedOutboundPorts* i *IdleTimeoutInMinutes* może znacząco zmienić zachowanie reguły ruchu wychodzącego dla modułu równoważenia obciążenia. Przed zaktualizowaniem tych wartości [Load Balancer Przejrzyj reguły ruchu][azure-lb-outbound-rules-overview]wychodzącego, reguły ruchu wychodzącego [modułu równoważenia obciążenia][azure-lb-outbound-rules]i [połączenia wychodzące na platformie Azure][azure-lb-outbound-connections] , aby w pełni zrozumieć wpływ zmian.

Przydzielono wychodzące porty i ich limity czasu bezczynności są [używane dla tego][azure-lb-outbound-connections]elementu. Domyślnie moduł równoważenia obciążenia *standardowej* jednostki SKU używa [automatycznego przypisywania dla liczby portów wychodzących opartych na rozmiarze puli zaplecza][azure-lb-outbound-preallocatedports] i limitu czasu bezczynności 30 minut dla każdego portu. Aby wyświetlić te wartości, użyj polecenie [AZ Network lb wychodzące-Rule list][az-network-lb-outbound-rule-list] , aby wyświetlić regułę ruchu wychodzącego dla modułu równoważenia obciążenia:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

W poprzednich poleceniach zostanie wystawiona Reguła ruchu wychodzącego dla modułu równoważenia obciążenia, na przykład:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Przykładowe dane wyjściowe pokazują wartość domyślną dla *AllocatedOutboundPorts* i *IdleTimeoutInMinutes*. Wartość 0 dla *AllocatedOutboundPorts* ustawia liczbę portów wychodzących przy użyciu automatycznego przypisywania dla liczby portów wychodzących na podstawie rozmiaru puli zaplecza. Na przykład jeśli klaster ma 50 lub mniej węzłów, przydzielono porty 1024 dla każdego węzła.

Rozważ zmianę ustawienia *allocatedOutboundPorts* lub *IdleTimeoutInMinutes* , jeśli zamierzasz obsłużyć wyczerpanie adresów w oparciu o powyższą konfigurację domyślną. Każdy dodatkowy adres IP pozwala na 64 000 dodatkowych portów do alokacji, jednak usługa Azure usługa Load Balancer w warstwie Standardowa nie zwiększa automatycznie portów na węzeł po dodaniu większej liczby adresów IP. Można zmienić te wartości poprzez ustawienie parametrów *równoważenia obciążenia-ruchu wychodzącego* i *równoważenia obciążenia — bezczynny limit czasu* . Przykład:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> Przed przystąpieniem do dostosowywania *allocatedOutboundPorts* należy [obliczyć limit przydziału][calculate-required-quota] , aby uniknąć problemów z łącznością lub skalowaniem. Wartość określona dla *allocatedOutboundPorts* musi być również wielokrotnością 8.

Podczas tworzenia klastra można również użyć parametrów *równoważenia obciążenia-ruchu wychodzącego* i *równoważenia obciążenia-limit czasu bezczynności* , ale należy również określić moduł równoważenia obciążenia-wychodzący- *IP-Count*, *równoważenia obciążenia-wychodzącego*i IP, jak również *prefiksy równoważenia obciążenia* wychodzącego.  Przykład:

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

W przypadku zmiany parametrów *równoważenia obciążenia — porty wychodzące* i *równoważenia obciążenia-limit czasu bezczynności* — ma wpływ na zachowanie profilu usługi równoważenia obciążenia, który ma wpływ na cały klaster.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>Wymagany limit przydziału allocatedOutboundPorts
Musisz mieć wystarczającą ilość wychodzącego adresu IP na podstawie liczby maszyn wirtualnych węzła i żądanych przyznanych portów wychodzących. Aby sprawdzić, czy masz wystarczającą ilość wychodzących adresów IP, należy użyć następującej formuły: 
 
*outboundIPs* \* 64 000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*.
 
Na przykład jeśli masz 3 *nodeVMs*i 50 000 *desiredAllocatedOutboundPorts*, musisz mieć co najmniej 3 *outboundIPs*. Zaleca się dołączenie dodatkowej pojemności wychodzącego adresu IP poza potrzebami. Ponadto należy uwzględnić automatyczne skalowanie klastra i możliwość uaktualniania puli węzłów przy obliczaniu wydajności wychodzącego adresu IP. W przypadku automatycznego skalowania klastra sprawdź bieżącą liczbę węzłów i maksymalną liczbę węzłów i użyj wyższej wartości. W przypadku uaktualniania należy uwzględnić dodatkową maszynę wirtualną węzłową dla każdej puli węzłów, która umożliwia uaktualnianie.
 
Podczas ustawiania *IdleTimeoutInMinutes* na inną wartość niż domyślnie 30 minut należy wziąć pod uwagę, jak długo obciążenia będą wymagały połączenia wychodzącego. Należy również wziąć pod uwagę domyślną wartość limitu czasu dla usługi równoważenia obciążenia w *warstwie Standardowa* używanej poza AKS wynosi 4 minuty. Wartość *IdleTimeoutInMinutes* , która dokładniej odzwierciedla Twoje określone obciążenie AKS może pomóc w zmniejszeniu wyczerpania spalin spowodowanych przez nawiązanie połączeń, które nie są już używane.

## <a name="restrict-access-to-specific-ip-ranges"></a>Ograniczanie dostępu do określonych zakresów adresów IP

Grupa zabezpieczeń sieci (sieciowej grupy zabezpieczeń) skojarzona z siecią wirtualną dla usługi równoważenia obciążenia domyślnie ma regułę zezwalającą na cały ruch przychodzący zewnętrzny. Tę regułę można zaktualizować tak, aby zezwalać tylko na określone zakresy adresów IP dla ruchu przychodzącego. Następujący manifest używa *loadBalancerSourceRanges* , aby określić nowy zakres adresów IP dla przychodzącego ruchu zewnętrznego:

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

Powyższy przykład aktualizuje regułę tak, aby zezwalała na ruch zewnętrzny przychodzący z zakresu *MY_EXTERNAL_IP_RANGE* . Więcej informacji na temat korzystania z tej metody w celu ograniczenia dostępu do usługi równoważenia obciążenia jest dostępnych w [dokumentacji Kubernetes][kubernetes-cloud-provider-firewall].

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach Kubernetes Services w [dokumentacji usług Kubernetes Services][kubernetes-services].

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
[azure-lb-comparison]: ../load-balancer/skus.md
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
[use-multiple-node-pools]: use-multiple-node-pools.md
