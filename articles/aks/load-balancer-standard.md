---
title: Użyj Load Balancer publicznego
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak używać publicznego modułu równoważenia obciążenia ze standardową jednostką SKU, aby udostępnić swoje usługi za pomocą usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 06/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: a58b00018f6ac89f024661d8d3f50ea5249e620b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89182126"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>Korzystanie z publicznej usługa Load Balancer w warstwie Standardowa w usłudze Azure Kubernetes Service (AKS)

Azure Load Balancer jest P4 modelu połączeń systemów otwartych (OSI), który obsługuje zarówno scenariusze przychodzące, jak i wychodzące. Dystrybuuje przepływy przychodzące, które docierają do frontonu modułu równoważenia obciążenia z wystąpieniami puli zaplecza.

**Publiczna** Load Balancer w przypadku zintegrowania z usługą AKS służy do dwóch celów:

1. Aby zapewnić połączenia wychodzące do węzłów klastra w sieci wirtualnej AKS. Osiąga ten cel przez przetłumaczenie prywatnego adresu IP węzłów na publiczny adres IP, który jest częścią jego *puli wychodzącej*.
2. Aby zapewnić dostęp do aplikacji za pośrednictwem usług Kubernetes Services typu `LoadBalancer` . Dzięki niej można łatwo skalować aplikacje i tworzyć usługi o wysokiej dostępności.

Używany jest **wewnętrzny (lub prywatny)** moduł równoważenia obciążenia, w przypadku którego jako frontonu można używać tylko prywatnych adresów IP. Wewnętrzne moduły równoważenia obciążenia są używane do równoważenia obciążenia ruchu w sieci wirtualnej. Dostęp do frontonu modułu równoważenia obciążenia można również uzyskać z sieci lokalnej w scenariuszu hybrydowym.

