---
title: Konfiguracja klastra w usłudze Azure Kubernetes Services (AKS)
description: Dowiedz się, jak skonfigurować klaster w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 4252e3a7f8c3ff9d0ec782a2a9222553c063463c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95533280"
---
# <a name="configure-an-aks-cluster"></a>Konfigurowanie klastra AKS

W ramach tworzenia klastra AKS może być konieczne dostosowanie konfiguracji klastra zgodnie z potrzebami. W tym artykule przedstawiono kilka opcji dostosowywania klastra AKS.

## <a name="os-configuration"></a>Konfiguracja systemu operacyjnego

AKS teraz obsługuje Ubuntu 18,04 jako system operacyjny węzła (OS) w ogólnej dostępności dla klastrów w wersjach Kubernetes wyższych niż 1.18.8. W przypadku wersji poniżej 1.18. x AKS Ubuntu 16,04 jest nadal domyślnym obrazem podstawowym. Z Kubernetes v 1.18. x i z lewej, domyślną bazą jest AKS Ubuntu 18,04.

> [!IMPORTANT]
> Pule węzłów utworzone w Kubernetes v 1.18 lub większe domyślne dla `AKS Ubuntu 18.04` obrazu węzła. Pule węzłów w obsługiwanej wersji Kubernetes mniejszej niż 1,18 `AKS Ubuntu 16.04` są odbierane jako obraz węzła, ale zostaną zaktualizowane do `AKS Ubuntu 18.04` chwili, gdy wersja Kubernetes puli węzłów zostanie zaktualizowana do wersji v 1.18 lub nowszej.
> 
> Zdecydowanie zaleca się przetestowanie obciążeń w puli węzłów AKS Ubuntu 18,04 przed użyciem klastrów w witrynie 1,18 lub nowszej. Przeczytaj, jak [przetestować pule węzłów Ubuntu 18,04](#use-aks-ubuntu-1804-existing-clusters-preview).

W poniższej sekcji wyjaśniono, jak używać i testować AKS Ubuntu 18,04 w klastrach, które nie używają jeszcze wersji Kubernetes 1.18. x lub nowszej, lub zostały utworzone przed udostępnieniem tej funkcji.

Wymagane są następujące zasoby:

- [Interfejs wiersza polecenia platformy Azure][azure-cli-install]w wersji 2.2.0 lub nowszej
- Rozszerzenie AKS-Preview 0.4.35

Aby zainstalować rozszerzenie AKS-Preview 0.4.35 lub nowsze, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension add --name aks-preview
az extension list
```

Zarejestruj `UseCustomizedUbuntuPreview` funkcję:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Wyświetlenie stanu jako **zarejestrowanego** może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Gdy stan jest wyświetlany jako zarejestrowane, Odśwież rejestrację `Microsoft.ContainerService` dostawcy zasobów przy użyciu polecenia [AZ Provider Register](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-ubuntu-1804-on-new-clusters-preview"></a>Korzystanie z AKS Ubuntu 18,04 w nowych klastrach (wersja zapoznawcza)

Skonfiguruj klaster tak, aby używał Ubuntu 18,04 podczas tworzenia klastra. Użyj `--aks-custom-headers` flagi, aby ustawić Ubuntu 18,04 jako domyślny system operacyjny.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Jeśli chcesz utworzyć klastry z obrazem AKS Ubuntu 16,04, możesz to zrobić, pomijając `--aks-custom-headers` znacznik niestandardowy.

### <a name="use-aks-ubuntu-1804-existing-clusters-preview"></a>Używanie istniejących klastrów w programie AKS Ubuntu 18,04 (wersja zapoznawcza)

Skonfiguruj nową pulę węzłów do używania Ubuntu 18,04. Użyj `--aks-custom-headers` flagi, aby ustawić Ubuntu 18,04 jako domyślny system operacyjny dla tej puli węzłów.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Jeśli chcesz utworzyć pule węzłów za pomocą obrazu AKS Ubuntu 16,04, możesz to zrobić, pomijając `--aks-custom-headers` tag niestandardowy.

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

W poniższej sekcji wyjaśniono, jak można używać i testować AKS z `containerD` klastrami, które nie korzystają jeszcze z Kubernetes w wersji 1,19 lub nowszej, lub zostały utworzone przed udostępnieniem tej funkcji, przy użyciu wersji zapoznawczej środowiska uruchomieniowego kontenera.

### <a name="use-containerd-as-your-container-runtime-preview"></a>Użyj `containerd` jako środowiska uruchomieniowego kontenera (wersja zapoznawcza)

Wymagane są następujące wymagania wstępne:

- Zainstalowano [interfejs wiersza polecenia platformy Azure][azure-cli-install]w wersji 2.8.0 lub nowszej
- Rozszerzenie AKS-Preview w wersji 0.4.53 lub nowszej
- `UseCustomizedContainerRuntime`Zarejestrowano flagę funkcji
- `UseCustomizedUbuntuPreview`Zarejestrowano flagę funkcji

Aby zainstalować rozszerzenie AKS-Preview 0.4.53 lub nowsze, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension add --name aks-preview
az extension list
```

Zarejestruj `UseCustomizedContainerRuntime` funkcje i `UseCustomizedUbuntuPreview` :

```azurecli
az feature register --name UseCustomizedContainerRuntime --namespace Microsoft.ContainerService
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService

```

Wyświetlenie stanu jako **zarejestrowanego** może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedContainerRuntime')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Gdy stan jest wyświetlany jako zarejestrowane, Odśwież rejestrację `Microsoft.ContainerService` dostawcy zasobów przy użyciu polecenia [AZ Provider Register](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

### <a name="use-containerd-on-new-clusters-preview"></a>Użyj `containerd` w nowych klastrach (wersja zapoznawcza)

Skonfiguruj klaster, który ma być używany `containerd` podczas tworzenia klastra. Użyj `--aks-custom-headers` flagi, aby ustawić `containerd` jako środowisko uruchomieniowe kontenera.

> [!NOTE]
> `containerd`Środowisko uruchomieniowe jest obsługiwane tylko w węzłach i pulach węzłów przy użyciu obrazu AKS Ubuntu 18,04.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Jeśli chcesz utworzyć klastry za pomocą środowiska uruchomieniowego Moby (Docker), możesz to zrobić, pomijając tag niestandardowy `--aks-custom-headers` .

### <a name="use-containerd-on-existing-clusters-preview"></a>Użyj `containerd` w istniejących klastrach (wersja zapoznawcza)

Skonfiguruj nową pulę węzłów do użycia `containerd` . Użyj `--aks-custom-headers` flagi, aby ustawić `containerd` jako środowisko uruchomieniowe dla tej puli węzłów.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Jeśli chcesz utworzyć pule węzłów za pomocą środowiska uruchomieniowego Moby (Docker), możesz to zrobić, pomijając `--aks-custom-headers` tag niestandardowy.


### <a name="containerd-limitationsdifferences"></a>`Containerd` ograniczenia/różnice

* Aby użyć `containerd` programu jako środowiska uruchomieniowego kontenera, musisz użyć AKS Ubuntu 18,04 jako obrazu podstawowego systemu operacyjnego.
* Mimo że zestaw narzędzi platformy Docker nadal znajduje się w węzłach, Kubernetes używa `containerd` jako środowiska uruchomieniowego kontenera. W związku z tym, ponieważ Moby/Docker nie zarządza kontenerami utworzonymi przez Kubernetes w węzłach, nie można wyświetlać kontenerów ani korzystać z nich przy użyciu poleceń platformy Docker (takich jak `docker ps` ) ani interfejsu API platformy Docker.
* W przypadku programu `containerd` zalecamy używanie [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) jako zastępczego interfejsu wiersza polecenia zamiast interfejsu wiersza polecenia platformy Docker w celu **rozwiązywania problemów** z magazynami, kontenerami i obrazami kontenerów w węzłach Kubernetes (na przykład `crictl ps` ). 
   * Nie zapewnia to pełnej funkcjonalności interfejsu wiersza polecenia platformy Docker. Jest ona przeznaczona tylko do rozwiązywania problemów.
   * `crictl` oferuje bardziej przyjazny kubernetesy widok kontenerów z pojęciami, takimi jak zasobniki itp.
* `Containerd` konfiguruje rejestrowanie przy użyciu standardowego `cri` formatu rejestrowania (który jest inny niż to, co jest obecnie uzyskiwane ze sterownika JSON platformy Docker). Twoje rozwiązanie do rejestrowania musi obsługiwać `cri` Format rejestrowania (na przykład [Azure monitor for Containers](../azure-monitor/insights/container-insights-enable-new-cluster.md))
* Nie można już uzyskiwać dostępu do aparatu platformy Docker `/var/run/docker.sock` lub używać platformy Docker-Docker (DinD).
  * W przypadku obecnie wyodrębniania dzienników aplikacji lub monitorowania danych z aparatu platformy Docker należy zamiast tego użyć takich elementów jak [Azure monitor for Containers](../azure-monitor/insights/container-insights-enable-new-cluster.md) . Ponadto AKS nie obsługuje uruchamiania jakichkolwiek poleceń poza pasmem w węzłach agenta, które mogą spowodować niestabilność.
  * Nawet w przypadku korzystania z Moby/Docker nie zaleca się tworzenia obrazów i bezpośrednio wykorzystujących aparat platformy Docker za pomocą powyższych metod. Kubernetes nie jest w pełni świadomy tych używanych zasobów, a te podejścia omawiają wiele problemów szczegółowych [tutaj](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) [, a na](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)przykład.
* Kompilowanie obrazów — Zalecanym podejściem do tworzenia obrazów jest użycie [zadań ACR](../container-registry/container-registry-quickstart-task-cli.md). Alternatywnym podejściem jest użycie bardziej bezpiecznych opcji w klastrze, takich jak [Docker buildx](https://github.com/docker/buildx).

## <a name="generation-2-virtual-machines-preview"></a>Maszyny wirtualne generacji 2 (wersja zapoznawcza)

Platforma Azure obsługuje [maszyny wirtualne generacji 2 (Gen2)](../virtual-machines/generation-2.md). Maszyny wirtualne generacji 2 obsługują kluczowe funkcje, które nie są obsługiwane w maszynach wirtualnych generacji 1 (Gen1). Te funkcje obejmują zwiększoną ilość pamięci, rozszerzenia funkcji ochrony oprogramowania firmy Intel (Intel SGX) i zwirtualizowaną pamięć trwałą (vPMEM).

Maszyny wirtualne generacji 2 wykorzystują nową architekturę rozruchową opartą na interfejsie UEFI zamiast architektury opartej na systemie BIOS używanej przez maszyny wirtualne generacji 1.
Tylko określone jednostki SKU i rozmiary obsługują maszyny wirtualne Gen2. Sprawdź [listę obsługiwanych rozmiarów](../virtual-machines/generation-2.md#generation-2-vm-sizes), aby sprawdzić, czy jednostka SKU obsługuje lub wymaga Gen2.

Ponadto nie wszystkie obrazy maszyn wirtualnych obsługują Gen2, na maszynach wirtualnych AKS Gen2 będzie używany nowy [obraz AKS Ubuntu 18,04](#os-configuration). Ten obraz obsługuje wszystkie jednostki SKU i rozmiary Gen2.

Aby korzystać z maszyn wirtualnych Gen2 w wersji zapoznawczej, wymagane są:
- `aks-preview`Rozszerzenie interfejsu wiersza polecenia zostało zainstalowane.
- `Gen2VMPreview`Zarejestrowano flagę funkcji.

Zarejestruj `Gen2VMPreview` funkcję:

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

Wyświetlenie stanu jako **zarejestrowanego** może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true) :

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

Gdy stan jest wyświetlany jako zarejestrowane, Odśwież rejestrację `Microsoft.ContainerService` dostawcy zasobów przy użyciu polecenia [AZ Provider Register](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Aby zainstalować rozszerzenie interfejsu wiersza polecenia AKS-Preview, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension add --name aks-preview
```

Aby zaktualizować rozszerzenie interfejsu wiersza polecenia AKS-Preview, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension update --name aks-preview
```

### <a name="use-gen2-vms-on-new-clusters-preview"></a>Korzystanie z maszyn wirtualnych Gen2 w nowych klastrach (wersja zapoznawcza)
Skonfiguruj klaster tak, aby korzystał z maszyn wirtualnych Gen2 dla wybranej jednostki SKU podczas tworzenia klastra. Użyj `--aks-custom-headers` flagi, aby ustawić Gen2 jako generację maszyny wirtualnej w nowym klastrze.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Jeśli chcesz utworzyć zwykły klaster przy użyciu maszyn wirtualnych generacji 1 (Gen1), możesz to zrobić, pomijając `--aks-custom-headers` znacznik niestandardowy. Możesz również dodać więcej maszyn wirtualnych Gen1 lub Gen2, tak jak na poniższej liście.

### <a name="use-gen2-vms-on-existing-clusters-preview"></a>Korzystanie z maszyn wirtualnych Gen2 w istniejących klastrach (wersja zapoznawcza)
Skonfiguruj nową pulę węzłów do używania maszyn wirtualnych Gen2. Użyj `--aks-custom-headers` flagi, aby ustawić Gen2 jako generację maszyny wirtualnej dla tej puli węzłów.

```azurecli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Jeśli chcesz utworzyć regularne pule węzłów Gen1, możesz to zrobić, pomijając `--aks-custom-headers` znacznik niestandardowy.


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
> Za pomocą tymczasowej wersji systemu operacyjnego można wdrożyć maszyny wirtualne i wystąpienia obrazów o rozmiarze do rozmiaru pamięci podręcznej maszyny wirtualnej. W przypadku AKS domyślna konfiguracja dysku systemu operacyjnego węzła używa 100GiB, co oznacza, że potrzebujesz rozmiaru maszyny wirtualnej, która ma pamięć podręczną o rozmiarze większym niż 100 GiB. Domyślny Standard_DS2_v2 ma rozmiar pamięci podręcznej wynoszący 86 GiB, która nie jest wystarczająco duża. Standard_DS3_v2 ma rozmiar pamięci podręcznej 172 GiB, która jest wystarczająco duża. Możesz również zmniejszyć domyślny rozmiar dysku systemu operacyjnego za pomocą polecenia `--node-osdisk-size` . Minimalny rozmiar obrazów AKS to 30GiB. 

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

- Dowiedz się `Kured` , jak [stosować aktualizacje zabezpieczeń i jądra do węzłów systemu Linux](node-updates-kured.md) w klastrze.
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
