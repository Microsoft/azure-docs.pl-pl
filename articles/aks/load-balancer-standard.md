---
title: Używanie publicznego Load Balancer
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak za pomocą publicznego równoważenia obciążenia z usługą SKU w chmurze w standardowych usługach uwidocznić Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 3f2219f5052aee0c0a9cd43aa87df8789adbcae2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783093"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>Używanie publicznego usługa Load Balancer w warstwie Standardowa w Azure Kubernetes Service (AKS)

Model Azure Load Balancer na L4 modelu open systems interconnection (OSI), który obsługuje scenariusze ruchu przychodzącego i wychodzącego. Dystrybuuje ona przepływy przychodzące przychodzące do frontonia usługi równoważenia obciążenia do wystąpień puli zaplecza.

Publiczna **Load Balancer** zintegrowana z usługą AKS służy do dwóch celów:

1. Zapewnianie połączeń wychodzących z węzłami klastra w sieci wirtualnej usługi AKS. Osiąga ten cel, tłumacząc prywatny adres IP węzłów na publiczny adres IP, który jest częścią *puli ruchu wychodzącego.*
2. Aby zapewnić dostęp do aplikacji za pośrednictwem usług Kubernetes typu `LoadBalancer` . Za jego pomocą można łatwo skalować aplikacje i tworzyć usługi o wysokiej jakości.

Używany **jest wewnętrzny (lub prywatny)** równoważenie obciążenia, w którym jako frontend są dozwolone tylko prywatne ip. Wewnętrzne usługi równoważenia obciążenia służą do równoważenia obciążenia ruchem w sieci wirtualnej. W scenariuszu hybrydowym dostęp do frontonia usługi równoważenia obciążenia można również uzyskać z sieci lokalnej.

Ten dokument zawiera informacje o integracji z publicznym usługą równoważenia obciążenia. Aby uzyskać informacje Load Balancer wewnętrznej integracji z usługą , zobacz dokumentację [wewnętrznego równoważenia obciążenia WKS.](internal-lb.md)

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Azure Load Balancer jest dostępna w dwóch jednostkach SKU — *Podstawowa* i *Standardowa.* Domyślnie podczas tworzenia *klastra* usługi AKS jest używana standardowa wartość SKU. Użyj *standardowej* wersji SKU, aby mieć dostęp do dodatkowych funkcji, takich jak większa pula [**zaplecza,**](use-multiple-node-pools.md)wiele pul węzłów [**i Strefy dostępności.**](availability-zones.md) Jest to zalecana Load Balancer SKU dla AKS.

Aby uzyskać więcej informacji na *temat* podstawowych i *standardowych* jednostki SKU, zobacz Porównanie jednostki SKU usługi [Azure Load Balancer.][azure-lb-comparison]

