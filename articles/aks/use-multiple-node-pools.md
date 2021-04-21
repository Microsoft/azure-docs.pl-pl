---
title: Używanie wielu pul węzłów w Azure Kubernetes Service (AKS)
description: Dowiedz się, jak utworzyć wiele pul węzłów dla klastra w u Azure Kubernetes Service (AKS) i zarządzać nimi
services: container-service
ms.topic: article
ms.date: 02/11/2021
ms.openlocfilehash: b7b54ccf6662e172ebfe95a84189df5e8e6e990f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832252"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Tworzenie wielu puli węzłów dla klastra w usłudze Azure Kubernetes Service (AKS) i zarządzanie nimi

W Azure Kubernetes Service (AKS) węzły tej samej konfiguracji są grupowane w *pule węzłów.* Te pule węzłów zawierają bazowe maszyny wirtualne, na których są uruchamiane aplikacje. Początkowa liczba węzłów i ich rozmiar (SKU) jest definiowana podczas tworzenia klastra usługi AKS, który tworzy [pulę węzłów systemu][use-system-pool]. Aby obsługiwać aplikacje o różnych wymaganiach obliczeniowych lub magazynowych, można utworzyć dodatkowe *pule węzłów użytkownika.* Pule węzłów systemu służą do hostowania krytycznych zasobników systemowych, takich jak CoreDNS i tunnelfront. Pule węzłów użytkownika służą do hostowania zasobników aplikacji. Zasobniki aplikacji można jednak zaplanować w pulach węzłów systemowych, jeśli w klastrze usługi AKS ma być tylko jedna pula. Pule węzłów użytkownika to miejsce, w którym umieszcza się zasobniki specyficzne dla aplikacji. Możesz na przykład użyć tych dodatkowych pul węzłów użytkownika, aby zapewnić procesory GPU dla aplikacji intensywnie obciążanych obliczeniami lub uzyskać dostęp do magazynu SSD o wysokiej wydajności.

> [!NOTE]
> Ta funkcja umożliwia większą kontrolę nad tworzeniem wielu pul węzłów i zarządzaniem nimi. W związku z tym do tworzenia/aktualizowania/usuwania są wymagane oddzielne polecenia. Wcześniej operacje klastra wykonywane za pośrednictwem interfejsu API managedCluster lub były używane i były jedyną opcją zmiany płaszczyzny sterowania `az aks create` `az aks update` i pojedynczej puli węzłów. Ta funkcja uwidacznia oddzielny zestaw operacji dla pul agentów za pośrednictwem interfejsu API puli agentów i wymaga użycia zestawu poleceń do wykonywania operacji na `az aks nodepool` poszczególnych pulach węzłów.

W tym artykule pokazano, jak utworzyć wiele pul węzłów w klastrze usługi AKS i zarządzać nimi.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Musisz zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="limitations"></a>Ograniczenia

Podczas tworzenia klastrów usługi AKS, które obsługują wiele pul węzłów i zarządzają nimi, obowiązują następujące ograniczenia:

* Zobacz [Limity przydziału, ograniczenia rozmiaru maszyny wirtualnej i dostępność regionów w Azure Kubernetes Service (AKS).][quotas-skus-regions]
* Pule węzłów systemowych można usunąć, o ile w klastrze usługi AKS znajduje się inna pula węzłów systemowych.
* Pule systemowe muszą zawierać co najmniej jeden węzeł, a pule węzłów użytkownika mogą zawierać zero lub więcej węzłów.
* Klaster usługi AKS musi używać usługi load balancer w standardowych sku do korzystania z wielu pul węzłów. Funkcja ta nie jest obsługiwana w przypadku podstawowych równoważenia obciążenia SKU.
* Klaster usługi AKS musi używać zestawów skalowania maszyn wirtualnych dla węzłów.
* Nazwa puli węzłów może zawierać tylko małe litery alfanumeryczne i musi zaczynać się małą literą. W przypadku pul węzłów systemu Linux długość musi zawierać od 1 do 12 znaków, a w przypadku pul węzłów systemu Windows długość musi zawierać od 1 do 6 znaków.
* Wszystkie pule węzłów muszą znajdować się w tej samej sieci wirtualnej.
* Podczas tworzenia wielu pul węzłów w czasie tworzenia klastra wszystkie wersje usługi Kubernetes używane przez pule węzłów muszą być zgodne z zestawem wersji dla płaszczyzny sterowania. Można ją zaktualizować po aprowizowania klastra przy użyciu operacji puli węzłów.

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

