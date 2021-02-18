---
title: Konfiguracja klastra w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się, jak skonfigurować klaster w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/09/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: eaf512915532b482c25e830cd9f2e01d61aa4524
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572785"
---
# <a name="configure-an-aks-cluster"></a>Konfigurowanie klastra usługi AKS

W ramach tworzenia klastra AKS może być konieczne dostosowanie konfiguracji klastra zgodnie z potrzebami. W tym artykule przedstawiono kilka opcji dostosowywania klastra AKS.

## <a name="os-configuration"></a>Konfiguracja systemu operacyjnego

Program AKS obsługuje teraz Ubuntu 18,04 jako domyślny system operacyjny (OS) w ogólnej dostępności (GA) dla klastrów w wersjach Kubernetes wyższych niż 1,18 dla wersji poniżej 1,18, AKS Ubuntu 16,04 nadal jest domyślnym obrazem podstawowym. W Kubernetes v 1.18 i wyższych wartość domyślna to AKS Ubuntu 18,04.

> [!IMPORTANT]
> Pule węzłów utworzone w Kubernetes v 1.18 lub większe domyślne dla `AKS Ubuntu 18.04` obrazu węzła. Pule węzłów w obsługiwanej wersji Kubernetes mniejszej niż 1,18 `AKS Ubuntu 16.04` są odbierane jako obraz węzła, ale zostaną zaktualizowane do `AKS Ubuntu 18.04` chwili, gdy wersja Kubernetes puli węzłów zostanie zaktualizowana do wersji v 1.18 lub nowszej.
> 
> Zdecydowanie zaleca się przetestowanie obciążeń w puli węzłów AKS Ubuntu 18,04 przed użyciem klastrów w witrynie 1,18 lub nowszej.


### <a name="use-aks-ubuntu-1804-ga-on-new-clusters"></a>Użyj AKS Ubuntu 18,04 (GA) dla nowych klastrów

Klastry utworzone w Kubernetes v 1.18 lub większe domyślne dla `AKS Ubuntu 18.04` obrazu węzła. Pule węzłów w obsługiwanej wersji Kubernetes mniejszej niż 1,18 nadal będą wyświetlane `AKS Ubuntu 16.04` jako obraz węzła, ale zostaną zaktualizowane do `AKS Ubuntu 18.04` wersji, gdy klaster lub Pula węzłów Kubernetes wersja została zaktualizowana do programu v 1.18 lub nowszego.

Zdecydowanie zaleca się przetestowanie obciążeń w puli węzłów AKS Ubuntu 18,04 przed użyciem klastrów w witrynie 1,18 lub nowszej.

Aby utworzyć klaster przy użyciu `AKS Ubuntu 18.04` obrazu węzła, po prostu Utwórz klaster z systemem Kubernetes v 1.18 lub nowszym, jak pokazano poniżej

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="use-aks-ubuntu-1804-ga-on-existing-clusters"></a>Użyj AKS Ubuntu 18,04 (GA) w istniejących klastrach

Klastry utworzone w Kubernetes v 1.18 lub większe domyślne dla `AKS Ubuntu 18.04` obrazu węzła. Pule węzłów w obsługiwanej wersji Kubernetes mniejszej niż 1,18 nadal będą wyświetlane `AKS Ubuntu 16.04` jako obraz węzła, ale zostaną zaktualizowane do `AKS Ubuntu 18.04` wersji, gdy klaster lub Pula węzłów Kubernetes wersja została zaktualizowana do programu v 1.18 lub nowszego.

Zdecydowanie zaleca się przetestowanie obciążeń w puli węzłów AKS Ubuntu 18,04 przed użyciem klastrów w witrynie 1,18 lub nowszej.

Jeśli klastry lub pule węzłów są gotowe do `AKS Ubuntu 18.04` obrazu węzła, można po prostu uaktualnić je do wersji 1.18 lub nowszej w następujący sposób.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

Jeśli chcesz tylko uaktualnić tylko jedną pulę węzłów:

