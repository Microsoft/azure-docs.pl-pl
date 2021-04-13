---
title: Planowanie i wdrażanie Kubernetes z włączoną funkcją Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 04/12/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Dołączanie dużej liczby klastrów do usługi Azure ARC z włączoną obsługą Kubernetes na potrzeby zarządzania konfiguracją
ms.openlocfilehash: 4b34cee08db508728f01d262ae4b1ee4ed4754e1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315491"
---
# <a name="plan-and-deploy-azure-arc-enabled-kubernetes"></a>Planowanie i wdrażanie Kubernetes z włączoną funkcją Azure Arc

Wdrożenie usługi infrastruktury IT lub aplikacji biznesowej jest wyzwaniem dla każdej firmy. Aby zapobiec niepożądanym niedodaniom lub nieplanowanym kosztom, należy dokładnie zaplanować, aby upewnić się, że wszystko jest gotowe. Taki plan powinien identyfikować kryteria projektowania i wdrażania, które należy spełnić, aby wykonać zadania.

Aby wdrożenie kontynuowało płynność, plan powinien jasno zrozumieć:

* Role i obowiązki.
* Spis wszystkich klastrów Kubernetes
* Spełnianie wymagań sieciowych.
* Zestaw umiejętności i szkolenia wymagane do zapewnienia pomyślnego wdrożenia i zarządzania nim.
* Kryteria akceptacji i sposób śledzenia sukcesu.
* Narzędzia lub metody służące do automatyzowania wdrożeń.
* Zidentyfikowano zagrożenia i plany zaradcze, aby uniknąć opóźnień i przerw w działaniu.
* Jak uniknąć przerw w działaniu podczas wdrażania.
* Jaka jest ścieżka eskalacji w przypadku wystąpienia znaczącego problemu?

Celem tego artykułu jest upewnienie się, że przygotowano do pomyślnego wdrożenia usługi Azure ARC z włączonym Kubernetes w wielu klastrach produkcyjnych w danym środowisku.

## <a name="prerequisites"></a>Wymagania wstępne