> [!Important]
> Jeśli uruchamiasz pojedynczą pulę węzłów systemowych dla klastra usługi AKS w środowisku produkcyjnym, zalecamy użycie co najmniej trzech węzłów dla puli węzłów.

Aby rozpocząć, utwórz klaster usługi AKS z jedną pulą węzłów. W poniższym przykładzie użyto [polecenia az group create,][az-group-create] aby utworzyć grupę zasobów o nazwie *myResourceGroup* w *regionie eastus.* Klaster usługi AKS o *nazwie myAKSCluster* jest następnie tworzony przy użyciu [polecenia az aks create.][az-aks-create]

> [!NOTE]
> W *przypadku korzystania z* wielu pul węzłów nie jest **obsługiwana** podstawowa sku usługi równoważenia obciążenia. Domyślnie klastry AKS są tworzone przy użyciu standardowej wersji SKU usługi *równoważenia* obciążenia z poziomu interfejsu wiersza polecenia platformy Azure i Azure Portal.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --load-balancer-sku standard
```

Utworzenie klastra trwa kilka minut.

> [!NOTE]
> Aby zapewnić niezawodne działanie klastra, należy uruchomić co najmniej 2 (dwa) węzły w domyślnej puli węzłów, ponieważ podstawowe usługi systemowe są uruchomione w tej puli węzłów.

Gdy klaster jest gotowy, użyj [polecenia az aks get-credentials,][az-aks-get-credentials] aby uzyskać poświadczenia klastra do użycia z programem `kubectl` :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Dodawanie puli węzłów

Klaster utworzony w poprzednim kroku ma jedną pulę węzłów. Dodajmy drugą pulę węzłów przy użyciu [polecenia az aks nodepool add.][az-aks-nodepool-add] Poniższy przykład tworzy pulę węzłów o *nazwie mynodepool z* *3 węzłami:*

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> Nazwa puli węzłów musi zaczynać się małą literą i może zawierać tylko znaki alfanumeryczne. W przypadku pul węzłów systemu Linux długość musi zawierać od 1 do 12 znaków, a w przypadku pul węzłów systemu Windows długość musi zawierać od 1 do 6 znaków.

Aby wyświetlić stan pul węzłów, użyj polecenia [az aks node pool list][az-aks-nodepool-list] i określ nazwę grupy zasobów i klastra:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

Następujące przykładowe dane wyjściowe pokazują, *że pula mynodepool* została pomyślnie utworzona z trzema węzłami w puli węzłów. Podczas tworzenia klastra usługi AKS w poprzednim kroku utworzono domyślną *bufor1* węzła z licznikiem *2 węzłów.*

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Jeśli podczas dodawania puli węzłów nie zostanie określona wartość *VmSize,* domyślny rozmiar to Standard_D2s_v3 pul węzłów systemu Windows i Standard_DS2_v2 *pul* węzłów systemu Linux.  Jeśli nie *określono wersji OrchestratorVersion,* domyślnie jest to ta sama wersja co płaszczyzna sterowania.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Dodawanie puli węzłów z unikatową podsiecią (wersja zapoznawcza)

Obciążenie może wymagać podziału węzłów klastra na oddzielne pule w celu izolacji logicznej. Ta izolacja może być obsługiwana z oddzielnymi podsieciami przeznaczonymi dla każdej puli węzłów w klastrze. Może to spełniać wymagania, takie jak brak ciągłej przestrzeni adresowej sieci wirtualnej do podziału między pule węzłów.

#### <a name="limitations"></a>Ograniczenia

* Wszystkie podsieci przypisane do puli węzłów muszą należeć do tej samej sieci wirtualnej.
* Zasobniki systemowe muszą mieć dostęp do wszystkich węzłów/zasobników w klastrze, aby zapewnić krytyczne funkcje, takie jak rozpoznawanie nazw DNS i tunelowanie dzienników kubectl/exec/serwer proxy przesyłania dalej portów.
* W przypadku rozwinięcia sieci wirtualnej po utworzeniu klastra należy zaktualizować klaster (wykonać dowolną zarządzaną operację clster, ale operacje puli węzłów nie są liczone) przed dodaniem podsieci poza oryginalnym cidr. W przypadku dodawania puli agentów przez aKS wystąpi błąd, chociaż pierwotnie na to pozwoliliśmy. Jeśli nie wiesz, jak uzgodnić plik klastra z biletem pomocy technicznej. 
* Zasady sieci calico nie są obsługiwane. 
* Usługa Azure Network Policy nie jest obsługiwana.
* Kube-proxy oczekuje jednego ciągłego cidr i używa go w przypadku trzech optmizations. Zobacz to [k.E.P.](https://github.com/kubernetes/enhancements/tree/master/keps/sig-network/2450-Remove-knowledge-of-pod-cluster-CIDR-from-iptables-rules) i --cluster-cidr [tutaj,](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/) aby uzyskać szczegółowe informacje. W usłudze azure cni podsieć pierwszej puli węzłów zostanie nadana serwerowi kube-proxy. 

Aby utworzyć pulę węzłów z dedykowaną podsiecią, przekaż identyfikator zasobu podsieci jako dodatkowy parametr podczas tworzenia puli węzłów.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Uaktualnianie puli węzłów

> [!NOTE]
> Operacje uaktualniania i skalowania w klastrze lub puli węzłów nie mogą wystąpić jednocześnie, jeśli zostanie zwrócony błąd. Zamiast tego każdy typ operacji musi zostać ukończony w zasobie docelowym przed następnym żądaniem dla tego samego zasobu. Przeczytaj więcej na ten temat w naszym [przewodniku rozwiązywania problemów.](./troubleshooting.md#im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade)

Polecenia w tej sekcji wyjaśniają, jak uaktualnić pojedynczą określoną pulę węzłów. Relacja między uaktualnieniem wersji kubernetes płaszczyzny sterowania a pulą węzłów jest wyjaśniona w [poniższej sekcji](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> Wersja obrazu systemu operacyjnego puli węzłów jest powiązana z wersją klastra Kubernetes. Po uaktualnieniu klastra otrzymasz tylko uaktualnienia obrazu systemu operacyjnego.

Ponieważ w tym przykładzie istnieją dwie pule węzłów, musimy użyć az [aks nodepool upgrade,][az-aks-nodepool-upgrade] aby uaktualnić pulę węzłów. Aby wyświetlić dostępne uaktualnienia, użyj [az aks get-upgrades][az-aks-get-upgrades]

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Uaktualnijmy *mynodepool*. Użyj polecenia [az aks nodepool upgrade,][az-aks-nodepool-upgrade] aby uaktualnić pulę węzłów, jak pokazano w poniższym przykładzie:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

Ponownie wyświetlić stan pul węzłów za pomocą [polecenia az aks node pool list.][az-aks-nodepool-list] W poniższym przykładzie *pokazano, że stan mynodepool* jest w stanie *Uaktualnianie* *do KUBERNETES_VERSION*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "KUBERNETES_VERSION",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Uaktualnienie węzłów do określonej wersji może potrwać kilka minut.

Najlepszym rozwiązaniem jest uaktualnienie wszystkich pul węzłów w klastrze usługi AKS do tej samej wersji rozwiązania Kubernetes. Domyślnym zachowaniem programu jest uaktualnienie wszystkich pul węzłów wraz z płaszczyzną sterowania w `az aks upgrade` celu osiągnięcia tego wyrównania. Możliwość uaktualniania poszczególnych pul węzłów umożliwia uaktualnienie stopniowe i planowanie zasobników między pulami węzłów w celu zachowania czasu pracy aplikacji w ramach wymienionych powyżej ograniczeń.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Uaktualnianie płaszczyzny sterowania klastra z wieloma pulami węzłów

> [!NOTE]
> W przypadku usługi Kubernetes używany jest standardowy schemat [wersji semantycznych.](https://semver.org/) Numer wersji jest wyrażony jako *x.y.z,* gdzie *x* jest wersją główną, *y* jest wersją pomocniczą, a *z* jest wersją poprawkową. Na przykład w wersji *1.12.6* 1 jest wersją główną, 12 jest wersją pomocniczą, a 6 jest wersją poprawkową. Wersja kubernetes płaszczyzny sterowania i początkowa pula węzłów są ustawiane podczas tworzenia klastra. Wszystkie dodatkowe pule węzłów mają ustawioną wersję usługi Kubernetes po dodaniu ich do klastra. Wersje kubernetes mogą się różnić między pulami węzłów, a także między pulą węzłów a płaszczyzną sterowania.

Klaster usługi AKS ma dwa obiekty zasobów klastra ze skojarzonymi wersjami usługi Kubernetes.

1. Wersja kubernetes płaszczyzny sterowania klastra.
2. Pula węzłów z wersją kubernetes.

Płaszczyzna sterowania mapuje do jednej lub wielu pul węzłów. Zachowanie operacji uaktualniania zależy od tego, które polecenie interfejsu wiersza polecenia platformy Azure jest używane.

Uaktualnianie płaszczyzny sterowania usługi AKS wymaga użycia programu `az aks upgrade` . To polecenie uaktualnia wersję płaszczyzny sterowania i wszystkie pule węzłów w klastrze.

Wydanie polecenia `az aks upgrade` z flagą `--control-plane-only` uaktualnia tylko płaszczyznę sterowania klastra. Żadna ze skojarzonych pul węzłów w klastrze nie zostanie zmieniona.

Uaktualnianie poszczególnych pul węzłów wymaga użycia programu `az aks nodepool upgrade` . To polecenie uaktualnia tylko docelową pulę węzłów z określoną wersją kubernetes

### <a name="validation-rules-for-upgrades"></a>Reguły weryfikacji dla uaktualnień

Prawidłowe uaktualnienia kubernetes dla płaszczyzny sterowania i pul węzłów klastra są weryfikowane przez następujące zestawy reguł.

* Reguły uaktualniania pul węzłów dla prawidłowych wersji:
   * Wersja puli węzłów musi mieć tę samą *wersję główną* co płaszczyzna sterowania.
   * Wersja pomocnicza *puli węzłów* musi znajdować się w *dwóch* wersjach pomocniczych wersji płaszczyzny sterowania.
   * Wersja puli węzłów nie może być większa niż wersja `major.minor.patch` kontroli.

* Reguły przesyłania operacji uaktualniania:
   * Nie można obniżyć poziomu płaszczyzny sterowania ani wersji kubernetes puli węzłów.
   * Jeśli nie określono wersji kubernetes puli węzłów, zachowanie zależy od używanego klienta. Deklaracja w Resource Manager powraca do istniejącej wersji zdefiniowanej dla puli węzłów, jeśli jest używana, jeśli nie ustawiono wersji płaszczyzny sterowania.
   * W danym momencie można uaktualnić lub skalować płaszczyznę sterowania albo pulę węzłów. Nie można jednocześnie przesłać wielu operacji na jednej płaszczyźnie sterowania lub zasobie puli węzłów.

## <a name="scale-a-node-pool-manually"></a>Ręczne skalowanie puli węzłów

W przypadku zmiany wymagań obciążenia aplikacji może być konieczne skalowanie liczby węzłów w puli węzłów. Liczbę węzłów można skalować w górę lub w dół.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Aby skalować liczbę węzłów w puli węzłów, użyj [polecenia az aks node pool scale.][az-aks-nodepool-scale] Poniższy przykład skaluje liczbę węzłów w *puli mynodepool* do *5:*

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Ponownie wyświetlić stan pul węzłów za pomocą [polecenia az aks node pool list.][az-aks-nodepool-list] W poniższym przykładzie *pokazano, że wartość mynodepool* jest w stanie *Skalowanie* z nową licznikiem *5 węzłów:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Ukończenie operacji skalowania może potrwać kilka minut.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Automatyczne skalowanie określonej puli węzłów przez włączenie funkcji automatycznego skalowania klastra

Usługa AKS oferuje oddzielną funkcję automatycznego skalowania pul węzłów z funkcją o nazwie [cluster autoscaler](cluster-autoscaler.md). Tę funkcję można włączyć dla puli węzłów z unikatową minimalną i maksymalną skalą na pulę węzłów. Dowiedz się, [jak używać funkcji automatycznego skalowania klastra na pulę węzłów.](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)

## <a name="delete-a-node-pool"></a>Usuwanie puli węzłów

Jeśli pula nie jest już potrzebna, możesz ją usunąć i usunąć bazowe węzły maszyny wirtualnej. Aby usunąć pulę węzłów, użyj [polecenia az aks node pool delete][az-aks-nodepool-delete] i określ nazwę puli węzłów. Poniższy przykład usuwa puli *mynoodepool* utworzonej w poprzednich krokach:

> [!CAUTION]
> Nie ma żadnych opcji odzyskiwania w przypadku utraty danych, które mogą wystąpić podczas usuwania puli węzłów. Jeśli nie można zaplanować zasobników w innych pulach węzłów, te aplikacje są niedostępne. Upewnij się, że pula węzłów nie jest usuwana, gdy w użyciu aplikacje nie mają kopii zapasowych danych ani nie można jej uruchamiać w innych pulach węzłów w klastrze.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Następujące przykładowe dane wyjściowe polecenia [az aks node pool list][az-aks-nodepool-list] pokazują, że *pula mynodepool* ma *stan Usuwanie:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Usunięcie węzłów i puli węzłów trwa kilka minut.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Określanie rozmiaru maszyny wirtualnej dla puli węzłów

W poprzednich przykładach tworzenia puli węzłów dla węzłów utworzonych w klastrze był używany domyślny rozmiar maszyny wirtualnej. Bardziej powszechnym scenariuszem jest tworzenie pul węzłów z różnymi rozmiarami i możliwościami maszyn wirtualnych. Można na przykład utworzyć pulę węzłów zawierającą węzły z dużymi ilościami procesora CPU lub pamięci albo pulę węzłów, która zapewnia obsługę procesora GPU. W następnym kroku użyjemy funkcji [taints i tolerations,](#setting-nodepool-taints) aby poinformować harmonogram usługi Kubernetes, jak ograniczyć dostęp do zasobników, które mogą być uruchamiane w tych węzłach.

W poniższym przykładzie utworzysz pulę węzłów opartą na procesorze graficznym GPU, która używa *Standard_NC6* maszyny wirtualnej. Te maszyny wirtualne są obsługiwane przez kartę NVIDIA Tesla K80. Aby uzyskać informacje na temat dostępnych rozmiarów maszyn wirtualnych, zobacz [Sizes for Linux virtual machines in Azure (Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure).][vm-sizes]

Ponownie utwórz pulę węzłów za pomocą [polecenia az aks node pool add.][az-aks-nodepool-add] Tym razem określ nazwę *gpunodepool* i użyj parametru , aby określić `--node-vm-size` *Standard_NC6* rozmiar:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Następujące przykładowe dane wyjściowe polecenia [az aks node pool list][az-aks-nodepool-list] pokazują, że wartość *gpunodepool* to *Tworzenie* węzłów o określonej wartości *VmSize*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Pomyślne utworzenia puli *gpunodepool* może potrwać kilka minut.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Określanie wartości, etykiety lub tagu dla puli węzłów

Podczas tworzenia puli węzłów można dodać do tej puli węzłów etykiety, etykiety lub tagi. Po dodaniu etykiety lub tagu wszystkie węzły w tej puli węzłów również uzyskają ten tag, etykietę lub etykietę.

> [!IMPORTANT]
> Dodawanie etykiet, etykiet lub tagów do węzłów powinno odbywać się dla całej puli węzłów przy użyciu funkcji `az aks nodepool` . Stosowanie usterek, lableli lub tagów do poszczególnych węzłów w puli węzłów przy użyciu programu `kubectl` nie jest zalecane.  

### <a name="setting-nodepool-taints"></a>Ustawianie wartości węzłów puli

Aby utworzyć pulę węzłów z awarii, użyj [az aks nodepool add][az-aks-nodepool-add]. Określ nazwę *taintnp i* użyj parametru , aby określić `--node-taints` parametr *sku=gpu:NoSchedule* dla tej wartości.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

> [!NOTE]
> Awarii można ustawić tylko dla pul węzłów podczas tworzenia puli węzłów.

Następujące przykładowe dane wyjściowe polecenia [az aks nodepool list][az-aks-nodepool-list] pokazują, że *taintnp* to *Tworzenie* węzłów z określonym *węzłemTaints:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  [
      "sku=gpu:NoSchedule"
    ],
    ...
  },
 ...
]
```

