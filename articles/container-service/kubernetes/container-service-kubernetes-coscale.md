---
title: (PRZESTARZAŁE) Monitorowanie klastra platformy Azure Kubernetes za pomocą coscale
description: Monitorowanie klastra usługi Kubernetes w usłudze Kontener usługi Azure przy użyciu usługi CoScale
author: fryckbos
ms.service: container-service
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f195a5c05c6c95dac898b2d471747952a3446d52
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681713"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>(PRZESTARZAŁE) Monitorowanie klastra kubernetes usługi kontenera platformy Azure za pomocą coscale

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

W tym artykule pokażemy, jak wdrożyć [agenta CoScale](https://web.archive.org/web/20180317071550/https://www.coscale.com/) do monitorowania wszystkich węzłów i kontenerów w klastrze kubernetes w usłudze Azure Container Service. Do tej konfiguracji potrzebne jest konto z coscale. 


## <a name="about-coscale"></a>Informacje o skali coscale 

CoScale to platforma monitorowania, która zbiera metryki i zdarzenia ze wszystkich kontenerów w kilku platformach aranżacji. CoScale oferuje monitorowanie pełnego stosu dla środowisk Kubernetes. Zapewnia wizualizacje i analizy dla wszystkich warstw w stosie: OS, Kubernetes, Docker i aplikacji działających wewnątrz kontenerów. CoScale oferuje kilka wbudowanych pulpitów nawigacyjnych monitorowania i ma wbudowane wykrywanie anomalii, aby umożliwić operatorom i deweloperom szybkie znajdowanie problemów z infrastrukturą i aplikacjami.

![Interfejs użytkownika coscale](./media/container-service-kubernetes-coscale/coscale.png)

Jak pokazano w tym artykule, można zainstalować agentów w klastrze Kubernetes do uruchamiania CoScale jako rozwiązanie SaaS. Jeśli chcesz zachować swoje dane na miejscu, CoScale jest również dostępna do instalacji lokalnej.


## <a name="prerequisites"></a>Wymagania wstępne

Najpierw musisz [utworzyć konto CoScale](https://web.archive.org/web/20170507123133/https://www.coscale.com/free-trial).

W tym przewodniku przyjęto założenie, że [utworzono klaster Kubernetes przy użyciu usługi Azure Container Service.](container-service-kubernetes-walkthrough.md)

Zakłada się również, że `az` masz `kubectl` zainstalowaną platformę Azure CLI i narzędzia.

Można sprawdzić, czy `az` masz zainstalowane narzędzie, uruchamiając:

```azurecli
az --version
```

Jeśli nie masz zainstalowanego `az` narzędzia, instrukcje są [tutaj](/cli/azure/install-azure-cli).

Można sprawdzić, czy `kubectl` masz zainstalowane narzędzie, uruchamiając:

```bash
kubectl version
```

Jeśli nie masz `kubectl` zainstalowanego, możesz uruchomić:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Instalowanie agenta CoScale z zestawem Demonów
[Zestawy demonów](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) są używane przez kubernetes do uruchamiania pojedynczego wystąpienia kontenera na każdym hoście w klastrze.
Są one idealne do uruchamiania agentów monitorowania, takich jak agent CoScale.

Po zalogowaniu się do CoScale przejdź do [strony agenta,](https://developer.newrelic.com/) aby zainstalować agentów CoScale w klastrze przy użyciu zestawu DaemonSet. Interfejs użytkownika CoScale zawiera kroki konfiguracji z przewodnikiem, aby utworzyć agenta i rozpocząć monitorowanie pełnego klastra Kubernetes.

![Konfiguracja agenta CoScale](./media/container-service-kubernetes-coscale/installation.png)

Aby uruchomić agenta w klastrze, uruchom podane polecenie:

![Uruchamianie agenta CoScale](./media/container-service-kubernetes-coscale/agent_script.png)

Gotowe. Gdy agenci są uruchomione, powinny być widoczne dane w konsoli w ciągu kilku minut. Odwiedź [stronę agenta,](https://developer.newrelic.com/) aby wyświetlić podsumowanie klastra, wykonać dodatkowe kroki konfiguracji i wyświetlić pulpity nawigacyjne, takie jak **omówienie klastra Kubernetes**.

![Omówienie klastra kubernetes](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

Agent CoScale jest automatycznie wdrażany na nowych komputerach w klastrze. Agent aktualizuje się automatycznie po wydaniu nowej wersji.


## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat rozwiązań do monitorowania coscale można znaleźć w dokumentacji i [blogu](https://web.archive.org/web/20170501021344/http://www.coscale.com:80/blog) [CoScale.](https://web.archive.org/web/20180415164304/http://docs.coscale.com:80/) 