```azurecli
az aks nodepool upgrade -name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="test-aks-ubuntu-1804-ga-on-existing-clusters"></a>Test AKS Ubuntu 18,04 (GA) dla istniejących klastrów

Pule węzłów utworzone w Kubernetes v 1.18 lub większe domyślne dla `AKS Ubuntu 18.04` obrazu węzła. Pule węzłów w obsługiwanej wersji Kubernetes mniejszej niż 1,18 nadal będą wyświetlane `AKS Ubuntu 16.04` jako obraz węzła, ale zostaną zaktualizowane do `AKS Ubuntu 18.04` wersji, gdy zostanie zaktualizowana wersja Kubernetes puli węzłów na wartość v 1.18 lub nowsza.

Zdecydowanie zaleca się przetestowanie obciążeń w pulach węzłów AKS Ubuntu 18,04 przed uaktualnieniem pul węzłów produkcyjnych.

Aby utworzyć pulę węzłów przy użyciu `AKS Ubuntu 18.04` obrazu węzła, wystarczy utworzyć pulę węzłów z systemem Kubernetes v 1.18 lub nowszym. Płaszczyzna kontroli klastra musi mieć co najmniej 1.18 w wersji v lub nowszej, ale inne pule węzłów mogą pozostawać w starszych wersjach Kubernetes.
Poniżej należy najpierw uaktualnić płaszczyznę kontroli, a następnie utworzyć nową pulę węzłów za pomocą 1.18 v, która otrzyma nową wersję systemu operacyjnego obrazu węzła.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14 --control-plane-only

az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

## <a name="container-runtime-configuration"></a>Konfiguracja środowiska uruchomieniowego kontenera

Środowisko uruchomieniowe kontenera to oprogramowanie, które wykonuje kontenery i zarządza obrazami kontenerów w węźle. Środowisko uruchomieniowe ułatwia abstrakcyjne wywołania sys lub systemu operacyjnego (OS) do uruchamiania kontenerów w systemie Linux lub Windows. Klastry AKS korzystające z pul węzłów Kubernetes w wersji 1,19 i większe użycie `containerd` jako środowiska uruchomieniowego kontenera. Klastry AKS korzystające z funkcji Kubernetes starszych niż v 1.19 dla pul węzłów używają [Moby](https://mobyproject.org/) (nadrzędnego Docker) jako środowiska uruchomieniowego kontenera.

![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) jest zgodnym podstawowym środowiskiem uruchomieniowym [kontenera (Open](https://opencontainers.org/) Container Initiative), który zapewnia minimalny zestaw funkcji wymaganych do wykonywania kontenerów i zarządzania obrazami w węźle. Zostało ono [przekazano do](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) natywnej usługi obliczeniowej Cloud Foundation (CNCF) w marcu 2017. Bieżąca wersja Moby używana przez AKS już korzysta z programu i została utworzona w oparciu o `containerd` , jak pokazano powyżej.

W przypadku `containerd` pul węzłów i węzłów opartych na interfejsie zamiast rozmowy z programem `dockershim` kubelet będzie komunikować się bezpośrednio z `containerd` za pośrednictwem wtyczki CRI (interfejsu środowiska uruchomieniowego kontenera), usuwając dodatkowe przeskoki w przepływie w porównaniu do implementacji platformy Docker CRI. W związku z tym zobaczysz lepszy czas oczekiwania na uruchomienie i mniejszą ilość zasobów (procesor CPU i pamięć).

Przy użyciu `containerd` for AKS nodes, pod kątem opóźnień uruchamiania, zwiększa i zmniejsza zużycie zasobów węzła przez środowisko uruchomieniowe kontenera. Te ulepszenia są włączane przez tę nową architekturę, w której kubelet się bezpośrednio do programu `containerd` za pomocą wtyczki CRI, a w przypadku architektury Moby/Docker kubelet będzie komunikować się z `dockershim` aparatem platformy Docker przed osiągnięciem `containerd` , dzięki czemu mają dodatkowe przeskoki w przepływie.

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` działa na każdej wersji systemu Kubernetes w AKS, a w każdej wersji Kubernetes w strumieniu przedniej powyżej v 1.19 i obsługuje wszystkie funkcje Kubernetes i AKS.