Informacje o tajemnej funkcji są widoczne na platformie Kubernetes w celu obsługi reguł planowania dla węzłów. Harmonogram usługi Kubernetes może używać najechań i tolerancji w celu ograniczenia obciążeń, które mogą być uruchamiane w węzłach.

* W **węźle** jest stosowana nieścisła, która wskazuje, że można na nich zaplanować tylko określone zasobniki.
* Tolerancja **jest następnie** stosowana do zasobnika, który pozwala im *tolerować* nieumyślne wyniszczenie węzła.

Aby uzyskać więcej informacji na temat korzystania z zaawansowanych funkcji zaplanowanych na platformie Kubernetes, zobacz Najlepsze rozwiązania dotyczące zaawansowanych funkcji harmonogramu w [u programie AKS][taints-tolerations]

W poprzednim kroku podczas tworzenia puli węzłów zastosowano wartość *sku=gpu:NoSchedule.* W poniższym podstawowym przykładzie manifest YAML używa tolerancji, aby umożliwić harmonogramowi Kubernetes uruchamianie zasobnika NGINX w węźle w tej puli węzłów.

Utwórz plik o nazwie `nginx-toleration.yaml` i skopiuj go w poniższym przykładzie YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Zaplanuj zasobnik przy użyciu `kubectl apply -f nginx-toleration.yaml` polecenia :