Ten dokument obejmuje integrację z publicznym modułem równoważenia obciążenia. Aby zapoznać się z wewnętrzną integracją Load Balancer, zobacz [dokumentację wewnętrznego modułu równoważenia obciążenia AKS](internal-lb.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Azure Load Balancer jest dostępny w dwóch jednostkach SKU — *podstawowa* i *standardowa*. Domyślnie *standardowa* jednostka SKU jest używana podczas tworzenia klastra AKS. Użyj *standardowej* jednostki SKU, aby mieć dostęp do dodatkowych funkcji, takich jak większa Pula zaplecza, [**wiele pul węzłów**](use-multiple-node-pools.md)i [**strefy dostępności**](availability-zones.md). Jest to zalecana Load Balancer jednostka SKU dla AKS.

Aby uzyskać więcej informacji na temat *podstawowych* i *standardowych* jednostek SKU, zobacz [porównanie jednostki SKU modułu równoważenia obciążenia platformy Azure][azure-lb-comparison].

W tym artykule przyjęto założenie, że istnieje klaster AKS z *standardową* jednostką SKU Azure Load Balancer i przedstawiono sposób użycia i skonfigurowania niektórych możliwości i funkcji modułu równoważenia obciążenia. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Jeśli wolisz nie korzystać z Azure Load Balancer w celu zapewnienia połączenia wychodzącego, a zamiast tego chcesz mieć własną bramę, zaporę lub serwer proxy, możesz pominąć tworzenie puli wychodzącej modułu równoważenia obciążenia i odpowiedniego adresu IP frontonu, używając [**typu wychodzącego jako UserDefinedRouting (UDR)**](egress-outboundtype.md). Typ wychodzący definiuje metodę wyjściową dla klastra i ma domyślnie typ: moduł równoważenia obciążenia.

## <a name="use-the-public-standard-load-balancer"></a>Korzystanie z publicznej usługi równoważenia obciążenia w warstwie Standardowa

Po utworzeniu klastra AKS z typem wychodzącym: Load Balancer (domyślnie) klaster jest gotowy do korzystania z modułu równoważenia obciążenia w celu udostępnienia usług.

Dla tego programu można utworzyć publiczną usługę typu `LoadBalancer` , jak pokazano w poniższym przykładzie. Zacznij od utworzenia manifestu usługi o nazwie `public-svc.yaml` :

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

Wdróż manifest usługi publicznej za pomocą [polecenia kubectl Zastosuj][kubectl-apply] i określ nazwę manifestu YAML:

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

Azure Load Balancer zostanie skonfigurowany przy użyciu nowego publicznego adresu IP, który będzie przedsunięty z tą nową usługą. Ponieważ Azure Load Balancer może mieć wiele adresów IP frontonu, każda nowo wdrożona usługa uzyska nowy dedykowany adres IP frontonu do unikatowego dostępu.

Można potwierdzić, że usługa zostanie utworzona, a moduł równoważenia obciążenia jest konfigurowany przez uruchomienie na przykład:

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

Po wyświetleniu szczegółów usługi publiczny adres IP utworzony dla tej usługi w usłudze równoważenia obciążenia zostanie wyświetlony w kolumnie *zewnętrzny adres IP* . Zmiana adresu IP z na rzeczywisty publiczny adres IP może potrwać minutę lub dwa *\<pending\>* , jak pokazano w powyższym przykładzie.

## <a name="configure-the-public-standard-load-balancer"></a>Konfigurowanie publicznego standardowego modułu równoważenia obciążenia

W przypadku korzystania z publicznej usługi równoważenia obciążenia w warstwie Standardowa istnieje zestaw opcji, które można dostosować podczas tworzenia lub aktualizując klaster. Te opcje umożliwiają dostosowanie Load Balancer, aby spełniały potrzeby obciążeń i zostały odpowiednio zweryfikowane. Za pomocą usługi równoważenia obciążenia w warstwie Standardowa można:

* Ustawianie lub skalowanie liczby zarządzanych wychodzących adresów IP
* Przenoszenie własnych niestandardowych [adresów IP lub prefiksu wychodzącego](#provide-your-own-outbound-public-ips-or-prefixes)
* Dostosuj liczbę przydzieloną portów wychodzących do każdego węzła klastra
* Skonfiguruj ustawienie limitu czasu dla połączeń bezczynnych

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>Skalowanie liczby zarządzanych publicznych adresów IP

Azure Load Balancer zapewnia łączność wychodzącą z sieci wirtualnej oprócz ruchu przychodzącego. Reguły ruchu wychodzącego ułatwiają konfigurowanie wychodzącej translacji adresów sieciowych usługa Load Balancer w warstwie Standardowa publicznej.

Podobnie jak w przypadku wszystkich reguł Load Balancer, reguły wychodzące mają taką samą znaną składnię jak równoważenie obciążenia i reguły NAT dla ruchu przychodzącego:

***Adresy IP frontonu + parametry + Pula zaplecza***

Reguła ruchu wychodzącego konfiguruje wychodzące NAT dla wszystkich maszyn wirtualnych identyfikowanych przez pulę zaplecza, które mają zostać przetłumaczone na fronton. I parametry zapewniają dodatkową kontrolę nad algorytmem NAT dla ruchu wychodzącego.

Reguła ruchu wychodzącego może być używana z tylko jednym publicznym adresem IP, ale reguły ruchu wychodzącego ułatwiają skalowanie w ramach ruchu wychodzącego NAT. Można użyć wielu adresów IP do zaplanowania scenariuszy o dużej skali i można użyć reguł ruchu wychodzącego, aby wyeliminować wzorce podatności na ruch wydechowy. Każdy dodatkowy adres IP dostarczony przez fronton zapewnia 64 000 portów tymczasowych dla Load Balancer, które mają być używane jako porty. 

W przypadku korzystania ze *standardowego* modułu równoważenia obciążenia jednostki SKU z zarządzanymi publicznymi adresami IP, które są tworzone domyślnie, można skalować liczbę zarządzanych wychodzących adresów IP, używając **`load-balancer-managed-ip-count`** parametru.

Aby zaktualizować istniejący klaster, uruchom następujące polecenie. Ten parametr można również ustawić podczas tworzenia klastra, aby miał wiele zarządzanych publicznych adresów IP.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

Powyższy przykład ustawia liczbę zarządzanych publicznych adresów IP z *2* dla klastra *MyAKSCluster* w ramach *zasobu*. 

Można również użyć parametru, **`load-balancer-managed-ip-count`** Aby ustawić początkową liczbę zarządzanych publicznych adresów IP, podczas tworzenia klastra, dołączając **`--load-balancer-managed-outbound-ip-count`** parametr i ustawiając go na żądaną wartość. Domyślna liczba zarządzanych publicznych adresów IP wychodzących to 1.

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>Podaj własne wychodzące publiczne adresy IP lub prefiksy

W przypadku korzystania ze *standardowego* modułu równoważenia obciążenia jednostki SKU klaster AKS automatycznie tworzy publiczny adres IP w grupie zasobów infrastruktura zarządzana przez AKS i przypisuje go do puli wychodzącej modułu równoważenia obciążenia.

Publiczny adres IP utworzony przez AKS jest traktowany jako zasób zarządzany przez AKS. Oznacza to, że ten publiczny adres IP jest przeznaczony do zarządzania przez AKS i nie wymaga żadnej akcji użytkownika bezpośrednio w publicznym zasobie IP. Alternatywnie można przypisać własny niestandardowy adres IP lub publiczny prefiks adresu IP podczas tworzenia klastra. Niestandardowe adresy IP można także zaktualizować we właściwościach modułu równoważenia obciążenia istniejącego klastra.

> [!NOTE]
> Niestandardowe publiczne adresy IP muszą być tworzone i własnością użytkownika. Zarządzane publiczne adresy IP utworzone za pomocą AKS nie mogą być ponownie używane jako dostarczenie własnego niestandardowego adresu IP, ponieważ może to spowodować konflikty zarządzania.

Przed wykonaniem tej operacji upewnij się, że spełniasz [wymagania wstępne i ograniczenia](../virtual-network/public-ip-address-prefix.md#constraints) , które są niezbędne do skonfigurowania wychodzących adresów IP lub wychodzących prefiksów.

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>Aktualizowanie klastra przy użyciu własnego publicznego adresu IP

Użyj polecenia [AZ Network Public-IP show][az-network-public-ip-show] , aby wyświetlić listę identyfikatorów publicznych adresów IP.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Powyższe polecenie wyświetla identyfikator publicznego adresu IP *myPublicIP* w *grupie zasobów zasobu* .

Użyj `az aks update` polecenia z **`load-balancer-outbound-ips`** parametrem, aby zaktualizować swój klaster przy użyciu publicznych adresów IP.

Poniższy przykład używa `load-balancer-outbound-ips` parametru z identyfikatorami z poprzedniego polecenia.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>Aktualizowanie klastra przy użyciu własnego publicznego prefiksu adresu IP

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

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>Tworzenie klastra przy użyciu własnego publicznego adresu IP lub prefiksów

Możesz chcieć wprowadzić własne adresy IP lub prefiksy IP dla ruchu wychodzącego w czasie tworzenia klastra, aby obsługiwać scenariusze, takie jak dodawanie punktów końcowych ruchu wychodzącego do listy dozwolonych. Dołącz te same parametry, które podano powyżej, do kroku tworzenia klastra, aby zdefiniować własne publiczne adresy IP i prefiksy adresów IPv4 na początku cyklu życia klastra.

Użyj polecenia *AZ AKS Create* z parametrem *Load-wychodzące-IP* , aby utworzyć nowy klaster z publicznymi adresami IP na początku.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Za pomocą polecenia *AZ AKS Create* z parametrem *równoważenia obciążenia wychodzącego IP prefiksów* Utwórz nowy klaster z prefiksami publicznych adresów IP na początku.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>Konfigurowanie przyznanych portów wychodzących

> [!IMPORTANT]
> Jeśli w klastrze znajdują się aplikacje, które oczekują na utworzenie dużej liczby połączeń z niewielkim zestawem miejsc docelowych, np. wiele wystąpień frontonu łączących się z bazą danych SQL to scenariusz, który jest bardzo podatny na wyczerpanie portów (od portów, z których można nawiązać połączenie). W tych scenariuszach zdecydowanie zaleca się zwiększenie przyznanych portów wychodzących i wychodzących adresów IP frontonu w usłudze równoważenia obciążenia. Zwiększenie należy wziąć pod uwagę, że jeden (1) dodatkowy adres IP dodaje 64 KB dodatkowych portów do dystrybucji między wszystkimi węzłami klastra.


O ile nie określono inaczej, AKS będzie używać domyślnej wartości przyznanych portów wychodzących, które usługa Load Balancer w warstwie Standardowa definiuje podczas konfigurowania. Ta wartość jest **równa null** w interfejsie API AKS lub **0** w INTERFEJSIE API usługi równoważenia obciążenia, jak pokazano w poniższym poleceniu:

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

To wyjście nie oznacza, że masz 0 portów, ale zamiast tego korzystasz z [automatycznego przypisania portu wychodzącego na podstawie rozmiaru puli zaplecza][azure-lb-outbound-preallocatedports], więc jeśli klaster ma 50 lub mniej węzłów, porty 1024 dla każdego węzła są przydzielane, ponieważ zwiększy się liczbę węzłów z tego miejsca.


Aby zdefiniować lub zwiększyć liczbę przyznanych portów wychodzących, można wykonać następujące czynności:


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

W tym przykładzie nadajesz 4000 przydzieloną liczbę portów wychodzących dla każdego węzła w klastrze, a w przypadku 7 adresów IP masz *4000 portów na węzeł * 100 węzły = 400 000 Total ports < = 448k Total Ports = 7 adresów IP * 64 000 portów na adres IP*. Pozwoli to na bezpieczne skalowanie do 100 węzłów i posiadanie domyślnej operacji uaktualniania. Alokacja wystarczającej liczby portów jest niezwykle ważna dla dodatkowych węzłów wymaganych do uaktualnienia i innych operacji. AKS domyślnie jeden węzeł buforu do uaktualnienia, w tym przykładzie wymaga 4000 wolnych portów w danym momencie. W przypadku używania [wartości maxSurge](upgrade-cluster.md#customize-node-surge-upgrade-preview)pomnóż porty wychodzące na węzeł za pomocą wartości maxSurge.

Aby bezpiecznie przechodzić powyżej 100 węzłów, trzeba dodać więcej adresów IP.


> [!IMPORTANT]
> Aby uniknąć problemów z łącznością lub skalowaniem, należy [obliczyć wymagany limit przydziału i sprawdzić wymagania][requirements] przed rozpoczęciem dostosowywania *allocatedOutboundPorts* .

Można również użyć **`load-balancer-outbound-ports`** parametrów podczas tworzenia klastra, ale należy również określić albo, **`load-balancer-managed-outbound-ip-count`** **`load-balancer-outbound-ips`** lub **`load-balancer-outbound-ip-prefixes`** .  Na przykład:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>Konfigurowanie limitu czasu bezczynności modułu równoważenia obciążenia

W przypadku wyczerpania zasobów portów współdziałania przepływy wychodzące kończą się niepowodzeniem, dopóki istniejące przepływy nie zwolnią portów. Load Balancer przejmowanie portów protokołu równorzędnego, gdy przepływ zostanie zamknięty, a moduł równoważenia obciążenia skonfigurowany przez AKS korzysta z przekroczenia limitu czasu bezczynności przez okres 30 minut na odzyskiwanie portów z bezczynnymi przepływami.
W razie potrzeby można również użyć transportu (na przykład **`TCP keepalives`** ) lub **`application-layer keepalives`** w celu odświeżenia przepływu bezczynności i zresetowania tego limitu czasu bezczynności. Ten limit czasu można skonfigurować zgodnie z poniższym przykładem: 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

Jeśli spodziewasz się wielu krótkich połączeń i nie ma żadnych połączeń, które są długotrwałe i mogą mieć długotrwałe czasy bezczynności, na przykład wykorzystując `kubectl proxy` lub `kubectl port-forward` Rozważ użycie niskiej wartości limitu czasu, takiego jak 4 minuty. Ponadto w przypadku utrzymywania aktywności przy użyciu protokołu TCP wystarczy włączyć je po jednej stronie połączenia. Na przykład wystarczające jest włączenie ich po stronie serwera tylko w celu zresetowania czasomierza bezczynności przepływu i nie jest konieczne dla obu stron, aby rozpocząć utrzymywanie aktywności protokołu TCP. Podobne pojęcia istnieją dla warstwy aplikacji, w tym konfiguracje klienta bazy danych. Sprawdź po stronie serwera, jakie opcje istnieją dla nieaktywności związanych z aplikacjami.

> [!IMPORTANT]
> AKS domyślnie włącza resetowanie protokołu TCP w trybie bezczynności i zaleca zachowanie tej konfiguracji i wykorzystanie jej do bardziej przewidywalnego zachowania aplikacji w scenariuszach.
> Parametr RST jest wysyłany tylko podczas połączenia TCP w stanie USTANOWIONym. Więcej informacji na ten temat znajduje się [tutaj](../load-balancer/load-balancer-tcp-reset.md).

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>Wymagania dotyczące dostosowywania przyznanych portów wychodzących i limitu czasu bezczynności

- Wartość określona dla *allocatedOutboundPorts* musi być również wielokrotnością 8.
- Musisz mieć wystarczającą ilość wychodzącego adresu IP na podstawie liczby maszyn wirtualnych węzła i wymaganych przyznanych portów wychodzących. Aby sprawdzić, czy masz wystarczającą ilość wychodzących adresów IP, należy użyć następującej formuły: 
 
*outboundIPs* \* 64 000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*.
 
Na przykład jeśli masz 3 *nodeVMs*i 50 000 *desiredAllocatedOutboundPorts*, musisz mieć co najmniej 3 *outboundIPs*. Zaleca się dołączenie dodatkowej pojemności wychodzącego adresu IP poza potrzebami. Ponadto należy uwzględnić automatyczne skalowanie klastra i możliwość uaktualniania puli węzłów przy obliczaniu wydajności wychodzącego adresu IP. W przypadku automatycznego skalowania klastra sprawdź bieżącą liczbę węzłów i maksymalną liczbę węzłów i użyj wyższej wartości. W przypadku uaktualniania należy uwzględnić dodatkową maszynę wirtualną węzłową dla każdej puli węzłów, która umożliwia uaktualnianie.

- Podczas ustawiania *IdleTimeoutInMinutes* na inną wartość niż domyślnie 30 minut należy wziąć pod uwagę, jak długo obciążenia będą wymagały połączenia wychodzącego. Należy również wziąć pod uwagę domyślną wartość limitu czasu dla usługi równoważenia obciążenia w *warstwie Standardowa* używanej poza AKS wynosi 4 minuty. Wartość *IdleTimeoutInMinutes* , która dokładniej odzwierciedla Twoje określone obciążenie AKS może pomóc w zmniejszeniu wyczerpania spalin spowodowanych przez nawiązanie połączeń, które nie są już używane.

> [!WARNING]
> Zmiana wartości parametrów *AllocatedOutboundPorts* i *IdleTimeoutInMinutes* może znacząco zmienić zachowanie reguły ruchu wychodzącego dla modułu równoważenia obciążenia i nie powinno być wykonywane w sposób jasny, bez zrozumienia kompromisów i wzorców połączeń aplikacji, zapoznaj się z [sekcją Rozwiązywanie problemów dotyczących translatora adresów sieciowych][troubleshoot-snat] i przejrzyj [Load Balancer reguły wychodzące][azure-lb-outbound-rules-overview] i [połączenia wychodzące na platformie Azure][azure-lb-outbound-connections] przed zaktualizowaniem tych wartości, aby w pełni zrozumieć wpływ zmian.

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>Ogranicz ruch przychodzący do określonych zakresów adresów IP

Następujący manifest używa *loadBalancerSourceRanges* , aby określić nowy zakres adresów IP dla przychodzącego ruchu zewnętrznego:

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
> Ruch przychodzący zewnętrznych przepływów z modułu równoważenia obciążenia do sieci wirtualnej klastra AKS. Sieć wirtualna ma grupę zabezpieczeń sieci (sieciowej grupy zabezpieczeń), która umożliwia cały ruch przychodzący z modułu równoważenia obciążenia. Ten sieciowej grupy zabezpieczeń używa [tagu usługi][service-tags] typu *równoważenia* obciążenia, aby zezwalać na ruch z modułu równoważenia obciążenia.

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>Obsługuj adres IP klienta w połączeniach przychodzących

Domyślnie usługa typu `LoadBalancer` [w Kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) i w AKS nie będzie utrzymywać adresu IP klienta w połączeniu z usługą. Źródłowy adres IP pakietu, który jest dostarczany do elementu pod, będzie prywatnym adresem IP węzła. Aby zachować adres IP klienta, należy ustawić `service.spec.externalTrafficPolicy` na wartość `local` w definicji usługi. Poniższy Manifest przedstawia przykład:

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

## <a name="additional-customizations-via-kubernetes-annotations"></a>Dodatkowe dostosowania za pośrednictwem adnotacji Kubernetes

Poniżej znajduje się lista adnotacji obsługiwanych przez usługi Kubernetes Services z typem. `LoadBalancer` Adnotacje dotyczą tylko przepływów **przychodzących** :

| Adnotacja | Wartość | Opis
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` lub `false`                     | Określ, czy moduł równoważenia obciążenia powinien być wewnętrzny. Jest ona domyślnie publiczna, jeśli nie została ustawiona.
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | Nazwa podsieci                    | Określ podsieć, z którą ma zostać powiązany wewnętrzny moduł równoważenia obciążenia. W przypadku, gdy nie ustawiono tego ustawienia, przyjrzyj się domyślnie do podsieci skonfigurowanej w pliku konfiguracji chmury.
| `service.beta.kubernetes.io/azure-dns-label-name`                 | Nazwa etykiety DNS dla publicznych adresów IP   | Określ nazwę etykiety DNS dla usługi **publicznej** . Jeśli jest ustawiona na pusty ciąg, wpis DNS w publicznym adresie IP nie będzie używany.
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` lub `false`                     | Określ, że usługa powinna być udostępniona przy użyciu reguły zabezpieczeń platformy Azure, która może być współużytkowana z inną usługą i specyfiką handlową reguł w celu zwiększenia liczby usług, które mogą być ujawnione. Ta adnotacja opiera się na funkcji [rozszerzone reguły zabezpieczeń](../virtual-network/security-overview.md#augmented-security-rules) platformy Azure w grupach zabezpieczeń sieci. 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | Nazwa grupy zasobów            | Określ grupę zasobów publicznych adresów IP usługi równoważenia obciążenia, które nie znajdują się w tej samej grupie zasobów co infrastruktura klastra (Grupa zasobów węzła).
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | Lista dozwolonych tagów usługi          | Określ listę dozwolonych [tagów usługi][service-tags] oddzielonych przecinkami.
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | Limity czasu bezczynności protokołu TCP w minutach          | Określ czas (w minutach) limitów czasu bezczynności połączenia TCP, które mają być wykonywane w ramach modułu równoważenia obciążenia. Wartość domyślna i minimalna to 4. Wartość maksymalna to 30. Musi być liczbą całkowitą.
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | Wyłącz `enableTcpReset` dla modułu równoważenia


## <a name="troubleshooting-snat"></a>Rozwiązywanie problemów z usługą

Jeśli wiesz, że masz wiele wychodzących połączeń TCP lub UDP z tym samym docelowym adresem IP i portem, a następnie zauważysz, że połączenia wychodzące nie są obsługiwane lub są zalecane w przypadku wyczerpania portów przydzielonego dostępu do danych wyjściowych, masz kilka ogólnych opcji zaradczych. Zapoznaj się z tymi opcjami i zdecyduj, co jest dostępne i najlepsze dla Twojego scenariusza. Istnieje możliwość, że co najmniej jedna może pomóc w zarządzaniu tym scenariuszem. Aby uzyskać szczegółowe informacje, zapoznaj się z [przewodnikiem rozwiązywania problemów dotyczących połączeń wychodzących](../load-balancer/troubleshoot-outbound-connection.md).

Często główną przyczyną wyczerpania ruchu związanego z ruchem przychodzącym jest wzorzec dla połączeń wychodzących, zarządzanych lub konfigurowalnych czasomierzy zmieniony z wartości domyślnych. Uważnie przejrzyj tę sekcję.

### <a name="steps"></a>Kroki
1. Sprawdź, czy połączenia pozostaną bezczynne przez długi czas i polegają na domyślnym limicie czasu bezczynności dla zwolnienia tego portu. Jeśli tak, może być konieczne zredukowanie domyślnego limitu czasu (30 min) w danym scenariuszu.
2. Zbadaj, w jaki sposób aplikacja tworzy łączność wychodzącą (na przykład przegląd kodu lub przechwycenie pakietu).
3. Ustal, czy to działanie jest oczekiwane, czy też czy aplikacja jest błędna. Użyj [metryk](../load-balancer/load-balancer-standard-diagnostics.md) i [dzienników](../load-balancer/load-balancer-monitor-log.md) w Azure monitor, aby uzasadnić swoje ustalenia. Użyj kategorii "Niepowodzenie" dla metryki połączeń z przyłączaniem adresów sieciowych na przykład.
4. Oceń, czy są obserwowane odpowiednie [wzorce](#design-patterns) .
5. Oceń, czy wyczerpanie portów podadresu IP powinno być skorygowane o [dodatkowe wychodzące i dodatkowe przydzieloną porty](#configure-the-allocated-outbound-ports) wychodzące.

### <a name="design-patterns"></a>Wzorce projektowe
Zawsze korzystaj z zalet użycia połączenia i puli połączeń, jeśli to możliwe. Wzorce te pozwolą uniknąć problemów z wyczerpaniem zasobów i powodować przewidywalne zachowanie. Elementy pierwotne dla tych wzorców można znaleźć w wielu bibliotekach i strukturach programistycznych.

- Żądania niepodzielne (jedno żądanie na połączenie) zazwyczaj nie są dobrym wyborem. Takie limity antywzorców są skalowane, zmniejszają wydajność i zmniejszają niezawodność. Zamiast tego należy ponownie użyć połączeń HTTP/S, aby zmniejszyć liczbę połączeń i skojarzonych z nimi portów. Skalowanie aplikacji zwiększy się i poprawi wydajność ze względu na obniżone, narzuty i koszt operacji kryptograficznej przy korzystaniu z protokołu TLS.
- W przypadku korzystania z klastra/niestandardowego systemu DNS lub niestandardowych serwerów nadrzędnych w systemie coreDNS należy pamiętać, że system DNS może wprowadzić wiele pojedynczych przepływów w woluminie, gdy klient nie buforuje wyniku rozpoznawania nazw DNS. Należy pamiętać, aby najpierw dostosować coreDNS zamiast używać niestandardowych serwerów DNS i zdefiniować dobrą wartość buforowania.
- Przepływy UDP (na przykład wyszukiwania DNS) przydzielą porty przydziałów adresów sieciowych przez czas trwania bezczynności. Im dłuższy limit czasu bezczynności, tym wyższe ciśnienie w portach źródłowego. Użyj krótkiego limitu czasu bezczynności (na przykład 4 minuty).
Użyj pul połączeń do kształtowania woluminu połączenia.
- Nigdy nie wolno odrzucać przepływu TCP i polegać na czasomierzach TCP w celu oczyszczenia przepływu. Jeśli nie zezwolisz na bezpośrednie zamknięcie połączenia protokołu TCP, stan pozostanie przydzielony w systemach pośrednich i punktach końcowych, a porty dla innych połączeń są niedostępne. Ten wzorzec może wyzwolić awarie aplikacji i wyczerpanie adresów współdziałania.
- Nie zmieniaj wartości czasomierza zamknięcia protokołu TCP na poziomie systemu operacyjnego bez specjalistycznej wiedzy o wpływie na nie. Gdy stos TCP zostanie odzyskany, wydajność aplikacji może mieć negatywny wpływ, gdy punkty końcowe połączenia mają niezgodne oczekiwania. Chcemy zmienić czasomierze zwykle jest oznaką podstawowego problemu projektowego. Przejrzyj poniższe zalecenia.


Powyższy przykład aktualizuje regułę tak, aby zezwalała na ruch zewnętrzny przychodzący z zakresu *MY_EXTERNAL_IP_RANGE* . Jeśli zastąpisz *MY_EXTERNAL_IP_RANGE* przy użyciu adresu IP podsieci wewnętrznej, ruch jest ograniczony tylko do wewnętrznych adresy IP klastra. Nie pozwoli to klientom spoza klastra Kubernetes na dostęp do modułu równoważenia obciążenia.

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Przechodzenie z usługi równoważenia obciążenia z podstawową jednostką SKU do standardowej jednostki SKU

Jeśli istnieje klaster z podstawową jednostką SKU Load Balancer, istnieją istotne różnice w działaniu podczas migrowania do używania klastra ze standardową jednostką SKU Load Balancer.

Na przykład w przypadku wdrożeń Blue/Green do migracji klastrów jest powszechną metodą, w której `load-balancer-sku` Typ klastra można zdefiniować tylko w czasie tworzenia klastra. Jednak *podstawowe* moduły równoważenia obciążenia SKU używają podstawowych adresów IP *jednostki SKU* , które nie są zgodne ze standardowymi MODUŁAMI równoważenia obciążenia *jednostki SKU* , ponieważ wymagają standardowych adresów IP *jednostki SKU* . W przypadku migrowania klastrów w celu uaktualnienia Load Balancer jednostek SKU będzie wymagane nowe adresy IP ze zgodną jednostką SKU adresu IP.

Aby uzyskać więcej informacji na temat migracji klastrów, zapoznaj się z [naszą dokumentacją dotyczącą zagadnień związanych z migracją](aks-migration.md) , aby wyświetlić listę ważnych tematów, które należy wziąć pod uwagę podczas migracji. Poniższe ograniczenia są również ważnymi różnicami w działaniu podczas korzystania ze standardowych modułów równoważenia obciążenia SKU w programie AKS.

## <a name="limitations"></a>Ograniczenia

Podczas tworzenia klastrów AKS i zarządzania nimi, które obsługują moduł równoważenia obciążenia ze *standardową* jednostką SKU, obowiązują następujące ograniczenia:

* Do zezwalania na ruch wychodzący z klastra AKS jest wymagany co najmniej jeden publiczny adres IP lub prefiks IP. Publiczny adres IP lub prefiks IP jest również wymagany do zapewnienia łączności między płaszczyzną kontroli i węzłami agenta i zachowania zgodności z poprzednimi wersjami AKS. Dostępne są następujące opcje określania publicznych adresów IP lub prefiksów protokołu IPv4 za pomocą usługi równoważenia obciążenia w *warstwie Standardowa* :
    * Udostępnianie własnych publicznych adresów IP.
    * Podaj własne prefiksy publicznych adresów IP.
    * Określ liczbę do 100, aby umożliwić klastrowi AKS tworzenie wielu publicznych adresów IP *standardowej* jednostki SKU w tej samej grupie zasobów utworzonej jako klaster AKS, która zazwyczaj nazywa się *MC_* na początku. AKS przypisuje publiczny adres IP do modułu równoważenia obciążenia *standardowej* jednostki SKU. Domyślnie jeden publiczny adres IP zostanie automatycznie utworzony w tej samej grupie zasobów co klaster AKS, jeśli nie określono publicznego adresu IP, publicznego prefiksu adresu IP lub liczby adresów IP. Należy również zezwolić na publiczne adresy i uniknąć tworzenia Azure Policy, które zakazują tworzenie adresów IP.
* Publicznego adresu IP utworzonego za pomocą AKS nie można użyć ponownie jako niestandardowego dołączenia do własnego publiczny adres IP. Wszystkie niestandardowe adresy IP muszą być tworzone i zarządzane przez użytkownika.
* Definiowanie jednostki SKU modułu równoważenia obciążenia można wykonać tylko podczas tworzenia klastra AKS. Nie można zmienić jednostki SKU modułu równoważenia obciążenia po utworzeniu klastra AKS.
* W pojedynczym klastrze można używać tylko jednego typu jednostki SKU usługi równoważenia obciążenia (Basic lub standard).
* *Standard* Usługi równoważenia obciążenia jednostki SKU obsługują tylko adresy IP *standardowej* jednostki SKU.


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach Kubernetes Services w [dokumentacji usług Kubernetes Services][kubernetes-services].

Dowiedz się więcej o używaniu wewnętrznego Load Balancer dla ruchu przychodzącego w [dokumentacji wewnętrznej Load Balancer AKS](internal-lb.md).

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
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
[service-tags]: ../virtual-network/security-overview.md#service-tags
