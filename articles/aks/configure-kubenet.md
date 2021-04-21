---
title: Konfigurowanie sieci kubenet w Azure Kubernetes Service (AKS)
description: Dowiedz się, jak skonfigurować sieć kubenet (podstawowa) w u Azure Kubernetes Service (AKS) w celu wdrożenia klastra usługi AKS w istniejącej sieci wirtualnej i podsieci.
services: container-service
ms.topic: article
ms.date: 06/02/2020
ms.reviewer: nieberts, jomore
ms.openlocfilehash: c373e45c8607f10c36f40a23c776bd081bf13207
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789523"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Używanie sieci kubenet z własnymi zakresami adresów IP w Azure Kubernetes Service (AKS)

Domyślnie klastry usługi AKS używają rozwiązania [kubenet,][kubenet]a sieć wirtualna i podsieć platformy Azure są tworzone automatycznie. W *przypadku rozwiązania kubenet* węzły uzyskają adres IP z podsieci sieci wirtualnej platformy Azure. Zasobniki uzyskują adresy IP z przestrzeni adresowej, która jest logicznie różna od podsieci sieci wirtualnej platformy Azure, używanej przez węzły. Dzięki skonfigurowaniu translatora adresów sieciowych (NAT) zasobniki mogą uzyskać dostęp do zasobów w sieci wirtualnej platformy Azure. Źródłowy adres IP ruchu to nat do podstawowego adresu IP węzła. To podejście znacznie zmniejsza liczbę adresów IP, które należy zarezerwować w przestrzeni sieciowej na potrzeby zasobników.

W [Azure Container Networking Interface (CNI)][cni-networking]każdy zasobnik uzyskuje adres IP z podsieci i jest dostępny bezpośrednio. Te adresy IP muszą być unikatowe w przestrzeni sieciowej i muszą być planowane z wyprzedzeniem. Każdy węzeł ma parametr konfiguracji dla maksymalnej liczby zasobników, które obsługuje. Równoważna liczba adresów IP na węzeł jest następnie zarezerwowana z góry dla tego węzła. Takie podejście wymaga większego planowania i często prowadzi do wyczerpania adresów IP lub konieczności ponownego kompilowania klastrów w większej podsieci w przypadku wzrostu wymagań aplikacji. Możesz skonfigurować maksymalną liczbę zasobników, które można wdrożyć w węźle podczas tworzenia klastra lub podczas tworzenia nowych pul węzłów. Jeśli nie określisz maxPods podczas tworzenia nowych pul węzłów, otrzymasz wartość domyślną 110 dla kubenet.

W tym artykule pokazano, jak za pomocą sieci *kubenet* utworzyć podsieć sieci wirtualnej dla klastra usługi AKS i używać jej. Aby uzyskać więcej informacji na temat opcji i kwestii dotyczących sieci, zobacz [Network concepts for Kubernetes and AKS (Pojęcia dotyczące sieci dla platform Kubernetes i AKS).][aks-network-concepts]

## <a name="prerequisites"></a>Wymagania wstępne

