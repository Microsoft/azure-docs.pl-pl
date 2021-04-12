---
title: Wyświetl metryki klastra dla usługi Azure Kubernetes Service (AKS)
description: Wyświetl metryki klastra dla usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 32d8745e0ea780b5f86588fabdc25b244cf2cd8e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969509"
---
# <a name="view-cluster-metrics-for-azure-kubernetes-service-aks"></a>Wyświetl metryki klastra dla usługi Azure Kubernetes Service (AKS)

AKS zawiera zestaw metryk dla płaszczyzny kontroli, w tym serwer interfejsu API i automatyczne skalowanie klastra oraz węzły klastra. Te metryki umożliwiają monitorowanie kondycji klastra i rozwiązywanie problemów. Metryki dla klastra można wyświetlić przy użyciu Azure Portal.

> [!NOTE]
> Metryki klastra AKS nakładają się na podzbiór [metryk dostarczonych przez Kubernetes][kubernetes-metrics].

## <a name="view-metrics-for-your-aks-cluster-using-the-azure-portal"></a>Wyświetlanie metryk dla klastra AKS przy użyciu Azure Portal

Aby wyświetlić metryki dla klastra AKS:

1. Zaloguj się do [Azure Portal][azure-portal] i przejdź do klastra AKS.
1. Po lewej stronie w obszarze *monitorowanie* wybierz pozycję *metryki*.
1. Utwórz wykres dla metryk, które chcesz wyświetlić. Na przykład Utwórz wykres:
    1. W obszarze *zakres* wybierz swój klaster.
    1. W *obszarze Przestrzeń nazw Metryka* wybierz *metryki standardowe usługi kontenerów (zarządzane)*.
    1. Dla *metryki* w obszarze *podst* . Wybierz *liczbę jednostek miary według fazy*.
    1. W obszarze *agregacja* wybierz wartość *średnia*.

:::image type="content" source="media/metrics/metrics-chart.png" alt-text="{alt-text}":::

Powyższy przykład przedstawia metryki dla średniej liczby *myAKSCluster*.

## <a name="available-metrics"></a>Dostępne metryki

Dostępne są następujące metryki klastra:

| Nazwa | Group (Grupa) | ID (Identyfikator) | Opis |
| --- | --- | --- | ---- |
| Żądania numerów porządkowych określających | Serwer interfejsu API (wersja zapoznawcza) |apiserver_current_inflight_requests | Maksymalna liczba aktywnych żądań numerów porządkowych określających na serwerze interfejsu API na typ żądania. |
| Kondycja klastra | Automatyczne skalowanie klastra (wersja zapoznawcza) | cluster_autoscaler_cluster_safe_to_autoscale | Określa, czy automatyczne skalowanie klastra będzie podejmować działania w klastrze. |
| Skalowanie w dół cooldown | Automatyczne skalowanie klastra (wersja zapoznawcza) | cluster_autoscaler_scale_down_in_cooldown | Określa, czy skalowanie w dół jest w cooldown — w tym przedziale czasowym nie zostaną usunięte żadne węzły. |
| Niepotrzebne węzły | Automatyczne skalowanie klastra (wersja zapoznawcza) | cluster_autoscaler_unneeded_nodes_count | Auotscaler klastra oznacza te węzły jako kandydatów do usunięcia i ostatecznie usunięte. |
| Unschedulable | Automatyczne skalowanie klastra (wersja zapoznawcza) | cluster_autoscaler_unschedulable_pods_count | Liczba zasobników, które są obecnie unschedulable w klastrze. |
| Łączna liczba dostępnych rdzeni procesora CPU w zarządzanym klastrze | Węzły | kube_node_status_allocatable_cpu_cores | Łączna liczba dostępnych rdzeni procesora CPU w zarządzanym klastrze. |
| Łączna ilość dostępnej pamięci w zarządzanym klastrze | Węzły | kube_node_status_allocatable_memory_bytes | Łączna ilość dostępnej pamięci w zarządzanym klastrze. |
| Stany różnych warunków węzła | Węzły | kube_node_status_condition | Stany różnych warunków węzła |
| Użycie procesora CPU Millicores | Węzły (wersja zapoznawcza) | node_cpu_usage_millicores | Zagregowane pomiary użycia procesora CPU w millicores w całym klastrze. |
| Procent użycia procesora CPU | Węzły (wersja zapoznawcza) | node_cpu_usage_percentage | Zagregowane średnie użycie procesora CPU wyrażone w procentach w klastrze. |
| Bajty RSS pamięci | Węzły (wersja zapoznawcza) | node_memory_rss_bytes | Pamięć RSS kontenera użyta w bajtach. |
| Wartość procentowa RSS pamięci | Węzły (wersja zapoznawcza) | node_memory_rss_percentage | Pamięć RSS kontenera użyta w procentach. |
| Bajty zestawu roboczego pamięci | Węzły (wersja zapoznawcza) | node_memory_working_set_bytes | Pamięć zestawu roboczego kontenera użyta w bajtach. |
| Procent zestawu roboczego pamięci | Węzły (wersja zapoznawcza) | node_memory_working_set_percentage | Pamięć zestawu roboczego kontenera użyta w procentach. |
| Zajęte bajty dysku | Węzły (wersja zapoznawcza) | node_disk_usage_bytes | Miejsce na dysku używane w bajtach według urządzenia. |
| Procent użycia dysku | Węzły (wersja zapoznawcza) | node_disk_usage_percentage | Miejsce na dysku używane w procentach przez urządzenie. |
| Sieć w bajtach | Węzły (wersja zapoznawcza) | node_network_in_bytes | Bajty odebrane przez sieć. |
| Bajty wychodzące z sieci | Węzły (wersja zapoznawcza) | node_network_out_bytes | Bajty przesyłane przez sieć. |
| Liczba zasobników w stanie gotowe | Zasobników | kube_pod_status_ready | Liczba zasobników w stanie *gotowe* . |
| Liczba etapów według fazy | Zasobników | kube_pod_status_phase | Liczba zasobników według fazy. |

> [!IMPORTANT]
> Metryki w wersji zapoznawczej można aktualizować lub zmieniać, łącznie z ich nazwami i opisami, w wersji zapoznawczej.

## <a name="next-steps"></a>Następne kroki

Oprócz metryk klastra dla usługi AKS można również użyć Azure Monitor z klastrem AKS. Aby uzyskać więcej informacji na temat używania Azure Monitor z AKS, zobacz [Azure monitor for Containers][aks-azure-monitory].

[aks-azure-monitory]: ../azure-monitor/containers/container-insights-overview.md
[azure-portal]: https://portal.azure.com/
[kubernetes-metrics]: https://kubernetes.io/docs/concepts/cluster-administration/system-metrics/