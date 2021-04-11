---
title: Najlepsze rozwiązania związane z ciągłością biznesową AKS i odzyskiwaniem po awarii
description: Zapoznaj się z najlepszymi rozwiązaniami operatora klastra, aby osiągnąć maksymalny czas pracy aplikacji, zapewniając wysoką dostępność i przygotowanie do odzyskiwania po awarii w usłudze Azure Kubernetes Service (AKS).
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 5bcd30c22132bc53ff28fdefcb73f686e08e34ea
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105089"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania związane z ciągłością biznesową i odzyskiwaniem po awarii w usłudze Azure Kubernetes Service (AKS)

Podczas zarządzania klastrami w usłudze Azure Kubernetes Service (AKS), działania aplikacji staną się ważne. Domyślnie AKS zapewnia wysoką dostępność przy użyciu wielu węzłów w [zestawie skalowania maszyn wirtualnych (VMSS)](../virtual-machine-scale-sets/overview.md). Jednak te wiele węzłów nie chroni systemu przed awarią regionu. Aby zmaksymalizować czas pracy, Planuj z wyprzedzeniem, aby zachować ciągłość działania i przygotować się do odzyskiwania po awarii.

Ten artykuł koncentruje się na tym, jak planować ciągłość działania i odzyskiwanie po awarii w programie AKS. Omawiane kwestie:

> [!div class="checklist"]
> * Planowanie klastrów AKS w wielu regionach.
> * Kierowanie ruchu sieciowego między wieloma klastrami przy użyciu usługi Azure Traffic Manager.
> * Użyj replikacji geograficznej dla rejestrów obrazów kontenerów.
> * Planowanie stanu aplikacji w wielu klastrach.
> * Replikowanie magazynu w wielu regionach.

## <a name="plan-for-multiregion-deployment"></a>Planowanie wdrożenia wieloregionowego

> **Najlepsze rozwiązanie**
>
> Podczas wdrażania wielu klastrów AKS wybierz regiony, w których AKS jest dostępny. Użyj sparowanych regionów.

Klaster AKS jest wdrażany w jednym regionie. Aby chronić system przed awarią regionu, wdróż aplikację w wielu klastrach AKS w różnych regionach. Planując miejsce wdrożenia klastra AKS, należy rozważyć następujące kwestie:

