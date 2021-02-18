---
title: Omówienie Azure Monitor kontenerów | Microsoft Docs
description: W tym artykule opisano Azure Monitor kontenerów monitorujących rozwiązanie AKS datacontainer Insights i wartość dostarczaną przez monitorowanie kondycji klastrów AKS i Container Instances na platformie Azure.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: a9b9e155884b20c19b9b82994a3b9b1bdf53f27a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621190"
---
# <a name="azure-monitor-for-containers-overview"></a>Omówienie usługi Azure Monitor dla kontenerów

Azure Monitor for Containers to funkcja przeznaczona do monitorowania wydajności obciążeń kontenera wdrożonych w celu:

- Klastry Managed Kubernetes hostowane w [usłudze Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md)
- Samozarządzane klastry Kubernetes hostowane na platformie Azure przy użyciu [aparatu AKS](https://github.com/Azure/aks-engine)
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- Samozarządzane klastry Kubernetes hostowane w [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) lub lokalnie
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)
- [Usługa Azure ARC z włączonym Kubernetes](../../azure-arc/kubernetes/overview.md) (wersja zapoznawcza)

Azure Monitor dla kontenerów obsługuje klastry z systemem operacyjnym Linux i Windows Server 2019. Obsługiwane środowiska uruchomieniowe kontenera to Docker, Moby i wszystkie zgodne z CRI środowiska uruchomieniowego, takie jak CRI-O i kontenery.

Monitorowanie kontenerów ma krytyczne znaczenie, szczególnie w przypadku korzystania z klastra produkcyjnego w odpowiedniej skali z wieloma aplikacjami.

Azure Monitor for Containers zapewnia wgląd w wydajność przez zbieranie metryk pamięci i procesorów z kontrolerów, węzłów i kontenerów dostępnych w Kubernetes za pośrednictwem interfejsu API metryk. Gromadzone są też dzienniki kontenerów.  Po włączeniu monitorowania z klastrów Kubernetes są automatycznie zbierane metryki i dzienniki za pośrednictwem platformy Log Analytics agenta dla systemu Linux. Metryki są zapisywane w magazynie metryk i dane dziennika są zapisywane w magazynie dzienników skojarzonym z obszarem roboczym [log Analytics](../log-query/log-query-overview.md) .

![Azure Monitor dla architektury kontenerów](./media/container-insights-overview/azmon-containers-architecture-01.png)

## <a name="what-does-azure-monitor-for-containers-provide"></a>Co zapewnia Azure Monitor kontenerów?

Azure Monitor for Containers oferuje kompleksowe środowisko monitorowania wykorzystujące różne funkcje Azure Monitor. Te funkcje umożliwiają zrozumienie wydajności i kondycji klastra Kubernetes z systemem operacyjnym Linux i Windows Server 2019 oraz obciążeń kontenerów. Dzięki usłudze Azure Monitor dla kontenerów można wykonać następujące czynności:

* Zidentyfikuj kontenery AKS, które są uruchomione w węźle, i ich średniego użycia procesora i pamięci. Ta wiedza ułatwia identyfikowanie wąskich gardeł zasobów.
* Identyfikuj użycie procesora i pamięci w grupach kontenerów i ich kontenerach hostowanych w Azure Container Instances.
* Określ, gdzie kontener znajduje się na kontrolerze lub pod. Ta wiedza może pomóc w wyświetleniu ogólnej wydajności kontrolera lub pod.
* Zapoznaj się z użyciem zasobów obciążeń uruchomionych na hoście, które nie są związane ze standardowymi procesami, które obsługują ten element.
* Zapoznaj się z zachowaniem klastra w obszarze średnie i najcięższe obciążenia. Ta wiedza ułatwia identyfikowanie potrzeb związanych z pojemnością i określanie maksymalnego obciążenia, które może utrzymywać klaster.
* Konfigurowanie alertów w celu aktywnego powiadamiania użytkownika lub rejestrowania go, gdy użycie procesora i pamięci w węzłach lub kontenerach przekracza progi lub w przypadku zmiany stanu kondycji w klastrze na zestawienie kondycji infrastruktury lub węzłów.
* Integracja z usługą [Prometheus](https://prometheus.io/docs/introduction/overview/) w celu wyświetlania metryk aplikacji i obciążeń zbieranych z węzłów i Kubernetes przy użyciu [zapytań](container-insights-log-search.md) w celu utworzenia niestandardowych alertów, pulpitów nawigacyjnych i szczegółowej szczegółowej analizy.
* Monitoruj obciążenia kontenera [wdrożone w aparacie AKS aparatów](https://github.com/Azure/aks-engine) lokalnych i [AKS na Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview).
* Monitoruj obciążenia kontenerów [wdrożone na platformie Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >Obsługa usługi Azure Red Hat OpenShift jest w tej chwili funkcją w publicznej wersji zapoznawczej.
    >

* Monitoruj obciążenia kontenerów [wdrożone w usłudze Azure ARC z włączonym Kubernetes (wersja zapoznawcza)](../../azure-arc/kubernetes/overview.md).

Główne różnice w monitorowaniu klastra z systemem Windows Server w porównaniu z klastrem systemu Linux są następujące:

- System Windows nie ma metryki pamięci RSS i w związku z tym nie jest dostępny dla węzłów i kontenerów systemu Windows. Dostępna jest Metryka [zestawu roboczego](/windows/win32/memory/working-set) .
- Informacje o pojemności magazynu dyskowego nie są dostępne dla węzłów systemu Windows.
- Monitorowane są tylko środowiska pod środowiskiem, a nie środowiska Docker.
- W wersji zapoznawczej obsługiwane są maksymalnie 30 kontenerów systemu Windows Server. To ograniczenie nie dotyczy kontenerów systemu Linux.

Zapoznaj się z poniższym filmem wideo dostarczającym poziom pośredni szczegółowe, aby uzyskać informacje na temat monitorowania klastra AKS przy użyciu Azure Monitor dla kontenerów.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Jak mogę uzyskać dostęp do tej funkcji?

Dostęp do Azure Monitor kontenerów można uzyskać na dwa sposoby, od Azure Monitor lub bezpośrednio z wybranego klastra AKS. Od Azure Monitor masz globalną perspektywę wszystkich wdrożonych kontenerów, które są monitorowane, które nie są, co pozwala na wyszukiwanie i filtrowanie w ramach subskrypcji i grup zasobów, a następnie przechodzenie do szczegółów Azure Monitor dla kontenerów z wybranego kontenera.  W przeciwnym razie możesz uzyskać dostęp do funkcji bezpośrednio z wybranego kontenera AKS na stronie AKS.

![Przegląd metod uzyskiwania dostępu do Azure Monitor kontenerów](./media/container-insights-overview/azmon-containers-experience.png)

Jeśli interesujesz się monitorowaniem i zarządzaniem hostami platformy Docker i kontenera systemu Windows działającym poza programem AKS, aby wyświetlić konfigurację, inspekcję i wykorzystanie zasobów, zobacz [rozwiązanie do monitorowania kontenerów](../insights/containers.md).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć monitorowanie klastra Kubernetes, zapoznaj się z [tematem jak włączyć Azure monitor dla kontenerów](container-insights-onboard.md) , aby zrozumieć wymagania i dostępne metody umożliwiające monitorowanie.