> [!IMPORTANT]
> Klastry z pulami węzłów utworzone w Kubernetes v 1.19 lub większe domyślne `containerd` dla środowiska uruchomieniowego kontenera. Klastry z pulami węzłów w obsługiwanej wersji Kubernetes mniejszej niż 1,19 są odbierane `Moby` dla środowiska uruchomieniowego kontenera, ale zostaną zaktualizowane do `ContainerD` wersji, gdy wersja Kubernetes puli węzłów zostanie zaktualizowana do programu v 1.19 lub nowszego. Nadal można używać `Moby` pul węzłów i klastrów w starszych obsługiwanych wersjach, dopóki te nie zostaną objęte wsparciem.
> 
> Zdecydowanie zaleca się przetestowanie obciążeń w pulach węzłów AKS za pomocą `containerD` starszych klastrów niż 1,19 lub więcej.

### <a name="containerd-limitationsdifferences"></a>`Containerd` ograniczenia/różnice

* Aby użyć `containerd` programu jako środowiska uruchomieniowego kontenera, musisz użyć AKS Ubuntu 18,04 jako obrazu podstawowego systemu operacyjnego.
* Mimo że zestaw narzędzi platformy Docker nadal znajduje się w węzłach, Kubernetes używa `containerd` jako środowiska uruchomieniowego kontenera. W związku z tym, ponieważ Moby/Docker nie zarządza kontenerami utworzonymi przez Kubernetes w węzłach, nie można wyświetlać kontenerów ani korzystać z nich przy użyciu poleceń platformy Docker (takich jak `docker ps` ) ani interfejsu API platformy Docker.
* W przypadku programu `containerd` zalecamy używanie [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) jako zastępczego interfejsu wiersza polecenia zamiast interfejsu wiersza polecenia platformy Docker w celu **rozwiązywania problemów** z magazynami, kontenerami i obrazami kontenerów w węzłach Kubernetes (na przykład `crictl ps` ). 
   * Nie zapewnia to pełnej funkcjonalności interfejsu wiersza polecenia platformy Docker. Jest ona przeznaczona tylko do rozwiązywania problemów.
   * `crictl` oferuje bardziej przyjazny kubernetesy widok kontenerów z pojęciami, takimi jak zasobniki itp.
* `Containerd` konfiguruje rejestrowanie przy użyciu standardowego `cri` formatu rejestrowania (który jest inny niż to, co jest obecnie uzyskiwane ze sterownika JSON platformy Docker). Twoje rozwiązanie do rejestrowania musi obsługiwać `cri` Format rejestrowania (na przykład [Azure monitor for Containers](../azure-monitor/containers/container-insights-enable-new-cluster.md))
* Nie można już uzyskiwać dostępu do aparatu platformy Docker `/var/run/docker.sock` lub używać platformy Docker-Docker (DinD).
  * W przypadku obecnie wyodrębniania dzienników aplikacji lub monitorowania danych z aparatu platformy Docker należy zamiast tego użyć takich elementów jak [Azure monitor for Containers](../azure-monitor/containers/container-insights-enable-new-cluster.md) . Ponadto AKS nie obsługuje uruchamiania jakichkolwiek poleceń poza pasmem w węzłach agenta, które mogą spowodować niestabilność.
  * Nawet w przypadku korzystania z Moby/Docker nie zaleca się tworzenia obrazów i bezpośrednio wykorzystujących aparat platformy Docker za pomocą powyższych metod. Kubernetes nie jest w pełni świadomy tych używanych zasobów, a te podejścia omawiają wiele problemów szczegółowych [tutaj](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) [, a na](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)przykład.
