---
title: Omówienie Azure Monitor kontenerów | Microsoft Docs
description: W tym artykule opisano Azure Monitor kontenerów monitorujących rozwiązanie AKS datacontainer Insights i wartość dostarczaną przez monitorowanie kondycji klastrów AKS i Container Instances na platformie Azure.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 3ff2c35ae9f5838447ce90e2a020649427920a43
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79275232"
---
# <a name="azure-monitor-for-containers-overview"></a>Omówienie usługi Azure Monitor dla kontenerów

Azure Monitor for Containers to funkcja przeznaczona do monitorowania wydajności obciążeń kontenera wdrożonych w celu:

- Klastry Managed Kubernetes hostowane w [usłudze Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md)
- Samozarządzane klastry Kubernetes hostowane na platformie Azure przy użyciu [aparatu AKS](https://github.com/Azure/aks-engine)
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- Samozarządzane klastry Kubernetes hostowane w [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) lub lokalnie
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

Azure Monitor dla kontenerów obsługuje klastry z systemem operacyjnym Linux i Windows Server 2019. 

Monitorowanie kontenerów ma krytyczne znaczenie, szczególnie w przypadku korzystania z klastra produkcyjnego w odpowiedniej skali z wieloma aplikacjami.

Azure Monitor for Containers zapewnia wgląd w wydajność przez zbieranie metryk pamięci i procesorów z kontrolerów, węzłów i kontenerów dostępnych w Kubernetes za pośrednictwem interfejsu API metryk. Gromadzone są też dzienniki kontenerów.  Po włączeniu monitorowania z klastrów Kubernetes są automatycznie zbierane metryki i dzienniki za pośrednictwem platformy Log Analytics agenta dla systemu Linux. Metryki są zapisywane w magazynie metryk i dane dziennika są zapisywane w magazynie dzienników skojarzonym z obszarem roboczym [log Analytics](../log-query/log-query-overview.md) . 

![Azure Monitor dla architektury kontenerów](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Co zapewnia Azure Monitor kontenerów?

Azure Monitor for Containers oferuje kompleksowe środowisko monitorowania wykorzystujące różne funkcje Azure Monitor. Te funkcje umożliwiają zrozumienie wydajności i kondycji klastra Kubernetes z systemem operacyjnym Linux i Windows Server 2019 oraz obciążeń kontenerów. Za pomocą Azure Monitor dla kontenerów można:

* Zidentyfikuj kontenery AKS, które są uruchomione w węźle, i ich średniego użycia procesora i pamięci. Ta wiedza ułatwia identyfikowanie wąskich gardeł zasobów.
* Identyfikuj użycie procesora i pamięci w grupach kontenerów i ich kontenerach hostowanych w Azure Container Instances.  
* Określ, gdzie kontener znajduje się na kontrolerze lub pod. Ta wiedza może pomóc w wyświetleniu ogólnej wydajności kontrolera lub pod. 
* Zapoznaj się z użyciem zasobów obciążeń uruchomionych na hoście, które nie są związane ze standardowymi procesami, które obsługują ten element.
* Zapoznaj się z zachowaniem klastra w obszarze średnie i najcięższe obciążenia. Ta wiedza ułatwia identyfikowanie potrzeb związanych z pojemnością i określanie maksymalnego obciążenia, które może utrzymywać klaster. 
* Konfigurowanie alertów w celu aktywnego powiadamiania użytkownika lub rejestrowania go, gdy użycie procesora i pamięci w węzłach lub kontenerach przekracza progi lub w przypadku zmiany stanu kondycji w klastrze na zestawienie kondycji infrastruktury lub węzłów.
* Integracja z usługą [Prometheus](https://prometheus.io/docs/introduction/overview/) w celu wyświetlania metryk aplikacji i obciążeń zbieranych z węzłów i Kubernetes przy użyciu [zapytań](container-insights-log-search.md) w celu utworzenia niestandardowych alertów, pulpitów nawigacyjnych i szczegółowej szczegółowej analizy.
* Monitoruj obciążenia kontenera [wdrożone w aparacie AKS aparatów](https://github.com/Azure/aks-engine) lokalnych i [AKS na Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
* Monitoruj obciążenia kontenerów [wdrożone na platformie Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >Obsługa usługi Azure Red Hat OpenShift jest w tej chwili funkcją w publicznej wersji zapoznawczej.
    >

Główne różnice w monitorowaniu klastra z systemem Windows Server w porównaniu z klastrem systemu Linux są następujące:

- Metryka RSS pamięci nie jest dostępna dla węzła i kontenerów systemu Windows.
- Informacje o pojemności magazynu dyskowego nie są dostępne dla węzłów systemu Windows.
- Dzienniki kontenerów nie są dostępne dla kontenerów uruchomionych w węzłach systemu Windows.
- Obsługa funkcji danych na żywo (wersja zapoznawcza) jest dostępna z wyjątkiem dzienników kontenera systemu Windows.
- Monitorowane są tylko środowiska pod środowiskiem, a nie środowiska Docker.
- W wersji zapoznawczej obsługiwane są maksymalnie 30 kontenerów systemu Windows Server. To ograniczenie nie dotyczy kontenerów systemu Linux. 

Zapoznaj się z poniższym filmem wideo dostarczającym poziom pośredni szczegółowe, aby uzyskać informacje na temat monitorowania klastra AKS przy użyciu Azure Monitor dla kontenerów.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Jak mogę uzyskać dostęp do tej funkcji?

Dostęp do Azure Monitor kontenerów można uzyskać na dwa sposoby, od Azure Monitor lub bezpośrednio z wybranego klastra AKS. Od Azure Monitor masz globalną perspektywę wszystkich wdrożonych kontenerów, które są monitorowane, które nie są, co pozwala na wyszukiwanie i filtrowanie w ramach subskrypcji i grup zasobów, a następnie przechodzenie do szczegółów Azure Monitor dla kontenerów z wybranego kontenera.  W przeciwnym razie możesz uzyskać dostęp do funkcji bezpośrednio z wybranego kontenera AKS na stronie AKS.  

![Przegląd metod uzyskiwania dostępu do Azure Monitor kontenerów](./media/container-insights-overview/azmon-containers-experience.png)

Jeśli interesujesz się monitorowaniem i zarządzaniem hostami platformy Docker i kontenera systemu Windows działającym poza programem AKS, aby wyświetlić konfigurację, inspekcję i wykorzystanie zasobów, zobacz [rozwiązanie do monitorowania kontenerów](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć monitorowanie klastra Kubernetes, zapoznaj się z [tematem jak włączyć Azure monitor dla kontenerów](container-insights-onboard.md) , aby zrozumieć wymagania i dostępne metody umożliwiające monitorowanie. 