* Istniejący klaster Kubernetes. Jeśli go nie masz, możesz utworzyć klaster przy użyciu jednej z następujących opcji:
    - [Kubernetes w Docker (rodzaj)](https://kind.sigs.k8s.io/)
    - Tworzenie klastra Kubernetes przy użyciu platformy Docker dla [komputerów Mac](https://docs.docker.com/docker-for-mac/#kubernetes) lub [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    - Samozarządzany klaster Kubernetes przy użyciu [interfejsu API klastra](https://cluster-api.sigs.k8s.io/user/quick-start.html)

* Maszyny mają łączność z siecią lokalną lub innym środowiskiem chmury do zasobów platformy Azure, bezpośrednio lub za pośrednictwem serwera proxy. Więcej szczegółów można znaleźć w sekcji [wymagania wstępne dotyczące sieci](quickstart-connect-cluster.md#meet-network-requirements).

* `kubeconfig`Plik wskazujący klaster, który ma zostać połączony z usługą Azure Arc.
* Uprawnienia "read" i "Write" dla nazwy głównej użytkownika lub usługi, w której jest tworzony typ zasobu Kubernetes usługi Azure Arc `Microsoft.Kubernetes/connectedClusters` .

## <a name="pilot"></a>Pilotaż

Przed wdrożeniem we wszystkich klastrach produkcyjnych Rozpocznij od oceny tego procesu wdrażania przed jego wdrożeniem w środowisku. W przypadku pilotażu należy zidentyfikować reprezentatywne próbkowanie klastrów, które nie są kluczowe dla firm, które nie mają możliwości prowadzenia działalności. Należy upewnić się, że jest wystarczająco dużo czasu na uruchomienie pilotażu i ocenić jego wpływ: zalecamy około 30 dni.

Ustanowienie formalnego planu opisującego zakres i szczegóły pilotażu. Następujący przykładowy plan powinien pomóc Ci rozpocząć pracę.

* **Cele** — opisuje sterowniki biznesowe i techniczne, które doprowadziły do decyzji, że pilotaż jest niezbędny.
* **Kryteria wyboru** — określa kryteria używane do wybierania aspektów rozwiązania, które będą prezentowane za pośrednictwem pilotażu.
* **Zakres** — obejmuje składniki rozwiązania, oczekiwany harmonogram, czas trwania pilotażu oraz liczbę klastrów docelowych.
* **Kryteria sukcesu i metryki** — zdefiniuj kryteria sukcesu pilotażu i określone miary używane do określania poziomu sukcesu.
* **Plan szkoleniowy** — zawiera opis planu szkolenia inżynierów systemów, administratorów itp., którzy są nowym sposobem na platformę Azure i usługi IT podczas pilotażu.
* **Plan przejścia** — opis strategii i kryteriów używanych do przejścia od pilotażu do środowiska produkcyjnego.
* **Wycofywanie** — zawiera opis procedur wycofywania pilotażu do stanu sprzed wdrożenia.
* **Ryzyka** — lista wszystkich zidentyfikowanych zagrożeń związanych z przeprowadzeniem pilotażu i związana z wdrożeniem produkcyjnym.

## <a name="phase-1-build-a-foundation"></a>Faza 1: Tworzenie podstawy

W tej fazie inżynierowie systemów lub Administratorzy wykonują podstawowe działania, takie jak tworzenie grup zasobów, tagów, przypisań ról, dzięki czemu można tworzyć i obsługiwać zasoby Kubernetes z funkcją Azure Arc.

|Zadanie |Szczegóły |Czas trwania |
|-----|-------|---------|
| [Tworzenie grupy zasobów](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Dedykowana Grupa zasobów obejmująca tylko zasoby Kubernetes z funkcją Azure ARC oraz scentralizowane zarządzanie i monitorowanie tych zasobów. | Jedna godzina |
| Zastosuj [Tagi](../../azure-resource-manager/management/tag-resources.md) , aby ułatwić organizowanie maszyn. | Oceń i opracowuj [strategię tagowania](/azure/cloud-adoption-framework/decision-guides/resource-tagging/)WYRÓWNANĄ przez nią. Może to pomóc w zmniejszeniu złożoności zarządzania zasobami Kubernetes z obsługą usługi Azure Arc i uproszczenia podejmowania decyzji dotyczących zarządzania. | Jeden dzień |
| Zidentyfikuj [konfiguracje](tutorial-use-gitops-connected-cluster.md) dla GitOps | Zidentyfikuj konfiguracje aplikacji lub linii bazowej, takie jak `PodSecurityPolicy` , `NetworkPolicy` które chcesz wdrożyć w klastrach | Jeden dzień |
| [Opracowywanie planu Azure Policy](../../governance/policy/overview.md) ładu | Określ, w jaki sposób ma zostać wdrożony nadzór nad klastrami Kubernetes w ramach subskrypcji lub grupy zasobów przy użyciu Azure Policy. | Jeden dzień |
| Konfigurowanie [kontroli dostępu opartej na rolach](../../role-based-access-control/overview.md) (RBAC) | Opracowywanie planu dostępu w celu zidentyfikowania, kto ma uprawnienia do odczytu/zapisu/wszystkich uprawnień w klastrach | Jeden dzień |

## <a name="phase-2-deploy-azure-arc-enabled-kubernetes"></a>Faza 2: wdrażanie Kubernetes z włączoną funkcją Azure Arc

W tej fazie łączymy klastry Kubernetes z platformą Azure:

|Zadanie |Szczegóły |Czas trwania |
|-----|-------|---------|
| [Połącz pierwszy klaster Kubernetes z usługą Azure Arc](quickstart-connect-cluster.md) | W ramach połączenia pierwszego klastra z usługą Azure Arc Skonfiguruj środowisko dołączania ze wszystkimi wymaganymi narzędziami, takimi jak interfejs wiersza polecenia platformy Azure, Helm i `connectedk8s` rozszerzenie dla interfejsu wiersza polecenia platformy Azure. | 15 minut |
| [Tworzenie jednostki usługi](create-onboarding-service-principal.md) | Utwórz nazwę główną usługi, aby połączyć klastry Kubernetes w sposób nieinteraktywny przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell. | Jedna godzina |


## <a name="phase-3-manage-and-operate"></a>Faza 3: zarządzanie i obsługa

W tej fazie wdrażamy konfiguracje aplikacji i linii bazowych w klastrach Kubernetes.

|Zadanie |Szczegóły |Czas trwania |
|-----|-------|---------|
|[Tworzenie konfiguracji](tutorial-use-gitops-connected-cluster.md) w klastrach | Utwórz konfiguracje wdrażania aplikacji na serwerze usługi Azure ARC z włączonym Kubernetes. | 15 minut |
|[Użyj Azure Policy](use-azure-policy.md) na potrzeby wymuszania konfiguracji na poziomie | Utwórz przypisania zasad, aby zautomatyzować wdrażanie konfiguracji bazowych we wszystkich klastrach w zakresie subskrypcji lub grupy zasobów. | 15 minut |
| [Uaktualnij agentów usługi Azure Arc](agent-upgrade.md) | Jeśli wyłączono funkcję autouaktualniania agentów w klastrach, zaktualizuj agentów ręcznie do najnowszej wersji, aby upewnić się, że masz najnowsze poprawki dotyczące zabezpieczeń i usterek. | 15 minut |

## <a name="next-steps"></a>Następne kroki

* Skorzystaj z naszego przewodnika Szybki Start, aby [połączyć klaster Kubernetes z usługą Azure Arc](./quickstart-connect-cluster.md).
* [Utwórz konfiguracje](./tutorial-use-gitops-connected-cluster.md) w klastrze Kubernetes z obsługą usługi Azure Arc.
* [Użyj Azure Policy, aby zastosować konfiguracje na dużą skalę](./use-azure-policy.md).