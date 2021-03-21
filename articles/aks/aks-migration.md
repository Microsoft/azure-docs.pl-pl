---
title: Migrowanie do usługi Azure Kubernetes Service (AKS)
description: Migruj do usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: e0c3e331dba08fc95f471e3ad40dfcbb10cc2f0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670634"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrowanie do usługi Azure Kubernetes Service (AKS)

W tym artykule opisano planowanie i wykonywanie pomyślnej migracji do usługi Azure Kubernetes Service (AKS). Aby ułatwić podejmowanie najważniejszych decyzji, ten przewodnik zawiera szczegółowe informacje dotyczące bieżącej zalecanej konfiguracji dla AKS. Ten artykuł nie obejmuje każdego scenariusza i w odpowiednim przypadku zawiera linki do bardziej szczegółowych informacji na temat planowania pomyślnej migracji.

Ten dokument może służyć do obsługi następujących scenariuszy:

* Konteneryzowania niektóre aplikacje i Migruj je do AKS przy użyciu [Azure Migrate](../migrate/migrate-services-overview.md)
* Migrowanie klastra AKS obsługiwanego przez [zestawy dostępności](../virtual-machines/windows/tutorial-availability-sets.md) do [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md)
* Migrowanie klastra AKS do korzystania ze [standardowego modułu równoważenia obciążenia SKU](./load-balancer-standard.md)
* Migrowanie z usługi [Azure Container Service (ACS) — wycofanie 31 stycznia 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) do AKS
* Migrowanie z [aparatu AKS](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) do AKS
* Migrowanie z klastrów Kubernetes opartych na systemie innym niż Azure do usługi AKS
* Przeniesienie istniejących zasobów do innego regionu

W przypadku migrowania upewnij się, że docelowa wersja Kubernetes znajduje się w obsługiwanym oknie dla AKS. W przypadku korzystania ze starszej wersji może nie znajdować się w obsługiwanym zakresie i wymagać uaktualnienia wersji, aby były obsługiwane przez AKS. Aby uzyskać więcej informacji, zobacz [AKS obsługiwane wersje Kubernetes](./supported-kubernetes-versions.md) .

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

Azure Migrate oferuje ujednoliconą platformę do oceny i migracji do lokalnych serwerów, infrastruktury, aplikacji i danych platformy Azure. W przypadku AKS można użyć Azure Migrate w następujący sposób:

* [Konteneryzowanie ASP.NET aplikacje i Migruj do AKS](../migrate/tutorial-containerize-aspnet-kubernetes.md)
* [Konteneryzowanie aplikacje sieci Web Java i Migruj do AKS](../migrate/tutorial-containerize-java-kubernetes.md)

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS z usługa Load Balancer w warstwie Standardowa i Virtual Machine Scale Sets

AKS to zarządzana usługa oferująca unikatowe możliwości o niższym obciążeniu zarządzania. W związku z tym, że jest usługą zarządzaną, należy wybrać zestaw [regionów](./quotas-skus-regions.md) , które obsługuje AKS. Przejście z istniejącego klastra do AKS może wymagać zmodyfikowania istniejących aplikacji, aby pozostały w dobrej kondycji na płaszczyźnie kontroli zarządzanej AKS.

Zalecamy używanie klastrów AKS, które są obsługiwane przez [Virtual Machine Scale Sets](../virtual-machine-scale-sets/index.yml) i [Usługa Load Balancer w warstwie Standardowa platformy Azure](./load-balancer-standard.md) , aby uzyskać funkcje takie [jak pule wielu węzłów](./use-multiple-node-pools.md), [strefy dostępności](../availability-zones/az-overview.md), [autoryzowane zakresy adresów IP](./api-server-authorized-ip-ranges.md), [Automatyczne skalowanie klastra](./cluster-autoscaler.md), [Azure Policy dla AKS](../governance/policy/concepts/policy-for-kubernetes.md)i inne nowe funkcje w miarę ich udostępnienia.

