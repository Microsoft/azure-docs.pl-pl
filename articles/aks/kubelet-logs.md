---
title: Wyświetlanie dzienników kubelet w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak wyświetlać informacje dotyczące rozwiązywania problemów w dziennikach kubelet z węzłów usługi Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 2ef38aa76f4ef9152e7bc55a1d74c84ef426f0ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87056763"
---
# <a name="get-kubelet-logs-from-azure-kubernetes-service-aks-cluster-nodes"></a>Pobieranie dzienników agenta kubelet z węzłów klastra usługi Azure Kubernetes Service

W ramach działania klastra AKS może być konieczne przejrzenie dzienników w celu rozwiązania problemu. Wbudowana w Azure Portal jest możliwość wyświetlania dzienników dla [głównych składników][aks-master-logs] lub [kontenerów AKS w klastrze AKS][azure-container-logs]. Czasami może być konieczne uzyskanie dzienników *kubelet* z węzła AKS w celu rozwiązywania problemów.

W tym artykule pokazano, jak można użyć `journalctl` programu do wyświetlania dzienników *kubelet* w węźle AKS.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

## <a name="create-an-ssh-connection"></a>Tworzenie połączenia SSH

Najpierw utwórz połączenie SSH z węzłem, na którym chcesz wyświetlić dzienniki *kubelet* . Ta operacja jest szczegółowo opisana w dokumencie [nodes SSH w usłudze Azure Kubernetes Service (AKS)][aks-ssh] .

## <a name="get-kubelet-logs"></a>Pobieranie dzienników agenta kubelet

Po nawiązaniu połączenia z węzłem Uruchom następujące polecenie, aby ściągnąć dzienniki *kubelet* :

```console
sudo journalctl -u kubelet -o cat
```

> [!NOTE]
> W przypadku węzłów systemu Windows dane dziennika są w `C:\k` i można je wyświetlić za pomocą polecenia *więcej* :
> ```
> more C:\k\kubelet.log
> ```

Następujące przykładowe dane wyjściowe przedstawiają dane dziennika *kubelet* :

```
I0508 12:26:17.905042    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:27.943494    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:28.920125    8672 server.go:796] GET /stats/summary: (10.370874ms) 200 [[Ruby] 10.244.0.2:52292]
I0508 12:26:37.964650    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:47.996449    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:58.019746    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:05.107680    8672 server.go:796] GET /stats/summary/: (24.853838ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:27:08.041736    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:18.068505    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:28.094889    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:38.121346    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:44.015205    8672 server.go:796] GET /stats/summary: (30.236824ms) 200 [[Ruby] 10.244.0.2:52588]
I0508 12:27:48.145640    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:58.178534    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:05.040375    8672 server.go:796] GET /stats/summary/: (27.78503ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:28:08.214158    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:18.242160    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:28.274408    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:38.296074    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:48.321952    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:58.344656    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
```

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz dodatkowych informacji dotyczących rozwiązywania problemów z poziomu wzorca Kubernetes, zobacz [Wyświetlanie dzienników głównego węzła Kubernetes w AKS][aks-master-logs].

<!-- LINKS - internal -->
[aks-ssh]: ssh.md
[aks-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-master-logs]: view-master-logs.md
[azure-container-logs]: ../azure-monitor/insights/container-insights-overview.md
