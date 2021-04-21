---
title: Konfiguracja klastra w usługach Azure Kubernetes Services (AKS)
description: Dowiedz się, jak skonfigurować klaster w Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/09/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 5740c1c299e8a6a2e8874bd13aae76b0353cc6a2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775874"
---
# <a name="configure-an-aks-cluster"></a>Konfigurowanie klastra usługi AKS

W ramach tworzenia klastra usługi AKS może być konieczne dostosowanie konfiguracji klastra do własnych potrzeb. W tym artykule oprowadzono kilka opcji dostosowywania klastra usługi AKS.

## <a name="os-configuration"></a>Konfiguracja systemu operacyjnego

Usługa AKS obsługuje teraz Ubuntu 18.04 jako domyślny system operacyjny węzła (OS) ogólnie dostępny dla klastrów w wersjach kubernetes wyższych niż 1.18 W przypadku wersji starszych niż 1.18 domyślnym obrazem bazowym jest nadal usługa AKS Ubuntu 16.04. W przypadku systemu Kubernetes w wersji 1.18 i wyższych domyślną bazą jest AKS Ubuntu 18.04.

> [!IMPORTANT]
> Pule węzłów utworzone na kubernetes w wersji 1.18 lub większej domyślnie obraz `AKS Ubuntu 18.04` węzła. Pule węzłów w obsługiwanej wersji kubernetes mniejszej niż 1.18 odbierają jako obraz węzła, ale zostaną zaktualizowane do wersji po zaktualizowania puli węzłów kubernetes do wersji `AKS Ubuntu 16.04` `AKS Ubuntu 18.04` 1.18 lub większej.
> 
> Zdecydowanie zaleca się przetestowanie obciążeń w pulach węzłów usługi AKS Ubuntu 18.04 przed użyciem klastrów w wersji 1.18 lub większej.


### <a name="use-aks-ubuntu-1804-ga-on-new-clusters"></a>Używanie usługi AKS Ubuntu 18.04 (GA) w nowych klastrach

Klastry utworzone na kubernetes w wersji 1.18 lub większej domyślnie obraz `AKS Ubuntu 18.04` węzła. Pule węzłów w obsługiwanej wersji kubernetes mniejszej niż 1.18 będą nadal odbierane jako obraz węzła, ale zostaną zaktualizowane do wersji po zaktualizowania klastra lub puli węzłów kubernetes do wersji `AKS Ubuntu 16.04` `AKS Ubuntu 18.04` 1.18 lub większej.

Zdecydowanie zaleca się przetestowanie obciążeń w pulach węzłów usługi AKS Ubuntu 18.04 przed użyciem klastrów w wersji 1.18 lub większej.

Aby utworzyć klaster przy użyciu obrazu węzła, po prostu utwórz klaster z uruchomionym platformą `AKS Ubuntu 18.04` Kubernetes w wersji 1.18 lub nowszą, jak pokazano poniżej

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="use-aks-ubuntu-1804-ga-on-existing-clusters"></a>Używanie usługi AKS Ubuntu 18.04 (GA) w istniejących klastrach

Klastry utworzone na kubernetes w wersji 1.18 lub większej domyślnie obraz `AKS Ubuntu 18.04` węzła. Pule węzłów w obsługiwanej wersji kubernetes mniejszej niż 1.18 będą nadal odbierane jako obraz węzła, ale zostaną zaktualizowane do wersji po zaktualizowania klastra lub puli węzłów kubernetes do wersji `AKS Ubuntu 16.04` `AKS Ubuntu 18.04` 1.18 lub większej.

Zdecydowanie zaleca się przetestowanie obciążeń w pulach węzłów usługi AKS Ubuntu 18.04 przed użyciem klastrów w wersji 1.18 lub większej.

Jeśli klastry lub pule węzłów są gotowe do użycia w obrazie węzła, możesz po prostu uaktualnić je do wersji `AKS Ubuntu 18.04` 1.18 lub wyższej, jak po przedstawiono poniżej.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

Jeśli chcesz tylko uaktualnić tylko jedną pulę węzłów:

```azurecli
az aks nodepool upgrade -name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

### <a name="test-aks-ubuntu-1804-ga-on-existing-clusters"></a>Testowanie usługi AKS Ubuntu 18.04 (GA) w istniejących klastrach

Pule węzłów utworzone na kubernetes w wersji 1.18 lub większej jako obraz `AKS Ubuntu 18.04` węzła. Pule węzłów w obsługiwanej wersji kubernetes mniejszej niż 1.18 będą nadal odbierane jako obraz węzła, ale zostaną zaktualizowane do wersji po zaktualizowania puli węzłów kubernetes do wersji `AKS Ubuntu 16.04` `AKS Ubuntu 18.04` 1.18 lub nowszej.

Zdecydowanie zaleca się przetestowanie obciążeń w pulach węzłów AKS Ubuntu 18.04 przed uaktualnieniem pul węzłów produkcyjnych.

Aby utworzyć pulę węzłów przy użyciu obrazu węzła, wystarczy utworzyć pulę węzłów z uruchomionym platformą `AKS Ubuntu 18.04` Kubernetes w wersji 1.18 lub nowszą. Płaszczyzna sterowania klastra musi być również co najmniej w wersji 1.18 lub większej, ale inne pule węzłów mogą pozostać w starszej wersji usługi Kubernetes.
Poniżej najpierw uaktualniamy płaszczyznę sterowania, a następnie tworzymy nową pulę węzłów z wersją 1.18, która otrzyma nową wersję systemu operacyjnego obrazu węzła.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14 --control-plane-only

az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.18.14
```

## <a name="container-runtime-configuration"></a>Konfiguracja środowiska uruchomieniowego kontenera

