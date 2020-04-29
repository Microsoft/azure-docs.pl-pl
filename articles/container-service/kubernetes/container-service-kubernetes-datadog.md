---
title: PRZESTARZAŁE Monitorowanie klastra usługi Azure Kubernetes za pomocą usługi Datadog
description: Monitorowanie klastra Kubernetes w Azure Container Service przy użyciu usługi Datadog
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 1f3f70c30ab397bd549a2f3305a738274ee4f64f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371175"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-datadog"></a>PRZESTARZAŁE Monitorowanie klastra Azure Container Service przy użyciu usługi Datadog

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym instruktażu przyjęto założenie, że [utworzono klaster Kubernetes przy użyciu Azure Container Service](container-service-kubernetes-walkthrough.md).

Przyjęto również założenie, że `az` masz zainstalowany Interfejs `kubectl` wiersza polecenia platformy Azure i narzędzia.

Możesz sprawdzić, czy `az` narzędzie jest zainstalowane, uruchamiając:

```azurecli
az --version
```

Jeśli nie masz zainstalowanego `az` narzędzia, w [tym miejscu](https://github.com/azure/azure-cli#installation)znajdują się instrukcje.

Możesz sprawdzić, czy `kubectl` narzędzie jest zainstalowane, uruchamiając:

```console
kubectl version
```

Jeśli nie masz `kubectl` zainstalowanego programu, możesz uruchomić następujące polecenie:

```azurecli
az acs kubernetes install-cli
```

## <a name="datadog"></a>Usługi Datadog
Usługi Datadog to usługa monitorowania, która zbiera dane monitorowania z kontenerów w klastrze Azure Container Service. Usługi Datadog ma pulpit nawigacyjny integracji platformy Docker, w którym można zobaczyć określone metryki w kontenerach. Metryki zbierane z kontenerów są zorganizowane według procesora CPU, pamięci, sieci i operacji we/wy. Usługi Datadog dzieli metryki na kontenery i obrazy.

Najpierw musisz [utworzyć konto](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Instalowanie agenta usługi Datadog z elementu daemonset
DaemonSets są używane przez Kubernetes do uruchamiania pojedynczego wystąpienia kontenera na każdym hoście w klastrze.
Są one idealne do uruchamiania agentów monitorowania.

Po zalogowaniu się do usługi usługi Datadog można wykonać [instrukcje usługi Datadog](https://app.datadoghq.com/account/settings#agent/kubernetes) w celu zainstalowania agentów usługi Datadog w klastrze za pomocą elementu daemonset.

## <a name="conclusion"></a>Podsumowanie
Gotowe. Gdy agenci zostaną zainstalowani, zobacz dane w konsoli w ciągu kilku minut. Aby zobaczyć podsumowanie klastra, można odwiedzić [pulpit nawigacyjny](https://app.datadoghq.com/screen/integration/kubernetes) zintegrowanej Kubernetes.
