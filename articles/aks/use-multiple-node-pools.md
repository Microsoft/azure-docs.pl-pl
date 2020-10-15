---
title: Korzystanie z wielu pul węzłów w usłudze Azure Kubernetes Service (AKS)
description: Informacje na temat tworzenia pul węzłów i zarządzania nimi dla klastra w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: 024b7adb254980ec87084b4794a9ced3eaea95eb
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074519"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Tworzenie wielu puli węzłów dla klastra w usłudze Azure Kubernetes Service (AKS) i zarządzanie nimi

W usłudze Azure Kubernetes Service (AKS) węzły tej samej konfiguracji są pogrupowane w *Pule węzłów*. Te pule węzłów zawierają bazowe maszyny wirtualne, na których działają aplikacje. Początkowa liczba węzłów i ich rozmiar (SKU) są definiowane podczas tworzenia klastra AKS, który tworzy [pulę węzłów systemowych][use-system-pool]. Aby obsługiwać aplikacje, które mają różne wymagania dotyczące obliczeń lub magazynu, można utworzyć dodatkowe *Pule węzłów użytkownika*. Pule węzłów systemu stanowią podstawowy cel hostingu krytycznych podstaw systemu, takich jak CoreDNS i tunnelfront. Pule węzłów użytkowników stanowią podstawowy cel hostingu podstaw aplikacji. Jednak w ramach klastra AKS można zaplanować pulę aplikacji w puli węzłów systemu. Pule węzłów użytkowników to miejsce, w którym umieszczane są Twoje zasobniki specyficzne dla aplikacji. Na przykład można użyć tych dodatkowych pul węzłów użytkownika, aby udostępnić procesory GPU dla aplikacji intensywnie korzystających z mocy obliczeniowej lub uzyskać dostęp do magazynu SSD o wysokiej wydajności.

> [!NOTE]
> Ta funkcja umożliwia większą kontrolę nad sposobem tworzenia wielu pul węzłów i zarządzania nimi. W związku z tym wymagane są osobne polecenia do tworzenia/aktualizowania/usuwania. Wcześniej operacje klastra przez `az aks create` lub `az aks update` używają interfejsu API managedCluster oraz były jedyną opcją zmiany płaszczyzny kontroli i puli jednego węzła. Ta funkcja uwidacznia oddzielny zestaw operacji dla pul agentów za pomocą interfejsu API nieznanej obiektu agentpool i wymaga użycia `az aks nodepool` polecenia ustawionego do wykonywania operacji w puli poszczególnych węzłów.

W tym artykule pokazano, jak utworzyć wiele pul węzłów i zarządzać nimi w klastrze AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="limitations"></a>Ograniczenia

Następujące ograniczenia są stosowane podczas tworzenia klastrów AKS i zarządzania nimi, które obsługują pule wielu węzłów:

* Zobacz [limity przydziałów, ograniczenia rozmiaru maszyny wirtualnej i dostępność regionów w usłudze Azure Kubernetes Service (AKS)][quotas-skus-regions].
* Pule węzłów systemowych można usunąć, pod warunkiem, że w klastrze AKS istnieje inna Pula węzłów systemu.
* Pule systemu muszą zawierać co najmniej jeden węzeł, a pule węzłów użytkownika mogą zawierać zero lub więcej węzłów.
* Klaster AKS musi używać usługi równoważenia obciążenia standardowej jednostki SKU do korzystania z wielu pul węzłów, ale ta funkcja nie jest obsługiwana w przypadku podstawowych modułów równoważenia obciążenia SKU.
* Klaster AKS musi używać zestawów skalowania maszyn wirtualnych dla węzłów.
* Nazwa puli węzłów może zawierać tylko małe znaki alfanumeryczne i musi zaczynać się małą literą. W przypadku pul węzłów systemu Linux długość musi należeć do zakresu od 1 do 12 znaków, długość musi mieć od 1 do 6 znaków.
* Wszystkie pule węzłów muszą znajdować się w tej samej sieci wirtualnej.
* Podczas tworzenia wielu pul węzłów podczas tworzenia klastra wszystkie wersje Kubernetes używane przez pule węzłów muszą być zgodne z wersją ustawioną dla płaszczyzny kontroli. Tę aktualizację można zaktualizować po zainicjowaniu obsługi administracyjnej klastra przy użyciu operacji dla puli węzłów.

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

