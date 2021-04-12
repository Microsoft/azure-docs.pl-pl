---
title: Najlepsze rozwiązania związane z izolacją klastra
titleSuffix: Azure Kubernetes Service
description: Zapoznaj się z najlepszymi rozwiązaniami operatora klastra na potrzeby izolacji w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: e51689d33711f127f775c63c9d7fc8ad4c901604
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105174"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania związane z izolacją klastra w usłudze Azure Kubernetes Service (AKS)

Podczas zarządzania klastrami w usłudze Azure Kubernetes Service (AKS) często trzeba izolować zespoły i obciążenia. AKS zapewnia elastyczność w zakresie uruchamiania klastrów wielodostępnych i izolowania zasobów. Aby zmaksymalizować swoją inwestycję w Kubernetes, należy najpierw zrozumieć i zaimplementować funkcje wielodostępności i izolacji AKS.

Ten artykuł dotyczący najlepszych rozwiązań koncentruje się na izolacji operatorów klastra. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Planowanie klastrów z wieloma dzierżawcami i separacja zasobów
> * Używanie izolacji logicznej lub fizycznej w klastrach AKS

## <a name="design-clusters-for-multi-tenancy"></a>Projektowanie klastrów dla wielu dzierżawców

Kubernetes umożliwia Logiczne izolowanie zespołów i obciążeń w tym samym klastrze. Celem jest zapewnienie najmniejszej liczby uprawnień w zakresie zasobów, których potrzebuje każdy zespół. [Przestrzeń nazw][k8s-namespaces] Kubernetes tworzy logiczną granicę izolacji. Dodatkowe funkcje Kubernetes oraz zagadnienia dotyczące izolacji i wielu dzierżawców obejmują następujące obszary:

### <a name="scheduling"></a>Planowanie

*Planowanie* używa podstawowych funkcji, takich jak przydziały zasobów i budżety na przerwy. Aby uzyskać więcej informacji o tych funkcjach, zobacz [najlepsze rozwiązania dotyczące podstawowych funkcji usługi Scheduler w AKS][aks-best-practices-scheduler].

Bardziej zaawansowane funkcje usługi Scheduler obejmują:
* Przyznaczenia i tolerowanie
* Selektory węzłów
* Koligacja węzła i pod. 

Aby uzyskać więcej informacji o tych funkcjach, zobacz [najlepsze rozwiązania dotyczące zaawansowanych funkcji usługi Scheduler w programie AKS][aks-best-practices-advanced-scheduler].

### <a name="networking"></a>Sieć

*Sieć* używa zasad sieciowych do sterowania przepływem ruchu do i z zasobników.

### <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

Użycie *uwierzytelniania i autoryzacji* :
* Kontrola dostępu oparta na rolach (RBAC)
* Integracja z usługą Azure Active Directory (AD)
* Tożsamości pod
* Wpisy tajne w Azure Key Vault 

Aby uzyskać więcej informacji o tych funkcjach, zobacz [najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w programie AKS][aks-best-practices-identity].

### <a name="containers"></a>Kontenery
*Kontenery* obejmują:
* Dodatek Azure Policy dla AKS wymusza zabezpieczenia pod względem zabezpieczeń.
* Korzystanie z kontekstów zabezpieczeń pod.
* Skanowanie obu obrazów i środowiska uruchomieniowego w poszukiwaniu luk w zabezpieczeniach. 
* Użycie ochrony aplikacji lub Seccomp (Zabezpieczanie obliczeń) w celu ograniczenia dostępu kontenera do podstawowego węzła.

## <a name="logically-isolate-clusters"></a>Logicznie Izoluj klastry

> **Wskazówki dotyczące najlepszych rozwiązań**
>
> Oddziel zespoły i projekty przy użyciu *izolacji logicznej*. Zminimalizuj liczbę fizycznych klastrów AKS wdrażanych w celu izolowania zespołów lub aplikacji.

