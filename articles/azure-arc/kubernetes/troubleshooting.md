---
title: Rozwiązywanie typowych problemów z usługą Azure ARC z obsługą Kubernetes (wersja zapoznawcza)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Rozwiązywanie typowych problemów z obsługą Arc klastrów Kubernetes.
keywords: Kubernetes, łuk, Azure, kontenery
ms.openlocfilehash: 1527f8d4ca06c2deaf4ce18b73bfdb515dcadc63
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83725588"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting-preview"></a>Kubernetes Rozwiązywanie problemów z usługą Azure ARC (wersja zapoznawcza)

W tym dokumencie przedstawiono kilka typowych scenariuszy rozwiązywania problemów z łącznością, uprawnieniami i agentami.

## <a name="general-troubleshooting"></a>Ogólne rozwiązywanie problemów

### <a name="azure-cli-set-up"></a>Konfiguracja interfejsu wiersza polecenia platformy Azure
Przed użyciem polecenia AZ connectedk8s lub AZ k8sconfiguration CLI, należy upewnić się, że program AZ jest ustawiony do pracy z poprawną subskrypcją platformy Azure.

```console
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Azure-Arc Agents
Wszystkie agenci z włączoną funkcją Azure Arc Kubernetes są wdrażane jako zasobniki w `azure-arc` przestrzeni nazw. W ramach zwykłych operacji wszystkie zasobniki powinny być uruchomione i przekazywać kontrolę kondycji.

Najpierw sprawdź, czy usługa Azure Arc Helm Release:

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Jeśli nie odnaleziono lub nie znaleziono wersji Helm, spróbuj ponownie dołączyć klaster.

Jeśli dostępna jest wersja Helm i `STATUS: deployed` określisz stan agentów przy użyciu `kubectl` :

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Wszystkie zasobniki powinny `STATUS` być wyświetlane jako `Running` i `READY` powinny mieć wartość `3/3` lub `2/2` . Pobieranie dzienników i opisywanie zasobników zwracających `Error` lub `CrashLoopBackOff` .

## <a name="unable-to-connect-my-kubernetes-cluster-to-azure"></a>Nie można połączyć mojego klastra Kubernetes z platformą Azure

Łączenie klastrów z platformą Azure wymaga dostępu zarówno do subskrypcji platformy Azure, jak i `cluster-admin` dostępu do klastra docelowego. Jeśli nie można połączyć się z klastrem lub nie ma wystarczających uprawnień do dołączania.

### <a name="insufficient-cluster-permissions"></a>Niewystarczające uprawnienia klastra

Jeśli podany plik kubeconfig nie ma wystarczających uprawnień do instalacji agentów usługi Azure ARC, polecenie interfejsu wiersza polecenia platformy Azure zwróci błąd próbujący wywołać interfejs API Kubernetes.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

Właściciel klastra powinien używać użytkownika Kubernetes z uprawnieniami administratora klastra.

### <a name="installation-timeouts"></a>Limity czasu instalacji

Instalacja agenta usługi Azure Arc wymaga uruchomienia zestawu kontenerów w klastrze docelowym. Jeśli klaster działa za pośrednictwem wolnego połączenia internetowego, ściąganie obrazów kontenera może trwać dłużej niż limity czasu interfejsu wiersza polecenia platformy Azure.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

There was a problem with connect-agent deployment. Please run 'kubectl -n azure-arc logs -l app.kubernetes.io/component=connect-agent -c connect-agent' to debug the error.
```

## <a name="configuration-management"></a>Zarządzanie konfiguracją

### <a name="general"></a>Ogólne
Aby pomóc w rozwiązywaniu problemów z konfiguracją kontroli źródła, uruchom polecenie AZ Commands with--Debug Switch.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8sconfiguration create <parameters> --debug
```

### <a name="create-source-control-configuration"></a>Utwórz konfigurację kontroli źródła
Rola współautor w zasobie Microsoft. Kubernetes/connectedCluster jest wymagana i wystarcza do utworzenia zasobu Microsoft. KubernetesConfiguration/sourceControlConfiguration.

### <a name="configuration-remains-pending"></a>Konfiguracja pozostaje`Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