```console
kubectl apply -f nginx-toleration.yaml
```

Zaplanowanie zasobnika i ściągnięcie obrazu NGINX może potrwać kilka sekund. Użyj polecenia [kubectl describe pod,][kubectl-describe] aby wyświetlić stan zasobnika. Następujące skrócone przykładowe dane wyjściowe pokazują, że zastosowano tolerancję *sku=gpu:NoSchedule.* W sekcji zdarzeń harmonogram przypisał zasobnik do węzła *aks-taintnp-28993262-vmss000000:*

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                Message
  ----    ------     ----   ----                -------
  Normal  Scheduled  4m48s  default-scheduler   Successfully assigned default/mypod to aks-taintnp-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet             pulling image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Pulled     4m43s  kubelet             Successfully pulled image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Created    4m40s  kubelet             Created container
  Normal  Started    4m40s  kubelet             Started container
```

W węzłach w stanie taintnp można zaplanować tylko zasobniki, dla których zastosowano tę *tolerancję.* Każdy inny zasobnik zostanie zaplanowany w *puli węzłów nodepool1.* Jeśli tworzysz dodatkowe pule węzłów, możesz użyć dodatkowych najechań i tolerancji, aby ograniczyć to, jakie zasobniki można zaplanować w tych zasobach węzłów.

### <a name="setting-nodepool-labels"></a>Ustawianie etykiet puli węzłów

Podczas tworzenia puli węzłów można również dodawać etykiety do puli węzłów. Etykiety ustawione w puli węzłów są dodawane do każdego węzła w puli węzłów. Te [etykiety są widoczne na platformie Kubernetes][kubernetes-labels] do obsługi reguł planowania dla węzłów.

Aby utworzyć pulę węzłów z etykietą, użyj [az aks nodepool add][az-aks-nodepool-add]. Określ nazwę *labelnp* i użyj parametru , `--labels` aby określić *etykiety dept=IT* i *costcenter=9999.*

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Etykietę można ustawić tylko dla pul węzłów podczas tworzenia puli węzłów. Etykiety muszą być również parą klucz/wartość i mieć [prawidłową składnię][kubernetes-label-syntax].

Następujące przykładowe dane wyjściowe polecenia [az aks nodepool list][az-aks-nodepool-list] pokazują, że *labelnp* to *Tworzenie* węzłów z *określonymi etykietami nodeLabels:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

### <a name="setting-nodepool-azure-tags"></a>Ustawianie tagów platformy Azure dla puli węzłów

Tag platformy Azure można zastosować do pul węzłów w klastrze usługi AKS. Tagi stosowane do puli węzłów są stosowane do każdego węzła w puli węzłów i utrwalane za pomocą uaktualnień. Tagi są również stosowane do nowych węzłów dodawanych do puli węzłów podczas operacji skalowania w zewnątrz. Dodanie tagu może ułatwić wykonywanie zadań, takich jak śledzenie zasad lub szacowanie kosztów.

Tagi platformy Azure mają klucze bez uwzględniania liter dla operacji, na przykład podczas pobierania tagu przez wyszukiwanie klucza. W takim przypadku tag z danym kluczem zostanie zaktualizowany lub pobrany niezależnie od wielkości liter. W wartościach tagów jest wielkość liter.

Jeśli w uchęce AKS ustawiono wiele tagów z identycznymi kluczami, ale z inną wielkością liter, używany tag jest pierwszym tagiem w kolejności alfabetycznej. Na przykład `{"Key1": "val1", "kEy1": "val2", "key1": "val3"}` wyniki i są `Key1` `val1` ustawiane.

Utwórz pulę węzłów przy użyciu [narzędzia az aks nodepool add][az-aks-nodepool-add]. Określ nazwę *tagnodepool* i użyj parametru , aby określić `--tag` wartości *dept=IT* i *costcenter=9999* dla tagów.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Można również użyć `--tags` parametru podczas korzystania [z polecenia az aks nodepool update,][az-aks-nodepool-update] a także podczas tworzenia klastra. Podczas tworzenia klastra parametr stosuje tag do początkowej puli `--tags` węzłów utworzonej za pomocą klastra. Wszystkie nazwy tagów muszą być zgodne z ograniczeniami z [tematu Organizowanie zasobów platformy Azure przy użyciu tagów.][tag-limitation] Zaktualizowanie puli węzłów za pomocą `--tags` parametru aktualizuje wszystkie istniejące wartości tagów i dołącza wszystkie nowe tagi. Jeśli na przykład pula węzłów zawierała tagi *dept=IT* i *costcenter=9999* i zaktualizowano ją przy użyciu tagów *team=dev* i *costcenter=111,* pula nodepool miałaby wartość *dept=IT,* *costcenter=111* i *team=dev* dla tagów.

Następujące przykładowe dane wyjściowe polecenia [az aks nodepool list][az-aks-nodepool-list] pokazują, że *tagnodepool* to *Tworzenie* węzłów z określonym *tagiem*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Zarządzanie pulami węzłów przy użyciu Resource Manager węzłów

Jeśli używasz szablonu Azure Resource Manager do tworzenia zasobów i zarządzania nimi, zazwyczaj możesz zaktualizować ustawienia w szablonie i ponownie je wdusić, aby zaktualizować zasób. W przypadku pul węzłów w u usługi AKS nie można zaktualizować początkowego profilu puli węzłów po utworzeniu klastra usługi AKS. To zachowanie oznacza, że nie można zaktualizować istniejącego szablonu Resource Manager, wprowadzić zmiany w pulach węzłów i ponownie je wduszyć. Zamiast tego należy utworzyć oddzielny szablon usługi Resource Manager który aktualizuje tylko pule węzłów dla istniejącego klastra usługi AKS.

Utwórz szablon, taki jak `aks-agentpools.json` i wklej poniższy przykładowy manifest. Ten przykładowy szablon konfiguruje następujące ustawienia:

* Aktualizuje *pulę węzłów* systemu Linux o *nazwie myagentpool,* aby uruchomić trzy węzły.
* Ustawia węzły w puli węzłów do uruchamiania usługi Kubernetes w *wersji 1.15.7.*
* Definiuje rozmiar węzła jako *Standard_DS2_v2*.

Edytuj te wartości zgodnie z potrzebami, aby w razie potrzeby aktualizować, dodawać lub usuwać pule węzłów:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

Wd wdrażaj ten szablon za pomocą [polecenia az deployment group create,][az-deployment-group-create] jak pokazano w poniższym przykładzie. Zostanie wyświetlony monit o nazwę i lokalizację istniejącego klastra usługi AKS:

```azurecli-interactive
az deployment group create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> Tag można dodać do puli węzłów, dodając właściwość *tagu* w szablonie, jak pokazano w poniższym przykładzie.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Aktualizacja klastra usługi AKS może potrwać kilka minut w zależności od ustawień i operacji puli węzłów, które zdefiniowano w szablonie Resource Manager węzłów.

