---
title: Konfigurowanie Azure CNI sieci w Azure Kubernetes Service (AKS)
description: Dowiedz się, jak skonfigurować Azure CNI sieci (zaawansowane) w usługach Azure Kubernetes Service (AKS), w tym wdrożyć klaster usługi AKS w istniejącej sieci wirtualnej i podsieci.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 839aa012cedaaa6f5bd3d1edad60e3ea7278133b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775893"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Konfigurowanie Azure CNI sieci w Azure Kubernetes Service (AKS)

Domyślnie klastry AKS korzystają z [usługi kubenet][kubenet], a sieć wirtualna i podsieć są tworzone automatycznie. W *przypadku rozwiązania kubenet* węzły uzyskają adres IP z podsieci sieci wirtualnej. Translator adresów sieciowych (NAT) jest następnie konfigurowany w węzłach, a zasobniki otrzymują adres IP "ukryty" za adresem IP węzła. Takie podejście zmniejsza liczbę adresów IP, które należy zarezerwować w przestrzeni sieciowej na potrzeby zasobników.

W [Azure Container Networking Interface (CNI)][cni-networking]każdy zasobnik pobiera adres IP z podsieci i jest dostępny bezpośrednio. Te adresy IP muszą być unikatowe w przestrzeni sieciowej i muszą być planowane z wyprzedzeniem. Każdy węzeł ma parametr konfiguracji dla maksymalnej liczby zasobników, które obsługuje. Równoważna liczba adresów IP na węzeł jest następnie rezerwowa z góry dla tego węzła. Takie podejście wymaga więcej planowania i często prowadzi do wyczerpania adresów IP lub konieczności ponownego kompilowania klastrów w większej podsieci w przypadku wzrostu zapotrzebowania aplikacji.

W tym artykule pokazano, jak używać sieci *Azure CNI* do tworzenia podsieci sieci wirtualnej dla klastra usługi AKS i korzystania z nich. Aby uzyskać więcej informacji na temat opcji i kwestii dotyczących sieci, zobacz [Network concepts for Kubernetes and AKS (Pojęcia dotyczące sieci dla platform Kubernetes i AKS).][aks-network-concepts]

## <a name="prerequisites"></a>Wymagania wstępne

