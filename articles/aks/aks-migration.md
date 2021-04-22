---
title: Migrowanie do Azure Kubernetes Service (AKS)
description: Migracja do Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 123f4e5c2442b913a53288602d1c56f199b131a6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872789"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrowanie do Azure Kubernetes Service (AKS)

Aby ułatwić planowanie i pomyślne wykonanie migracji do usługi Azure Kubernetes Service (AKS), ten przewodnik zawiera szczegółowe informacje dotyczące bieżącej zalecanej konfiguracji usługi AKS. Chociaż ten artykuł nie obejmuje każdego scenariusza, zawiera linki do bardziej szczegółowych informacji dotyczących planowania pomyślnej migracji.

Ten dokument ułatwia obsługę następujących scenariuszy:

* Konteneryzowanie niektórych aplikacji i migrowanie ich do usługi AKS przy [użyciu Azure Migrate](../migrate/migrate-services-overview.md).
* Migrowanie klastra AKS z zestawami [](../virtual-machine-scale-sets/overview.md)dostępności w celu [Virtual Machine Scale Sets](../virtual-machines/windows/tutorial-availability-sets.md) .
* Migrowanie klastra usługi AKS w celu korzystania z usługi [load balancer w standardowych SKU.](./load-balancer-standard.md)
* Migrowanie [z Azure Container Service (ACS) — wycofanie 31 stycznia 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) r. do aks.
* Migrowanie z [aparatu AKS do](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) AKS.
* Migrowanie z klastrów Kubernetes spoza platformy Azure do usługi AKS.
* Przenoszenie istniejących zasobów do innego regionu.

Podczas migracji upewnij się, że docelowa wersja kubernetes znajduje się w oknie obsługiwanym przez platformę AKS. Starsze wersje mogą nie różnić się od obsługiwanego zakresu i będą wymagać uaktualnienia wersji, aby było obsługiwane przez usługę AKS. Aby uzyskać więcej informacji, zobacz [Wersje kubernetes obsługiwane przez aKS.](./supported-kubernetes-versions.md)

Jeśli migrujesz do nowszej wersji środowiska Kubernetes, zapoznaj się z zasadami obsługi nieskwerowania wersji i wersji środowiska [Kubernetes.](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)

Migracja może być pomocna w kilku narzędziach typu open source, w zależności od scenariusza:

* [Velero](https://velero.io/) (wymaga kubernetes 1.7+)
* [Rozszerzenie interfejsu wiersza polecenia usługi Azure Kube](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

W tym artykule podsumowujemy szczegóły migracji dla:

> [!div class="checklist"]
> * Konteneryzowanie aplikacji za pośrednictwem Azure Migrate 
> * AKS z usługa Load Balancer w warstwie Standardowa i Virtual Machine Scale Sets
> * Istniejące dołączone usługi platformy Azure
> * Upewnij się, że przydziały są prawidłowe
> * Wysoka dostępność i ciągłość działalności biznesowej
> * Zagadnienia dotyczące aplikacji bez stanowych
> * Zagadnienia dotyczące aplikacji stanowych
> * Wdrażanie konfiguracji klastra

## <a name="use-azure-migrate-to-migrate-your-applications-to-aks"></a>Używanie Azure Migrate do migrowania aplikacji do aks

Azure Migrate oferuje ujednoliconą platformę do oceniania i migrowania na lokalne serwery, infrastrukturę, aplikacje i dane platformy Azure. W przypadku korzystania z Azure Migrate AKS można wykonywać następujące zadania:

* [Konteneryzowanie ASP.NET i migrowanie do aks](../migrate/tutorial-containerize-aspnet-kubernetes.md)
* [Konteneryzowanie aplikacji internetowych Java i migrowanie do usługi AKS](../migrate/tutorial-containerize-java-kubernetes.md)

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS z usługa Load Balancer w warstwie Standardowa i Virtual Machine Scale Sets

AKS to usługa zarządzana oferująca unikatowe możliwości o niższym narzucie na zarządzanie. Ponieważ usługa AKS jest usługą zarządzaną, należy wybrać z zestawu [regionów,](./quotas-skus-regions.md) które obsługuje usługa AKS. Może być konieczne zmodyfikowanie istniejących aplikacji w celu utrzymania ich dobrej kondycji na płaszczyźnie sterowania zarządzanej przez usługę AKS podczas przechodzenia z istniejącego klastra do usługi AKS.

Zalecamy korzystanie z klastrów AKS Virtual Machine Scale Sets i usługi [Azure usługa Load Balancer w warstwie Standardowa,](./load-balancer-standard.md) aby upewnić się, że masz takie funkcje jak: [](../virtual-machine-scale-sets/index.yml)
* [Pule wielu węzłów,](./use-multiple-node-pools.md)
* [Strefy dostępności](../availability-zones/az-overview.md),
* [Autoryzowane zakresy adresów IP,](./api-server-authorized-ip-ranges.md)
* [Cluster Autoscaler](./cluster-autoscaler.md),
* [Azure Policy dla AKS](../governance/policy/concepts/policy-for-kubernetes.md), i
* Inne nowe funkcje, gdy zostaną wydane.

Klastry AKS wspierane przez [zestawy dostępności maszyn](../virtual-machines/availability.md#availability-sets) wirtualnych nie obsługują wielu z tych funkcji.

Poniższy przykład tworzy klaster usługi AKS z pulą z jednym węzłem, która jest zapasowa przez zestaw skalowania maszyn wirtualnych. Klaster:
* Używa standardowego równoważenia obciążenia. 
* Włącza funkcję automatycznego skalowania klastra w puli węzłów dla klastra.
* Ustawia co najmniej *1* lub maksymalnie *3 węzły.*

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

Podczas migracji klastrów być może dołączono zewnętrzne usługi platformy Azure. Podczas gdy następujące usługi nie wymagają ponownego użycia zasobów, będą wymagać aktualizacji połączeń z poprzednich do nowych klastrów w celu utrzymania funkcjonalności.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Konto magazynu
* Zewnętrzne bazy danych

## <a name="ensure-valid-quotas"></a>Upewnij się, że przydziały są prawidłowe

Ponieważ inne maszyny wirtualne zostaną wdrożone w ramach subskrypcji podczas migracji, należy sprawdzić, czy limity przydziału i limity są wystarczające dla tych zasobów. W razie potrzeby zażądaj zwiększenia limitu przydziału [procesorów wirtualnych](../azure-portal/supportability/per-vm-quota-requests.md).

Może być konieczne zażądanie zwiększenia limitów przydziału [sieci w](../azure-portal/supportability/networking-quota-requests.md) celu zapewnienia, że nie wyczerpiesz liczby ip. Aby uzyskać więcej informacji, zobacz [sieci i zakresy adresów IP dla usługi AKS.](./configure-kubenet.md)

Aby uzyskać więcej informacji, zobacz [Azure subscription and service limits (Limity subskrypcji i usług platformy Azure).](../azure-resource-manager/management/azure-subscription-service-limits.md) Aby sprawdzić bieżące limity przydziału, w Azure Portal przejdź do bloku [subskrypcji,](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)wybierz swoją subskrypcję, a następnie wybierz pozycję Użycie **i przydziały.**

## <a name="high-availability-and-business-continuity"></a>Wysoka dostępność i ciągłość działalności biznesowej

Jeśli aplikacja nie może obsłużyć przestoju, należy postępować zgodnie z najlepszymi rozwiązaniami dla scenariuszy migracji o wysokiej dostępności. Przeczytaj więcej na temat najlepszych rozwiązań dotyczących złożonego planowania ciągłości działalności biznesowej, odzyskiwania po awarii i maksymalizowania czasu pracy [w Azure Kubernetes Service (AKS).](./operator-best-practices-multi-region.md)

W przypadku złożonych aplikacji zwykle przeprowadza się migrację w czasie, a nie jednocześnie, co oznacza, że stare i nowe środowiska mogą wymagać komunikacji za pośrednictwem sieci. Aplikacje, które wcześniej komunikowały się z usługami, muszą być udostępniane jako `ClusterIP` typ `LoadBalancer` i odpowiednio zabezpieczone.

Aby ukończyć migrację, należy wskazać klientom nowe usługi działające w u usługi AKS. Zalecamy przekierowywanie ruchu przez zaktualizowanie systemu DNS w taki sposób, aby Load Balancer znajduje się przed klastrem usługi AKS.

[Azure Traffic Manager](../traffic-manager/index.yml) może kierować klientów do żądanego klastra Kubernetes i wystąpienia aplikacji. Traffic Manager jest opartym na systemie DNS równoważeniem obciążenia ruchu, który może dystrybuować ruch sieciowy między regionami. Aby uzyskać najlepszą wydajność i nadmiarowość, przekieruj cały ruch aplikacji przez Traffic Manager przed jego skierowaniem do klastra usługi AKS. 

W przypadku wdrożenia z wieloma klastrami klienci powinni łączyć się z Traffic Manager DNS, która wskazuje usługi w każdym klastrze usługi AKS. Zdefiniuj te usługi przy użyciu Traffic Manager końcowych. Każdy punkt końcowy to *adres IP usługi równoważenia obciążenia*. Ta konfiguracja pozwala kierować ruch sieciowy z punktu końcowego Traffic Manager w jednym regionie do punktu końcowego w innym regionie.

![AKS z Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure Front Door Service](../frontdoor/front-door-overview.md) jest kolejną opcją routingu ruchu dla klastrów usługi AKS. Dzięki Azure Front Door Service można definiować i monitorować globalny routing ruchu internetowego oraz zarządzać nim, optymalizując wydajność i błyskawiczne globalne działanie w celu zachowania wysokiej dostępności. 

### <a name="considerations-for-stateless-applications"></a>Zagadnienia dotyczące aplikacji bez stanowych

Migracja aplikacji bez stanowych jest najprostszym przypadekem:
1. Zastosuj definicje zasobów (YAML lub Helm) do nowego klastra.
1. Upewnij się, że wszystko działa zgodnie z oczekiwaniami.
1. Przekieruj ruch, aby aktywować nowy klaster.

### <a name="considerations-for-stateful-applications"></a>Zagadnienia dotyczące aplikacji stanowych

Starannie zaplanuj migrację aplikacji stanowych, aby uniknąć utraty danych lub nieoczekiwanych przestojów.

* Jeśli używasz Azure Files, możesz zainstalować udział plików jako wolumin w nowym klastrze. Zobacz [Mount Static Azure Files as a Volume (Zainstaluj statyczny Azure Files jako wolumin).](./azure-files-volume.md#mount-file-share-as-an-persistent-volume)
* Jeśli używasz usługi Azure Dyski zarządzane, możesz zainstalować dysk tylko wtedy, gdy jest niedołączony do dowolnej maszyny wirtualnej. Zobacz Mount Static Azure Disk as a Volume (Zainstaluj [statyczny dysk platformy Azure jako wolumin).](./azure-disk-volume.md#mount-disk-as-volume)
* Jeśli żadna z tych metod nie działa, możesz użyć opcji tworzenia kopii zapasowej i przywracania. Zobacz [Velero na platformie Azure.](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

#### <a name="azure-files"></a>Azure Files

W przeciwieństwie do dysków Azure Files mogą być instalowane jednocześnie na wielu hostach. W klastrze usługi AKS platformy Azure i Kubernetes nie uniemożliwiają utworzenia zasobnika, który będzie nadal używany przez klaster usługi AKS. Aby zapobiec utracie danych i nieoczekiwanemu zachowaniu, upewnij się, że klastry nie zapisowały jednocześnie w tych samych plikach.

Jeśli aplikacja może hostować wiele replik, które wskazują ten sam udział plików, wykonaj kroki migracji bez stanowej i wdróż definicje YAML w nowym klastrze. 

Jeśli nie, jedno z możliwych podejść do migracji obejmuje następujące kroki:

1. Sprawdź, czy aplikacja działa prawidłowo.
1. Przekieruj ruch na żywo do nowego klastra usługi AKS.
1. Odłącz stary klaster.

Jeśli chcesz rozpocząć od pustego udziału i utworzyć kopię danych źródłowych, możesz użyć poleceń , aby przeprowadzić [`az storage file copy`](/cli/azure/storage/file/copy) migrację danych.


#### <a name="migrating-persistent-volumes"></a>Migrowanie trwałych woluminów

W przypadku migrowania istniejących trwałych woluminów do aplikacji AKS zwykle należy wykonać następujące kroki:

1. Tryb quiesce zapisuje do aplikacji. 
    * Ten krok jest opcjonalny i wymaga przestoju.
1. Tworzenie migawek dysków.
1. Tworzenie nowych dysków zarządzanych z migawek.
1. Tworzenie trwałych woluminów w uchwale AKS.
1. Zaktualizuj specyfikacje zasobników, [aby używać istniejących woluminów,](./azure-disk-volume.md) a nie persistentVolumeClaims (statyczne inicjowanie obsługi administracyjnej).
1. Wdrażanie aplikacji w u usługi AKS.
1. Sprawdź, czy aplikacja działa prawidłowo.
1. Przekieruj ruch na żywo do nowego klastra usługi AKS.

> [!IMPORTANT]
> Jeśli nie chcesz wywłaszczać zapisu, musisz replikować dane do nowego wdrożenia. W przeciwnym razie pominiesz dane zapisane po zrobieniu migawek dysku.

Niektóre narzędzia typu open source mogą ułatwić tworzenie dysków zarządzanych i migrowanie woluminów między klastrami Kubernetes:

* [Rozszerzenie kopiowania dysków interfejsu wiersza polecenia platformy Azure](https://github.com/noelbundick/azure-cli-disk-copy-extension) kopiuje i konwertuje dyski między grupami zasobów i regionami platformy Azure.
* [Rozszerzenie interfejsu wiersza polecenia usługi Azure Kube](https://github.com/yaron2/azure-kube-cli) wylicza woluminy ACS Kubernetes i migruje je do klastra usługi AKS.


### <a name="deployment-of-your-cluster-configuration"></a>Wdrażanie konfiguracji klastra

Zalecamy użycie istniejącego potoku ciągłej integracji (CI) i ciągłego dostarczania (CD) w celu wdrożenia znanej dobrej konfiguracji w u usługi AKS. Możesz użyć Azure Pipelines do [kompilowania i wdrażania aplikacji w u usługi AKS.](/azure/devops/pipelines/ecosystems/kubernetes/aks-template) Sklonuj istniejące zadania wdrażania i upewnij się, `kubeconfig` że wskazuje nowy klaster usługi AKS.

Jeśli nie jest to możliwe, wyeksportuj definicje zasobów z istniejącego klastra Kubernetes, a następnie zastosuj je do usługi AKS. Można użyć do `kubectl` wyeksportowania obiektów.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Przenoszenie istniejących zasobów do innego regionu

Możesz przenieść klaster usługi AKS do innego [regionu obsługiwanego przez usługi AKS.][region-availability] Zalecamy utworzenie nowego klastra w innym regionie, a następnie wdrożenie zasobów i aplikacji w nowym klastrze. 

Ponadto jeśli masz jakiekolwiek usługi, takie jak [Azure Dev Spaces][azure-dev-spaces] uruchomione w klastrze usługi AKS, musisz zainstalować i skonfigurować te usługi w klastrze w nowym regionie.


W tym artykule podsumowano szczegóły migracji dla:

> [!div class="checklist"]
> * AKS z usługa Load Balancer w warstwie Standardowa i Virtual Machine Scale Sets
> * Istniejące dołączone usługi platformy Azure
> * Upewnij się, że przydziały są prawidłowe
> * Wysoka dostępność i ciągłość działalności biznesowej
> * Zagadnienia dotyczące aplikacji bez stanowych
> * Zagadnienia dotyczące aplikacji stanowych
> * Wdrażanie konfiguracji klastra


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: ../dev-spaces/index.yml