## <a name="assign-a-public-ip-per-node-for-your-node-pools"></a>Przypisywanie publicznego adresu IP na węzeł dla pul węzłów

Węzły usługi AKS nie wymagają własnych publicznych adresów IP do komunikacji. Jednak scenariusze mogą wymagać od węzłów w puli węzłów odbierania własnych dedykowanych publicznych adresów IP. Częstym scenariuszem są obciążenia związane z grami, w których konsola musi nawiązaniu bezpośredniego połączenia z maszyną wirtualną w chmurze w celu zminimalizowania przeskoków. Ten scenariusz można osiągnąć w u usługach AKS przy użyciu publicznego adresu IP węzła.

Najpierw utwórz nową grupę zasobów.

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

Utwórz nowy klaster usługi AKS i dołącz publiczny adres IP dla węzłów. Każdy z węzłów w puli węzłów otrzymuje unikatowy publiczny adres IP. Możesz to sprawdzić, patrząc na wystąpienia zestawu skalowania maszyn wirtualnych.

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

W przypadku istniejących klastrów usługi AKS można również dodać nową pulę węzłów i dołączyć publiczny adres IP dla węzłów.

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

### <a name="use-a-public-ip-prefix"></a>Używanie prefiksu publicznego adresu IP

Użycie prefiksu [publicznego adresu IP][public-ip-prefix-benefits]ma wiele zalet. Usługa AKS obsługuje używanie adresów z istniejącego prefiksu publicznego adresu IP dla węzłów przez przekazanie identyfikatora zasobu z flagą podczas tworzenia nowego klastra lub `node-public-ip-prefix` dodawania puli węzłów.