* Tworzenie obrazów — możesz nadal używać bieżącego przepływu pracy kompilacji platformy Docker jako normalnego, chyba że tworzysz obrazy wewnątrz klastra AKS. W takim przypadku Rozważ przełączenie do zalecanej metody tworzenia obrazów przy użyciu [zadań ACR](../container-registry/container-registry-quickstart-task-cli.md)lub bezpieczniejszej opcji w klastrze, takiej jak [Docker buildx](https://github.com/docker/buildx).

## <a name="generation-2-virtual-machines"></a>Maszyny wirtualne 2. generacji:

Platforma Azure obsługuje [maszyny wirtualne generacji 2 (Gen2)](../virtual-machines/generation-2.md). Maszyny wirtualne generacji 2 obsługują kluczowe funkcje, które nie są obsługiwane w maszynach wirtualnych generacji 1 (Gen1). Te funkcje obejmują zwiększoną ilość pamięci, rozszerzenia funkcji ochrony oprogramowania firmy Intel (Intel SGX) i zwirtualizowaną pamięć trwałą (vPMEM).

Maszyny wirtualne generacji 2 wykorzystują nową architekturę rozruchową opartą na interfejsie UEFI zamiast architektury opartej na systemie BIOS używanej przez maszyny wirtualne generacji 1.
Tylko określone jednostki SKU i rozmiary obsługują maszyny wirtualne Gen2. Sprawdź [listę obsługiwanych rozmiarów](../virtual-machines/generation-2.md#generation-2-vm-sizes), aby sprawdzić, czy jednostka SKU obsługuje lub wymaga Gen2.

Ponadto nie wszystkie obrazy maszyn wirtualnych obsługują Gen2, na maszynach wirtualnych AKS Gen2 będzie używany nowy [obraz AKS Ubuntu 18,04](#os-configuration). Ten obraz obsługuje wszystkie jednostki SKU i rozmiary Gen2.

## <a name="ephemeral-os"></a>Tymczasowych systemów operacyjnych

Domyślnie platforma Azure automatycznie replikuje dysk systemu operacyjnego maszyny wirtualnej do usługi Azure Storage, aby uniknąć utraty danych, gdy maszyna wirtualna musi zostać przeniesiona do innego hosta. Ponieważ jednak kontenery nie mają trwałego stanu lokalnego, to zachowanie oferuje ograniczoną wartość, a jednocześnie zapewnia pewne wady, w tym wolniejsze Inicjowanie obsługi węzłów oraz wyższe opóźnienia odczytu i zapisu.

Z kolei tymczasowe dyski systemu operacyjnego są przechowywane tylko na komputerze-hoście, podobnie jak dysk tymczasowy. Zapewnia to mniejsze opóźnienie odczytu/zapisu oraz szybsze skalowanie węzłów i uaktualnień klastra.

Podobnie jak w przypadku dysku tymczasowego, tymczasowy dysk systemu operacyjnego jest dołączany do ceny maszyny wirtualnej, dzięki czemu nie są naliczane żadne dodatkowe koszty magazynowania.

> [!IMPORTANT]
>Jeśli użytkownik nie zażąda jawnie usługi Managed disks dla systemu operacyjnego, AKS będzie domyślnie mieć tymczasowych systemów operacyjnych, jeśli jest to możliwe dla danej konfiguracji nodepool.

W przypadku korzystania z tymczasowych systemów operacyjnych dysk systemu operacyjnego musi pasować do pamięci podręcznej maszyny wirtualnej. Rozmiary pamięci podręcznej maszyn wirtualnych są dostępne w [dokumentacji platformy Azure](../virtual-machines/dv3-dsv3-series.md) w nawiasach obok pozycji przepływność we/wy ("rozmiar pamięci podręcznej w GIB").

Użycie domyślnego rozmiaru maszyny wirtualnej AKS Standard_DS2_v2 z domyślnym rozmiarem dysku systemu operacyjnego 100 GB na przykład ten rozmiar maszyny wirtualnej obsługuje system operacyjny, ale ma tylko 86GB rozmiaru pamięci podręcznej. Ta konfiguracja będzie domyślnie obsługiwana w przypadku dysków zarządzanych, jeśli użytkownik nie określi jawnie. Jeśli użytkownik jawnie zażądał tymczasowej wersji systemu operacyjnego, wystąpi błąd walidacji.

Jeśli użytkownik zażąda tego samego Standard_DS2_v2 z dyskiem systemu operacyjnego o pojemności 60 GB, ta konfiguracja będzie domyślnie w systemie operacyjnym tymczasowych: żądany rozmiar dysku 60 GB jest mniejszy niż maksymalny rozmiar pamięci podręcznej 86GB.

Przy użyciu Standard_D8s_v3 z dyskiem systemu operacyjnego 100 GB ten rozmiar maszyny wirtualnej obsługuje tymczasowe systemy operacyjne i 200 GB miejsce w pamięci podręcznej. Jeśli użytkownik nie określi typu dysku systemu operacyjnego, nodepool domyślnie otrzymuje system operacyjny. 

Tymczasowe systemy operacyjne wymagają co najmniej wersji 2.15.0 interfejsu wiersza polecenia platformy Azure.

### <a name="use-ephemeral-os-on-new-clusters"></a>Używanie tymczasowych systemów operacyjnych w nowych klastrach

Skonfiguruj klaster do używania tymczasowych dysków systemu operacyjnego podczas tworzenia klastra. Użyj `--node-osdisk-type` flagi, aby ustawić tymczasowych systemów operacyjnych jako typ dysku systemu operacyjnego dla nowego klastra.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

Jeśli chcesz utworzyć zwykły klaster przy użyciu dysków systemu operacyjnego podłączonych do sieci, możesz to zrobić przez określenie opcji `--node-osdisk-type=Managed` . Możesz również dodać więcej pul węzłów systemu operacyjnego z systemem operacyjnym poniżej.

### <a name="use-ephemeral-os-on-existing-clusters"></a>Używanie tymczasowych systemów operacyjnych w istniejących klastrach
Skonfiguruj nową pulę węzłów do używania tymczasowych dysków systemu operacyjnego. Użyj `--node-osdisk-type` flagi, aby ustawić jako typ dysku systemu operacyjnego jako typ dysku systemu operacyjnego dla tej puli węzłów.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> Za pomocą tymczasowej wersji systemu operacyjnego można wdrożyć maszyny wirtualne i wystąpienia obrazów o rozmiarze do rozmiaru pamięci podręcznej maszyny wirtualnej. W przypadku AKS domyślna konfiguracja dysku systemu operacyjnego węzła używa 128 GB, co oznacza, że potrzebujesz rozmiaru maszyny wirtualnej o rozmiarze pamięci podręcznej większej niż 128 GB. Domyślny Standard_DS2_v2 ma rozmiar pamięci podręcznej 86GB, który nie jest wystarczająco duży. Standard_DS3_v2 ma rozmiar pamięci podręcznej 172GB, który jest wystarczająco duży. Możesz również zmniejszyć domyślny rozmiar dysku systemu operacyjnego za pomocą polecenia `--node-osdisk-size` . Minimalny rozmiar obrazów AKS to 30 GB. 

Jeśli chcesz utworzyć pule węzłów z dyskami systemu operacyjnego dołączonymi do sieci, możesz to zrobić, określając polecenie `--node-osdisk-type Managed` .

## <a name="custom-resource-group-name"></a>Nazwa niestandardowej grupy zasobów

Podczas wdrażania klastra usługi Azure Kubernetes na platformie Azure zostanie utworzona druga grupa zasobów dla węzłów procesu roboczego. Domyślnie AKS będzie nazwać grupę zasobów węzła `MC_resourcegroupname_clustername_location` , ale możesz również podać własną nazwę.

Aby określić własną nazwę grupy zasobów, zainstaluj rozszerzenie AKS-Preview interfejsu wiersza polecenia platformy Azure w wersji 0.3.2 lub nowszej. Korzystając z interfejsu wiersza polecenia platformy Azure, użyj `--node-resource-group` parametru `az aks create` polecenie, aby określić niestandardową nazwę grupy zasobów. W przypadku wdrażania klastra AKS za pomocą szablonu Azure Resource Manager można zdefiniować nazwę grupy zasobów za pomocą `nodeResourceGroup` właściwości.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Dodatkowa grupa zasobów jest automatycznie tworzona przez dostawcę zasobów platformy Azure we własnej subskrypcji. Podczas tworzenia klastra można określić niestandardową nazwę grupy zasobów. 

Podczas pracy z grupą zasobów węzła należy pamiętać, że nie można:

- Określ istniejącą grupę zasobów dla grupy zasobów węzła.
- Określ inną subskrypcję dla grupy zasobów węzła.
- Zmień nazwę grupy zasobów węzła po utworzeniu klastra.
- Określ nazwy zarządzanych zasobów w grupie zasobów węzła.
- Modyfikuj lub Usuń Tagi zarządzane przez platformę Azure w ramach grupy zasobów węzła.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak uaktualnić obrazy węzłów](node-image-upgrade.md) w klastrze.
- Zobacz [Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)](upgrade-cluster.md) , aby dowiedzieć się, jak uaktualnić klaster do najnowszej wersji Kubernetes.
- Przeczytaj więcej na temat [ `containerd` i Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- Zapoznaj się z listą [często zadawanych pytań dotyczących AKS](faq.md) , aby znaleźć odpowiedzi na niektóre często zadawane pytania dotyczące AKS.
- Przeczytaj więcej na temat [tymczasowych dysków systemu operacyjnego](../virtual-machines/ephemeral-os-disks.md).


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
