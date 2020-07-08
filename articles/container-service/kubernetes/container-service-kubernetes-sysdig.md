---
title: PRZESTARZAŁE Monitorowanie klastra usługi Azure Kubernetes — narzędzia Sysdig
description: Monitorowanie klastra Kubernetes w Azure Container Service przy użyciu narzędzia Sysdig
author: bburns
ms.service: container-service
ms.topic: conceptual
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 68136d5b9ec16c822cb62e4fee85b8ace9b1899a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79371103"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-using-sysdig"></a>PRZESTARZAŁE Monitorowanie klastra Azure Container Service Kubernetes przy użyciu narzędzia Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym instruktażu przyjęto założenie, że [utworzono klaster Kubernetes przy użyciu Azure Container Service](container-service-kubernetes-walkthrough.md).

Przyjęto również założenie, że masz zainstalowane narzędzia interfejsu wiersza polecenia platformy Azure i polecenia kubectl.

Możesz sprawdzić, czy `az` Narzędzie jest zainstalowane, uruchamiając:

```azurecli
az --version
```

Jeśli nie masz `az` zainstalowanego narzędzia, w [tym miejscu](https://github.com/azure/azure-cli#installation)znajdują się instrukcje.

Możesz sprawdzić, czy `kubectl` Narzędzie jest zainstalowane, uruchamiając:

```console
kubectl version
```

Jeśli nie masz `kubectl` zainstalowanego programu, możesz uruchomić następujące polecenie:

```azurecli
az acs kubernetes install-cli
```

## <a name="sysdig"></a>Sysdig
Narzędzia Sysdig to zewnętrzne monitorowanie jako firma usługi, która może monitorować kontenery w klastrze Kubernetes działającym na platformie Azure. Korzystanie z narzędzia Sysdig wymaga aktywnego konta narzędzia Sysdig.
Możesz utworzyć konto w swojej [witrynie](https://app.sysdigcloud.com).

Po zalogowaniu w witrynie sieci Web chmury Sysdig kliknij swoją nazwę użytkownika, a na stronie powinien zostać wyświetlony klucz dostępu. 

![Klucz interfejsu API usługi Sysdig](./media/container-service-kubernetes-sysdig/sysdig2.png)

## <a name="installing-the-sysdig-agents-to-kubernetes"></a>Instalowanie agentów narzędzia Sysdig w usłudze Kubernetes
Aby monitorować kontenery, narzędzia Sysdig uruchamia proces na poszczególnych maszynach przy użyciu Kubernetes `DaemonSet` .
DaemonSets są obiektami interfejsu API Kubernetes, które uruchamiają pojedyncze wystąpienie kontenera na maszynę.
Doskonale nadaje się do instalowania narzędzi, takich jak narzędzia Sysdig Agent monitorowania.

Aby zainstalować narzędzia Sysdig elementu daemonset, należy najpierw pobrać [szablon](https://github.com/draios/sysdig-cloud-scripts/tree/master/agent_deploy/kubernetes) z narzędzia Sysdig. Zapisz ten plik jako `sysdig-daemonset.yaml` .

W systemie Linux i OS X można uruchomić:

```console
curl -O https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml
```

W programie PowerShell:

```powershell
Invoke-WebRequest -Uri https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/kubernetes/sysdig-daemonset.yaml | Select-Object -ExpandProperty Content > sysdig-daemonset.yaml
```

Następnie Edytuj ten plik, aby wstawić swój klucz dostępu uzyskany na podstawie konta usługi narzędzia Sysdig.

Na koniec Utwórz elementu daemonset:

```console
kubectl create -f sysdig-daemonset.yaml
```

## <a name="view-your-monitoring"></a>Wyświetlanie monitorowania
Po zainstalowaniu i uruchomieniu Agenci powinni wypompić dane z powrotem do narzędzia Sysdig.  Wróć do [pulpitu nawigacyjnego narzędzia Sysdig](https://app.sysdigcloud.com) , aby wyświetlić informacje o kontenerach.

Pulpity nawigacyjne specyficzne dla Kubernetes można także zainstalować za pomocą [Kreatora nowego pulpitu nawigacyjnego](https://app.sysdigcloud.com/#/dashboards/new).
