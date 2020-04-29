---
title: (PRZESTARZAŁE) Samouczek usługi Azure Container Service — monitorowanie usługi Kubernetes
description: Samouczek usługi Azure Container Service — monitorowanie usługi Kubernetes przy użyciu usługi Log Analytics
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 84c2438a8c25b1b64f46e12923212812beac687d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78273325"
---
# <a name="deprecated-monitor-a-kubernetes-cluster-with-log-analytics"></a>(PRZESTARZAŁE) Monitorowanie klastra usługi Kubernetes przy użyciu usługi Log Analytics

> [!TIP]
> Aby poznać zaktualizowaną wersję tego samouczka korzystającą z usługi Azure Kubernetes Service, zobacz [Azure Monitor for containers (Preview) overview](../../azure-monitor/insights/container-insights-overview.md) (Przegląd usługi Azure Monitor dla kontenerów — wersja zapoznawcza).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Monitorowanie klastra i kontenerów usługi Kubernetes ma krytyczne znaczenie, szczególnie w przypadku zarządzania klastrem produkcyjnym o dużej skali i z wieloma aplikacjami.

Możesz korzystać z kilku rozwiązań do monitorowania usługi Kubernetes pochodzących od firmy Microsoft lub innych dostawców. W tym samouczku przedstawiono monitorowanie klastra Kubernetes za pomocą rozwiązania Containers w usłudze [Log Analytics](../../operations-management-suite/operations-management-suite-overview.md) — oprogramowaniu firmy Microsoft do zarządzania chmurową infrastrukturą IT. (Rozwiązanie Containers jest dostępne w wersji zapoznawczej).

Ten samouczek (część 7 z 7) obejmuje następujące zadania:

> [!div class="checklist"]
> * Pobieranie ustawień obszaru roboczego usługi Log Analytics
> * Konfigurowanie agentów usługi Log Analytics w węzłach Kubernetes
> * Uzyskiwanie dostępu do informacji monitorowania w portalu usługi Log Analytics lub w witrynie Azure Portal

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach aplikacja została spakowana w postaci obrazów kontenera, obrazy zostały przekazane do usługi Azure Container Registry i utworzono klaster usługi Kubernetes.

Jeśli nie wykonano tych kroków, a chcesz kontynuować pracę, wróć do części [Samouczek 1 — tworzenie obrazów kontenera](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="get-workspace-settings"></a>Pobieranie ustawień obszaru roboczego

Po uzyskaniu dostępu do [portalu usługi Log Analytics](https://mms.microsoft.com) przejdź do pozycji **Ustawienia** > **Połączone źródła** > **Serwery z systemem Linux**. Tu możesz znaleźć *identyfikator obszaru roboczego* i podstawowy lub pomocniczy *klucz obszaru roboczego*. Zanotuj te wartości. Będą one potrzebne do skonfigurowania agentów usługi Log Analytics w klastrze.

## <a name="create-kubernetes-secret"></a>Tworzenie wpisu tajnego rozwiązania Kubernetes

Zapisz ustawienia obszaru roboczego usługi Log Analytics we wpisie tajnym rozwiązania Kubernetes o nazwie `omsagent-secret` za pomocą polecenia [kubectl create secret][kubectl-create-secret]. Zaktualizuj element `WORKSPACE_ID` za pomocą identyfikatora obszaru roboczego usługi Log Analytics, a element `WORKSPACE_KEY` za pomocą klucza obszaru roboczego.

```console
kubectl create secret generic omsagent-secret --from-literal=WSID=WORKSPACE_ID --from-literal=KEY=WORKSPACE_KEY
```

## <a name="set-up-log-analytics-agents"></a>Konfigurowanie agentów usługi Log Analytics

Do skonfigurowania agentów monitorowania kontenerów w klastrze Kubernetes można użyć następującego pliku manifestu usługi Kubernetes. Umożliwia on utworzenie elementu [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) usługi Kubernetes, który uruchamia pojedyncze, identyczne zasobniki w każdym węźle klastra.

Zapisz poniższy tekst do pliku o nazwie `oms-daemonset.yaml`.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: 1.4.3-174
    dockerProviderVersion: 1.0.0-30
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log
          name: host-log
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        - mountPath: /var/lib/docker/containers
          name: containerlog-path
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   nodeSelector:
    beta.kubernetes.io/os: linux
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
   volumes:
    - name: docker-sock
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
    - name: omsagent-secret
      secret:
       secretName: omsagent-secret
    - name: containerlog-path
      hostPath:
       path: /var/lib/docker/containers
```

Utwórz element DaemonSet przy użyciu następującego polecenia:

```console
kubectl create -f oms-daemonset.yaml
```

Aby sprawdzić, czy element DaemonSet został utworzony, uruchom polecenie:

```console
kubectl get daemonset
```

Dane wyjściowe będą podobne do następujących:

```output
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

Po uruchomieniu agentów pozyskanie i przetworzenie danych przez usługę Log Analytics trwa kilka minut.

## <a name="access-monitoring-data"></a>Dostęp do danych monitorowania

Dane monitorowania kontenera można wyświetlać i analizować za pomocą [rozwiązania Containers](../../azure-monitor/insights/containers.md) w portalu usługi Log Analytics lub w witrynie Azure Portal.

Aby zainstalować rozwiązanie Containers przy użyciu [portalu usługi Log Analytics](https://mms.microsoft.com), przejdź do **Galerii rozwiązań**. Następnie dodaj element **Rozwiązanie Containers**. Rozwiązanie Containers możesz też dodać z witryny [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

W portalu usługi Log Analytics wyszukaj kafelek podsumowania **Containers** na pulpicie nawigacyjnym. Kliknij kafelek, aby wyświetlić szczegółowe informacje, w tym zdarzenia kontenera, błędy, stan, spis obrazów oraz użycie procesora i pamięci. Aby uzyskać bardziej szczegółowe informacje, kliknij wiersz w dowolnym kafelku lub [przeszukaj dzienniki](../../log-analytics/log-analytics-log-searches.md).

![Pulpit nawigacyjny rozwiązania Containers w witrynie Azure Portal](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Podobnie w witrynie Azure Portal wybierz pozycję **Log Analytics** i wybierz nazwę obszaru roboczego. Aby wyświetlić kafelek podsumowania **Containers**, kliknij pozycję **Rozwiązania** > **Containers**. Aby wyświetlić szczegóły, kliknij kafelek.

Szczegółowy przewodnik dotyczący odpytywania i analizowania danych monitorowania znajduje się w [dokumentacji usługi Azure Log Analytics](../../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób monitorowania klastra Kubernetes za pomocą usługi Log Analytics. Wykonano następujące zadania:

> [!div class="checklist"]
> * Pobieranie ustawień obszaru roboczego usługi Log Analytics
> * Konfigurowanie agentów usługi Log Analytics w węzłach Kubernetes
> * Uzyskiwanie dostępu do informacji monitorowania w portalu usługi Log Analytics lub w witrynie Azure Portal


Kliknij ten link, aby wyświetlić wstępnie utworzone przykładowe skrypty dla usługi Container Service.

> [!div class="nextstepaction"]
> [Przykłady skryptów usługi Azure Container Service](cli-samples.md)
