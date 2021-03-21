---
title: Konfigurowanie sieci korzystającą wtyczki kubenet w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak skonfigurować sieć korzystającą wtyczki kubenet (podstawowa) w usłudze Azure Kubernetes Service (AKS) w celu wdrożenia klastra AKS w istniejącej sieci wirtualnej i podsieci.
services: container-service
ms.topic: article
ms.date: 06/02/2020
ms.reviewer: nieberts, jomore
ms.openlocfilehash: a32b06163f446fe0df7f1f1581d741d889cf8e9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583517"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Korzystanie z sieci korzystającą wtyczki kubenet z własnymi zakresami adresów IP w usłudze Azure Kubernetes Service (AKS)

Domyślnie klastry AKS korzystają z [korzystającą wtyczki kubenet][kubenet], a sieć wirtualna platformy Azure jest tworzona dla Ciebie. W przypadku *korzystającą wtyczki kubenet* węzły uzyskują adres IP z podsieci sieci wirtualnej platformy Azure. Zasobniki uzyskują adresy IP z przestrzeni adresowej, która jest logicznie różna od podsieci sieci wirtualnej platformy Azure, używanej przez węzły. Dzięki skonfigurowaniu translatora adresów sieciowych (NAT) zasobniki mogą uzyskać dostęp do zasobów w sieci wirtualnej platformy Azure. Źródłowy adres IP ruchu to NAT do podstawowego adresu IP węzła. Takie podejście znacznie zmniejsza liczbę adresów IP, które należy zarezerwować w przestrzeni sieciowej, aby można było użyć używanych przez nią zasobników.

[Usługa Azure Container Network Interface (CNI)][cni-networking]w każdym przypadku Pobiera adres IP z podsieci i jest dostępny bezpośrednio. Te adresy IP muszą być unikatowe w przestrzeni sieci i muszą być planowane z wyprzedzeniem. Każdy węzeł ma parametr konfiguracji dla maksymalnej liczby obsługiwanych przez nią zasobników. Równoważna liczba adresów IP na węzeł jest następnie rezerwowana na początku dla tego węzła. Takie podejście wymaga większego planowania i często prowadzi do wyczerpania adresów IP lub trzeba ponownie skompilować klastry w większej podsieci, w miarę wzrostu wymagań aplikacji. Można skonfigurować maksymalną liczbę zestawów do wdrożenia w węźle podczas tworzenia klastra lub podczas tworzenia nowych pul węzłów. Jeśli nie określisz maxPods podczas tworzenia nowych pul węzłów, otrzymasz wartość domyślną 110 dla korzystającą wtyczki kubenet.

W tym artykule pokazano, jak za pomocą sieci *korzystającą wtyczki kubenet* utworzyć i używać podsieci sieci wirtualnej dla klastra AKS. Aby uzyskać więcej informacji o opcjach sieciowych i zagadnieniach, zobacz [pojęcia sieci dotyczące Kubernetes i AKS][aks-network-concepts].

## <a name="prerequisites"></a>Wymagania wstępne