* Sieć wirtualna klastra AKS musi zezwalać na wychodzącą łączność z Internetem.
* Klastry usługi AKS nie mogą używać wartości , , ani dla zakresu adresów usługi `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` Kubernetes, zakresu adresów zasobników ani zakresu adresów sieci `192.0.2.0/24` wirtualnej klastra.
* Tożsamość klastra używana przez klaster usługi [](../role-based-access-control/built-in-roles.md#network-contributor) AKS musi mieć co najmniej uprawnienia Współautor sieci w podsieci w sieci wirtualnej. Jeśli chcesz zdefiniować rolę [niestandardową zamiast](../role-based-access-control/custom-roles.md) wbudowanej roli Współautor sieci, wymagane są następujące uprawnienia:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* Podsieć przypisana do puli węzłów usługi AKS nie może być [podsiecią delegowaną](../virtual-network/subnet-delegation-overview.md).

## <a name="plan-ip-addressing-for-your-cluster"></a>Planowanie adresowania IP dla klastra

Klastry skonfigurowane z Azure CNI sieciowe wymagają dodatkowego planowania. Rozmiar sieci wirtualnej i jej podsieć muszą uwzględniać liczbę zasobników, które mają zostać uruchomione, oraz liczbę węzłów klastra.

Adresy IP zasobników i węzłów klastra są przypisywane z określonej podsieci w sieci wirtualnej. Każdy węzeł jest skonfigurowany przy użyciu podstawowego adresu IP. Domyślnie 30 dodatkowych adresów IP jest wstępnie konfigurowanych przez Azure CNI, które są przypisane do zasobników zaplanowanych w węźle. W przypadku skalowania klastra w zewnątrz każdy węzeł jest podobnie skonfigurowany z adresami IP z podsieci. Można również wyświetlić [maksymalną liczbę zasobników na węzeł](#maximum-pods-per-node).

> [!IMPORTANT]
> Wymagana liczba adresów IP powinna uwzględniać zagadnienia dotyczące operacji uaktualniania i skalowania. Jeśli ustawisz zakres adresów IP tak, aby obsługiwać tylko stałą liczbę węzłów, nie będzie można uaktualnić ani skalować klastra.
>
> * Podczas **uaktualniania** klastra usługi AKS nowy węzeł jest wdrażany w klastrze. Usługi i obciążenia zaczynają działać w nowym węźle, a starszy węzeł jest usuwany z klastra. Ten proces uaktualniania stopniowego wymaga, aby dostępny był co najmniej jeden dodatkowy blok adresów IP. Liczba węzłów to `n + 1` .
>   * Ta uwaga jest szczególnie ważna w przypadku używania pul węzłów systemu Windows Server. Węzły systemu Windows Server w u usługi AKS nie stosują automatycznie aktualizacji systemu Windows. Zamiast tego należy wykonać uaktualnienie puli węzłów. To uaktualnienie wdraża nowe węzły z najnowszymi poprawkami zabezpieczeń i obrazu węzła podstawowego systemu Windows Server 2019. Aby uzyskać więcej informacji na temat uaktualniania puli węzłów systemu Windows Server, zobacz [Upgrade a node pool in AKS (Uaktualnianie puli węzłów w u usługach AKS).][nodepool-upgrade]
>
> * Podczas **skalowania** klastra usługi AKS w klastrze jest wdrażany nowy węzeł. Usługi i obciążenia zaczynają działać w nowym węźle. Zakres adresów IP musi uwzględniać sposób skalowania w górę liczby węzłów i zasobników, które może obsługiwać klaster. Powinien zostać również uwzględniony jeden dodatkowy węzeł dla operacji uaktualniania. Liczba węzłów to `n + number-of-additional-scaled-nodes-you-anticipate + 1` .

Jeśli oczekujesz, że w węzłach będzie działać maksymalna liczba zasobników oraz regularnie niszczenie i wdrażanie zasobników, należy również wziąć pod uwagę niektóre dodatkowe adresy IP na węzeł. Te dodatkowe adresy IP należy wziąć pod uwagę może potrwać kilka sekund, aby usługa została usunięta i adres IP zwalniany dla nowej usługi do wdrożenia i uzyskania adresu.

Plan adresów IP klastra usługi AKS składa się z sieci wirtualnej, co najmniej jednej podsieci dla węzłów i zasobników oraz zakresu adresów usługi Kubernetes.

| Zakres adresów/ zasób platformy Azure | Limity i zmiany rozmiaru |
| --------- | ------------- |
| Sieć wirtualna | Sieć wirtualna platformy Azure może być tak duża jak /8, ale jest ograniczona do 65 536 skonfigurowanych adresów IP. Przed skonfigurowaniem przestrzeni adresowej należy wziąć pod uwagę wszystkie potrzeby dotyczące sieci, w tym komunikację z usługami w innych sieciach wirtualnych. Jeśli na przykład skonfigurujesz zbyt dużą przestrzeń adresową, mogą wystąpić problemy z nakładającymi się innymi przestrzeniami adresowymi w sieci.|
| Podsieć | Musi być wystarczająco duży, aby pomieścić węzły, zasobniki oraz wszystkie zasoby platformy Kubernetes i platformy Azure, które mogą być aprowowane w klastrze. Na przykład w przypadku wdrożenia wewnętrznego Azure Load Balancer ich ip frontonie są przydzielane z podsieci klastra, a nie publicznych. Rozmiar podsieci powinien również uwzględniać operacje uaktualniania lub przyszłe potrzeby skalowania.<p />Aby obliczyć minimalny *rozmiar* podsieci, w tym dodatkowy węzeł dla operacji uaktualniania: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Przykład klastra z 50 węzłami: `(51) + (51  * 30 (default)) = 1,581` (/21 lub większy)<p/>Przykład klastra z 50 węzłami, który obejmuje również aprowizowanie skalowania w górę dodatkowych 10 węzłów: `(61) + (61 * 30 (default)) = 1,891` (/21 lub większy)<p>Jeśli podczas tworzenia klastra nie określisz maksymalnej liczby zasobników na węzeł, maksymalna liczba zasobników na węzeł zostanie ustawiona na *30*. Minimalna wymagana liczba adresów IP jest oparta na tej wartości. Jeśli obliczysz minimalne wymagania dotyczące adresów IP [](#configure-maximum---new-clusters) dla innej wartości maksymalnej, zobacz jak skonfigurować maksymalną liczbę zasobników na węzeł, aby ustawić tę wartość podczas wdrażania klastra. |
| Zakres adresów usługi Kubernetes | Ten zakres nie powinien być używany przez żaden element sieci w tej sieci wirtualnej ani z tą siecią wirtualną. Adres usługi CIDR musi być mniejszy niż /12. Możesz ponownie użyć tego zakresu w różnych klastrach usługi AKS. |
| Adres IP usługi DNS platformy Kubernetes | Adres IP w zakresie adresów usługi Kubernetes, który będzie używany przez odnajdywanie usługi klastra. Nie używaj pierwszego adresu IP z zakresu adresów, takiego jak .1. Pierwszy adres w zakresie podsieci jest używany dla adresu *kubernetes.default.svc.cluster.local.* |
| Adres mostka platformy Docker | Adres mostka platformy Docker to domyślny adres sieciowy mostka *docker0*, który jest obecny we wszystkich instalacjach platformy Docker. Chociaż *mostek docker0* nie jest używany przez klastry usługi AKS ani same zasobniki, należy ustawić ten adres, aby nadal obsługiwać scenariusze, takie jak kompilacja platformy *Docker* w klastrze usługi AKS. Należy wybrać cidr dla adresu sieciowego mostka platformy Docker, ponieważ w przeciwnym razie docker automatycznie wybierze podsieć, co może kolidować z innymi przewodnikami CIDR. Należy wybrać przestrzeń adresową, która nie koliduje z resztą woluminów CIDR w sieciach, w tym z przestrzenią CIDR usługi klastra i cidr zasobnika. Wartość domyślna to 172.17.0.1/16. Możesz ponownie użyć tego zakresu w różnych klastrach usługi AKS. |

## <a name="maximum-pods-per-node"></a>Maksymalna liczba zasobników na węzeł

Maksymalna liczba zasobników na węzeł w klastrze usługi AKS wynosi 250. *Domyślna* maksymalna liczba zasobników na węzeł różni  się w zależności od Azure CNI *kubenet* i sieci oraz metody wdrażania klastra.

| Metoda wdrażania | Ustawienie domyślne kubenet | Azure CNI domyślne | Konfigurowalne podczas wdrażania |
| -- | :--: | :--: | -- |
| Interfejs wiersza polecenia platformy Azure | 110 | 30 | Tak (maksymalnie 250) |
| Szablon usługi Resource Manager | 110 | 30 | Tak (maksymalnie 250) |
| Portal | 110 | 110 (skonfigurowane na karcie Pule węzłów) | Nie |

### <a name="configure-maximum---new-clusters"></a>Konfigurowanie wartości maksymalnej — nowe klastry

Można skonfigurować maksymalną liczbę zasobników na węzeł w czasie wdrażania klastra lub podczas dodawania nowych pul węzłów. W przypadku wdrażania przy użyciu interfejsu wiersza polecenia platformy Azure Resource Manager szablonu można ustawić maksymalną liczbę zasobników na węzeł na 250.

Jeśli nie określisz wartości maxPods podczas tworzenia nowych pul węzłów, otrzymasz wartość domyślną 30 dla Azure CNI.

Minimalna wartość maksymalnej liczby zasobników na węzeł jest wymuszana w celu zagwarantowania miejsca dla zasobników systemowych o krytycznym znaczeniu dla kondycji klastra. Minimalna wartość, która może być ustawiona dla maksymalnej liczby zasobników na węzeł, wynosi 10 tylko wtedy, gdy konfiguracja każdej puli węzłów ma miejsce na co najmniej 30 zasobników. Na przykład ustawienie maksymalnej liczby zasobników na węzeł na co najmniej 10 wymaga, aby każda pojedyncza pula węzłów zawierała co najmniej 3 węzły. To wymaganie dotyczy również każdej nowo utworzonej puli węzłów, więc jeśli 10 jest zdefiniowanych jako maksymalna liczba zasobników na węzeł, każda kolejna dodana pula węzłów musi mieć co najmniej 3 węzły.

| Sieć | Minimum | Maksimum |
| -- | :--: | :--: |
| Azure CNI | 10 | 250 |
| Kubenet | 10 | 110 |

> [!NOTE]
> Wartość minimalna w powyższej tabeli jest ściśle wymuszana przez usługę AKS. Nie można ustawić wartości maxPods niższej niż wartość minimalna pokazana jako taka, aby uniemożliwić uruchomienie klastra.

* **Interfejs wiersza polecenia** platformy Azure: `--max-pods` określ argument podczas wdrażania klastra za pomocą polecenia [az aks create.][az-aks-create] Wartość maksymalna to 250.
* **Resource Manager szablonu:** określ właściwość w obiekcie `maxPods` [ManagedClusterAgentPoolProfile] podczas wdrażania klastra przy użyciu Resource Manager szablonu. Wartość maksymalna to 250.
* **Azure Portal:** nie można zmienić maksymalnej liczby zasobników na węzeł podczas wdrażania klastra z Azure Portal. Azure CNI sieciowe są ograniczone do 30 zasobników na węzeł podczas wdrażania przy użyciu Azure Portal.

### <a name="configure-maximum---existing-clusters"></a>Konfigurowanie wartości maksymalnej — istniejące klastry

Ustawienie maxPod na węzeł można zdefiniować podczas tworzenia nowej puli węzłów. Jeśli chcesz zwiększyć ustawienie maxPod na węzeł w istniejącym klastrze, dodaj nową pulę węzłów z nową żądaną licznikiem maxPod. Po zmigrowaniu zasobników do nowej puli usuń starszą pulę. Aby usunąć wszystkie starsze pule w klastrze, upewnij się, że ustawiasz tryby puli węzłów zgodnie z definicją w dokumencie [pul węzłów systemowych][system-node-pools].

## <a name="deployment-parameters"></a>Parametry wdrożenia

Podczas tworzenia klastra usługi AKS można skonfigurować następujące parametry dla Azure CNI sieci:

**Sieć wirtualna:** sieć wirtualna, w której chcesz wdrożyć klaster Kubernetes. Jeśli chcesz utworzyć nową sieć wirtualną  dla klastra, wybierz pozycję Utwórz nową i wykonaj kroki opisane w *sekcji Tworzenie sieci wirtualnej.* Aby uzyskać informacje na temat limitów i przydziałów dla sieci wirtualnej platformy Azure, zobacz [Azure subscription and service limits, quotas, and constraints](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)(Limity, przydziały i ograniczenia usługi i subskrypcji platformy Azure).

**Podsieć:** podsieć w sieci wirtualnej, w której chcesz wdrożyć klaster. Jeśli chcesz utworzyć nową podsieć w sieci  wirtualnej dla klastra, wybierz pozycję Utwórz nową i wykonaj kroki opisane w *sekcji Tworzenie podsieci.* W przypadku łączności hybrydowej zakres adresów nie powinien pokrywać się z żadnymi innymi sieciami wirtualnymi w środowisku.

**Wtyczka** sieci platformy Azure: gdy jest używana wtyczka sieci platformy Azure, nie można uzyskać dostępu do wewnętrznej usługi LoadBalancer z elementem "externalTrafficPolicy=Local" z maszyn wirtualnych z adresem IP w klastrzeCIDR, który nie należy do klastra usługi AKS.

**Zakres adresów usługi Kubernetes:** ten parametr to zestaw wirtualnych adresów IP, które usługa Kubernetes przypisuje do usług wewnętrznych [w][services] klastrze. Możesz użyć dowolnego zakresu adresów prywatnych, który spełnia następujące wymagania:

* Nie może znajdować się w zakresie adresów IP sieci wirtualnej klastra
* Nie może pokrywać się z innymi sieciami wirtualnymi, z którymi równorzędnie są równorzędne sieci wirtualne klastra
* Nie może pokrywać się z żadnymi lokalnymi ip
* Nie może znajdować się w zakresach `169.254.0.0/16` `172.30.0.0/16` , , , `172.31.0.0/16` lub `192.0.2.0/24`

Chociaż technicznie jest możliwe określenie zakresu adresów usługi w tej samej sieci wirtualnej co klaster, nie jest to zalecane. Nieprzewidywalne zachowanie może spowodować, jeśli zostaną użyte nakładające się zakresy adresów IP. Aby uzyskać więcej informacji, zobacz [sekcję często zadawanych](#frequently-asked-questions) pytań tego artykułu. Aby uzyskać więcej informacji na temat usług Kubernetes, zobacz [Usługi][services] w dokumentacji usługi Kubernetes.

**Adres IP usługi DNS kubernetes:** adres IP usługi DNS klastra. Ten adres musi znajdować się w *zakresie adresów usługi Kubernetes*. Nie używaj pierwszego adresu IP z zakresu adresów, takiego jak .1. Pierwszy adres w zakresie podsieci jest używany dla adresu *kubernetes.default.svc.cluster.local.*

**Adres mostka platformy Docker:** adres sieciowy mostka platformy Docker reprezentuje domyślny adres sieciowy mostka *docker0* obecny we wszystkich instalacjach platformy Docker. Chociaż *mostek docker0* nie jest używany przez klastry usługi AKS ani same zasobniki, należy ustawić ten adres, aby nadal obsługiwać scenariusze, takie jak *kompilacja platformy Docker* w klastrze usługi AKS. Należy wybrać cidr dla adresu sieciowego mostka platformy Docker, ponieważ w przeciwnym razie docker automatycznie wybierze podsieć, co może kolidować z innymi przewodnikami CIDR. Należy wybrać przestrzeń adresową, która nie koliduje z resztą woluminów CIDR w sieciach, w tym z przestrzenią CIDR usługi klastra i cidr zasobnika.

## <a name="configure-networking---cli"></a>Konfigurowanie sieci — interfejs wiersza polecenia

Podczas tworzenia klastra usługi AKS za pomocą interfejsu wiersza polecenia platformy Azure można również skonfigurować Azure CNI sieciowe. Użyj poniższych poleceń, aby utworzyć nowy klaster usługi AKS z włączoną Azure CNI sieci.

Najpierw pobierz identyfikator zasobu podsieci dla istniejącej podsieci, do której zostanie przyłączony klaster usługi AKS:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Użyj polecenia [az aks create][az-aks-create] z `--network-plugin azure` argumentem , aby utworzyć klaster z zaawansowaną siecią. Zaktualizuj wartość `--vnet-subnet-id` przy użyciu identyfikatora podsieci zebranego w poprzednim kroku:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>Konfigurowanie sieci — portal

Poniższy zrzut ekranu z Azure Portal przykład konfigurowania tych ustawień podczas tworzenia klastra usługi AKS:

! [Zaawansowana konfiguracja sieci w Azure Portal] [portal-01-networking-advanced]

## <a name="dynamic-allocation-of-ips-and-enhanced-subnet-support-preview"></a>Dynamiczna alokacja ip i rozszerzona obsługa podsieci (wersja zapoznawcza)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

> [!NOTE] 
> Ta funkcja w wersji zapoznawczej jest obecnie dostępna w następujących regionach:
>
> * Zachodnio-środkowe stany USA

Wadą tradycyjnej sieci CNI jest wyczerpanie adresów IP zasobników w miarę rozwoju klastra usługi AKS, co wymaga ponownego skompilowania całego klastra w większej podsieci. Nowa funkcja dynamicznej alokacji adresów IP w programie Azure CNI rozwiązuje ten problem przez przydzielenie adresów IP zasobników z podsieci niezależnej od podsieci hostujące klaster usługi AKS.  Oferuje ona następujące korzyści:

* **Lepsze wykorzystanie adresów IP:** adresy IP są przydzielane dynamicznie do zasobników klastra z podsieci Pod. Prowadzi to do lepszego wykorzystania ip w klastrze w porównaniu do tradycyjnego rozwiązania CNI, które powoduje statyczną alokację ip dla każdego węzła.  

* **Skalowalne i elastyczne:** podsieci węzłów i zasobników można skalować niezależnie. Podsieć pojedynczego zasobnika może być współużytkowana w wielu pulach węzłów klastra lub w wielu klastrach usługi AKS wdrożonych w tej samej sieci wirtualnej. Możesz również skonfigurować oddzielną podsieć zasobników dla puli węzłów.  

* **Wysoka wydajność:** ponieważ zasobniki mają przypisane wirtualne sieci IP, mają one bezpośrednią łączność z innymi zasobnikami i zasobami klastra w sieci wirtualnej. Rozwiązanie obsługuje bardzo duże klastry bez żadnego pogorszenia wydajności.

* **Oddzielne zasady sieci wirtualnej** dla zasobników: ponieważ zasobniki mają oddzielną podsieć, można skonfigurować dla nich oddzielne zasady sieci wirtualnej, które różnią się od zasad węzłów. Umożliwia to korzystanie z wielu przydatnych scenariuszy, takich jak zezwalanie na łączność z Internetem tylko dla zasobników, a nie dla węzłów, naprawianie źródłowego adresu IP zasobnika w puli węzłów przy użyciu nat sieci wirtualnej i filtrowanie ruchu między pulami węzłów przy użyciu sieciowych grup sieciowych.  

* **Zasady sieciowe platformy Kubernetes:** z tym nowym rozwiązaniem działają zarówno zasady sieci platformy Azure, jak i mechanizm Calico.  

### <a name="install-the-aks-preview-azure-cli"></a>Instalowanie interfejsu `aks-preview` wiersza polecenia platformy Azure

Potrzebne będzie rozszerzenie interfejsu wiersza polecenia platformy Azure *aks-preview.* Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure *aks-preview* za pomocą [polecenia az extension add.][az-extension-add] Możesz też zainstalować wszystkie dostępne aktualizacje za pomocą [polecenia az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-podsubnetpreview-preview-feature"></a>Rejestrowanie funkcji w `PodSubnetPreview` wersji zapoznawczej

Aby korzystać z tej funkcji, należy również włączyć `PodSubnetPreview` flagę funkcji w subskrypcji.

Zarejestruj `PodSubnetPreview` flagę funkcji za pomocą [polecenia az feature register,][az-feature-register] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "PodSubnetPreview"
```

Wyświetlanie stanu Zarejestrowane trwa kilka *minut.* Sprawdź stan rejestracji za pomocą [polecenia az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSubnetPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, odśwież rejestrację dostawcy *zasobów Microsoft.ContainerService* za pomocą [polecenia az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="additional-prerequisites"></a>Dodatkowe wymagania wstępne

Wymagania wstępne wymienione już dla Azure CNI nadal obowiązują, ale istnieje kilka dodatkowych ograniczeń:

* Obsługiwane są tylko klastry węzłów systemu Linux i pule węzłów.
* Aparat AKS i klastry DIY nie są obsługiwane.

### <a name="planning-ip-addressing"></a>Planowanie adresowania IP

W przypadku korzystania z tej funkcji planowanie jest znacznie prostsze. Ponieważ węzły i zasobniki są skalowane niezależnie, ich przestrzenie adresowe można również zaplanować oddzielnie. Ponieważ podsieci zasobników można skonfigurować do poziomu szczegółowości puli węzłów, klienci mogą zawsze dodać nową podsieć podczas dodawania puli węzłów. Zasobniki systemowe w puli klastra/węzła również odbierają ip z podsieci zasobników, więc to zachowanie należy uwzględnić.

Planowanie ip dla usług K8S i mostka platformy Docker pozostaje niezmienione.

### <a name="maximum-pods-per-node-in-a-cluster-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>Maksymalna liczba zasobników na węzeł w klastrze z dynamiczną alokacją ip i rozszerzoną obsługą podsieci

Wartości zasobników na węzeł w przypadku używania Azure CNI z dynamiczną alokacją ip nieco się zmieniły od tradycyjnego zachowania CNI:

|CNI|Metoda wdrażania|Domyślne|Konfigurowalne podczas wdrażania|
|--|--| :--: |--|
|Tradycyjne Azure CNI|Interfejs wiersza polecenia platformy Azure|30|Tak (maksymalnie 250)|
|Azure CNI z dynamiczną alokacją ip|Interfejs wiersza polecenia platformy Azure|250|Tak (maksymalnie 250)|

Wszystkie inne wskazówki związane z konfigurowaniem maksymalnej liczby węzłów na zasobnik pozostają takie same.

### <a name="additional-deployment-parameters"></a>Dodatkowe parametry wdrożenia

Wszystkie opisane powyżej parametry wdrożenia są nadal prawidłowe, z jednym wyjątkiem:

* Parametr **podsieci** odwołuje się teraz do podsieci powiązanej z węzłami klastra.
* Dodatkowy parametr **podsieć zasobnika** służy do określania podsieci, której adresy IP będą przydzielane dynamicznie do zasobników.

### <a name="configure-networking---cli-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>Konfigurowanie sieci — interfejs wiersza polecenia z dynamiczną alokacją ip i rozszerzoną obsługą podsieci

Używanie dynamicznej alokacji ip i rozszerzonej obsługi podsieci w klastrze jest podobne do domyślnej metody konfigurowania klastra Azure CNI. W poniższym przykładzie przedstawiono tworzenie nowej sieci wirtualnej z podsiecią dla węzłów i podsieci dla zasobników oraz tworzenie klastra, który używa usługi Azure CNI z dynamiczną alokacją ip i rozszerzoną obsługą podsieci. Pamiętaj, aby zastąpić zmienne, takie jak `$subscription` własnymi wartościami:

Najpierw utwórz sieć wirtualną z dwiema podsieciami:

```azurecli-interactive
$resourceGroup="myResourceGroup"
$vnet="myVirtualNetwork"

# Create our two subnet network 
az network vnet create -g $rg --name $vnet --address-prefixes 10.0.0.0/8 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name nodesubnet --address-prefixes 10.240.0.0/16 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name podsubnet --address-prefixes 10.241.0.0/16 -o none 
```

Następnie utwórz klaster, odwołując się do podsieci node przy użyciu funkcji `--vnet-subnet-id` i podsieć przy użyciu funkcji `--pod-subnet-id` :

```azurecli-interactive
$clusterName="myAKSCluster"
$location="eastus"
$subscription="aaaaaaa-aaaaa-aaaaaa-aaaa"

az aks create -n $clusterName -g $resourceGroup -l $location --max-pods 250 --node-count 2 --network-plugin azure --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/nodesubnet --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/podsubnet  
```

#### <a name="adding-node-pool"></a>Dodawanie puli węzłów

Podczas dodawania puli węzłów odwołuj się do podsieci node przy użyciu funkcji `--vnet-subnet-id` i podsieci zasobników przy użyciu . `--pod-subnet-id` Poniższy przykład tworzy dwie nowe podsieci, do których następnie odwołuje się utworzenie nowej puli węzłów:

```azurecli-interactive
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name node2subnet --address-prefixes 10.242.0.0/16 -o none 
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name pod2subnet --address-prefixes 10.243.0.0/16 -o none 

az aks nodepool add --cluster-name $clusterName -g $resourceGroup  -n newNodepool --max-pods 250 --node-count 2 --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/node2subnet  --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/pod2subnet --no-wait 
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Poniższe pytania i odpowiedzi dotyczą konfiguracji Azure CNI **sieci.**

* *Czy mogę wdrożyć maszyny wirtualne w podsieci klastra?*

  Tak.

* *Jaki źródłowy adres IP widzą systemy zewnętrzne dla ruchu pochodzącego z Azure CNI zasobnika z włączoną obsługą systemu?*

  Systemy w tej samej sieci wirtualnej co klaster usługi AKS widzą adres IP zasobnika jako adres źródłowy dla dowolnego ruchu z zasobnika. Systemy spoza sieci wirtualnej klastra AKS widzą adres IP węzła jako adres źródłowy dla dowolnego ruchu z zasobnika.

* *Czy można skonfigurować zasady sieci dla zasobnika?*

  Tak, zasady sieci kubernetes są dostępne w u usługach AKS. Aby rozpocząć pracę, zobacz [Bezpieczny ruch między zasobnikami przy użyciu zasad sieciowych w u usługi AKS.][network-policy]

* *Czy maksymalną liczbę zasobników można konfigurować w węźle?*

  Tak, podczas wdrażania klastra przy użyciu interfejsu wiersza polecenia platformy Azure lub Resource Manager szablonu. Zobacz [Maksymalna liczba zasobników na węzeł](#maximum-pods-per-node).

  Nie można zmienić maksymalnej liczby zasobników na węzeł w istniejącym klastrze.

* *Jak mogę skonfigurować dodatkowe właściwości podsieci utworzonej podczas tworzenia klastra usługi AKS? Na przykład punkty końcowe usługi.*

  Pełną listę właściwości sieci wirtualnej i podsieci tworzyć podczas tworzenia klastra AKS można skonfigurować na stronie standardowej konfiguracji sieci wirtualnej w Azure Portal.

* *Czy mogę użyć innej podsieci w sieci wirtualnej* klastra dla zakresu adresów usługi **Kubernetes?**

  Nie jest to zalecane, ale taka konfiguracja jest możliwa. Zakres adresów usługi to zestaw wirtualnych adresów IP (VIP), które usługa Kubernetes przypisuje do usług wewnętrznych w klastrze. Azure Networking nie ma wglądu w zakres adresów IP usługi klastra Kubernetes. Ze względu na brak widoczności zakresu adresów usług klastra można później utworzyć nową podsieć w sieci wirtualnej klastra, która nakłada się na zakres adresów usług. Jeśli takie nakładanie się wystąpi, usługa Kubernetes może przypisać usłudze adres IP, który jest już przez inny zasób w podsieci, co powoduje nieprzewidywalne zachowanie lub awarie. Upewniając się, że używasz zakresu adresów poza siecią wirtualną klastra, możesz uniknąć tego ryzyka nakładania się.

### <a name="dynamic-allocation-of-ip-addresses-and-enhanced-subnet-support-faqs"></a>Dynamiczna alokacja adresów IP i rozszerzonej obsługi podsieci : często zadawane pytania

Poniższe pytania i odpowiedzi dotyczą konfiguracji sieci Azure CNI w przypadku korzystania z dynamicznej alokacji adresów IP i **rozszerzonej obsługi podsieci.**

* *Czy mogę przypisać wiele podsieci zasobników do puli klastra/węzła?*

  Do klastra lub puli węzłów można przypisać tylko jedną podsieć. Jednak wiele klastrów lub pul węzłów może współdzielić jedną podsieć.

* *Czy można przypisać podsieci Zasobnik z innej sieci wirtualnej?*

  Podsieć zasobników powinna pochodzić z tej samej sieci wirtualnej co klaster.  

* *Czy niektóre pule węzłów w klastrze mogą używać tradycyjnej sieci CNI, podczas gdy inne używają nowej sieci CNI?*

  Cały klaster powinien używać tylko jednego typu CNI.

## <a name="aks-engine"></a>Aparat usługi AKS

[Azure Kubernetes Service Engine (AKS Engine)][aks-engine] to projekt typu open source, który generuje szablony Azure Resource Manager, których można użyć do wdrażania klastrów Kubernetes na platformie Azure.

Klastry Kubernetes utworzone za pomocą aparatu AKS obsługują wtyczki [kubenet][kubenet] [i Azure CNI.][cni-networking] W związku z tym oba scenariusze sieciowe są obsługiwane przez aparat usługi AKS.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat sieci w u usługi AKS można uzyskać w następujących artykułach:

* [Używanie statycznego adresu IP z Azure Kubernetes Service równoważenia obciążenia usługi (AKS)](static-ip.md)
* [Używanie wewnętrznego równoważenia obciążenia z Azure Container Service (AKS)](internal-lb.md)

* [Tworzenie podstawowego kontrolera ruchu wychodzącego z zewnętrzną łącznością sieciową][aks-ingress-basic]
* [Włączanie dodatku routingu aplikacji HTTP][aks-http-app-routing]
* [Tworzenie kontrolera ruchu wychodzącego, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
* [Tworzenie kontrolera ruchu wychodzącego z dynamicznym publicznym adresem IP i konfigurowanie szyfrowania Let's Encrypt w celu automatycznego generowania certyfikatów TLS][aks-ingress-tls]
* [Tworzenie kontrolera ruchu wychodzącego ze statycznym publicznym adresem IP i konfigurowanie szyfrowania Let's Encrypt w celu automatycznego generowania certyfikatów TLS][aks-ingress-static-tls]
<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png [portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
[system-node-pools]: use-system-pools.md