Klastry AKS obsługiwane przez [zestawy dostępności maszyn wirtualnych](../virtual-machines/availability.md#availability-sets) nie obsługują wielu z tych funkcji.

W poniższym przykładzie jest tworzony klaster AKS z pulą jednego węzła, które są obsługiwane przez zestaw skalowania maszyn wirtualnych. Używa ona standardowego modułu równoważenia obciążenia. Włącza również automatyczne skalowanie klastra w puli węzłów klastra i ustawia co najmniej *1* i maksymalnie *3* węzły:

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

Podczas migrowania klastrów możesz dołączyć zewnętrzne usługi platformy Azure. Te nie wymagają ponownego tworzenia zasobów, ale będą wymagały aktualizacji połączeń z wcześniejszych do nowych klastrów w celu utrzymania funkcjonalności.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Konto magazynu
* Zewnętrzne bazy danych

## <a name="ensure-valid-quotas"></a>Upewnij się, że prawidłowe limity przydziału

Ponieważ dodatkowe maszyny wirtualne zostaną wdrożone w ramach subskrypcji podczas migracji, należy sprawdzić, czy limity przydziału i ograniczenia są wystarczające dla tych zasobów. Może być konieczne zażądanie zwiększenia [limitu przydziału vCPU](../azure-portal/supportability/per-vm-quota-requests.md).

Może być konieczne zażądanie zwiększenia liczby [przydziałów sieci](../azure-portal/supportability/networking-quota-requests.md) , aby upewnić się, że nie wyczerpuje się adresów IP. Aby uzyskać dodatkowe informacje, zobacz artykuł [sieci i zakresy adresów IP dla usługi AKS](./configure-kubenet.md) .

Aby uzyskać więcej informacji, zobacz [limity subskrypcji i usług platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md). Aby sprawdzić bieżące przydziały, w Azure Portal przejdź do [bloku subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), wybierz subskrypcję, a następnie wybierz pozycję **użycie i przydziały**.

## <a name="high-availability-and-business-continuity"></a>Wysoka dostępność i ciągłość biznesowa

Jeśli aplikacja nie może obsłużyć przestoju, należy postępować zgodnie z najlepszymi rozwiązaniami dotyczącymi scenariuszy migracji wysokiej dostępności.  Najlepsze rozwiązania związane z ciągłym planowaniem ciągłości biznesowej, odzyskiwaniem po awarii i maksymalizacją czasu pracy wykraczają poza zakres tego dokumentu.  Przeczytaj więcej o [najlepszych rozwiązaniach dotyczących ciągłości działania i odzyskiwania po awarii w usłudze Azure Kubernetes Service (AKS)](./operator-best-practices-multi-region.md) , aby dowiedzieć się więcej.

W przypadku złożonych aplikacji zwykle przeprowadzana jest migracja w czasie, a nie wszystkie jednocześnie. Oznacza to, że stare i nowe środowiska mogą wymagać komunikacji za pośrednictwem sieci. Aplikacje, które wcześniej korzystały `ClusterIP` z usług do komunikacji, mogą być narażone jako typ `LoadBalancer` i być odpowiednio zabezpieczone.

Aby ukończyć migrację, należy wskazać klientom nowe usługi, które działają w systemie AKS. Zalecamy przekierowanie ruchu przez zaktualizowanie systemu DNS w taki sposób, aby wskazywał Load Balancer, które znajduje się przed klastrem AKS.

[Usługa Azure Traffic Manager](../traffic-manager/index.yml) może kierować klientów do żądanego klastra Kubernetes i wystąpienia aplikacji.  Traffic Manager to moduł równoważenia obciążenia oparty na systemie DNS, który może dystrybuować ruch sieciowy między regionami.  Aby uzyskać najlepszą wydajność i nadmiarowość, należy skierować cały ruch aplikacji przez Traffic Manager przed przejściem do klastra AKS.  W przypadku wdrożenia wieloklastrowego klienci powinni łączyć się z Traffic Manager nazwą DNS, która wskazuje usługi w każdym klastrze AKS. Zdefiniuj te usługi przy użyciu punktów końcowych Traffic Manager. Każdy punkt końcowy jest *adresem IP modułu równoważenia obciążenia usługi*. Ta konfiguracja umożliwia kierowanie ruchu sieciowego z punktu końcowego Traffic Manager w jednym regionie do punktu końcowego w innym regionie.

![AKS z Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Usługa frontonu platformy Azure](../frontdoor/front-door-overview.md) jest kolejną opcją routingu ruchu dla klastrów AKS.  Usługa Azure Front Door Service umożliwia definiowanie i monitorowanie globalnego routingu ruchu internetowego oraz zarządzanie nim przez optymalizowanie pod kątem uzyskiwania najlepszej wydajności i natychmiastowego przechodzenia w tryb failover w celu zapewnienia wysokiej dostępności. 

### <a name="considerations-for-stateless-applications"></a>Zagadnienia dotyczące bezstanowych aplikacji

Migracja aplikacji bezstanowych jest najłatwiej. Zastosuj definicje zasobów (YAML lub Helm) do nowego klastra, upewnij się, że wszystko działa zgodnie z oczekiwaniami, i Przekieruj ruch, aby aktywować nowy klaster.

### <a name="considerations-for-stateful-applications"></a>Zagadnienia dotyczące aplikacji stanowych

Starannie zaplanuj migrację aplikacji stanowych, aby uniknąć utraty danych lub nieoczekiwanego przestoju.

Jeśli używasz Azure Files, możesz zainstalować udział plików jako wolumin w nowym klastrze:
* [Zainstaluj Azure Files statyczny jako wolumin](./azure-files-volume.md#mount-file-share-as-an-persistent-volume)

W przypadku korzystania z usługi Azure Managed Disks można zainstalować tylko dysk, jeśli nie dołączono do maszyny wirtualnej:
* [Instalowanie statycznego dysku platformy Azure jako woluminu](./azure-disk-volume.md#mount-disk-as-volume)

Jeśli żadna z tych metod nie będzie działała, można użyć opcji tworzenia kopii zapasowej i przywracania:
* [Velero na platformie Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

#### <a name="azure-files"></a>Azure Files

W przeciwieństwie do dysków Azure Files można instalować na wielu hostach współbieżnie. W klastrze AKS platforma Azure i usługa Kubernetes nie uniemożliwiają tworzenia usługi na tym, że nadal używa klastra ACS. Aby zapobiec utracie danych i nieoczekiwanym zachowaniu, należy się upewnić, że klastry nie zapisują jednocześnie do tych samych plików.

Jeśli aplikacja może hostować wiele replik, które wskazują ten sam udział plików, postępuj zgodnie z krokami migracji bezstanowej i Wdróż definicje YAML w nowym klastrze. W przeciwnym razie jedno z możliwych rozwiązań migracji obejmuje następujące kroki:

* Sprawdź, czy aplikacja działa prawidłowo.
* Wskaż swój ruch na żywo do nowego klastra AKS.
* Odłącz stary klaster.

Jeśli chcesz rozpocząć od pustego udziału i utworzyć kopię danych źródłowych, możesz użyć [`az storage file copy`](/cli/azure/storage/file/copy) poleceń, aby przeprowadzić migrację danych.


#### <a name="migrating-persistent-volumes"></a>Migrowanie woluminów trwałych

W przypadku migrowania istniejących woluminów trwałych do AKS na ogół wykonaj następujące kroki:

* Przełączenie w stan spoczynku zapis do aplikacji. (Ten krok jest opcjonalny i wymaga przestoju).
* Wykonaj migawki dysków.
* Utwórz nowe dyski zarządzane na podstawie migawek.
* Utwórz woluminy trwałe w AKS.
* Zaktualizuj specyfikacje pod, aby [korzystać z istniejących woluminów](./azure-disk-volume.md) zamiast PersistentVolumeClaims (statycznej aprowizacji).
* Wdróż aplikację w usłudze AKS.
* Sprawdź, czy aplikacja działa prawidłowo.
* Wskaż swój ruch na żywo do nowego klastra AKS.

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

Możesz chcieć przenieść klaster AKS do [innego regionu obsługiwanego przez AKS][region-availability]. Zalecamy utworzenie nowego klastra w innym regionie, a następnie wdrożenie zasobów i aplikacji w nowym klastrze. Ponadto, jeśli masz jakiekolwiek usługi, takie jak [Azure dev Spaces][azure-dev-spaces] uruchomione w klastrze AKS, musisz również zainstalować i skonfigurować te usługi w klastrze w nowym regionie.


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