> [!Important]
> W przypadku uruchamiania jednej puli węzłów systemu dla klastra AKS w środowisku produkcyjnym zaleca się użycie co najmniej trzech węzłów dla puli węzłów.

Aby rozpocząć, Utwórz klaster AKS z pulą jednego węzła. W poniższym przykładzie za pomocą polecenia [AZ Group Create][az-group-create] można utworzyć grupę zasobów o nazwie Moja *zasobów* w regionie *wschodnim* . Klaster AKS o nazwie *myAKSCluster* jest tworzony przy użyciu polecenia [AZ AKS Create][az-aks-create] .

> [!NOTE]
> Jednostka SKU usługi równoważenia obciążenia w warstwie *podstawowa* **nie jest obsługiwana** w przypadku używania wielu pul węzłów. Domyślnie klastry AKS są tworzone z użyciem jednostki SKU modułu *równoważenia obciążenia w ramach interfejsu* wiersza polecenia platformy Azure i Azure Portal.

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
> Aby zapewnić niezawodne działanie klastra, należy uruchomić co najmniej 2 (dwa) węzły w domyślnej puli węzłów, ponieważ podstawowe usługi systemowe działają w tej puli węzłów.

Gdy klaster jest gotowy, użyj polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] , aby uzyskać poświadczenia klastra do użycia z programem `kubectl` :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Dodawanie puli węzłów

Klaster utworzony w poprzednim kroku ma pulę jednego węzła. Dodajmy do drugiego pulę węzłów za pomocą polecenia [AZ AKS nodepool Add][az-aks-nodepool-add] . Poniższy przykład tworzy pulę węzłów o nazwie *mynodepool* , która uruchamia *3* węzły:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> Nazwa puli węzłów musi rozpoczynać się od małej litery i może zawierać tylko znaki alfanumeryczne. W przypadku pul węzłów systemu Linux długość musi należeć do zakresu od 1 do 12 znaków, długość musi mieć od 1 do 6 znaków.

Aby wyświetlić stan pul węzłów, użyj polecenia [AZ AKS Node Pool list][az-aks-nodepool-list] i określ grupę zasobów i nazwę klastra:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

Następujące przykładowe dane wyjściowe pokazują, że *mynodepool* został pomyślnie utworzony z trzema węzłami w puli węzłów. Gdy klaster AKS został utworzony w poprzednim kroku, utworzono domyślny *nodepool1* z liczbą węzłów wynoszącą *2*.

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
> Jeśli *VmSize* nie zostanie określony podczas dodawania puli węzłów, domyślny rozmiar jest *Standard_D2s_v3* dla pul węzłów systemu Windows i *Standard_DS2_v2* dla pul węzłów w systemie Linux. Jeśli *OrchestratorVersion* nie jest określony, domyślnie jest to taka sama wersja, jak płaszczyzna kontroli.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Dodawanie puli węzłów z unikatową podsiecią (wersja zapoznawcza)

Obciążenie może wymagać dzielenia węzłów klastra na oddzielne pule na potrzeby izolacji logicznej. Izolacja ta może być obsługiwana w oddzielnym podsieciach przeznaczonych dla każdej puli węzłów w klastrze. Może to dotyczyć wymagań, takich jak brak ciągłej przestrzeni adresowej sieci wirtualnej do podziału między pule węzłów.

#### <a name="limitations"></a>Ograniczenia

* Wszystkie podsieci przypisane do nodepools muszą należeć do tej samej sieci wirtualnej.
* Aby zapewnić krytyczne funkcje, takie jak rozpoznawanie nazw DNS za pośrednictwem coreDNS, konieczne jest uzyskanie dostępu do wszystkich węzłów w klastrze.
* Przypisanie unikatowej podsieci na pulę węzłów jest ograniczone do usługi Azure CNI w trakcie okresu zapoznawczego.
* Korzystanie z zasad sieciowych z unikatową pulą podsieci na węzeł nie jest obsługiwane w ramach wersji zapoznawczej.