* Sieć wirtualna klastra AKS musi zezwalać na wychodzącą łączność z Internetem.
* Nie należy tworzyć więcej niż jednego klastra usługi AKS w tej samej podsieci.
* Klastry usługi AKS nie mogą używać wartości , , ani dla zakresu adresów usługi `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` Kubernetes, zakresu adresów zasobników ani zakresu adresów sieci `192.0.2.0/24` wirtualnej klastra.
* Tożsamość klastra używana przez klaster usługi AKS musi mieć co [najmniej](../role-based-access-control/built-in-roles.md#network-contributor) rolę Współautor sieci w podsieci w sieci wirtualnej. Musisz również mieć odpowiednie uprawnienia, takie jak właściciel subskrypcji, aby utworzyć tożsamość klastra i przypisać do niego uprawnienia. Jeśli chcesz zdefiniować rolę [niestandardową zamiast](../role-based-access-control/custom-roles.md) wbudowanej roli Współautor sieci, wymagane są następujące uprawnienia:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> Aby używać pul węzłów systemu Windows Server, należy użyć Azure CNI. Użycie usługi kubenet jako modelu sieci nie jest dostępne dla kontenerów systemu Windows Server.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Musisz mieć zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Omówienie sieci kubenet z własną podsiecią

W wielu środowiskach zdefiniowano sieci wirtualne i podsieci z przydzielonymi zakresami adresów IP. Te zasoby sieci wirtualnej są używane do obsługi wielu usług i aplikacji. Aby zapewnić łączność sieciową, klastry AKS mogą używać rozwiązania *kubenet* (sieć podstawowa) lub Azure CNI *(sieć zaawansowana).*

W *przypadku rozwiązania kubenet* tylko węzły otrzymują adres IP w podsieci sieci wirtualnej. Zasobniki nie mogą komunikować się bezpośrednio ze sobą. Zamiast tego routing zdefiniowany przez użytkownika (UDR) i przekazywanie IP są używane do łączności między zasobnikami w węzłach. Domyślnie trasy zdefiniowane przez użytkownika i konfiguracja przekazywania IP są tworzone i utrzymywane przez usługę AKS, ale istnieje możliwość przyniesienia własnej tabeli tras do niestandardowego zarządzania [trasami.][byo-subnet-route-table] Można również wdrożyć zasobniki za usługą, która odbiera przypisany adres IP i równoważy obciążenie ruchu aplikacji. Na poniższym diagramie przedstawiono sposób, w jaki węzły usługi AKS otrzymują adres IP w podsieci sieci wirtualnej, ale nie w zasobnikach:

![Model sieci Kubenet z klastrem AKS](media/use-kubenet/kubenet-overview.png)

Platforma Azure obsługuje maksymalnie 400 tras w trasie UDR, więc nie można mieć klastra usługi AKS większego niż 400 węzłów. Węzły wirtualne [usługi][virtual-nodes] AKS i zasady sieci platformy Azure nie są obsługiwane w przypadku *platformy Kubenet.*  Możesz użyć zasad [sieciowych Calico,][calico-network-policies]ponieważ są one obsługiwane przez kubenet.

W *Azure CNI* zasobnik otrzymuje adres IP w podsieci IP i może bezpośrednio komunikować się z innymi zasobnikami i usługami. Klastry mogą być tak duże, jak określony zakres adresów IP. Jednak zakres adresów IP musi być zaplanowany z wyprzedzeniem, a wszystkie adresy IP są używane przez węzły usługi AKS na podstawie maksymalnej liczby zasobników, które mogą obsługiwać. Zaawansowane funkcje sieciowe i scenariusze, takie jak [węzły wirtualne][virtual-nodes] lub zasady sieciowe (Azure lub Calico), są obsługiwane w Azure CNI *.*

### <a name="limitations--considerations-for-kubenet"></a>Ograniczenia & dotyczące kubenet

* W projekcie kubenet jest wymagany dodatkowy przeskok, co zwiększa niewielkie opóźnienie komunikacji zasobników.
* Tabele tras i trasy zdefiniowane przez użytkownika są wymagane do korzystania z usługi kubenet, co zwiększa złożoność operacji.
* Bezpośrednie adresowanie zasobników nie jest obsługiwane w przypadku rozwiązania kubenet ze względu na projekt kubenet.
* W Azure CNI klastrach wiele klastrów Kubenet nie może współdzielić podsieci.
* Funkcje, **które nie są obsługiwane w sieci kubenet,** obejmują:
   * [Zasady sieci platformy Azure](use-network-policies.md#create-an-aks-cluster-and-enable-network-policy), ale zasady sieci Calico są obsługiwane w usłudze kubenet
   * [Pule węzłów systemu Windows](./windows-faq.md)
   * [Dodatek dla węzłów wirtualnych](virtual-nodes.md#network-requirements)

### <a name="ip-address-availability-and-exhaustion"></a>Dostępność i wyczerpanie adresów IP

W *Azure CNI* typowe problemy to zbyt mały zakres przypisanych adresów IP, aby dodać kolejne węzły podczas skalowania lub uaktualniania klastra. Zespół sieciowy może również nie być w stanie wystawiać wystarczająco dużego zakresu adresów IP, aby obsługiwać oczekiwane wymagania aplikacji.

W ramach naruszenia zabezpieczeń można utworzyć klaster usługi AKS, który używa platformy *kubenet* i nawiązać połączenie z istniejącą podsiecią sieci wirtualnej. Takie podejście umożliwia węzłom odbieranie zdefiniowanych adresów IP bez konieczności rezerwowania z góry dużej liczby adresów IP dla wszystkich potencjalnych zasobników, które mogą być uruchamiane w klastrze.

Za *pomocą rozwiązania kubenet* można użyć znacznie mniejszego zakresu adresów IP i obsługiwać duże klastry i wymagania aplikacji. Na przykład nawet w przypadku zakresu adresów IP */27* w podsieci można uruchomić klaster 20–25 węzłów z wystarczającą ilość miejsca do skalowania lub uaktualniania. Ten rozmiar klastra będzie obsługiwać *maksymalnie 2200–2750* zasobników (domyślnie maksymalnie 110 zasobników na węzeł). Maksymalna liczba zasobników na węzeł, które można skonfigurować za pomocą usługi *kubenet* w u usługi AKS wynosi 110.

Następujące podstawowe obliczenia porównują różnicę w modelach sieciowych:

- **kubenet** — prosty zakres adresów IP */24* może obsługiwać maksymalnie *251 węzłów* w klastrze (każda podsieć sieci wirtualnej platformy Azure rezerwuje pierwsze trzy adresy IP na czas operacji zarządzania)
  - Ta liczba węzłów może obsługiwać *maksymalnie 27 610* zasobników (domyślnie maksymalnie 110 zasobników na węzeł z *kubenetem)*
- **Azure CNI** — ten sam podstawowy *zakres podsieci /24* może obsługiwać maksymalnie *8 węzłów* w klastrze
  - Ta liczba węzłów może obsługiwać maksymalnie *240* zasobników (domyślnie maksymalnie 30 zasobników na węzeł z Azure CNI *)*

> [!NOTE]
> Te maksymalne wartości nie biorą pod uwagę operacji uaktualniania ani skalowania. W praktyce nie można uruchomić maksymalnej liczby węzłów, które obsługuje zakres adresów IP podsieci. Należy pozostawić niektóre adresy IP dostępne do użycia podczas skalowania operacji uaktualniania.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Komunikacja równorzędna sieci wirtualnych i połączenia usługi ExpressRoute

Aby zapewnić łączność lokalną, metody *sieci kubenet* i *Azure-CNI* mogą korzystać z komunikacji równorzędnej sieci wirtualnych platformy [Azure][vnet-peering] lub [połączeń usługi ExpressRoute.][express-route] Należy starannie zaplanować zakresy adresów IP, aby zapobiec nakładaniu się i nieprawidłowemu routingowi ruchu. Na przykład wiele sieci lokalnych używa zakresu adresów *10.0.0.0/8* anonsowanego za pośrednictwem połączenia expressRoute. Zaleca się utworzenie klastrów usługi AKS w podsieciach sieci wirtualnej platformy Azure spoza tego zakresu adresów, takich jak *172.16.0.0/16.*

### <a name="choose-a-network-model-to-use"></a>Wybieranie modelu sieci do użycia

Wybór wtyczki sieciowej do użycia dla klastra usługi AKS zwykle zapewnia równowagę między elastycznością a zaawansowanymi potrzebami w zakresie konfiguracji. Poniższe zagadnienia ułatwiają konspekt, kiedy każdy model sieciowy może być najbardziej odpowiedni.

Użyj *kubenet,* gdy:

- Masz ograniczoną przestrzeń adresów IP.
- Większość komunikacji zasobników znajduje się w klastrze.
- Nie potrzebujesz zaawansowanych funkcji usługi AKS, takich jak węzły wirtualne lub usługa Azure Network Policy.  Użyj [zasad sieciowych firmy Calico.][calico-network-policies]

Użyj *Azure CNI,* gdy:

- Masz dostępną przestrzeń adresów IP.
- Większość komunikacji zasobników jest z zasobami spoza klastra.
- Nie chcesz zarządzać trasami zdefiniowanymi przez użytkownika dla łączności z zasobnikami.
- Potrzebne są zaawansowane funkcje usługi AKS, takie jak węzły wirtualne lub usługa Azure Network Policy.  Użyj [zasad sieciowych firmy Calico.][calico-network-policies]

Aby uzyskać więcej informacji, które pomogą Ci zdecydować, którego modelu sieci użyć, zobacz Porównanie modeli [sieciowych i ich zakresu pomocy technicznej.][network-comparisons]

## <a name="create-a-virtual-network-and-subnet"></a>Tworzenie sieci wirtualnej i podsieci

Aby rozpocząć korzystanie z *platformy kubenet i* własnej podsieci sieci wirtualnej, najpierw utwórz grupę zasobów za pomocą [polecenia az group create.][az-group-create] Poniższy przykład tworzy grupę zasobów o *nazwie myResourceGroup* w *lokalizacji eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Jeśli nie masz istniejącej sieci wirtualnej i podsieci do użycia, utwórz te zasoby sieciowe za pomocą [polecenia az network vnet create.][az-network-vnet-create] W poniższym przykładzie sieć wirtualna nosi nazwę *myVnet* z prefiksem *adresu 192.168.0.0/16.* Zostanie utworzona podsieć o nazwie *myAKSSubnet* z prefiksem *adresu 192.168.1.0/24.*

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Tworzenie jednostki usługi i przypisywanie uprawnień

Jednostka usługi Azure Active Directory zezwala klastrowi usługi AKS na interakcje z innymi zasobami platformy Azure. Główna usługa musi mieć uprawnienia do zarządzania siecią wirtualną i podsiecią używaną przez węzły usługi AKS. Aby utworzyć jednostkę usługi, użyj [polecenia az ad sp create-for-rbac:][az-ad-sp-create-for-rbac]

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Następujące przykładowe dane wyjściowe pokazują identyfikator aplikacji i hasło dla jednostki usługi. Te wartości są używane w dodatkowych krokach w celu przypisania roli do jednostki usługi, a następnie utworzenia klastra usługi AKS:

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Aby przypisać poprawne delegowanie w pozostałych krokach, użyj poleceń [az network vnet show][az-network-vnet-show] i az network [vnet subnet show,][az-network-vnet-subnet-show] aby uzyskać wymagane identyfikatory zasobów. Te identyfikatory zasobów są przechowywane jako zmienne i przywołyowane w pozostałych krokach:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Teraz przypisz jednostkę usługi  dla uprawnień współautora sieci klastra AKS w sieci wirtualnej za pomocą [polecenia az role assignment create.][az-role-assignment-create] Podaj własną, *\<appId>* jak pokazano w danych wyjściowych poprzedniego polecenia, aby utworzyć jednostkę usługi:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role "Network Contributor"
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Tworzenie klastra usługi AKS w sieci wirtualnej

Utworzono sieć wirtualną i podsieć oraz utworzono i przypisano uprawnienia dla jednostki usługi do korzystania z tych zasobów sieciowych. Teraz utwórz klaster usługi AKS w sieci wirtualnej i podsieci za pomocą [polecenia az aks create.][az-aks-create] Zdefiniuj własną jednostkę usługi i , jak pokazano w danych wyjściowych poprzedniego polecenia, *\<appId>* *\<password>* aby utworzyć jednostkę usługi.

Następujące zakresy adresów IP są również definiowane w ramach procesu tworzenia klastra:

* Element *--service-cidr służy* do przypisywania usług wewnętrznych w klastrze usługi AKS adresu IP. Ten zakres adresów IP powinien być przestrzenią adresową, która nie jest w użyciu w innym miejscu w środowisku sieciowym, w tym zakresami sieci lokalnej, jeśli łączysz się z sieciami wirtualnymi platformy Azure lub planujesz je połączyć za pomocą usługi Express Route lub połączenia sieci VPN typu lokacja-lokacja.

* Adres *--dns-service-ip* powinien być adresem *.10* zakresu adresów IP usługi.

* *--pod-cidr* powinna być dużą przestrzenią adresową, która nie jest w użyciu w innym miejscu w środowisku sieciowym. Ten zakres obejmuje wszystkie zakresy sieci lokalnych, jeśli łączysz się z sieciami wirtualnymi platformy Azure lub planujesz je połączyć za pomocą usługi Express Route lub połączenia sieci VPN typu lokacja-lokacja.
    * Ten zakres adresów musi być wystarczająco duży, aby obsłużyć liczbę węzłów, do których chcesz skalować w górę. Nie można zmienić tego zakresu adresów po wdrożeniu klastra, jeśli potrzebujesz więcej adresów dla dodatkowych węzłów.
    * Zakres adresów IP zasobników służy do przypisywania przestrzeni adresowej */24* do każdego węzła w klastrze. W poniższym przykładzie *--pod-cidr* *10.244.0.0/16* przypisuje pierwszy węzeł *10.244.0.0/24,* drugi węzeł *10.244.1.0/24,* a trzeci węzeł *10.244.2.0/24*.
    * W przypadku skalowania lub uaktualniania klastra platforma Azure nadal przypisuje zakres adresów IP zasobników do każdego nowego węzła.
    
* Adres *--docker-bridge-address umożliwia* węzłom usługi AKS komunikowanie się z podstawową platformą zarządzania. Ten adres IP nie może znajdować się w zakresie adresów IP sieci wirtualnej klastra i nie powinien pokrywać się z innymi zakresami adresów w sieci.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

> [!Note]
> Jeśli chcesz włączyć klaster usługi AKS w celu dołączyć zasady [sieciowe Calico,][calico-network-policies] możesz użyć następującego polecenia.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet --network-policy calico \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

Podczas tworzenia klastra usługi AKS sieciowa grupa zabezpieczeń i tabela tras są tworzone automatycznie. Te zasoby sieciowe są zarządzane przez płaszczyznę sterowania usługi AKS. Sieciowa grupa zabezpieczeń jest automatycznie skojarzona z wirtualnymi kartami sieciowym w węzłach. Tabela tras jest automatycznie skojarzona z podsiecią sieci wirtualnej. Reguły sieciowych grup zabezpieczeń i tabele tras są automatycznie aktualizowane podczas tworzenia i uwidoczniania usług.

## <a name="bring-your-own-subnet-and-route-table-with-kubenet"></a>Bring your own subnet and route table with kubenet (Bring your own subnet and route table with kubenet )Bring your own subnet and route table with kubenet

W przypadku sieci kubenet tabela tras musi istnieć w podsieciach klastra. Usługi AKS obsługują przenosząc własną istniejącą podsieć i tabelę tras.

Jeśli niestandardowa podsieć nie zawiera tabeli tras, usługa AKS utworzy ją automatycznie i doda do niej reguły w całym cyklu życia klastra. Jeśli podsieć niestandardowa zawiera tabelę tras podczas tworzenia klastra, usługa AKS potwierdza istniejącą tabelę tras podczas operacji klastra i odpowiednio dodaje/aktualizuje reguły dla operacji dostawcy chmury.

> [!WARNING]
> Reguły niestandardowe można dodawać do niestandardowej tabeli tras i aktualizować. Jednak reguły są dodawane przez dostawcę chmury kubernetes, którego nie można aktualizować ani usuwać. Reguły, takie jak 0.0.0.0/0, muszą zawsze istnieć w danej tabeli tras i być mapować na element docelowy bramy internetowej, na przykład urządzenie WUS lub inną bramę ruchu wychodzącego. Podczas aktualizowania reguł należy zachować ostrożność, że modyfikowane są tylko reguły niestandardowe.

Dowiedz się więcej o konfigurowaniu [niestandardowej tabeli tras.][custom-route-table]

Sieć Kubenet wymaga uporządkowanych reguł tabeli tras w celu pomyślnego rozsyłania żądań. Ze względu na ten projekt tabele tras muszą być starannie konserwowane dla każdego klastra, który na nim opiera. Wiele klastrów nie może współdzielić tabeli tras, ponieważ woluminy CIDR zasobników z różnych klastrów mogą się nakładać, co powoduje nieoczekiwany i przerwany routing. Podczas konfigurowania wielu klastrów w tej samej sieci wirtualnej lub poświęcania sieci wirtualnej na każdy klaster należy uwzględnić następujące ograniczenia.

Ograniczenia:

* Uprawnienia należy przypisać przed utworzeniem klastra. Upewnij się, że używasz jednostki usługi z uprawnieniami do zapisu w niestandardowej podsieci i niestandardowej tabeli tras.
* Przed utworzeniem klastra usługi AKS należy skojarzyć tabelę tras niestandardowych z podsiecią.
* Po utworzeniu klastra nie można zaktualizować skojarzonego zasobu tabeli tras. Chociaż nie można zaktualizować zasobu tabeli tras, reguły niestandardowe można modyfikować w tabeli tras.
* Każdy klaster usługi AKS musi używać pojedynczej, unikatowej tabeli tras dla wszystkich podsieci skojarzonych z klastrem. Nie można ponownie użyć tabeli tras z wieloma klastrami ze względu na możliwość nakładania się reguł CIDR zasobników i konfliktowych reguł routingu.

Po utworzeniu niestandardowej tabeli tras i skojarzeniu jej z podsiecią w sieci wirtualnej można utworzyć nowy klaster usługi AKS, który używa tabeli tras.
Musisz użyć identyfikatora podsieci dla miejsca, w którym planujesz wdrożyć klaster usługi AKS. Ta podsieć musi być również skojarzona z niestandardową tabelą tras.

```azurecli-interactive
# Find your subnet ID
az network vnet subnet list --resource-group
                            --vnet-name
                            [--subscription]
```

```azurecli-interactive
# Create a kubernetes cluster with with a custom subnet preconfigured with a route table
az aks create -g MyResourceGroup -n MyManagedCluster --vnet-subnet-id MySubnetID
```

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu klastra usługi AKS w istniejącej podsieci sieci wirtualnej można teraz używać klastra w zwykły sposób. Rozpoczynanie pracy z [tworzeniem nowych aplikacji przy użyciu programu Helm][develop-helm] lub [wdrażanie istniejących aplikacji przy użyciu programu Helm.][use-helm]

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az_group_create
[az-network-vnet-create]: /cli/azure/network/vnet#az_network_vnet_create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az_network_vnet_show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[byo-subnet-route-table]: #bring-your-own-subnet-and-route-table-with-kubenet
[develop-helm]: quickstart-helm.md
[use-helm]: kubernetes-helm.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
[custom-route-table]: ../virtual-network/manage-route-table.md