Najpierw utwórz prefiks publicznego adresu IP przy użyciu [narzędzia az network public-ip prefix create][az-public-ip-prefix-create]:

```azurecli-interactive
az network public-ip prefix create --length 28 --location eastus --name MyPublicIPPrefix --resource-group MyResourceGroup3
```

Wyświetl dane wyjściowe i zanotuj `id` prefiks :

```output
{
  ...
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix",
  ...
}
```

Na koniec podczas tworzenia nowego klastra lub dodawania nowej puli węzłów użyj flagi i przekaż identyfikator zasobu `node-public-ip-prefix` prefiksu:

```azurecli-interactive
az aks create -g MyResourceGroup3 -n MyManagedCluster -l eastus --enable-node-public-ip --node-public-ip-prefix /subscriptions/<subscription-id>/resourcegroups/MyResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix
```

### <a name="locate-public-ips-for-nodes"></a>Lokalizowanie publicznych ip dla węzłów

Publiczne adres IP węzłów można zlokalizować na różne sposoby:

* Użyj polecenia interfejsu wiersza polecenia platformy Azure [az vmss list-instance-public-ips.][az-list-ips]
* Użyj [poleceń programu PowerShell lub powłoki Bash.][vmss-commands] 
* Możesz również wyświetlić publiczne ip w Azure Portal, wyświetlając wystąpienia w zestawie skalowania maszyn wirtualnych.