Aby utworzyć pulę węzłów z dedykowaną podsiecią, należy przekazać identyfikator zasobu podsieci jako dodatkowy parametr podczas tworzenia puli węzłów.

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
> Operacje uaktualniania i skalowania w klastrze lub puli węzłów nie mogą występować jednocześnie, jeśli zostanie zwrócony błąd. W zamian każdy typ operacji musi zakończyć się w odniesieniu do zasobu docelowego przed następnym żądaniem tego samego zasobu. Więcej informacji na ten temat znajdziesz w naszym [przewodniku rozwiązywania problemów](./troubleshooting.md#im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade).

W poleceniach w tej sekcji wyjaśniono, jak uaktualnić pojedynczą określoną pulę węzłów. Relacja między uaktualnianiem wersji Kubernetes płaszczyzny kontroli a pulą węzłów znajduje się w [sekcji poniżej](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> Wersja obrazu systemu operacyjnego puli węzłów jest powiązana z wersją Kubernetes klastra. Uaktualnienia obrazu systemu operacyjnego są uzyskiwane tylko po uaktualnieniu klastra.

Ponieważ w tym przykładzie istnieją dwa pule węzłów, należy użyć [AZ AKS nodepool upgrade][az-aks-nodepool-upgrade] , aby uaktualnić pulę węzłów. Aby wyświetlić dostępne uaktualnienia, użyj [AZ AKS Get-Upgrades][az-aks-get-upgrades]

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Uaktualnimy *mynodepool*. Użyj polecenia [AZ AKS nodepool upgrade][az-aks-nodepool-upgrade] , aby uaktualnić pulę węzłów, jak pokazano w następującym przykładzie:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

Ponownie utwórz listę stan pul węzłów za pomocą polecenia [AZ AKS Node Pool list][az-aks-nodepool-list] . Poniższy przykład pokazuje, że *mynodepool* jest w stanie *uaktualnienia* do *KUBERNETES_VERSION*:

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

Najlepszym rozwiązaniem jest uaktualnienie wszystkich pul węzłów w klastrze AKS do tej samej wersji Kubernetes. Domyślnym zachowaniem programu `az aks upgrade` jest uaktualnienie wszystkich pul węzłów razem z płaszczyzną kontroli w celu osiągnięcia tego wyrównania. Możliwość uaktualnienia poszczególnych pul węzłów umożliwia przeprowadzenie uaktualnienia stopniowego i zaplanowanie między pulami węzłów, aby zachować czas działania aplikacji w ramach powyższych ograniczeń wymienionych powyżej.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Uaktualnianie płaszczyzny kontroli klastra z wieloma pulami węzłów

> [!NOTE]
> Kubernetes używa standardowego schematu obsługi wersji [semantycznej](https://semver.org/) . Numer wersji jest wyrażony jako *x. y. z*, gdzie *x* jest wersją główną, *y* jest wersją pomocniczą, a *z* to wersja poprawki. Na przykład w wersji *1.12.6*1 jest wersją główną, 12 jest wersją pomocniczą, a 6 to wersja poprawki. Wersja Kubernetes płaszczyzny kontroli i początkowa Pula węzłów są ustawiane podczas tworzenia klastra. Wszystkie dodatkowe pule węzłów mają ustawioną wersję Kubernetes po dodaniu ich do klastra. Wersje Kubernetes mogą się różnić między pulami węzłów, a także między pulą węzłów a płaszczyzną kontroli.

Klaster AKS ma dwa obiekty zasobów klastra z skojarzonymi wersjami Kubernetes.

1. Kubernetes wersja płaszczyzny kontroli klastra.
2. Pula węzłów z wersją Kubernetes.

Płaszczyzna kontrolna jest mapowana na jedną lub wiele pul węzłów. Zachowanie operacji uaktualniania zależy od tego, które polecenie interfejsu wiersza polecenia platformy Azure jest używane.

Uaktualnianie płaszczyzny kontroli AKS wymaga użycia `az aks upgrade` . To polecenie uaktualnia wersję płaszczyzny kontroli i wszystkie pule węzłów w klastrze.

Wydawanie `az aks upgrade` polecenia z `--control-plane-only` flagą uaktualnia tylko płaszczyznę kontroli klastra. Żadna ze skojarzonych pul węzłów w klastrze nie zostanie zmieniona.

Uaktualnianie poszczególnych pul węzłów wymaga użycia `az aks nodepool upgrade` . To polecenie uaktualnia tylko pulę węzłów docelowych z określoną wersją Kubernetes

### <a name="validation-rules-for-upgrades"></a>Reguły walidacji dla uaktualnień

Prawidłowe uaktualnienia Kubernetes dla płaszczyzny kontroli i pul węzłów klastra są weryfikowane przez następujące zestawy reguł.

* Reguły dotyczące prawidłowych wersji do uaktualnienia pul węzłów:
   * Wersja puli węzłów musi mieć taką samą wersję *główną* jak płaszczyzna kontroli.
   * Wersja *pomocnicza* puli węzłów musi znajdować się w dwóch *mniejszych* wersjach wersji płaszczyzny kontroli.
   * Wersja puli węzłów nie może być większa niż wersja formantu `major.minor.patch` .

* Reguły przesyłania operacji uaktualniania:
   * Nie można zmienić wersji Kubernetes płaszczyzny kontroli ani puli węzłów.
   * Jeśli nie określono wersji Kubernetes puli węzłów, zachowanie zależy od używanego klienta. Deklaracja w szablonach Menedżer zasobów powraca do istniejącej wersji zdefiniowanej dla puli węzłów, jeśli jest używana, jeśli żadna z nich nie jest ustawiona, zostanie użyta wersja płaszczyzny kontroli.
   * Można uaktualnić lub skalować płaszczyznę kontroli lub pulę węzłów w danym momencie, nie można jednocześnie przesłać wielu operacji na pojedynczej płaszczyźnie kontroli lub w puli węzłów.

## <a name="scale-a-node-pool-manually"></a>Ręczne skalowanie puli węzłów

Gdy obciążenie aplikacji wymaga zmiany, może być konieczne skalowanie liczby węzłów w puli węzłów. Liczbę węzłów można skalować w górę lub w dół.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Aby skalować liczbę węzłów w puli węzłów, użyj polecenia [AZ AKS Node Pool Scale][az-aks-nodepool-scale] . Poniższy przykład skaluje liczbę węzłów w *mynodepool* do *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Ponownie utwórz listę stan pul węzłów za pomocą polecenia [AZ AKS Node Pool list][az-aks-nodepool-list] . Poniższy przykład pokazuje, że *mynodepool* jest w stanie *skalowania* z nową liczbą *5* węzłów:

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

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Automatyczne skalowanie określonej puli węzłów przez włączenie automatycznego skalowania klastra

AKS oferuje osobną funkcję automatycznego skalowania pul węzłów za pomocą funkcji zwanej [autoskalowaniem klastra](cluster-autoscaler.md). Tę funkcję można włączyć dla puli węzłów z unikatowymi minimalnymi i maksymalnymi liczbami skalowania na pulę węzłów. Dowiedz się [, jak korzystać z automatycznego skalowania klastra na pulę węzłów](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Usuwanie puli węzłów

Jeśli pula nie jest już potrzebna, można ją usunąć i usunąć źródłowe węzły maszyn wirtualnych. Aby usunąć pulę węzłów, użyj polecenia [AZ AKS Node Pool Delete][az-aks-nodepool-delete] i określ nazwę puli węzłów. Poniższy przykład usuwa *mynoodepool* utworzone w poprzednich krokach:

> [!CAUTION]
> Brak opcji odzyskiwania dla utraty danych, które mogą wystąpić po usunięciu puli węzłów. Jeśli nie można zaplanować puli dla innych pul węzłów, te aplikacje są niedostępne. Upewnij się, że nie usuniesz puli węzłów, gdy aplikacje w użyciu nie mają kopii zapasowych danych ani nie mogą być uruchamiane na innych pulach węzłów w klastrze.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Następujące przykładowe dane wyjściowe z polecenia [AZ AKS Node Pool list][az-aks-nodepool-list] pokazują, że *mynodepool* jest w stanie *usuwania* :

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

Usunięcie węzłów i puli węzłów może potrwać kilka minut.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Określanie rozmiaru maszyny wirtualnej dla puli węzłów

W poprzednich przykładach w celu utworzenia puli węzłów dla węzłów utworzonych w klastrze użyto domyślnego rozmiaru maszyny wirtualnej. Bardziej typowy scenariusz polega na tworzeniu pul węzłów o różnych rozmiarach i możliwościach maszyn wirtualnych. Można na przykład utworzyć pulę węzłów zawierającą węzły z dużą ilością procesora lub pamięci albo pulę węzłów, która zapewnia obsługę procesora GPU. W następnym kroku należy użyć przystawek [i tolerowania](#schedule-pods-using-taints-and-tolerations) , aby poinformować usługę Kubernetes Scheduler, jak ograniczyć dostęp do zasobników, które mogą być uruchamiane w tych węzłach.

W poniższym przykładzie Utwórz pulę węzłów opartą na procesorze GPU, która używa *Standard_NC6* rozmiaru maszyny wirtualnej. Te maszyny wirtualne są obsługiwane przez kartę NVIDIA Tesla K80. Aby uzyskać informacje na temat dostępnych rozmiarów maszyn wirtualnych, zobacz [rozmiary maszyn wirtualnych z systemem Linux na platformie Azure][vm-sizes].

Utwórz pulę węzłów za pomocą polecenia [AZ AKS Node Pool Add][az-aks-nodepool-add] . Tym razem Określ nazwę *gpunodepool*i Użyj `--node-vm-size` parametru, aby określić rozmiar *Standard_NC6* :

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Następujące przykładowe dane wyjściowe z polecenia [AZ AKS Node Pool list][az-aks-nodepool-list] pokazują, że *gpunodepool* *tworzy* węzły o określonym *VmSize*:

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

Pomyślne utworzenie *gpunodepool* może potrwać kilka minut.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Zaplanuj użycie zasobników z zmianami i tolerowaniem

W klastrze znajdują się teraz dwa pule węzłów — domyślna pula węzłów została początkowo utworzona i Pula węzłów oparta na procesorze GPU. Użyj [polecenia kubectl Pobierz węzły][kubectl-get] polecenie, aby wyświetlić węzły w klastrze. Następujące przykładowe dane wyjściowe przedstawiają węzły:

```console
kubectl get nodes
```

```output
NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

Harmonogram Kubernetes może używać przyniesień i tolerowanych elementów w celu ograniczenia obciążeń, które mogą być uruchamiane w węzłach.

* Do **węzła jest stosowany** obiekt, który wskazuje na ich zaplanowanie tylko określonych zasobników.
* **Tolerowana** jest następnie stosowana do elementu, który umożliwia im *tolerowanie* kształtu węzła.

Aby uzyskać więcej informacji na temat korzystania z zaawansowanych funkcji usługi Kubernetes, zobacz [najlepsze rozwiązania dotyczące zaawansowanych funkcji usługi Scheduler w AKS][taints-tolerations]

W tym przykładzie Zastosuj przybarwienie do węzła opartego na procesorach GPU przy użyciu polecenia--Node-. Określ nazwę węzła opartego na procesorze GPU z danych wyjściowych poprzedniego `kubectl get nodes` polecenia. Ten obiekt jest stosowany jako para *klucz = wartość* , a następnie opcja planowania. W poniższym przykładzie używa pary *SKU = GPU* i definiujemy *w inny sposób możliwości* noscheduler:

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

Poniższy podstawowy przykład manifestu YAML korzysta z tolerowania, aby umożliwić usłudze Kubernetes Scheduler uruchamianie NGINX na węźle opartym na procesorze GPU. Aby uzyskać bardziej odpowiedni, ale czasochłonny Przykładowo, aby uruchomić zadanie Tensorflow w odniesieniu do zestawu danych MNIST ręcznie, zobacz [Korzystanie z procesorów GPU na potrzeby obciążeń intensywnie korzystających z obliczeń na AKS][gpu-cluster].

Utwórz plik o nazwie `gpu-toleration.yaml` i skopiuj w poniższym przykładzie YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
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

Zaplanuj przy użyciu `kubectl apply -f gpu-toleration.yaml` polecenia:

```console
kubectl apply -f gpu-toleration.yaml
```

Zaplanowanie i ściągnięcie obrazu NGINX może potrwać kilka sekund. Użyj polecenia [polecenia kubectl opisz pod][kubectl-describe] , aby wyświetlić stan pod. Następujące wąskie przykładowe dane wyjściowe pokazują, że jest stosowane tolerowanie *jednostki SKU = GPU: NoSchedule* . W sekcji Events Scheduler przypisano do węzła *AKS-gpunodepool-28993262-vmss000000* procesora GPU:

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

Na węzłach w *gpunodepool*można zaplanować tylko te, dla których zastosowano to tolerowanie. Wszystkie inne na stronie zaplanowano w puli węzłów *nodepool1* . W przypadku tworzenia dodatkowych pul węzłów można użyć dodatkowych przydziałów i tolerowania, aby ograniczyć liczbę elementów, które można zaplanować w tych zasobach węzła.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Określ przebarwienie, etykietę lub tag dla puli węzłów

### <a name="setting-nodepool-taints"></a>Ustawianie nodepool

Podczas tworzenia puli węzłów można dodawać do niej takie same, etykiety lub Tagi. Po dodaniu opcji przeciąganie, etykietka lub tag wszystkie węzły w puli węzłów również pobierają ten obiekt, etykietę lub tag.

Aby utworzyć pulę węzłów z przebarwieniem, użyj [AZ AKS nodepool Add][az-aks-nodepool-add]. Określ nazwę *taintnp* i użyj parametru, `--node-taints` Aby określić *jednostkę SKU = GPU: NoSchedule dla zmiany* czasu.

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
> Dla pul węzłów można ustawić tylko ten obiekt.

Następujące przykładowe dane wyjściowe z polecenia [AZ AKS nodepool list][az-aks-nodepool-list] pokazują, że *taintnp* *tworzy* węzły z określonym *nodeTaints*:

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

Informacje o zasobie są widoczne w Kubernetes na potrzeby obsługi reguł planowania dla węzłów.

### <a name="setting-nodepool-labels"></a>Ustawianie etykiet nodepool

Podczas tworzenia puli węzłów można również dodać etykiety do puli węzłów. Etykiety ustawione w puli węzłów są dodawane do każdego węzła w puli węzłów. Te [etykiety są widoczne w Kubernetes][kubernetes-labels] na potrzeby obsługi reguł planowania dla węzłów.

Aby utworzyć pulę węzłów za pomocą etykiety, użyj [AZ AKS nodepool Add][az-aks-nodepool-add]. Określ nazwę *labelnp* i użyj parametru, `--labels` Aby określić *Wydział = IT* i *costcenter = 9999* dla etykiet.

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

Następujące przykładowe dane wyjściowe z polecenia [AZ AKS nodepool list][az-aks-nodepool-list] pokazują, że *labelnp* *tworzy* węzły z określonym *nodeLabels*:

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

### <a name="setting-nodepool-azure-tags"></a>Ustawianie tagów platformy Azure nodepool

Możesz zastosować tag platformy Azure do pul węzłów w klastrze AKS. Tagi zastosowane do puli węzłów są stosowane do każdego węzła w puli węzłów i są utrwalane za pomocą uaktualnień. Tagi są również stosowane do nowych węzłów dodanych do puli węzłów podczas operacji skalowania w poziomie. Dodanie tagu może ułatwić wykonywanie zadań, takich jak śledzenie zasad lub szacowanie kosztów.

Tagi platformy Azure mają klucze, w których wielkość liter nie jest uwzględniana w operacjach, np. podczas pobierania tagu przez przeszukiwanie klucza. W takim przypadku tag z danym kluczem zostanie zaktualizowany lub pobrany niezależnie od wielkości liter. W wartościach tagów jest rozróżniana wielkość liter.

W AKS, jeśli wiele tagów jest ustawionych z identycznymi kluczami, ale z inną wielkością liter, używany tag to pierwszy w kolejności alfabetycznej. Na przykład `{"Key1": "val1", "kEy1": "val2", "key1": "val3"}` wyniki `Key1` `val1` są ustawiane.

Utwórz pulę węzłów za pomocą polecenia [AZ AKS nodepool Add][az-aks-nodepool-add]. Określ nazwę *tagnodepool* i użyj parametru, `--tag` Aby określić *Wydział = IT* i *costcenter = 9999* dla tagów.

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
> Przy `--tags` użyciu polecenia [AZ AKS nodepool Update][az-aks-nodepool-update] można także użyć parametru, jak również podczas tworzenia klastra. Podczas tworzenia klastra `--tags` parametr stosuje tag do początkowej puli węzłów utworzonej w klastrze. Wszystkie nazwy tagów muszą być zgodne z ograniczeniami w sposobie [używania tagów do organizowania zasobów platformy Azure][tag-limitation]. Aktualizacja puli węzłów za pomocą `--tags` parametru aktualizuje wszystkie istniejące wartości tagów i dołącza wszelkie nowe tagi. Na przykład jeśli Pula węzłów ma *Wydział = IT* i *costcenter = 9999* dla tagów i Zaktualizowano ją z *zespołem = dev* i *costcenter = 111* dla tagów, nodepool byłoby miały *Wydział = IT*, *costcenter = 111*i *zespół = dev* for Tags.

Następujące przykładowe dane wyjściowe z polecenia [AZ AKS nodepool list][az-aks-nodepool-list] pokazują, że *tagnodepool* *tworzy* węzły z określonym *tagiem*:

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

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Zarządzanie pulami węzłów przy użyciu szablonu Menedżer zasobów

W przypadku tworzenia i zarządzania zasobami przy użyciu szablonu Azure Resource Manager można zwykle zaktualizować ustawienia w szablonie i wdrożyć je ponownie w celu zaktualizowania zasobu. W przypadku pul węzłów w AKS nie można zaktualizować profilu początkowej puli węzłów po utworzeniu klastra AKS. To zachowanie oznacza, że nie można zaktualizować istniejącego szablonu Menedżer zasobów, wprowadzić zmiany w pulach węzłów i ponownie wdrożyć. Zamiast tego należy utworzyć oddzielny szablon Menedżer zasobów, który aktualizuje tylko pule węzłów dla istniejącego klastra AKS.

Utwórz szablon, taki jak `aks-agentpools.json` i wklej następujący przykładowy manifest. Ten przykładowy szablon służy do konfigurowania następujących ustawień:

* Aktualizuje pulę węzłów systemu *Linux* o nazwie *myagentpool* , aby uruchamiać trzy węzły.
* Ustawia węzły w puli węzłów do uruchomienia Kubernetes w wersji *1.15.7*.
* Określa rozmiar węzła jako *Standard_DS2_v2*.

W razie potrzeby należy edytować te wartości jako wymagające aktualizacji, dodania lub usunięcia pul węzłów:

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

Wdróż ten szablon przy użyciu polecenia [AZ Group Deployment Create][az-group-deployment-create] , jak pokazano w poniższym przykładzie. Zostanie wyświetlony monit o istniejącą nazwę i lokalizację klastra AKS:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> Możesz dodać tag do puli węzłów, dodając Właściwość *tag* w szablonie, jak pokazano w poniższym przykładzie.
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

Zaktualizowanie klastra AKS może potrwać kilka minut, w zależności od ustawień puli węzłów i operacji zdefiniowanych w szablonie Menedżer zasobów.

## <a name="assign-a-public-ip-per-node-for-your-node-pools-preview"></a>Przypisz publiczny adres IP na węzeł dla pul węzłów (wersja zapoznawcza)

> [!WARNING]
> Aby korzystać z funkcji Public IP na węzeł, należy zainstalować rozszerzenie 0.4.43 w wersji zapoznawczej interfejsu wiersza polecenia.

Węzły AKS nie wymagają swoich własnych publicznych adresów IP do komunikacji. Jednak scenariusze mogą wymagać, aby węzły w puli węzłów otrzymywały własne dedykowane publiczne adresy IP. Typowy scenariusz dotyczy obciążeń gier, gdzie konsola programu musi nawiązać bezpośrednie połączenie z maszyną wirtualną w chmurze, aby zminimalizować liczbę przeskoków. Ten scenariusz można uzyskać w witrynie AKS, rejestrując się w celu uzyskania funkcji w wersji zapoznawczej, publicznego adresu IP węzła (wersja zapoznawcza).

Aby zainstalować i zaktualizować najnowsze rozszerzenie AKS-Preview, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension add --name aks-preview
az extension update --name aks-preview
az extension list
```

Zarejestruj się, aby uzyskać dostęp do funkcji publicznego adresu IP węzła przy użyciu następującego polecenia platformy Azure:

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```
Zarejestrowanie funkcji może potrwać kilka minut.  Stan można sprawdzić za pomocą następującego polecenia:

```azurecli-interactive
 az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodePublicIPPreview')].{Name:name,State:properties.state}"
```

Po pomyślnej rejestracji Utwórz nową grupę zasobów.

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

Utwórz nowy klaster AKS i Dołącz publiczny adres IP dla węzłów. Każdy węzeł w puli węzłów otrzymuje unikatowy publiczny adres IP. Można to sprawdzić, przeglądając wystąpienia zestawu skalowania maszyn wirtualnych.

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

W przypadku istniejących klastrów AKS można również dodać nową pulę węzłów i dołączyć publiczny adres IP dla węzłów.

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

> [!Important]
> W trakcie okresu zapoznawczego usługa Azure Instance Metadata Service nie obsługuje obecnie pobierania publicznych adresów IP dla jednostki SKU maszyny wirtualnej w warstwie Standardowa. Ze względu na to ograniczenie nie można używać poleceń polecenia kubectl do wyświetlania publicznych adresów IP przypisanych do węzłów. Jednak adresy IP są przypisywane i funkcjonują zgodnie z oczekiwaniami. Publiczne adresy IP dla węzłów są dołączone do wystąpień w zestawie skalowania maszyn wirtualnych.

Publiczne adresy IP dla węzłów można znaleźć na różne sposoby:

* Korzystanie z interfejsu wiersza polecenia platformy Azure [AZ VMSS list-instance-Public-IP][az-list-ips]
* Użyj [poleceń programu PowerShell lub bash][vmss-commands]. 
* Możesz również wyświetlić publiczne adresy IP w Azure Portal, wyświetlając wystąpienia w zestawie skalowania maszyn wirtualnych.

> [!Important]
> [Grupa zasobów węzła][node-resource-group] zawiera węzły i ich publiczne adresy IP. Użyj grupy zasobów węzła podczas wykonywania poleceń, aby znaleźć publiczne adresy IP dla węzłów.

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tym artykule opisano tworzenie klastra AKS zawierającego węzły oparte na procesorach GPU. Aby zmniejszyć niepotrzebny koszt, warto usunąć *gpunodepool*lub cały klaster AKS.

Aby usunąć pulę węzłów opartą na procesorze GPU, użyj polecenia [AZ AKS nodepool Delete][az-aks-nodepool-delete] , jak pokazano w poniższym przykładzie:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Aby usunąć klaster, użyj polecenia [AZ Group Delete][az-group-delete] , aby usunąć grupę zasobów AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

Można również usunąć dodatkowy klaster utworzony dla publicznego adresu IP dla scenariusza pule węzłów.

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [pulach węzła systemowego][use-system-pool].

W tym artykule przedstawiono sposób tworzenia wielu pul węzłów w klastrze AKS i zarządzania nimi. Aby uzyskać więcej informacji na temat sterowania zestawami w puli węzłów, zobacz [najlepsze rozwiązania dotyczące zaawansowanych funkcji usługi Scheduler w AKS][operator-best-practices-advanced-scheduler].

Aby utworzyć i użyć pul węzłów kontenera systemu Windows Server, zobacz [Tworzenie kontenera systemu Windows Server w AKS][aks-windows].

Używaj [grup umieszczania w sąsiedztwie][reduce-latency-ppg] , aby ograniczyć czas oczekiwania na aplikacje AKS.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-upgrades]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-upgrades
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
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
[az-list-ips]: /cli/azure/vmss?view=azure-cli-latest.md#az-vmss-list-instance-public-ips
[reduce-latency-ppg]: reduce-latency-ppg.md