* [**Dostępność regionu AKS**](./quotas-skus-regions.md#region-availability)
    * Wybierz regiony blisko Twoich użytkowników. 
    * AKS ciągle rozszerza się w nowe regiony.
* [**Sparowane regiony platformy Azure**](../best-practices-availability-paired-regions.md)
    * W obszarze geograficznym wybierz dwa regiony sparowane.
    * Sparowane regiony koordynują aktualizacje platformy i ustalają priorytety działań związanych z odzyskiwaniem w razie konieczności.
* **Dostępność usługi**
    * Zdecyduj, czy sparowane regiony powinny mieć gorącą/gorącą, gorącą/ciepłą lub gorącą/zimną.
    * Czy chcesz uruchomić oba regiony w tym samym czasie, z jednym regionem *gotowym* do uruchomienia ruchu? Lub:
    * Czy chcesz nadać jednemu regionowi czas na przygotowanie do obsługi ruchu?

AKS region dostępności i sparowane regiony są wspólne. Wdróż klastry AKS w sparowanych regionach zaprojektowanych do zarządzania odzyskiwaniem po awarii regionu. Na przykład AKS jest dostępna w regionach Wschodnie stany USA i zachodnie stany USA. Te regiony są sparowane. Wybierz te dwa regiony podczas tworzenia strategii AKS BC/DR.

Podczas wdrażania aplikacji należy dodać kolejny krok do potoku ciągłej integracji/ciągłego wdrażania, aby wdrożyć go w wielu klastrach AKS. Aktualizowanie potoków wdrożenia uniemożliwia wdrażanie aplikacji w tylko jednym regionie i w klastrach AKS. W tym scenariuszu ruch klienta kierowany do regionu pomocniczego nie będzie otrzymywać najnowszych aktualizacji kodu.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Kierowanie ruchu przy użyciu usługi Azure Traffic Manager

> **Najlepsze rozwiązanie**
>
> Usługa Azure Traffic Manager może kierować do najbliższego klastra AKS i wystąpienia aplikacji. Aby uzyskać najlepszą wydajność i nadmiarowość, należy skierować cały ruch aplikacji przez Traffic Manager przed przejściem do klastra AKS.

Jeśli masz wiele klastrów AKS w różnych regionach, użyj Traffic Manager do kontrolowania przepływu ruchu do aplikacji uruchomionych w każdym klastrze. [Azure Traffic Manager](../traffic-manager/index.yml) to oparty na systemie DNS moduł równoważenia obciążenia, który może dystrybuować ruch sieciowy między regionami. Użyj Traffic Manager, aby kierować użytkowników na podstawie czasu odpowiedzi klastra lub na podstawie lokalizacji geograficznej.

![AKS z Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Jeśli masz pojedynczy klaster AKS, zazwyczaj nawiązujesz połączenie z adresem IP usługi lub nazwą DNS danej aplikacji. W przypadku wdrożenia obejmującego wiele klastrów należy nawiązać połączenie z nazwą DNS Traffic Manager, która wskazuje usługi w każdym klastrze AKS. Zdefiniuj te usługi przy użyciu punktów końcowych Traffic Manager. Każdy punkt końcowy jest *adresem IP modułu równoważenia obciążenia usługi*. Ta konfiguracja umożliwia kierowanie ruchu sieciowego z punktu końcowego Traffic Manager w jednym regionie do punktu końcowego w innym regionie.

![Routing geograficzny przez Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager wykonuje wyszukiwania DNS i zwraca najbardziej odpowiedni punkt końcowy. Profile zagnieżdżone mogą określać priorytet lokalizacji podstawowej. Na przykład należy nawiązać połączenie z najbliższym regionem geograficznym. Jeśli ten region ma problem, Traffic Manager kieruje do regionu pomocniczego. Takie podejście zapewnia, że można nawiązać połączenie z wystąpieniem aplikacji nawet wtedy, gdy najbliższy region geograficzny jest niedostępny.

Aby uzyskać informacje na temat konfigurowania punktów końcowych i routingu, zobacz [Konfigurowanie metody routingu ruchu geograficznego przy użyciu Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).

### <a name="application-routing-with-azure-front-door-service"></a>Kierowanie aplikacji za pomocą usługi Azure Front drzwiczk

Korzystając z podziału protokołu emisji pojedynczej opartego na protokole TCP, [Usługa frontonu platformy Azure](../frontdoor/front-door-overview.md) bezzwłocznie łączy użytkowników końcowych z najbliższym punktem pop z przodu (punkt obecności). Więcej funkcji usługi Azure front-drzwi:
* Zakończenie protokołu TLS
* Domena niestandardowa
* Zapora aplikacji internetowej
* Ponowne zapisywanie adresów URL
* Koligacja sesji 

Przejrzyj potrzeby ruchu aplikacji, aby zrozumieć, które rozwiązanie jest najbardziej odpowiednie.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Regiony programu InterConnect z globalną siecią wirtualną sieci wirtualnej

Połącz sieci wirtualne ze sobą za pomocą [komunikacji równorzędnej sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md) , aby umożliwić komunikację między klastrami. Wirtualne sieci równorzędne łączą się z sieciami wirtualnymi, zapewniając wysoką przepustowość w sieci szkieletowej firmy Microsoft — nawet w różnych regionach geograficznych.

Przed równorzędnymi sieciami wirtualnymi z uruchomionymi klastrami AKS należy używać standardowego Load Balancer w klastrze AKS. To wymaganie wstępne sprawia, że usługi Kubernetes są dostępne dla komunikacji równorzędnej sieci wirtualnej.

## <a name="enable-geo-replication-for-container-images"></a>Włącz replikację geograficzną dla obrazów kontenerów

> **Najlepsze rozwiązanie**
> 
> Przechowuj obrazy kontenerów w Azure Container Registry i geograficznie Replikuj rejestr do każdego regionu AKS.

Aby wdrażać i uruchamiać aplikacje w programie AKS, musisz mieć możliwość przechowywania i ściągania obrazów kontenerów. Container Registry integruje się z usługą AKS, dzięki czemu może bezpiecznie przechowywać obrazy kontenerów lub wykresy Helm. Container Registry obsługuje wielogłówną replikację geograficzną, aby automatycznie replikować obrazy do regionów platformy Azure na całym świecie. 

Aby zwiększyć wydajność i dostępność:
1. Użyj Container Registry replikacji geograficznej, aby utworzyć rejestr w każdym regionie, w którym znajduje się klaster AKS. 
1. Każdy klaster AKS następnie ściąga obrazy kontenerów z rejestru kontenerów lokalnych w tym samym regionie:

![Container Registry replikację geograficzną dla obrazów kontenerów](media/operator-best-practices-bc-dr/acr-geo-replication.png)

W przypadku używania Container Registry replikacji geograficznej do ściągania obrazów z tego samego regionu wyniki są następujące:

* **Szybsze**: ściąganie obrazów z połączeń sieciowych o dużej szybkości i małych opóźnieniach w tym samym regionie świadczenia usługi Azure.
* **Bardziej niezawodne**: jeśli region jest niedostępny, klaster AKS pobiera obrazy z dostępnego rejestru kontenerów.
* **Tańsze**: brak opłaty za wyjście z sieci między centrami danych.

Replikacja geograficzna to funkcja rejestru kontenerów jednostki SKU w *warstwie Premium* . Aby uzyskać informacje na temat konfigurowania replikacji geograficznej, zobacz [Container Registry replikacji geograficznej](../container-registry/container-registry-geo-replication.md).

## <a name="remove-service-state-from-inside-containers"></a>Usuwanie stanu usługi z wewnątrz kontenerów

> **Najlepsze rozwiązanie**
> 
> Unikaj zapisywania stanu usługi wewnątrz kontenera. Zamiast tego należy użyć platformy Azure jako usługi (PaaS), która obsługuje replikację w wielu regionach.

*Stan usługi* odnosi się do danych znajdujących się w pamięci lub na dysku, które są wymagane przez usługę do działania. Stan obejmuje struktury danych i zmienne składowe, które Usługa odczytuje i zapisuje. W zależności od sposobu tworzenia architektury usługi stan może zawierać również pliki lub inne zasoby przechowywane na dysku. Na przykład stan może obejmować pliki, których baza danych używa do przechowywania danych i dzienników transakcji.

Stan może być zewnętrzny lub wspólnie zlokalizowany z kodem, który operuje na stanie. Zazwyczaj Externalize stan przy użyciu bazy danych lub innego magazynu danych, który działa na różnych maszynach za pośrednictwem sieci lub który został uruchomiony na tym samym komputerze.

Kontenery i mikrousługi są najbardziej odporne, gdy procesy, które w nich działają, nie zachowują stanu. Ponieważ aplikacje prawie zawsze zawierają jakiś stan, należy użyć rozwiązania PaaS, takiego jak:
* Azure Cosmos DB
* Azure Database for PostgreSQL
* Azure Database for MySQL
* Azure SQL Database

Aby skompilować aplikacje przenośne, zapoznaj się z następującymi wskazówkami:

* [Metodologia aplikacji 12-Factor](https://12factor.net/)
* [Uruchamianie aplikacji sieci Web w wielu regionach platformy Azure](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Utwórz plan migracji magazynu

> **Najlepsze rozwiązanie**
>
> Jeśli używasz usługi Azure Storage, przygotuj i przetestuj sposób migrowania magazynu z regionu podstawowego do regionu kopii zapasowej.

Aplikacje mogą używać usługi Azure Storage do przechowywania swoich danych. W takim przypadku aplikacje są rozłożone na wiele klastrów AKS w różnych regionach. Należy zachować synchronizację magazynu. Poniżej przedstawiono dwa typowe sposoby replikowania magazynu:

* Replikacja asynchroniczna oparta na infrastrukturze
* Replikacja asynchroniczna oparta na aplikacji

### <a name="infrastructure-based-asynchronous-replication"></a>Replikacja asynchroniczna oparta na infrastrukturze

Aplikacje mogą wymagać trwałego magazynu nawet po usunięciu pod. W programie Kubernetes można używać woluminów trwałych w celu utrwalania magazynu danych. Woluminy trwałe są instalowane na maszynę wirtualną węzła, a następnie udostępniane do zasobników. Woluminy trwałe są zgodne z wielkością i nawet wtedy, gdy zasobniki są przenoszone do innego węzła w tym samym klastrze.

Stosowana strategia replikacji zależy od rozwiązania do magazynowania. Następujące typowe rozwiązania magazynu zapewniają własne wskazówki dotyczące odzyskiwania po awarii i replikacji:
* [Gluster](https://docs.gluster.org/en/latest/Administrator-Guide/Geo-Replication/)
* [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/)
* [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)
* [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) 

Zwykle udostępniasz wspólny punkt magazynu, w którym aplikacje zapisują swoje dane. Te dane są następnie replikowane w regionach i dostępne lokalnie.

![Replikacja asynchroniczna oparta na infrastrukturze](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Jeśli używasz usługi Azure Managed Disks, możesz użyć [Velero na platformie Azure][velero] i [Kasten][kasten] do obsługi replikacji i odzyskiwania po awarii. Te opcje służą do tworzenia kopii zapasowych rozwiązań natywnych, ale nieobsługiwanych przez Kubernetes.

### <a name="application-based-asynchronous-replication"></a>Replikacja asynchroniczna oparta na aplikacji

Kubernetes obecnie nie zapewnia natywnej implementacji replikacji asynchronicznej opartej na aplikacji. Ponieważ kontenery i Kubernetes są luźno powiązane, należy zastosować wszelkie tradycyjne podejście do aplikacji lub języka. Zazwyczaj aplikacje same replikujeją żądania magazynu, które są następnie zapisywane do magazynu danych w klastrze.

![Replikacja asynchroniczna oparta na aplikacji](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Następne kroki

Ten artykuł koncentruje się na zagadnieniach związanych z ciągłością biznesową i odzyskiwaniem po awarii w przypadku klastrów AKS. Aby uzyskać więcej informacji na temat operacji klastra w programie AKS, zobacz następujące artykuły o najlepszych rozwiązaniach:

* [Wielodostępność i izolacja klastra][aks-best-practices-cluster-isolation]
* [Podstawowe funkcje usługi Kubernetes Scheduler][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md

[velero]: https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md
[kasten]: https://www.kasten.io/