W tym artykule przyjęto założenie,  że masz klaster usługi AKS ze standardową Azure Load Balancer SKU, i opisano sposób używania i konfigurowania niektórych funkcji i możliwości usługi równoważenia obciążenia. Jeśli potrzebujesz klastra usługi AKS, zobacz przewodnik Szybki start usługi AKS przy użyciu interfejsu wiersza polecenia platformy [Azure][aks-quickstart-cli] lub [interfejsu Azure Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Jeśli wolisz nie korzystać z usługi Azure Load Balancer w celu zapewnienia połączenia wychodzącego, a zamiast tego masz własną bramę, zaporę lub serwer proxy, możesz pominąć tworzenie puli ruchu wychodzącego usługi równoważenia obciążenia i odpowiedniego adresu IP frontonu, używając typu ruchu wychodzącego jako [**userDefinedRouting (UDR).**](egress-outboundtype.md) Typ Ruchu wychodzącego definiuje metodę ruchu wychodzącego dla klastra i domyślnie typ: load balancer.

## <a name="use-the-public-standard-load-balancer"></a>Korzystanie z publicznego standardowego równoważenia obciążenia

Po utworzeniu klastra usługi AKS z typem ruchu wychodzącego: Load Balancer (ustawienie domyślne) klaster jest gotowy do użycia usługi równoważenia obciążenia, aby również uwidocznić usługi.

W tym celu możesz utworzyć usługę publiczną typu , `LoadBalancer` jak pokazano w poniższym przykładzie. Rozpocznij od utworzenia manifestu usługi o nazwie `public-svc.yaml` :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: public-svc
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: public-app
```

Wdróż manifest usługi publicznej przy użyciu [narzędzia kubectl apply][kubectl-apply] i określ nazwę manifestu YAML:

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

Adres Azure Load Balancer zostanie skonfigurowany przy użyciu nowego publicznego adresu IP, który będzie obsługiował tę nową usługę. Ponieważ serwer Azure Load Balancer wiele adresów IP frontony, każda nowa wdrożona usługa otrzyma nowy dedykowany adres IP frontony, do którego będzie można uzyskać unikatowy dostęp.

Możesz potwierdzić, że usługa została utworzona, a usługa równoważenia obciążenia jest skonfigurowana, uruchamiając na przykład:

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

Podczas wyświetlania szczegółów usługi publiczny adres IP utworzony dla tej usługi w usłudze równoważenia obciążenia jest wyświetlany w *kolumnie EXTERNAL-IP.* Zmiana adresu IP z na rzeczywisty publiczny adres IP, jak pokazano w powyższym przykładzie, może potrwać *\<pending\>* minutę lub dwie.

## <a name="configure-the-public-standard-load-balancer"></a>Konfigurowanie publicznego standardowego równoważenia obciążenia

W przypadku korzystania z publicznego równoważenia obciążenia w standardowych SKU dostępny jest zestaw opcji, które można dostosować podczas tworzenia lub przez zaktualizowanie klastra. Te opcje umożliwiają dostosowanie konfiguracji Load Balancer do potrzeb obciążeń i należy je odpowiednio przejrzeć. Za pomocą standardowego równoważenia obciążenia można:

* Ustawianie lub skalowanie liczby zarządzanych wychodzących ip
* Bring your own custom [Outbound IPPs or Outbound IP Prefix](#provide-your-own-outbound-public-ips-or-prefixes) (Przynieź własne niestandardowe adresy IP ruchu wychodzącego lub prefiks adresu IP ruchu wychodzącego)
* Dostosowywanie liczby przydzielonych portów wychodzących do każdego węzła klastra
* Konfigurowanie ustawienia limitu czasu dla bezczynnych połączeń

> [!IMPORTANT]
> W danym momencie można użyć tylko jednej opcji wychodzącego adresu IP (zarządzanych adresów IP, bring your own IP lub prefiksu IP).

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>Skalowanie liczby zarządzanych publicznych ip ruchu wychodzącego

Azure Load Balancer zapewnia łączność wychodzącą z sieci wirtualnej oprócz ruchu przychodzącego. Reguły ruchu wychodzącego sprawiają, że konfigurowanie translatora adresów usługa Load Balancer w warstwie Standardowa ruchu wychodzącego jest proste.

Podobnie jak Load Balancer reguły ruchu wychodzącego, reguły ruchu wychodzącego są zgodne z taką samą składnią jak równoważenie obciążenia i reguły NAT dla ruchu przychodzącego:

***ip frontendu i parametry + pula zaplecza***

Reguła ruchu wychodzącego konfiguruje translę na frontonie dla wszystkich maszyn wirtualnych zidentyfikowanych przez pulę zaplecza. Parametry i zapewniają dodatkową ładną kontrolę nad wychodzącym algorytmem NAT.

Reguła ruchu wychodzącego może być używana tylko z jednym publicznym adresem IP, ale reguły ruchu wychodzącego ułatwiają konfigurowanie skalowania wychodzącego adresu NAT. Do planowania scenariuszy na dużą skalę można użyć wielu adresów IP, a reguły ruchu wychodzącego mogą pomóc w zminimalizowaniu wzorców podatnych na wyczerpanie SNAT. Każdy dodatkowy adres IP zapewnia frontonie 64k portów efemeralnych, które Load Balancer do użycia jako porty SNAT. 

W przypadku  korzystania z usługi równoważenia obciążenia w standardowych sku z zarządzanymi wychodzącymi publicznymi ip, które są tworzone domyślnie, można skalować liczbę zarządzanych wychodzących publicznych ip przy użyciu **`load-balancer-managed-ip-count`** parametru .

Aby zaktualizować istniejący klaster, uruchom następujące polecenie. Ten parametr można również ustawić w czasie tworzenia klastra, aby mieć wiele zarządzanych publicznych ip ruchu wychodzącego.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

W powyższym przykładzie liczba zarządzanych publicznych ip ruchu wychodzącego jest ustawiana na *2* dla klastra *myAKSCluster* w *grupie myResourceGroup.* 

Możesz również użyć parametru , aby ustawić początkową liczbę zarządzanych publicznych ip ruchu wychodzącego podczas tworzenia klastra, dołączając parametr i ustawiając dla niego **`load-balancer-managed-ip-count`** **`--load-balancer-managed-outbound-ip-count`** żądaną wartość. Domyślna liczba zarządzanych publicznych ip ruchu wychodzącego wynosi 1.

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>Podaj własne publiczne lub prefiksy dla ruchu wychodzącego

W przypadku korzystania z usługi równoważenia obciążenia *w* standardowych SKU klaster usługi AKS automatycznie tworzy publiczny adres IP w grupie zasobów infrastruktury zarządzanej przez usługę AKS i przypisuje go do puli ruchu wychodzącego usługi równoważenia obciążenia.

Publiczny adres IP utworzony przez usługę AKS jest traktowany jako zasób zarządzany przez usługę AKS. Oznacza to, że cykl życia tego publicznego adresu IP jest przeznaczony do zarządzania przez usługę AKS i nie wymaga żadnej akcji użytkownika bezpośrednio w zasobie publicznego adresu IP. Alternatywnie możesz przypisać własny niestandardowy publiczny adres IP lub prefiks publicznego adresu IP podczas tworzenia klastra. Niestandardowe ip można również zaktualizować we właściwościach istniejącego klastra równoważenia obciążenia.

Wymagania dotyczące używania własnego publicznego adresu IP lub prefiksu:

- Niestandardowe publiczne adresy IP muszą być tworzone i posiadane przez użytkownika. Zarządzanych publicznych adresów IP utworzonych przez usługę AKS nie można używać ponownie jako własnego niestandardowego adresu IP, ponieważ może to powodować konflikty zarządzania.
- Musisz upewnić się, że tożsamość klastra usługi AKS (jednostki usługi lub tożsamość zarządzana) ma uprawnienia dostępu do wychodzącego adresu IP. Zgodnie z [listą wymaganych uprawnień publicznego adresu IP](kubernetes-service-principal.md#networking).
- Upewnij się, że [spełniasz](../virtual-network/public-ip-address-prefix.md#constraints) wymagania wstępne i ograniczenia niezbędne do skonfigurowania wychodzących adresów IP lub prefiksów IP ruchu wychodzącego.

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>Aktualizowanie klastra przy użyciu własnego publicznego adresu IP ruchu wychodzącego

Użyj polecenia [az network public-ip show,][az-network-public-ip-show] aby wyświetlić listę identyfikatorów publicznych adresów IP.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Powyższe polecenie pokazuje identyfikator publicznego adresu IP *myPublicIP* w grupie zasobów *myResourceGroup.*

Użyj polecenia `az aks update` z **`load-balancer-outbound-ips`** parametrem , aby zaktualizować klaster przy użyciu publicznych adresów IP.

W poniższym przykładzie użyto `load-balancer-outbound-ips` parametru z identyfikatorami z poprzedniego polecenia.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>Zaktualizuj klaster własnym prefiksem publicznego adresu IP ruchu wychodzącego

Można również użyć prefiksów publicznych adresów IP dla danych wychodzących z *usługą* równoważenia obciążenia w standardowych SKU. W poniższym przykładzie użyto [polecenia az network public-ip prefix show,][az-network-public-ip-prefix-show] aby wyświetlić listę identyfikatorów prefiksów publicznego adresu IP:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Powyższe polecenie wyświetla identyfikator prefiksu publicznego adresu IP *myPublicIPPrefix* w grupie zasobów *myResourceGroup.*

W poniższym przykładzie *użyto parametru load-balancer-outbound-ip-prefixes* z identyfikatorami z poprzedniego polecenia.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>Tworzenie klastra z własnym publicznym adresem IP lub prefiksami

Możesz chcieć wprowadzić własne adresy IP lub prefiksy IP dla danych wychodzących w czasie tworzenia klastra, aby obsługiwać scenariusze, takie jak dodawanie punktów końcowych wychodzącego do listy zezwalań. Dołącz te same parametry, które przedstawiono powyżej, do kroku tworzenia klastra, aby zdefiniować własne publiczne adresy IP i prefiksy adresów IP na początku cyklu życia klastra.

Użyj polecenia *az aks create* z parametrem *load-balancer-outbound-ips,* aby utworzyć nowy klaster z publicznymi adresami IP na początku.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Użyj *polecenia az aks create* z parametrem *load-balancer-outbound-ip-prefixes,* aby utworzyć nowy klaster z prefiksami publicznego adresu IP na początku.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>Konfigurowanie przydzielonych portów wychodzących

> [!IMPORTANT]
> Jeśli masz aplikacje w klastrze, które powinny ustanowić dużą liczbę połączeń z małym zestawem miejsc docelowych, np. wiele wystąpień frontonu łączących się z bazą danych SQL, scenariusz jest bardzo podatny na wyczerpanie portów SNAT (zabraknie portów do nawiązania połączenia). W tych scenariuszach zdecydowanie zaleca się zwiększenie przydzielonych portów wychodzących i wychodzących ip frontonie w równoważeniu obciążenia. Wzrost należy wziąć pod uwagę, że jeden (1) dodatkowy adres IP dodaje 64k dodatkowych portów do dystrybucji we wszystkich węzłach klastra.


Jeśli nie określono inaczej, podczas konfigurowania zostanie określona wartość domyślna przydzielonego usługa Load Balancer w warstwie Standardowa wychodzącego. Ta wartość ma **wartość null** w interfejsie API usługi AKS lub **0** w interfejsie API SLB, jak pokazano w poniższym poleceniu:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Poprzednie polecenia będą zawierały regułę ruchu wychodzącego dla twojego równoważenia obciążenia, na przykład:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Te dane wyjściowe nie oznaczają, że masz 0 portów, ale zamiast tego używasz automatycznego przypisywania portów wychodzących na podstawie rozmiaru puli [zaplecza,][azure-lb-outbound-preallocatedports]więc jeśli na przykład klaster ma 50 lub mniej węzłów, przydzielane są 1024 porty dla każdego węzła, ponieważ zwiększasz liczbę węzłów z tego poziomu, stopniowo będziesz mieć dostęp do mniejszej liczby portów na węzeł.


Aby zdefiniować lub zwiększyć liczbę przydzielonych portów wychodzących, możesz wykonać następujące czynności:


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

Ten przykład daje 4000 przydzielonych portów wychodzących dla każdego węzła w klastrze, a 7 adresów IP będzie mieć 4000 portów na węzeł ** 100 węzłów = 400 000* wszystkich portów < = 448 tys. wszystkich portów = 7 adresów IP * 64 tys. portów na adres IP. Pozwoli to bezpiecznie skalować do 100 węzłów i mieć domyślną operację uaktualniania. Kluczowe znaczenie ma przydzielenie wystarczającej liczby portów dla dodatkowych węzłów wymaganych do uaktualnienia i innych operacji. Domyślnie do uaktualnienia WKS jest wymagany jeden węzeł buforu. W tym przykładzie wymaga to 4000 wolnych portów w danym momencie. Jeśli używasz [wartości maxSurge,](upgrade-cluster.md#customize-node-surge-upgrade)pomnóż porty wychodzące na węzeł przez wartość maxSurge.

Aby bezpiecznie przejść powyżej 100 węzłów, należy dodać więcej ip.


> [!IMPORTANT]
> Należy obliczyć [wymagany limit przydziału i sprawdzić][requirements] wymagania przed dostosowaniem przydzielonych portów wychodzących, aby uniknąć problemów z łącznością lub skalowaniem. 

Parametrów można również użyć podczas tworzenia klastra, ale należy również określić **`load-balancer-outbound-ports`** **`load-balancer-managed-outbound-ip-count`** wartość , lub **`load-balancer-outbound-ips`** **`load-balancer-outbound-ip-prefixes`** .  Na przykład:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>Konfigurowanie limitu czasu bezczynności usługi równoważenia obciążenia

Gdy zasoby portów SNAT zostaną wyczerpane, przepływy wychodzące będą się nie powieść, dopóki istniejące przepływy nie zwolnią portów SNAT. Load Balancer odzyskuje porty SNAT po zamknięciu przepływu, a skonfigurowany przez usługę AKS równoważenie obciążenia używa 30-minutowego limitu czasu bezczynności do odzyskiwania portów SNAT z bezczynnych przepływów.
Można również użyć transportu (na przykład ) lub odświeżyć bezczynny przepływ i w razie potrzeby zresetować ten limit czasu **`TCP keepalives`** **`application-layer keepalives`** bezczynności. Ten limit czasu można skonfigurować zgodnie z poniższym przykładem: 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

Jeśli oczekujesz, że będziesz mieć wiele krótkich połączeń i nie będziesz mieć długotrwałych połączeń, które mogą długo być w stanie bezczynności, na przykład wykorzystanie lub rozważ zastosowanie niskiej wartości limitu czasu, takiej jak `kubectl proxy` `kubectl port-forward` 4 minuty. Ponadto w przypadku korzystania z keepalives protokołu TCP wystarczy włączyć je po jednej stronie połączenia. Na przykład wystarczy włączyć je po stronie serwera tylko w celu zresetowania czasomierza bezczynności przepływu i nie jest konieczne, aby obie strony uruchamiały keepalives protokołu TCP. Istnieją podobne pojęcia dotyczące warstwy aplikacji, w tym konfiguracje klient-serwer bazy danych. Sprawdź po stronie serwera, jakie opcje istnieją dla poszczególnych aplikacji.

> [!IMPORTANT]
> AKS domyślnie włącza resetowanie protokołu TCP w stanie bezczynności i zaleca zachowanie tej konfiguracji i korzystanie z niej w celu bardziej przewidywalnego zachowania aplikacji w scenariuszach.
> Protokół TCP RST jest wysyłany tylko podczas połączenia TCP w stanie ESTABLISHED. Więcej informacji na ten temat znajduje się [tutaj](../load-balancer/load-balancer-tcp-reset.md).

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>Wymagania dotyczące dostosowywania przydzielonych portów wychodzących i limitu czasu bezczynności

- Wartość określana dla *allocatedOutboundPorts* również musi być wielokrotnością 8.
- Musisz mieć wystarczającą pojemność wychodzącego adresu IP na podstawie liczby maszyn wirtualnych węzła i wymaganych przydzielonych portów wychodzących. Aby sprawdzić, czy masz wystarczającą pojemność wychodzącego adresu IP, użyj następującej formuły: 
 
*outboundIPs* \* 64 000 \> *węzłówVMs* \* *desiredAllocatedOutboundPorts*.
 
Jeśli na przykład masz 3 *nodeVMs* i 50 000 *desiredAllocatedOutboundPorts,* musisz mieć co najmniej 3 wychodzące porty *IP.* Zaleca się uwzględnienie dodatkowej pojemności wychodzącego adresu IP wykraczających poza to, czego potrzebujesz. Ponadto podczas obliczania pojemności wychodzącego adresu IP należy uwzględnić funkcję automatycznego skalowania klastra i możliwość uaktualnienia puli węzłów. W przypadku funkcji automatycznego skalowania klastra przejrzyj bieżącą liczbę węzłów i maksymalną liczbę węzłów i użyj wyższej wartości. W przypadku uaktualniania należy uwzględnić dodatkową maszynę wirtualną węzła dla każdej puli węzłów, która umożliwia uaktualnienie.

- W przypadku ustawienia wartości *IdleTimeoutInMinutes* na wartość inną niż domyślna 30 minut należy wziąć pod uwagę, jak długo obciążenia będą potrzebować połączenia wychodzącego. Należy również wziąć pod uwagę domyślną wartość limitu czasu dla *standardowego* równoważenia obciążenia SKU używanego poza aksem wynosi 4 minuty. Wartość *IdleTimeoutInMinutes,* która dokładniej odzwierciedla określone obciążenie AKS, może pomóc zmniejszyć wyczerpanie SNAT spowodowane przez wiązanie połączeń, które nie są już używane.

> [!WARNING]
> Zmiana wartości *parametrów AllocatedOutboundPorts* i *IdleTimeoutInMinutes* może znacząco zmienić zachowanie reguły ruchu wychodzącego dla usługi równoważenia obciążenia i nie powinna być wykonywana w sposób lekki, bez zrozumienia kompromisów i wzorców połączeń aplikacji, zapoznaj się z sekcją Rozwiązywanie problemów z [SNAT][troubleshoot-snat] poniżej i zapoznaj się z regułami ruchu wychodzącego usługi Load Balancer i połączeniami wychodzącymi na platformie [Azure][azure-lb-outbound-connections] przed zaktualizowaniem tych wartości, [aby][azure-lb-outbound-rules-overview] w pełni zrozumieć wpływ zmian.

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>Ograniczanie ruchu przychodzącego do określonych zakresów adresów IP

W poniższym *manifeście użyto loadBalancerSourceRanges,* aby określić nowy zakres adresów IP dla przychodzącego ruchu zewnętrznego:

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

> [!NOTE]
> Ruch przychodzący, zewnętrzny przepływ ruchu z usługi Load Balancer do sieci wirtualnej klastra usługi AKS. Sieć wirtualna ma sieciową grupę zabezpieczeń, która zezwala na cały ruch przychodzący z usługi równoważenia obciążenia. Ta sieciowa żadna z sieciowych gieł [używa tagu usługi][service-tags] typu *LoadBalancer,* aby zezwolić na ruch z usługi równoważenia obciążenia.

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>Obsługa adresu IP klienta dla połączeń przychodzących

Domyślnie usługa typu na platformie `LoadBalancer` [Kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) i w usłudze AKS nie utrwali adresu IP klienta w połączeniu z zasobnikiem. Źródłowy adres IP pakietu dostarczonego do zasobnika będzie prywatnym adresem IP węzła. Aby zachować adres IP klienta, należy ustawić wartość `service.spec.externalTrafficPolicy` w `local` definicji usługi. Poniższy manifest przedstawia przykład:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="additional-customizations-via-kubernetes-annotations"></a>Dodatkowe dostosowania za pomocą adnotacji Kubernetes

Poniżej znajduje się lista adnotacji obsługiwanych dla usług Kubernetes z typem . Te adnotacje dotyczą `LoadBalancer` tylko **przepływów PRZYCHODZĄCYCH:**

| Adnotacja | Wartość | Opis
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` lub `false`                     | Określ, czy równoważenie obciążenia ma być wewnętrzne. Domyślnie jest ona publiczna, jeśli nie jest ustawiona.
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | Nazwa podsieci                    | Określ podsieć, z którą ma być powiązany wewnętrzny równoważenie obciążenia. Domyślnie jest to podsieć skonfigurowana w pliku konfiguracji chmury, jeśli nie została ustawiona.
| `service.beta.kubernetes.io/azure-dns-label-name`                 | Nazwa etykiety DNS w publicznych adresach IP   | Określ nazwę etykiety DNS dla **usługi publicznej.** Jeśli jest ustawiona na pusty ciąg, wpis DNS w publicznym adresie IP nie będzie używany.
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` lub `false`                     | Określ, że usługa powinna być udostępniana przy użyciu reguły zabezpieczeń platformy Azure, która może być udostępniana innej usłudze, i określ specyficzność handlową reguł w celu zwiększenia liczby usług, które mogą być udostępniane. Ta adnotacja opiera się na funkcji [rozszerzonych reguł zabezpieczeń platformy](../virtual-network/network-security-groups-overview.md#augmented-security-rules) Azure sieciowych grup zabezpieczeń. 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | Nazwa grupy zasobów            | Określ grupę zasobów publicznych ip usługi równoważenia obciążenia, które nie są w tej samej grupie zasobów co infrastruktura klastra (grupa zasobów węzła).
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | Lista dozwolonych tagów usługi          | Określ listę dozwolonych [tagów usługi rozdzielonych][service-tags] przecinkami.
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | Limity czasu bezczynności protokołu TCP w minutach          | Określ czas(w minutach) przeoczania limitu czasu bezczynności połączenia TCP w równoważeniu obciążenia. Wartość domyślna i minimalna to 4. Wartość maksymalna to 30. Musi być liczbą całkowitą.
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | Wyłącz `enableTcpReset` dla usługi SLB


## <a name="troubleshooting-snat"></a>Rozwiązywanie problemów z SNAT

Jeśli wiesz, że uruchamiasz wiele wychodzących połączeń TCP lub UDP z tym samym docelowym adresem IP i portem, i obserwujesz niepowodzenie połączeń wychodzących lub zaleca się, że wyczerpiesz porty SNAT (wstępnie nieprzydzielone porty efemericzne używane przez pat), masz kilka ogólnych opcji ograniczania ryzyka. Przejrzyj te opcje i zdecyduj, co jest dostępne i najlepsze dla Twojego scenariusza. Możliwe, że co najmniej jeden z nich może pomóc w zarządzaniu tym scenariuszem. Aby uzyskać szczegółowe informacje, zapoznaj się z [przewodnikiem rozwiązywania problemów z połączeniami wychodzącymi.](../load-balancer/troubleshoot-outbound-connection.md)

Często główną przyczyną wyczerpania SNAT jest wzorzec sposobu, w jaki czasomierze wychodzące są nawiązane, zarządzane lub konfigurowalne, zmieniane z ich wartości domyślnych. Uważnie przejrzyj tę sekcję.

### <a name="steps"></a>Kroki
1. Sprawdź, czy połączenia pozostają bezczynne przez długi czas, i polegaj na domyślnym limitie czasu bezczynności podczas zwalniania tego portu. W takim przypadku może być konieczne skrócenie domyślnego limitu czasu (30 minut) dla scenariusza.
2. Sprawdź, w jaki sposób aplikacja tworzy łączność wychodzącą (na przykład przegląd kodu lub przechwytywanie pakietów).
3. Ustal, czy to działanie jest oczekiwane, czy też aplikacja działa nieprawidłowo. Użyj [metryk i](../load-balancer/load-balancer-standard-diagnostics.md) [dzienników w](../load-balancer/load-balancer-monitor-log.md) Azure Monitor, aby zawęzić swoje wyniki. Użyj kategorii "Niepowodzenie" na przykład dla metryki połączeń SNAT.
4. Oceń, czy [są przestrzegane odpowiednie](#design-patterns) wzorce.
5. Sprawdź, czy należy ograniczyć wyczerpanie portów SNAT przy użyciu dodatkowych wychodzących adresów [IP i dodatkowych przydzielonych portów wychodzących.](#configure-the-allocated-outbound-ports)

### <a name="design-patterns"></a>Wzorce projektowe
Zawsze używaj ponownego użycia połączenia i puli połączeń zawsze, gdy jest to możliwe. Wzorce te będą unikać problemów z wyczerpaniem zasobów i powodować przewidywalne zachowanie. Typy pierwotne dla tych wzorców można znaleźć w wielu bibliotekach i platformach programistów.

- Niepodzielne żądania (jedno żądanie na połączenie) zazwyczaj nie są dobrym wyborem w projekcie. Takie antyw wzorzec ogranicza skalę, zmniejsza wydajność i zmniejsza niezawodność. Zamiast tego należy ponownie użyć połączeń HTTP/S w celu zmniejszenia liczby połączeń i skojarzonych portów SNAT. Skala aplikacji zwiększy się, a wydajność poprawi się ze względu na zmniejszenie liczby uściślić, narzutów i kosztów operacji kryptograficznych podczas korzystania z usługi TLS.
- Jeśli używasz poza klastrem/niestandardowym systemem DNS lub niestandardowych serwerów nadrzędnych na serwerach coreDNS, pamiętaj, że system DNS może wprowadzać wiele poszczególnych przepływów na woluminie, gdy klient nie jest buforowaniem wyniku programu rozpoznawania nazw DNS. Pamiętaj, aby najpierw dostosować nazwę coreDNS zamiast niestandardowych serwerów DNS, i zdefiniuj dobrą wartość buforowania.
- Przepływy UDP (na przykład wyszukiwania DNS) przydzielają porty SNAT na czas bezczynności. Im dłuższy limit czasu bezczynności, tym większe jest ciśnienie na portach SNAT. Użyj krótkiego limitu czasu bezczynności (na przykład 4 minuty).
Użyj pul połączeń, aby ukształtować wolumin połączenia.
- Nigdy nie porzucaj przepływu TCP w trybie dyskretnym i polegaj na czasomierzach TCP w celu oczyszczenia przepływu. Jeśli nie pozwolisz na jawne zamknięcie połączenia za pomocą protokołu TCP, stan pozostanie przydzielony w pośrednich systemach i punktach końcowych i sprawia, że porty SNAT są niedostępne dla innych połączeń. Ten wzorzec może wyzwalać błędy aplikacji i wyczerpanie SNAT.
- Nie zmieniaj wartości czasomierza powiązanego z zamykaniem tcp na poziomie systemu operacyjnego bez specjalistycznej wiedzy o wpływie. Gdy stos TCP zostanie odzyskany, może to mieć negatywny wpływ na wydajność aplikacji, gdy punkty końcowe połączenia mają niezgodne oczekiwania. Zmiana czasomierzy jest zwykle oznaką podstawowego problemu projektowego. Zapoznaj się z poniższymi zaleceniami.


Powyższy przykład aktualizuje regułę tak, aby zezwalała tylko na przychodzący ruch zewnętrzny *z MY_EXTERNAL_IP_RANGE* zakresu. Jeśli *zastąpisz MY_EXTERNAL_IP_RANGE* adresem IP podsieci wewnętrznej, ruch będzie ograniczony tylko do wewnętrznych adresów IP klastra. Nie pozwoli to klientom spoza klastra Kubernetes na dostęp do usługi równoważenia obciążenia.

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Przechodzenie z podstawowego równoważenia obciążenia do standardowej wersji SKU

Jeśli masz istniejący klaster z podstawową Load Balancer SKU, istnieją ważne różnice w zachowaniu, o których należy pamiętać podczas migracji w celu używania klastra ze standardową Load Balancer.

Na przykład częstą praktyką jest tworzenie niebieskich/zielonych wdrożeń w celu migrowania klastrów, ponieważ typ klastra można zdefiniować tylko w `load-balancer-sku` czasie tworzenia klastra. Jednak podstawowe usługi równoważenia obciążenia *SKU* używają podstawowych adresów IP *SKU,* które nie są zgodne z usługami równoważenia obciążenia w standardowych *SKU,* ponieważ wymagają standardowych adresów IP *SKU.* Podczas migrowania klastrów w celu uaktualnienia Load Balancer SKU wymagany będzie nowy adres IP ze zgodną jednostką SKU adresu IP.

Więcej zagadnień dotyczących migrowania klastrów można znaleźć w naszej dokumentacji dotyczącej zagadnień związanych z migracją, aby wyświetlić listę ważnych tematów, które należy wziąć pod uwagę podczas migracji. [](aks-migration.md) Poniższe ograniczenia są również istotnymi różnicami w zachowaniu, które należy zwrócić uwagę podczas korzystania z usług Load Balancer w standardowych SKU w u usługach AKS.

## <a name="limitations"></a>Ograniczenia

Następujące ograniczenia mają zastosowanie podczas tworzenia klastrów usługi AKS, które obsługują równoważenie obciążenia za pomocą standardowej *wersji* SKU, i zarządzania nimi:

* Aby zezwalać na ruch wychodzący z klastra usługi AKS, wymagany jest co najmniej jeden publiczny adres IP lub prefiks IP. Publiczny adres IP lub prefiks IP jest również wymagany do utrzymania łączności między płaszczyzną sterowania i węzłami agenta oraz do zachowania zgodności z poprzednimi wersjami usługi AKS. Dostępne są następujące opcje określania publicznych adresów IP lub prefiksów IP przy użyciu *usługi* równoważenia obciążenia w standardowych sku:
    * Podaj własne publiczne adres IP.
    * Podaj własne prefiksy publicznych adresów IP.
    * Określ liczbę do 100, aby umożliwić klastrowi usługi AKS utworzenie wielu publicznych adresów IP standardowej wersji *SKU* w tej samej grupie zasobów utworzonej jako klaster usługi AKS, która na początku ma zazwyczaj nazwę *MC_.* AKS przypisuje publiczny adres IP do usługi load balancer w *standardowych* sku. Domyślnie jeden publiczny adres IP zostanie automatycznie utworzony w tej samej grupie zasobów co klaster usługi AKS, jeśli nie określono publicznego adresu IP, prefiksu publicznego adresu IP ani liczby adresów IP. Należy również zezwolić na adresy publiczne i uniknąć tworzenia jakichkolwiek Azure Policy, które Azure Policy tworzenia adresów IP.
* Publicznego adresu IP utworzonego przez usługę AKS nie można użyć ponownie jako niestandardowego publicznego adresu IP. Wszystkie niestandardowe adresy IP muszą być tworzone i zarządzane przez użytkownika.
* Definiowanie sku usługi równoważenia obciążenia można wykonać tylko podczas tworzenia klastra usługi AKS. Nie można zmienić sku usługi równoważenia obciążenia po utworzeniu klastra usługi AKS.
* W jednym klastrze można używać tylko jednego typu wersji SKU usługi równoważenia obciążenia (Podstawowa lub Standardowa).
* *Standardowa* Usługi równoważenia obciążenia SKU obsługują tylko *adresy* IP standardowej wersji SKU.


## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat usług Kubernetes można znaleźć w [dokumentacji usług Kubernetes.][kubernetes-services]

Więcej informacji na temat korzystania z Load Balancer ruchu przychodzącego można znaleźć w dokumentacji usługi [AKS Internal Load Balancer .](internal-lb.md)

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
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
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-group-create]: /cli/azure/group#az_group_create
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule#az_network_lb_outbound_rule_list
[az-network-public-ip-show]: /cli/azure/network/public-ip#az_network_public_ip_show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
[service-tags]: ../virtual-network/network-security-groups-overview.md#service-tags
