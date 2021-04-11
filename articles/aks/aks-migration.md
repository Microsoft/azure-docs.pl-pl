---
title: Migrowanie do usługi Azure Kubernetes Service (AKS)
description: Migruj do usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.custom: mvc
ms.openlocfilehash: d5e3543fd6b7cd1b5534d6e363e51f1778cc7fc9
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012131"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrowanie do usługi Azure Kubernetes Service (AKS)

Ten przewodnik zawiera szczegółowe informacje dotyczące bieżącej zalecanej konfiguracji AKS, aby pomóc w zaplanowaniu i wykonaniu pomyślnej migracji do usługi Azure Kubernetes Service (AKS). Chociaż w tym artykule nie omówiono każdego scenariusza, zawiera on linki do bardziej szczegółowych informacji na temat planowania pomyślnej migracji.

Ten dokument ułatwia obsługę następujących scenariuszy:

* Konteneryzowania niektóre aplikacje i Migruj je do AKS przy użyciu [Azure Migrate](../migrate/migrate-services-overview.md).
* Migrowanie klastra AKS obsługiwanego przez [zestawy dostępności](../virtual-machines/windows/tutorial-availability-sets.md) do [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md).
* Migrowanie klastra AKS do korzystania ze [standardowego modułu równoważenia obciążenia jednostki SKU](./load-balancer-standard.md).
* Migrowanie z usługi [Azure Container Service (ACS) — wycofanie 31 stycznia 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) do AKS.
* Migrowanie z [aparatu AKS](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) do AKS.
* Migrowanie z klastrów Kubernetes opartych na systemie innym niż Azure do AKS.
* Przeniesienie istniejących zasobów do innego regionu.

W przypadku migrowania upewnij się, że docelowa wersja Kubernetes znajduje się w obsługiwanym oknie dla AKS. Starsze wersje nie mogą znajdować się w obsługiwanym zakresie i będą wymagały, aby uaktualnienie wersji było obsługiwane przez AKS. Aby uzyskać więcej informacji, zobacz [AKS obsługiwane wersje Kubernetes](./supported-kubernetes-versions.md).