Z izolacją logiczną pojedynczy klaster AKS może być używany w wielu obciążeniach, zespołach lub środowiskach. [Przestrzenie nazw][k8s-namespaces] Kubernetes tworzą granice logicznej izolacji dla obciążeń i zasobów.

![Logiczna izolacja klastra Kubernetes w AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Logiczne rozdzielenie klastrów zwykle zapewnia wyższą gęstość pod względem klastrów niż fizycznie izolowane. dzięki mniejszej wydajności obliczeniowej w klastrze. W połączeniu z automatycznym skalowaniem klastra Kubernetes można skalować liczbę węzłów w górę lub w dół w celu spełnienia wymagań. To najlepsze rozwiązanie do automatycznego skalowania minimalizuje koszty, uruchamiając tylko wymaganą liczbę węzłów.

Obecnie środowiska Kubernetes nie są całkowicie bezpieczne dla niebezpiecznym użyciem wielu dzierżawców. W środowisku z wieloma dzierżawcami wiele dzierżawców pracuje nad wspólną, udostępnioną infrastrukturą. Jeśli wszyscy dzierżawcy nie będą Zaufani, konieczne będzie dodatkowe planowanie, aby uniemożliwić dzierżawcom wpływ na bezpieczeństwo i obsługę innych usług.

Dodatkowe funkcje zabezpieczeń, takie jak *zasady zabezpieczeń* , lub Kubernetes RBAC dla węzłów, efektywnie uniemożliwiają luki w zabezpieczeniach. W celu zapewnienia prawdziwych zabezpieczeń w przypadku nieprzechodnich obciążeń z wieloma dzierżawcami należy jedynie zaufać funkcji hypervisor. Domena zabezpieczeń dla Kubernetes jest cały klaster, a nie pojedynczy węzeł. 

W przypadku tych typów nieszkodliwych obciążeń z wieloma dzierżawcami należy używać klastrów fizycznie izolowanych.

## <a name="physically-isolate-clusters"></a>Izolowanie klastrów

> **Wskazówki dotyczące najlepszych rozwiązań** 
>
> Zminimalizuj użycie izolacji fizycznej dla każdego oddzielnego wdrożenia zespołu lub aplikacji. Zamiast tego należy użyć izolacji *logicznej* , zgodnie z opisem w poprzedniej sekcji.

Fizyczne oddzielenie klastrów AKS jest typowym podejściem do izolacji klastra. W tym modelu izolacji zespoły lub obciążenia są przypisani do własnego klastra AKS. Chociaż izolacja fizyczna może wyglądać jak Najprostszym sposobem izolowania obciążeń lub zespołów, dodaje koszty zarządzania i finansowe. Teraz należy zachować te wiele klastrów i indywidualnie zapewnić dostęp i uprawnienia do przypisywania. Opłaty są naliczane za każdy węzeł.

![Fizyczna izolacja pojedynczych klastrów Kubernetes w AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fizycznie oddzielne klastry mają zazwyczaj niską gęstość pod. Ponieważ każdy zespół lub obciążenie ma własny klaster AKS, klaster jest często nadmiernie inicjowany przy użyciu zasobów obliczeniowych. Często w tych węzłach zaplanowano małą liczbę zasobników. Nie można używać nieobjętej pojemności węzła w przypadku aplikacji lub usług programistycznych przez inne zespoły. Te nadmiarowe zasoby przyczyniają się do dodatkowych kosztów w oddzielnym klastrze.

## <a name="next-steps"></a>Następne kroki

Ten artykuł koncentruje się na izolowaniu klastra. Aby uzyskać więcej informacji na temat operacji klastra w programie AKS, zobacz następujące najlepsze rozwiązania:

* [Podstawowe funkcje usługi Kubernetes Scheduler][aks-best-practices-scheduler]
* [Zaawansowane funkcje usługi Scheduler Kubernetes][aks-best-practices-advanced-scheduler]
* [Uwierzytelnianie i autoryzacja][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