Środowisko uruchomieniowe kontenera to oprogramowanie, które wykonuje kontenery i zarządza obrazami kontenerów w węźle. Środowisko uruchomieniowe pomaga odsejść od funkcji wywołań systemu operacyjnego lub systemu operacyjnego w celu uruchamiania kontenerów w systemie Linux lub Windows. Klastry AKS korzystające z pul węzłów Kubernetes w wersji 1.19 i w większym stopniu `containerd` używają ich jako środowiska uruchomieniowego kontenera. Klastry AKS korzystające z platformy Kubernetes w przypadku pul węzłów w wersjach wcześniejszych niż 1.19 używają [środowiska uruchomieniowego kontenera Moby](https://mobyproject.org/) (nadrzędnej platformy Docker).

![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) to podstawowe środowisko uruchomieniowe kontenerów zgodne ze standardem [OCI](https://opencontainers.org/) (Open Container Initiative), które zapewnia minimalny zestaw funkcji wymaganych do wykonywania kontenerów i zarządzania obrazami w węźle. W [marcu](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) 2017 r. została ona ułaskawiona na rzecz platformy Cloud Native Compute Foundation (KUF). Bieżąca wersja moby używana przez usługi AKS jest już używana i jest zbudowana na podstawie `containerd` , jak pokazano powyżej.

W przypadku puli węzłów opartych na węzłach i węzłów, zamiast z programem , kubelet będzie rozmawiał bezpośrednio z wtyczką CRI (container runtime interface), usuwając dodatkowe przeskoki w przepływie w porównaniu z implementacją `containerd` `dockershim` `containerd` docker CRI. W związku z tym zobaczysz lepsze opóźnienie uruchamiania zasobnika i mniejsze użycie zasobów (procesora i pamięci).

W przypadku węzłów usługi AKS zwiększa się opóźnienie uruchamiania zasobnika i zmniejsza się zużycie zasobów węzła `containerd` przez środowisko uruchomieniowe kontenera. Te ulepszenia są włączane przez tę nową architekturę, w której kubelet rozmawia bezpośrednio z usługą za pośrednictwem wtyczki CRI, podczas gdy w architekturze Moby/docker kubelet rozmawia z aparatem platformy Docker i przed dotarciem do rozwiązania , co ma dodatkowe przeskoki w `containerd` `dockershim` `containerd` przepływie.

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` Program działa w każdej wersji gachodniej kubernetes w UKS i w każdej nadrzędnej wersji kubernetes powyżej 1.19 i obsługuje wszystkie funkcje kubernetes i AKS.

> [!IMPORTANT]
> Klastry z pulami węzłów utworzonymi na platformie Kubernetes w wersji 1.19 lub większej domyślnej dla `containerd` środowiska uruchomieniowego kontenera. Klastry z pulami węzłów w obsługiwanej wersji usługi Kubernetes mniejszej niż 1.19 odbierają dane dla środowiska uruchomieniowego kontenera, ale zostaną zaktualizowane do wersji po zaktualizowania puli węzłów kubernetes do wersji `Moby` `ContainerD` 1.19 lub większej. Nadal można używać pul `Moby` węzłów i klastrów w starszych obsługiwanych wersjach, dopóki nie wyłączą obsługi.
> 
> Zdecydowanie zaleca się przetestowanie obciążeń w pulach węzłów usługi AKS przed użyciem klastrów w `containerD` 1.19 lub większej.

### <a name="containerd-limitationsdifferences"></a>`Containerd` ograniczenia/różnice

* Aby użyć `containerd` jako środowiska uruchomieniowego kontenera, musisz użyć usługi AKS Ubuntu 18.04 jako podstawowego obrazu systemu operacyjnego.
* Mimo że zestaw narzędzi platformy Docker jest nadal obecny w węzłach, środowisko Kubernetes używa `containerd` go jako środowiska uruchomieniowego kontenera. W związku z tym, ponieważ moby/docker nie zarządza kontenerami utworzonymi przez usługę Kubernetes w węzłach, nie można wyświetlać kontenerów ani wchodzić z nimi w interakcje przy użyciu poleceń platformy Docker (takich jak ) ani interfejsu `docker ps` API platformy Docker.
* W przypadku systemu zalecamy używanie jako zastępczego interfejsu wiersza polecenia zamiast interfejsu wiersza polecenia platformy Docker do rozwiązywania problemów z zasobnikami, kontenerami i obrazami kontenerów w węzłach `containerd` [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) Kubernetes (na przykład  `crictl ps` ). 
   * Nie zapewnia ona pełnej funkcjonalności interfejsu wiersza polecenia platformy Docker. Jest ona przeznaczona tylko do rozwiązywania problemów.
   * `crictl` Oferuje bardziej przyjazny dla kubernetes widok kontenerów z koncepcjami, np. zasobnikami itp.
* `Containerd` Konfiguruje rejestrowanie przy użyciu standardowego formatu rejestrowania (który różni się od tego, co obecnie można uzyskać ze sterownika `cri` JSON platformy Docker). Rozwiązanie rejestrowania musi obsługiwać format rejestrowania (taki jak `cri` Azure Monitor for [Containers](../azure-monitor/containers/container-insights-enable-new-cluster.md))
* Nie możesz już uzyskać dostępu do aparatu platformy Docker lub użyć platformy `/var/run/docker.sock` Docker-in-Docker (Dind).
  * Jeśli obecnie wyodrębniasz dzienniki aplikacji lub dane monitorowania z aparatu platformy Docker, użyj zamiast tego Azure Monitor [for Containers.](../azure-monitor/containers/container-insights-enable-new-cluster.md) Ponadto usługę AKS nie obsługuje uruchamiania żadnych poleceń poza pasmem na węzłach agenta, które mogłyby spowodować niestabilność.
  * Nawet w przypadku korzystania z aparatu Moby/docker zdecydowanie nie zaleca się budowania obrazów i bezpośredniego korzystania z aparatu platformy Docker za pośrednictwem powyższych metod. Kubernetes nie w pełni wie o tych zużytych zasobach, a te podejścia prezentują wiele problemów szczegółowo tutaj i [tutaj,](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)na przykład. [](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/)
* Tworzenie obrazów — możesz nadal używać bieżącego przepływu pracy kompilacji platformy Docker w zwykły sposób, chyba że tworzysz obrazy w klastrze usługi AKS. W takim przypadku rozważ przełączenie się do zalecanego podejścia do tworzenia obrazów przy użyciu programu [zadania usługi ACR](../container-registry/container-registry-quickstart-task-cli.md)lub bezpieczniejszej opcji w klastrze, takiej [jak docker buildx.](https://github.com/docker/buildx)

## <a name="generation-2-virtual-machines"></a>Maszyny wirtualne 2. generacji:

Platforma Azure obsługuje maszyny wirtualne generacji [2 (Gen2).](../virtual-machines/generation-2.md) Maszyny wirtualne generacji 2 obsługują kluczowe funkcje, które nie są obsługiwane w przypadku maszyn wirtualnych 1. generacji (Gen1). Te funkcje obejmują zwiększoną ilość pamięci, procesor Intel Software Guard Extensions (Intel SGX) i zwirtualizowana pamięć trwała (vPMEM).

Maszyny wirtualne generacji 2 używają nowej architektury rozruchu opartej na interfejsie UEFI, a nie architektury opartej na systemie BIOS używanej przez maszyny wirtualne generacji 1.
Tylko określone jednostki SKU i rozmiary obsługują maszyny wirtualne gen2. Sprawdź listę [obsługiwanych rozmiarów,](../virtual-machines/generation-2.md#generation-2-vm-sizes)aby sprawdzić, czy twoja sKU obsługuje lub wymaga generacji 2.

Ponadto nie wszystkie obrazy maszyn wirtualnych obsługują usługę Gen2, a na maszynach wirtualnych usługi AKS Gen2 będzie używać nowego obrazu [usługi AKS Ubuntu 18.04.](#os-configuration) Ten obraz obsługuje wszystkie jednostki SKU i rozmiary gen2.

## <a name="ephemeral-os"></a>Efemeralny system operacyjny

Domyślnie platforma Azure automatycznie replikuje dysk systemu operacyjnego maszyny wirtualnej do usługi Azure Storage, aby uniknąć utraty danych w przypadku konieczności przeniesienia maszyny wirtualnej na innego hosta. Jednak ponieważ kontenery nie są przeznaczone do utrwalania stanu lokalnego, to zachowanie zapewnia ograniczoną wartość, jednocześnie zapewniając pewne wady, takie jak wolniejsze inicjowanie obsługi węzłów i większe opóźnienie odczytu/zapisu.

Z kolei efemeracyjne dyski systemu operacyjnego są przechowywane tylko na maszynie hosta, podobnie jak dysk tymczasowy. Zapewnia to mniejsze opóźnienie odczytu/zapisu oraz szybsze skalowanie węzłów i uaktualnianie klastra.

Podobnie jak w przypadku dysku tymczasowego, efemeralny dysk systemu operacyjnego jest uwzględniony w cenie maszyny wirtualnej, więc nie są naliczane żadne dodatkowe koszty magazynowania.

> [!IMPORTANT]
>Jeśli użytkownik nie zażąda jawnie dysków zarządzanych dla systemu operacyjnego, domyślnie dla danej konfiguracji puli węzłów usługi AKS będzie domyślnie efemeryzowy system operacyjny.

W przypadku korzystania z efemeralnego systemu operacyjnego dysk systemu operacyjnego musi mieścić się w pamięci podręcznej maszyny wirtualnej. Rozmiary pamięci podręcznej maszyn wirtualnych są dostępne w dokumentacji platformy [Azure](../virtual-machines/dv3-dsv3-series.md) w nawiasach obok przepływności we/wy ("rozmiar pamięci podręcznej w GiB").

Na przykład przy użyciu domyślnego rozmiaru maszyny wirtualnej usługi AKS Standard_DS2_v2 z domyślnym rozmiarem dysku systemu operacyjnego 100 GB ten rozmiar maszyny wirtualnej obsługuje efemerowy system operacyjny, ale ma tylko 86 GB pamięci podręcznej. Ta konfiguracja będzie domyślnie dyskami zarządzanymi, jeśli użytkownik nie określi jawnie. Jeśli użytkownik jawnie zażądał efemeralnego systemu operacyjnego, otrzyma błąd weryfikacji.

Jeśli użytkownik zażąda tego samego Standard_DS2_v2 z dyskiem systemu operacyjnego o rozmiarze 60 GB, ta konfiguracja będzie domyślnie efemeryjna: żądany rozmiar 60 GB jest mniejszy niż maksymalny rozmiar pamięci podręcznej 86 GB.

Przy Standard_D8s_v3 z dyskiem systemu operacyjnego o rozmiarze 100 GB ten rozmiar maszyny wirtualnej obsługuje efemerowy system operacyjny i ma 200 GB miejsca w pamięci podręcznej. Jeśli użytkownik nie określi typu dysku systemu operacyjnego, bufor węzłów domyślnie otrzyma efemerowy system operacyjny. 

Efemeracyjny system operacyjny wymaga co najmniej wersji 2.15.0 interfejsu wiersza polecenia platformy Azure.

### <a name="use-ephemeral-os-on-new-clusters"></a>Używanie efemeralnego systemu operacyjnego w nowych klastrach

Skonfiguruj klaster do używania efemeralnych dysków systemu operacyjnego podczas jego tworzenia. Użyj `--node-osdisk-type` flagi , aby ustawić efemeralny system operacyjny jako typ dysku systemu operacyjnego dla nowego klastra.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

Jeśli chcesz utworzyć zwykły klaster przy użyciu dysków systemu operacyjnego dołączonych do sieci, możesz to zrobić, określając `--node-osdisk-type=Managed` . Możesz również dodać więcej efemeralnych pul węzłów systemu operacyjnego zgodnie z poniższymi instrukcjami.

### <a name="use-ephemeral-os-on-existing-clusters"></a>Używanie efemeralnego systemu operacyjnego w istniejących klastrach
Skonfiguruj nową pulę węzłów do używania efemeralnych dysków systemu operacyjnego. Użyj flagi , aby ustawić jako typ dysku systemu operacyjnego jako typ `--node-osdisk-type` dysku systemu operacyjnego dla tej puli węzłów.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> Efemeralny system operacyjny umożliwia wdrażanie obrazów maszyn wirtualnych i wystąpień do rozmiaru pamięci podręcznej maszyny wirtualnej. W przypadku usługi AKS domyślna konfiguracja dysku systemu operacyjnego węzła używa 128 GB, co oznacza, że potrzebny jest rozmiar maszyny wirtualnej z pamięcią podręczną większą niż 128 GB. Domyślny rozmiar Standard_DS2_v2 pamięci podręcznej to 86 GB, co nie jest wystarczająco duże. Rozmiar Standard_DS3_v2 pamięci podręcznej wynosi 172 GB, co jest wystarczająco duże. Możesz również zmniejszyć domyślny rozmiar dysku systemu operacyjnego przy użyciu narzędzia `--node-osdisk-size` . Minimalny rozmiar obrazów WKS wynosi 30 GB. 

Jeśli chcesz utworzyć pule węzłów z dyskami systemu operacyjnego dołączonymi do sieci, możesz to zrobić, określając wartość `--node-osdisk-type Managed` .

## <a name="custom-resource-group-name"></a>Nazwa niestandardowej grupy zasobów

Podczas wdrażania klastra Azure Kubernetes Service na platformie Azure tworzona jest druga grupa zasobów dla węzłów procesu roboczego. Domyślnie usługi AKS będą nazywać grupę zasobów węzła `MC_resourcegroupname_clustername_location` , ale możesz również podać własną nazwę.

Aby określić własną nazwę grupy zasobów, zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure aks-preview w wersji 0.3.2 lub nowszej. Za pomocą interfejsu wiersza polecenia platformy Azure użyj parametru polecenia , aby `--node-resource-group` `az aks create` określić niestandardową nazwę grupy zasobów. Jeśli używasz szablonu Azure Resource Manager do wdrożenia klastra usługi AKS, możesz zdefiniować nazwę grupy zasobów przy użyciu `nodeResourceGroup` właściwości .

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Pomocnicza grupa zasobów jest tworzona automatycznie przez dostawcę zasobów platformy Azure we własnej subskrypcji. Nazwę niestandardowej grupy zasobów można określić tylko podczas tworzenia klastra. 

Podczas pracy z grupą zasobów węzła należy pamiętać, że nie można:

- Określ istniejącą grupę zasobów dla grupy zasobów węzła.
- Określ inną subskrypcję dla grupy zasobów węzła.
- Zmień nazwę grupy zasobów węzła po utworzeniu klastra.
- Określ nazwy zarządzanych zasobów w grupie zasobów węzła.
- Modyfikowanie lub usuwanie tagów zarządzanych zasobów utworzonych przez platformę Azure w grupie zasobów węzła.

## <a name="next-steps"></a>Następne kroki

- Dowiedz [się, jak uaktualnić obrazy węzłów](node-image-upgrade.md) w klastrze.
- Zobacz [Upgrade an Azure Kubernetes Service (AKS) cluster (Uaktualnianie](upgrade-cluster.md) klastra usługi AKS), aby dowiedzieć się, jak uaktualnić klaster do najnowszej wersji rozwiązania Kubernetes.
- Dowiedz się więcej na [ `containerd` temat i kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- Zapoznaj się z listą [często zadawanych pytań dotyczących aks,](faq.md) aby znaleźć odpowiedzi na niektóre typowe pytania dotyczące AKS.
- Przeczytaj więcej na [temat efemeralnych dysków systemu operacyjnego.](../virtual-machines/ephemeral-os-disks.md)


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