* Sieć wirtualna klastra AKS musi zezwalać na wychodzącą łączność z Internetem.
* Nie należy tworzyć więcej niż jednego klastra AKS w tej samej podsieci.
* Klastry AKS nie mogą korzystać z zakresów adresów usługi Kubernetes,,, ani ich zakresu, z zakresu adresów `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` `192.0.2.0/24` lub zakresu adresów sieci wirtualnej klastra.
* Tożsamość klastra używana przez klaster AKS musi mieć co najmniej rolę [współautor sieci](../role-based-access-control/built-in-roles.md#network-contributor) w podsieci w sieci wirtualnej. Musisz również mieć odpowiednie uprawnienia, takie jak właściciel subskrypcji, aby utworzyć tożsamość klastra i przypisać im uprawnienia. Jeśli chcesz zdefiniować [rolę niestandardową](../role-based-access-control/custom-roles.md) , zamiast korzystać z wbudowanej roli współautor sieci, wymagane są następujące uprawnienia:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> Aby korzystać z pul węzłów systemu Windows Server, należy użyć usługi Azure CNI. Użycie korzystającą wtyczki kubenet jako modelu sieci nie jest dostępne w przypadku kontenerów systemu Windows Server.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Omówienie sieci korzystającą wtyczki kubenet z własną podsiecią

W wielu środowiskach zdefiniowano sieci wirtualne i podsieci z przydzielonymi zakresami adresów IP. Te zasoby sieci wirtualnej są używane do obsługi wielu usług i aplikacji. Aby zapewnić łączność sieciową, klastry AKS mogą korzystać z *korzystającą wtyczki kubenet* (Basic Network) lub Azure CNI (*Advanced Network*).

W przypadku *korzystającą wtyczki kubenet* tylko węzły otrzymują adres IP w podsieci sieci wirtualnej. Nie mogą komunikować się ze sobą bezpośrednio. Zamiast tego, zdefiniowane przez użytkownika Routing (UDR) i przekazywanie adresów IP są używane do łączności między różnymi węzłami. Domyślnie konfiguracja przesyłania dalej UDR i IP jest tworzona i utrzymywana przez usługę AKS, ale należy wybrać opcję [przeprowadzenia własnej tabeli tras na potrzeby zarządzania trasami niestandardowymi][byo-subnet-route-table]. Można również wdrożyć moduły równoważenia obciążenia za usługą, która odbiera przypisany adres IP i równoważy obciążenie dla aplikacji. Na poniższym diagramie przedstawiono sposób, w jaki węzły AKS odbierają adres IP w podsieci sieci wirtualnej, ale nie są to:

![Model sieci korzystającą wtyczki kubenet z klastrem AKS](media/use-kubenet/kubenet-overview.png)

Platforma Azure obsługuje maksymalnie 400 tras w UDR, więc nie można mieć klastra AKS większego niż 400 węzłów. AKS [węzły wirtualne][virtual-nodes] i zasady sieci platformy Azure nie są obsługiwane w programie *korzystającą wtyczki kubenet*.  [Zasad sieciowych Calico][calico-network-policies]można użyć, ponieważ są one obsługiwane przez program korzystającą wtyczki kubenet.

W przypadku *usługi Azure CNI* każdy z nich otrzymuje adres IP w podsieci IP i może komunikować się bezpośrednio z innymi identyfikatorami i usługami. Klastry mogą być tak duże jak zakres adresów IP, który określisz. Jednak zakres adresów IP musi być planowany z wyprzedzeniem, a wszystkie adresy IP są używane przez węzły AKS na podstawie maksymalnej liczby jednostek, które mogą obsługiwać. Zaawansowane funkcje sieciowe i scenariusze, takie jak [węzły wirtualne][virtual-nodes] lub zasady sieciowe (Azure lub Calico), są obsługiwane za pomocą *usługi Azure CNI*.

### <a name="limitations--considerations-for-kubenet"></a>Ograniczenia & zagadnienia dotyczące korzystającą wtyczki kubenet

* Wymagany jest dodatkowy przeskok w projekcie korzystającą wtyczki kubenet, który dodaje niewielkie opóźnienie do komunikacji pod względem.
* Tabele tras i trasy zdefiniowane przez użytkownika są wymagane do korzystania z korzystającą wtyczki kubenet, który zwiększa złożoność operacji.
* Bezpośrednie adresowanie pod nie jest obsługiwane dla korzystającą wtyczki kubenet ze względu na projekt korzystającą wtyczki kubenet.
* W przeciwieństwie do klastrów usługi Azure CNI, wiele klastrów korzystającą wtyczki kubenet nie może współdzielić podsieci.
* Funkcje **nieobsługiwane w programie korzystającą wtyczki kubenet** obejmują:
   * [Zasady sieci platformy Azure](use-network-policies.md#create-an-aks-cluster-and-enable-network-policy), ale zasady sieciowe Calico są obsługiwane w usłudze korzystającą wtyczki kubenet
   * [Pule węzłów systemu Windows](./windows-faq.md)
   * [Dodatek węzłów wirtualnych](virtual-nodes.md#network-requirements)

### <a name="ip-address-availability-and-exhaustion"></a>Dostępność i wyczerpanie adresów IP

W przypadku *usługi Azure CNI* typowym problemem jest zbyt mały zakres adresów IP, aby dodać dodatkowe węzły podczas skalowania lub uaktualniania klastra. Zespół sieci może również nie być w stanie wydać dużego zakresu adresów IP, aby obsługiwał oczekiwane wymagania aplikacji.

Ze względu na kompromis można utworzyć klaster AKS, który używa *korzystającą wtyczki kubenet* i nawiązać połączenie z istniejącą podsiecią sieci wirtualnej. Takie podejście pozwala węzłom odbierać zdefiniowane adresy IP bez konieczności zastrzegania dużej liczby adresów IP na początku dla wszystkich potencjalnych zasobników, które można uruchomić w klastrze.

Za pomocą *korzystającą wtyczki kubenet* można użyć znacznie mniejszego zakresu adresów IP i zapewnić obsługę dużych klastrów i wymagań aplikacji. Na przykład, nawet z zakresem adresów IP */27* w podsieci, można uruchomić klaster węzłów 20-25 z wystarczającą ilością miejsca do skalowania lub uaktualnienia. Ten rozmiar klastra będzie obsługiwał do *2200-2750owych* zasobników (z domyślną maksymalną równą 110 zasobników na węzeł). Maksymalna liczba zasobników na węzeł, który można skonfigurować za pomocą *korzystającą wtyczki kubenet* w AKS, to 110.

Poniższe podstawowe obliczenia porównują różnicę w modelach sieci:

- **korzystającą wtyczki kubenet** — prosty */24* zakres adresów IP może obsługiwać do *251* węzłów w klastrze (Każda podsieć sieci wirtualnej platformy Azure rezerwuje trzy pierwsze adresy IP dla operacji zarządzania)
  - Ta liczba węzłów może obsługiwać do *27 610* zasobników (z domyślną maksymalnie 110 zasobników na węzeł z *korzystającą wtyczki kubenet*)
- **Azure CNI** — ten sam zakres podsieci w warstwie Podstawowa */24* może obsługiwać maksymalnie *8* węzłów w klastrze.
  - Ta liczba węzłów może obsługiwać maksymalnie *240* zasobników (z domyślną maksymalnie 30 zasobników na węzeł przy użyciu *usługi Azure CNI*)

> [!NOTE]
> Te wartości maksymalne nie uwzględniają operacji uaktualniania ani skalowania. W tej chwili nie można uruchomić maksymalnej liczby węzłów obsługiwanej przez zakres adresów IP podsieci. Należy pozostawić niektóre adresy IP dostępne do użycia podczas skalowania operacji uaktualniania.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Wirtualne sieci równorzędne i połączenia ExpressRoute

Aby zapewnić łączność lokalną, podejścia do sieci *korzystającą wtyczki kubenet* i *Azure-CNI* mogą korzystać z połączeń [równorzędnych sieci wirtualnych platformy Azure][vnet-peering] lub [ExpressRoute][express-route]. Należy uważnie zaplanować zakresy adresów IP, aby zapobiec nakładaniu się i niepoprawnym kierowaniu ruchu sieciowego. Na przykład wiele sieci lokalnych używa zakresu adresów *10.0.0.0/8* , który jest anonsowany za pośrednictwem połączenia usługi ExpressRoute. Zaleca się utworzenie klastrów AKS w podsieciach sieci wirtualnej platformy Azure spoza tego zakresu adresów, takich jak *172.16.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Wybierz model sieciowy do użycia

Wybór wtyczki sieciowej używanej przez klaster AKS jest zwykle kompromisem między elastycznością i zaawansowaną konfiguracją. Poniższe zagadnienia ułatwiają tworzenie konspektu, gdy każdy model sieci może być najbardziej odpowiedni.

Użyj *korzystającą wtyczki kubenet* , gdy:

- Masz ograniczoną przestrzeń adresów IP.
- Większość komunikacji pod kątem jest w klastrze.
- Nie potrzebujesz zaawansowanych funkcji AKS, takich jak węzły wirtualne lub zasady sieci platformy Azure.  Użyj [zasad sieciowych Calico][calico-network-policies].

Użyj *usługi Azure CNI* , gdy:

- Dostępna jest przestrzeń adresów IP.
- Większość komunikacji pod względem źródła jest zasobami spoza klastra.
- Nie chcesz zarządzać trasami zdefiniowanymi przez użytkownika pod kątem łączności pod.
- Potrzebujesz AKS zaawansowanych funkcji, takich jak węzły wirtualne lub zasady sieci platformy Azure.  Użyj [zasad sieciowych Calico][calico-network-policies].

Aby uzyskać więcej informacji ułatwiających decydowanie o modelu sieci, który ma być używany, zobacz [porównanie modeli sieci i ich zakresu obsługi][network-comparisons].

## <a name="create-a-virtual-network-and-subnet"></a>Tworzenie sieci wirtualnej i podsieci

Aby rozpocząć korzystanie z usługi *korzystającą wtyczki kubenet* i własnej podsieci sieci wirtualnej, najpierw utwórz grupę zasobów za pomocą polecenia [AZ Group Create][az-group-create] . Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Jeśli nie masz istniejącej sieci wirtualnej i podsieci do użycia, Utwórz te zasoby sieciowe za pomocą polecenia [AZ Network VNET Create][az-network-vnet-create] . W poniższym przykładzie Sieć wirtualna nosi nazwę *myVnet* z prefiksem adresu *192.168.0.0/16*. Zostanie utworzona podsieć o nazwie *myAKSSubnet* z prefiksem adresu *192.168.1.0/24*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Tworzenie nazwy głównej usługi i przypisywanie uprawnień

Jednostka usługi Azure Active Directory zezwala klastrowi usługi AKS na interakcje z innymi zasobami platformy Azure. Nazwa główna usługi musi mieć uprawnienia do zarządzania siecią wirtualną i podsiecią używaną przez węzły AKS. Aby utworzyć nazwę główną usługi, użyj polecenia [AZ AD Sp Create-for-RBAC][az-ad-sp-create-for-rbac] :

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Poniższe przykładowe dane wyjściowe przedstawiają identyfikator aplikacji i hasło dla nazwy głównej usługi. Te wartości są używane w dodatkowych krokach, aby przypisać rolę do jednostki usługi, a następnie utworzyć klaster AKS:

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Aby przypisać poprawne delegowania w pozostałych krokach, użyj polecenia [AZ Network VNET show][az-network-vnet-show] i [AZ Network VNET Subnet show][az-network-vnet-subnet-show] , aby uzyskać wymagane identyfikatory zasobów. Te identyfikatory zasobów są przechowywane jako zmienne i przywoływane w pozostałych krokach:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Teraz Przypisz jednostkę usługi do uprawnień *współautora sieci* klastra AKS w sieci wirtualnej za pomocą polecenia [AZ role Assign Create][az-role-assignment-create] . Podaj własne *\<appId>* , jak pokazano w danych wyjściowych poprzedniego polecenia, aby utworzyć jednostkę usługi:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role "Network Contributor"
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Tworzenie klastra AKS w sieci wirtualnej

Utworzono sieć wirtualną i podsieć, a następnie utworzono i przypisano uprawnienia do jednostki usługi w celu używania tych zasobów sieciowych. Teraz Utwórz klaster AKS w sieci wirtualnej i podsieci przy użyciu polecenia [AZ AKS Create][az-aks-create] . Zdefiniuj własną nazwę główną usługi *\<appId>* i *\<password>* , jak pokazano w danych wyjściowych poprzedniego polecenia, aby utworzyć nazwę główną usługi.

Następujące zakresy adresów IP są również zdefiniowane jako część procesu tworzenia klastra:

* *--Service-CIDR* służy do przypisywania wewnętrznych usług w klastrze AKS jako adres IP. Ten zakres adresów IP powinien być przestrzenią adresową, która nie jest używana w innym miejscu w środowisku sieciowym, łącznie z dowolnymi zakresami sieci lokalnych, Jeśli nawiązujesz połączenie lub planujesz nawiązać połączenie z sieciami wirtualnymi platformy Azure przy użyciu trasy Express lub połączenia sieci VPN typu lokacja-lokacja.

* Wartość *--DNS-Service-IP* musi być adresem *.10* zakresu adresów IP usługi.

* Wartość *--pod-CIDR* powinna być dużą przestrzenią adresową, która nie jest używana w innym miejscu w środowisku sieciowym. Ten zakres obejmuje wszystkie lokalne zakresy sieci w przypadku nawiązania połączenia lub zaplanowania połączenia z sieciami wirtualnymi platformy Azure przy użyciu trasy Express lub połączenia sieci VPN typu lokacja-lokacja.
    * Ten zakres adresów musi być wystarczająco duży, aby pomieścić liczbę węzłów, do których można skalować w górę. Nie można zmienić tego zakresu adresów, gdy klaster zostanie wdrożony, jeśli potrzebujesz więcej adresów dla dodatkowych węzłów.
    * Zakres adresów IP pod jest używany do przypisywania przestrzeni adresowej */24* do każdego węzła w klastrze. W poniższym przykładzie, *--pod-CIDR* *10.244.0.0/16* przypisuje pierwszy węzeł *10.244.0.0/24*, drugi węzeł *10.244.1.0/24* i trzeci węzeł *10.244.2.0/24*.
    * W miarę skalowania lub uaktualniania klastra platforma Azure nadal przypisuje zakres adresów IP pod każdym nowym węzłem.
    
* Wartość *--Docker-Bridge-Address* umożliwia węzłom AKS komunikowanie się z podstawową platformą zarządzania. Ten adres IP nie może należeć do zakresu adresów IP sieci wirtualnej w klastrze i nie powinien nakładać się na inne zakresy adresów używane w sieci.

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
> Aby umożliwić klastrowi AKS uwzględnienie [zasad sieciowych Calico][calico-network-policies] , można użyć następującego polecenia.

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

Podczas tworzenia klastra AKS są tworzone automatycznie sieciowe grupy zabezpieczeń i trasy. Te zasoby sieciowe są zarządzane przez płaszczyznę kontroli AKS. Sieciowa Grupa zabezpieczeń jest automatycznie skojarzona z wirtualnymi kartami sieciowymi w węzłach. Tabela tras jest automatycznie skojarzona z podsiecią sieci wirtualnej. Reguły sieciowej grupy zabezpieczeń i tabele tras są automatycznie aktualizowane podczas tworzenia i uwidaczniania usług.

## <a name="bring-your-own-subnet-and-route-table-with-kubenet"></a>Przenoszenie własnej podsieci i tabeli tras za pomocą korzystającą wtyczki kubenet

W przypadku korzystającą wtyczki kubenet tabela tras musi istnieć w podsieciach klastra. AKS obsługuje nadawanie własnej istniejącej podsieci i tabeli tras.

Jeśli podsieć niestandardowa nie zawiera tabeli tras, AKS tworzy ją dla Ciebie i dodaje do niej reguły w całym cyklu życia klastra. Jeśli podsieć niestandardowa zawiera tabelę tras podczas tworzenia klastra, AKS przyjmuje istniejącą tabelę tras podczas operacji klastra i odpowiednio dodaje/aktualizuje reguły dla operacji dostawcy chmury.

> [!WARNING]
> Reguły niestandardowe można dodać do niestandardowej tabeli tras i zaktualizować. Jednak reguły są dodawane przez dostawcę chmury Kubernetes, który nie może zostać zaktualizowany ani usunięty. Reguły, takie jak 0.0.0.0/0, muszą zawsze istnieć w danej tabeli tras i mapowane na obiekt docelowy bramy internetowej, np. urządzenie WUS lub inną bramę ruchu wychodzącego. Należy zachować ostrożność podczas aktualizowania reguł, które są modyfikowane tylko dla reguł niestandardowych.

Dowiedz się więcej o konfigurowaniu [niestandardowej tabeli tras][custom-route-table].

Sieć korzystającą wtyczki kubenet wymaga zorganizowanych reguł tabeli tras, aby pomyślnie kierować żądania. Ze względu na ten projekt tabele tras muszą być starannie utrzymywane dla każdego klastra, który opiera się na nim. Wiele klastrów nie może współdzielić tabeli tras, ponieważ ścieżki CIDR z różnych klastrów mogą się nakładać, co powoduje nieoczekiwane i przerwane Routing. Podczas konfigurowania wielu klastrów w tej samej sieci wirtualnej lub przydzielenia sieci wirtualnej do każdego klastra upewnij się, że są uwzględniane następujące ograniczenia.

Ograniczenia:

* Przed utworzeniem klastra należy przypisać uprawnienia. Upewnij się, że używasz jednostki usługi z uprawnieniami do zapisu w niestandardowej podsieci i niestandardowej tabeli tras.
* Aby można było utworzyć klaster AKS, niestandardowa tabela tras musi być skojarzona z podsiecią.
* Nie można zaktualizować skojarzonego zasobu tabeli tras po utworzeniu klastra. Nie można zaktualizować zasobu tabeli tras, reguły niestandardowe można modyfikować w tabeli tras.
* Każdy klaster AKS musi korzystać z jednej unikatowej tabeli tras dla wszystkich podsieci skojarzonych z klastrem. Nie można ponownie użyć tabeli tras z wieloma klastrami ze względu na potencjał nakładający się w CIDR i sprzeczne reguły routingu.

Po utworzeniu niestandardowej tabeli tras i skojarzeniu jej z podsiecią w sieci wirtualnej można utworzyć nowy klaster AKS, który korzysta z tabeli tras.
Należy użyć identyfikatora podsieci dla miejsca, w którym planujesz wdrożyć klaster AKS. Ta podsieć musi być również skojarzona z niestandardową tabelą tras.

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

W przypadku klastra AKS wdrożonego w istniejącej podsieci sieci wirtualnej można teraz używać klastra jako normalnego. Rozpocznij pracę z [tworzeniem nowych aplikacji przy użyciu usługi Helm][develop-helm] lub [Wdróż istniejące aplikacje za pomocą usługi Helm][use-helm].

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[byo-subnet-route-table]: #bring-your-own-subnet-and-route-table-with-kubenet
[develop-helm]: quickstart-helm.md
[use-helm]: kubernetes-helm.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
[custom-route-table]: ../virtual-network/manage-route-table.md
