---
title: Najlepsze rozwiązania związane z izolacją klastra
titleSuffix: Azure Kubernetes Service
description: Zapoznaj się z najlepszymi rozwiązaniami operatora klastra na potrzeby izolacji w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: cdeecabf569e3c6f9b280e6b0179e5378f5b1c95
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011374"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania związane z izolacją klastra w usłudze Azure Kubernetes Service (AKS)

Podczas zarządzania klastrami w usłudze Azure Kubernetes Service (AKS) często trzeba izolować zespoły i obciążenia. AKS zapewnia elastyczność w zakresie uruchamiania klastrów wielodostępnych i izolowania zasobów. Aby zmaksymalizować swoją inwestycję w Kubernetes, należy zrozumieć i zaimplementować te funkcje do obsługi wielu dzierżawców i izolacji.

Ten artykuł dotyczący najlepszych rozwiązań koncentruje się na izolacji operatorów klastra. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Planowanie klastrów z wieloma dzierżawcami i separacja zasobów
> * Używanie izolacji logicznej lub fizycznej w klastrach AKS

## <a name="design-clusters-for-multi-tenancy"></a>Projektowanie klastrów dla wielu dzierżawców

Platforma Kubernetes udostępnia funkcje, które umożliwiają logiczną izolację zespołów i obciążeń w tym samym klastrze. Liczba uprawnień powinna być jak najmniejsza, a same uprawnienia — ograniczone do zasobów używanych w danym zespole. [Przestrzeń nazw][k8s-namespaces] w Kubernetes tworzy logiczną granicę izolacji. Dodatkowe funkcje Kubernetes oraz zagadnienia dotyczące izolacji i wielu dzierżawców obejmują następujące obszary:

* **Planowanie** obejmuje korzystanie z podstawowych funkcji, takich jak przydziały zasobów i budżety na przerwy. Aby uzyskać więcej informacji o tych funkcjach, zobacz [najlepsze rozwiązania dotyczące podstawowych funkcji usługi Scheduler w AKS][aks-best-practices-scheduler].
  * Bardziej zaawansowane funkcje harmonogramu obejmują: przeznaczenia i tolerowanie, selektory węzłów oraz koligacje węzła i pod. Aby uzyskać więcej informacji o tych funkcjach, zobacz [najlepsze rozwiązania dotyczące zaawansowanych funkcji usługi Scheduler w programie AKS][aks-best-practices-advanced-scheduler].
* **Sieć** obejmuje korzystanie z zasad sieciowych w celu kontrolowania przepływu ruchu do i z zasobników.
* **Uwierzytelnianie i autoryzacja** obejmują użytkownika dotyczące integracji kontroli dostępu opartej na ROLACH (RBAC) i Azure Active Directory (AD), tożsamości pod i wpisy tajne w Azure Key Vault. Aby uzyskać więcej informacji o tych funkcjach, zobacz [najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w programie AKS][aks-best-practices-identity].
* **Kontenery** zawierają dodatek Azure Policy dla AKS, aby wymusić zabezpieczenia, użycie kontekstów zabezpieczeń na poziomie i skanowanie obu obrazów i środowiska uruchomieniowego pod kątem luk w zabezpieczeniach. Obejmuje również ochronę aplikacji lub Seccomp (Zabezpieczanie obliczeniowe) w celu ograniczenia dostępu kontenera do podstawowego węzła.

## <a name="logically-isolate-clusters"></a>Logicznie Izoluj klastry

**Wskazówki dotyczące najlepszych** rozwiązań — Użyj izolacji logicznej do oddzielenia zespołów i projektów. Spróbuj zminimalizować liczbę fizycznych klastrów AKS wdrażanych w celu izolowania zespołów lub aplikacji.

Z izolacją logiczną pojedynczy klaster AKS może być używany w wielu obciążeniach, zespołach lub środowiskach. [Przestrzenie nazw][k8s-namespaces] Kubernetes tworzą granice logicznej izolacji dla obciążeń i zasobów.

![Logiczna izolacja klastra Kubernetes w AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Logiczne rozdzielenie klastrów zwykle zapewnia wyższą gęstość pod względem klastra izolowanego. W klastrze jest mniej nadmiarowa pojemność obliczeniowa, która jest bezczynna. W połączeniu z automatycznym skalowaniem klastra Kubernetes można skalować liczbę węzłów w górę lub w dół w celu spełnienia wymagań. Takie najlepsze podejście do skalowania automatycznego umożliwia uruchamianie tylko wymaganej liczby węzłów i zminimalizowania kosztów.

Środowiska Kubernetes, w AKS lub w innym miejscu, nie są całkowicie bezpieczne do korzystania z wielu dzierżawców. W środowisku wielodostępnym wiele dzierżawców pracuje w wspólnej, udostępnionej infrastrukturze. W związku z tym jeśli wszyscy dzierżawcy nie będą Zaufani, musisz wykonać dodatkowe planowanie, aby uniknąć jednej dzierżawy wpływającej na bezpieczeństwo i usługę innej. Dodatkowe funkcje zabezpieczeń, takie jak *zasady zabezpieczeń* i bardziej precyzyjna kontrola dostępu oparta na ROLACH (RBAC) dla węzłów sprawia, że luki w zabezpieczeniach są trudniejsze. Jednak w celu zapewnienia prawdziwych zabezpieczeń przy uruchamianiu nieprzechodnich obciążeń z wieloma dzierżawcami funkcja hypervisor jest jedynym poziomem zabezpieczeń, który należy zaufać. Domena zabezpieczeń dla Kubernetes jest cały klaster, a nie pojedynczy węzeł. W przypadku tych typów nieszkodliwych obciążeń z wieloma dzierżawcami należy używać klastrów fizycznie izolowanych.

## <a name="physically-isolate-clusters"></a>Izolowanie klastrów

**Wskazówki dotyczące najlepszych** rozwiązań — minimalizuje wykorzystanie fizycznej izolacji dla każdego oddzielnego wdrożenia zespołu lub aplikacji. Zamiast tego należy użyć izolacji *logicznej* , zgodnie z opisem w poprzedniej sekcji.

Typowym podejściem do izolacji klastra jest używanie fizycznie oddzielnych klastrów AKS. W tym modelu izolacji zespoły lub obciążenia są przypisani do własnego klastra AKS. Takie podejście często wygląda jak Najprostszym sposobem izolowania obciążeń lub zespołów, ale powoduje dodanie dodatkowych kosztów związanych z zarządzaniem i finansami. Teraz musisz obsługiwać te wiele klastrów i mieć indywidualny dostęp i uprawnienia do przypisywania. Opłaty są naliczane również za wszystkie poszczególne węzły.

![Fizyczna izolacja pojedynczych klastrów Kubernetes w AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fizycznie oddzielne klastry mają zazwyczaj niską gęstość pod. Ponieważ każdy zespół lub obciążenie ma własny klaster AKS, klaster jest często nadmiernie inicjowany przy użyciu zasobów obliczeniowych. Często w tych węzłach zaplanowano małą liczbę zasobników. Nie można używać nieużywanej pojemności na węzłach w przypadku aplikacji lub usług programistycznych przez inne zespoły. Te nadmiarowe zasoby przyczyniają się do dodatkowych kosztów w oddzielnym klastrze.

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