> [!Important]
> Grupa [zasobów węzła zawiera][node-resource-group] węzły i ich publiczne ip. Użyj grupy zasobów węzła podczas wykonywania poleceń, aby znaleźć publiczne ip dla węzłów.

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tym artykule utworzono klaster usługi AKS, który zawiera węzły oparte na procesorach GPU. Aby zmniejszyć niepotrzebne koszty, można usunąć puli *gpunodepool* lub cały klaster usługi AKS.

Aby usunąć pulę węzłów opartą na procesorze GPU, użyj [polecenia az aks nodepool delete,][az-aks-nodepool-delete] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Aby usunąć sam klaster, użyj [polecenia az group delete,][az-group-delete] aby usunąć grupę zasobów usługi AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

Możesz również usunąć dodatkowy klaster utworzony dla scenariusza publicznego adresu IP dla pul węzłów.

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [pulach węzłów systemowych.][use-system-pool]

W tym artykule opisano sposób tworzenia wielu pul węzłów i zarządzania nimi w klastrze usługi AKS. Aby uzyskać więcej informacji na temat kontrolowania zasobników w pulach węzłów, zobacz [Best practices for advanced scheduler features in AKS][operator-best-practices-advanced-scheduler](Najlepsze rozwiązania dotyczące zaawansowanych funkcji harmonogramu w u usługach AKS).

Aby utworzyć pule węzłów kontenera systemu Windows Server i korzystać z nich, zobacz Create a Windows Server container in AKS (Tworzenie [kontenera systemu Windows Server w UKS).][aks-windows]

Użyj [grup umieszczania w pobliżu,][reduce-latency-ppg] aby zmniejszyć opóźnienie dla aplikacji AKS.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_credentials
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_create
[az-aks-get-upgrades]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_upgrades
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_update
[az-group-create]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_create
[az-group-delete]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete
[az-deployment-group-create]: /cli/azure/deployment/group?view=azure-cli-latest&preserve-view=true#az_deployment_group_create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-system-pool]: use-system-pools.md
[ip-limitations]: ../virtual-network/virtual-network-ip-addresses-overview-arm#standard
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[vmss-commands]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine
[az-list-ips]: /cli/azure/vmss?view=azure-cli-latest&preserve-view=true#az_vmss_list_instance_public_ips
[reduce-latency-ppg]: reduce-latency-ppg.md
[public-ip-prefix-benefits]: ../virtual-network/public-ip-address-prefix.md#why-create-a-public-ip-address-prefix
[az-public-ip-prefix-create]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest&preserve-view=true#az_network_public_ip_prefix_create