Jeśli przeprowadzasz migrację do nowszej wersji programu Kubernetes, przejrzyj [zasady obsługi wersji Kubernetes i pochylenie wersji](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

Kilka narzędzi typu "open source" może pomóc w migracji, w zależności od scenariusza:

* [Velero](https://velero.io/) (wymaga Kubernetes 1.7 +)
* [Rozszerzenie interfejsu wiersza polecenia platformy Azure polecenia](https://github.com/yaron2/azure-kube-cli)
* [Przesunięcia](https://github.com/mhausenblas/reshifter)

W tym artykule zostaną podsumowane szczegóły migracji dotyczące programu:

> [!div class="checklist"]
> * Konteneryzowania aplikacje za poorednictwem Azure Migrate 
> * AKS z usługa Load Balancer w warstwie Standardowa i Virtual Machine Scale Sets
> * Istniejące dołączone usługi platformy Azure
> * Upewnij się, że prawidłowe limity przydziału
> * Wysoka dostępność i ciągłość biznesowa
> * Zagadnienia dotyczące bezstanowych aplikacji
> * Zagadnienia dotyczące aplikacji stanowych
> * Wdrażanie konfiguracji klastra

## <a name="use-azure-migrate-to-migrate-your-applications-to-aks"></a>Użyj Azure Migrate do migrowania aplikacji do AKS

Azure Migrate oferuje ujednoliconą platformę do oceny i migracji do lokalnych serwerów, infrastruktury, aplikacji i danych platformy Azure. W przypadku AKS można użyć Azure Migrate dla następujących zadań:

* [Konteneryzowanie ASP.NET aplikacje i Migruj do AKS](../migrate/tutorial-containerize-aspnet-kubernetes.md)
* [Konteneryzowanie aplikacje sieci Web Java i Migruj do AKS](../migrate/tutorial-containerize-java-kubernetes.md)

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS z usługa Load Balancer w warstwie Standardowa i Virtual Machine Scale Sets

AKS to zarządzana usługa oferująca unikatowe możliwości o niższym obciążeniu zarządzania. Ponieważ AKS jest usługą zarządzaną, należy wybrać spośród zestawu [regionów](./quotas-skus-regions.md) , które obsługuje AKS. Może być konieczne zmodyfikowanie istniejących aplikacji, aby zachować je w dobrej kondycji na kontrolowanej przez AKS płaszczyźnie formantu podczas przejścia z istniejącego klastra do AKS.

Zalecamy używanie klastrów AKS, które są obsługiwane przez [Virtual Machine Scale Sets](../virtual-machine-scale-sets/index.yml) i [Usługa Load Balancer w warstwie Standardowa platformy Azure](./load-balancer-standard.md) , aby uzyskać funkcje takie jak:
* [Pule wielu węzłów](./use-multiple-node-pools.md),
* [Strefy dostępności](../availability-zones/az-overview.md),
* [Autoryzowane zakresy adresów IP](./api-server-authorized-ip-ranges.md),
* [Automatyczne skalowanie klastra](./cluster-autoscaler.md),
* [Azure Policy dla AKS](../governance/policy/concepts/policy-for-kubernetes.md)i
* Inne nowe funkcje po ich udostępnieniu.

Klastry AKS obsługiwane przez [zestawy dostępności maszyn wirtualnych](../virtual-machines/availability.md#availability-sets) nie obsługują wielu z tych funkcji.

W poniższym przykładzie jest tworzony klaster AKS z pulą jednego węzła, które są obsługiwane przez zestaw skalowania maszyn wirtualnych. Klaster:
* Używa usługi równoważenia obciążenia w warstwie Standardowa. 
* Włącza automatyczne skalowanie klastra w puli węzłów klastra.
* Ustawia co najmniej *1* i maksymalnie *3* węzły.

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>Istniejące dołączone usługi platformy Azure

W przypadku migrowania klastrów możesz dołączyć zewnętrzne usługi platformy Azure. Mimo że następujące usługi nie wymagają ponownego tworzenia zasobów, będą wymagały aktualizacji połączeń z wcześniejszych do nowych klastrów w celu utrzymania funkcjonalności.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Konto magazynu
* Zewnętrzne bazy danych

## <a name="ensure-valid-quotas"></a>Upewnij się, że prawidłowe limity przydziału

Ponieważ inne maszyny wirtualne zostaną wdrożone w ramach subskrypcji podczas migracji, należy upewnić się, że limity przydziału i limity są wystarczające dla tych zasobów. W razie potrzeby poproś o zwiększenie [limitu przydziału vCPU](../azure-portal/supportability/per-vm-quota-requests.md).

Może być konieczne zażądanie zwiększenia liczby [przydziałów sieci](../azure-portal/supportability/networking-quota-requests.md) , aby upewnić się, że nie wyczerpuje się adresów IP. Aby uzyskać więcej informacji, zobacz temat [sieci i zakresy adresów IP dla AKS](./configure-kubenet.md).

Aby uzyskać więcej informacji, zobacz [limity subskrypcji i usług platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md). Aby sprawdzić bieżące przydziały, w Azure Portal przejdź do [bloku subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), wybierz subskrypcję, a następnie wybierz pozycję **użycie i przydziały**.

## <a name="high-availability-and-business-continuity"></a>Wysoka dostępność i ciągłość biznesowa

Jeśli aplikacja nie może obsłużyć przestoju, należy postępować zgodnie z najlepszymi rozwiązaniami dotyczącymi scenariuszy migracji wysokiej dostępności. Przeczytaj więcej o [najlepszych rozwiązaniach dotyczących planowania ciągłości działania, odzyskiwania po awarii i maksymalizowania czasu pracy w usłudze Azure Kubernetes Service (AKS)](./operator-best-practices-multi-region.md).

W przypadku złożonych aplikacji zwykle przeprowadzana jest migracja w czasie, a nie wszystkie jednocześnie, co oznacza, że stare i nowe środowiska mogą wymagać komunikacji za pośrednictwem sieci. Aplikacje korzystające wcześniej `ClusterIP` z usług do komunikowania mogą być narażone jako typ `LoadBalancer` i być odpowiednio zabezpieczone.

Aby ukończyć migrację, należy wskazać klientom nowe usługi, które działają w systemie AKS. Zalecamy przekierowanie ruchu przez zaktualizowanie systemu DNS w taki sposób, aby wskazywał Load Balancer znajdujące się przed klastrem AKS.

[Usługa Azure Traffic Manager](../traffic-manager/index.yml) może kierować klientów do żądanego klastra Kubernetes i wystąpienia aplikacji. Traffic Manager to moduł równoważenia obciążenia oparty na systemie DNS, który może dystrybuować ruch sieciowy między regionami. Aby uzyskać najlepszą wydajność i nadmiarowość, należy skierować cały ruch aplikacji przez Traffic Manager przed przejściem do klastra AKS. 

W przypadku wdrożenia obejmującego wiele klastrów klienci powinni łączyć się z Traffic Manager nazwą DNS, która wskazuje usługi w każdym klastrze AKS. Zdefiniuj te usługi przy użyciu punktów końcowych Traffic Manager. Każdy punkt końcowy jest *adresem IP modułu równoważenia obciążenia usługi*. Ta konfiguracja umożliwia kierowanie ruchu sieciowego z punktu końcowego Traffic Manager w jednym regionie do punktu końcowego w innym regionie.

![AKS z Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Usługa frontonu platformy Azure](../frontdoor/front-door-overview.md) jest kolejną opcją routingu ruchu dla klastrów AKS. Za pomocą usługi Azure Front drzwiczk można definiować i monitorować globalne Routing ruchu sieciowego oraz zarządzać nim, optymalizując w celu uzyskania najlepszej wydajności i natychmiastowego globalnego trybu failover w celu zapewnienia wysokiej dostępności. 

### <a name="considerations-for-stateless-applications"></a>Zagadnienia dotyczące bezstanowych aplikacji

Migracja aplikacji bezstanowych jest najłatwiej:
1. Zastosuj definicje zasobów (YAML lub Helm) do nowego klastra.
1. Upewnij się, że wszystko działa zgodnie z oczekiwaniami.
1. Przekieruj ruch, aby aktywować nowy klaster.

### <a name="considerations-for-stateful-applications"></a>Zagadnienia dotyczące aplikacji stanowych

Starannie zaplanuj migrację aplikacji stanowych, aby uniknąć utraty danych lub nieoczekiwanego przestoju.

* Jeśli używasz Azure Files, możesz zainstalować udział plików jako wolumin w nowym klastrze. Zobacz [Instalowanie statyczne Azure Files jako woluminu](./azure-files-volume.md#mount-file-share-as-an-persistent-volume).
* W przypadku korzystania z usługi Azure Managed Disks można zainstalować tylko dysk, jeśli nie dołączono do maszyny wirtualnej. Zobacz [Instalowanie statycznego dysku platformy Azure jako woluminu](./azure-disk-volume.md#mount-disk-as-volume).
* Jeśli żadna z tych metod nie będzie działała, można użyć opcji tworzenia kopii zapasowej i przywracania. Zobacz [Velero na platformie Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md).

#### <a name="azure-files"></a>Azure Files

W przeciwieństwie do dysków Azure Files można instalować na wielu hostach współbieżnie. W klastrze AKS platforma Azure i usługa Kubernetes nie uniemożliwiają tworzenia w tym przypadku klastra AKS. Aby zapobiec utracie danych i nieoczekiwanym zachowaniu, należy się upewnić, że klastry nie zapisują jednocześnie do tych samych plików.

Jeśli aplikacja może hostować wiele replik, które wskazują ten sam udział plików, postępuj zgodnie z krokami migracji bezstanowej i Wdróż definicje YAML w nowym klastrze. 

W przeciwnym razie jedno z możliwych rozwiązań migracji obejmuje następujące kroki:

1. Sprawdź, czy aplikacja działa prawidłowo.
1. Wskaż swój ruch na żywo do nowego klastra AKS.
1. Odłącz stary klaster.

Jeśli chcesz rozpocząć od pustego udziału i utworzyć kopię danych źródłowych, możesz użyć [`az storage file copy`](/cli/azure/storage/file/copy) poleceń, aby przeprowadzić migrację danych.


#### <a name="migrating-persistent-volumes"></a>Migrowanie woluminów trwałych

W przypadku migrowania istniejących woluminów trwałych do AKS na ogół wykonaj następujące kroki:

1. Przełączenie w stan spoczynku zapis do aplikacji. 
    * Ten krok jest opcjonalny i wymaga przestoju.
1. Wykonaj migawki dysków.
1. Utwórz nowe dyski zarządzane na podstawie migawek.
1. Utwórz woluminy trwałe w AKS.
1. Zaktualizuj specyfikacje pod, aby [korzystać z istniejących woluminów](./azure-disk-volume.md) zamiast PersistentVolumeClaims (statycznej aprowizacji).
1. Wdróż aplikację w usłudze AKS.
1. Sprawdź, czy aplikacja działa prawidłowo.
1. Wskaż swój ruch na żywo do nowego klastra AKS.

> [!IMPORTANT]
> Jeśli nie zdecydujesz się na przełączenie w stan spoczynku, musisz zreplikować dane do nowego wdrożenia. W przeciwnym razie trafisz dane zapisane po wykonaniu migawek dysków.

Niektóre narzędzia Open Source mogą pomóc w tworzeniu dysków zarządzanych i migracji woluminów między klastrami Kubernetes:

* [Rozszerzenie do kopiowania dysków interfejsu wiersza polecenia platformy Azure](https://github.com/noelbundick/azure-cli-disk-copy-extension) kopiuje i konwertuje dyski między grupami zasobów i regionami świadczenia usługi Azure.
* [Rozszerzenie interfejsu wiersza polecenia platformy Azure polecenia](https://github.com/yaron2/azure-kube-cli) wylicza woluminy usługi ACS Kubernetes i migruje je do klastra AKS.


### <a name="deployment-of-your-cluster-configuration"></a>Wdrażanie konfiguracji klastra

Zalecamy używanie istniejącego potoku ciągłej integracji i ciągłego dostarczania, aby wdrożyć znaną dobrą konfigurację do AKS. Za pomocą Azure Pipelines można [kompilować i wdrażać aplikacje w programie AKS](/azure/devops/pipelines/ecosystems/kubernetes/aks-template). Sklonuj istniejące zadania wdrażania i upewnij się, że `kubeconfig` wskazuje nowy klaster AKS.

Jeśli nie jest to możliwe, należy wyeksportować definicje zasobów z istniejącego klastra Kubernetes, a następnie zastosować je do AKS. Można użyć `kubectl` do eksportowania obiektów.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Przeniesienie istniejących zasobów do innego regionu

Możesz chcieć przenieść klaster AKS do [innego regionu obsługiwanego przez AKS][region-availability]. Zalecamy utworzenie nowego klastra w innym regionie, a następnie wdrożenie zasobów i aplikacji w nowym klastrze. 

Ponadto, jeśli masz jakiekolwiek usługi, takie jak [Azure dev Spaces][azure-dev-spaces] uruchomione w klastrze AKS, musisz zainstalować i skonfigurować te usługi w klastrze w nowym regionie.


W tym artykule opisano podsumowanie migracji dotyczące programu:

> [!div class="checklist"]
> * AKS z usługa Load Balancer w warstwie Standardowa i Virtual Machine Scale Sets
> * Istniejące dołączone usługi platformy Azure
> * Upewnij się, że prawidłowe limity przydziału
> * Wysoka dostępność i ciągłość biznesowa
> * Zagadnienia dotyczące bezstanowych aplikacji
> * Zagadnienia dotyczące aplikacji stanowych
> * Wdrażanie konfiguracji klastra


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: ../dev-spaces/index.yml
