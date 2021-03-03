---
title: Rozwiązywanie typowych problemów z obsługą usługi Azure Arc Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Rozwiązywanie typowych problemów z obsługą Arc klastrów Kubernetes.
keywords: Kubernetes, łuk, Azure, kontenery
ms.openlocfilehash: e1f4e84f16c6b584f1ffbd918a86c251f47efcca
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654004"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>Kubernetes Rozwiązywanie problemów z usługą Azure Arc

Ten dokument zawiera przewodniki dotyczące rozwiązywania problemów z łącznością, uprawnieniami i agentami.

## <a name="general-troubleshooting"></a>Ogólne rozwiązywanie problemów

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Przed użyciem `az connectedk8s` `az k8s-configuration` poleceń lub interfejsu wiersza polecenia należy sprawdzić, czy interfejs wiersza polecenia platformy Azure jest ustawiony do pracy z poprawną subskrypcją platformy Azure.

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Agenci usługi Azure Arc

Wszystkie agenci z włączoną funkcją Azure Arc Kubernetes są wdrażane jako zasobniki w `azure-arc` przestrzeni nazw. Wszystkie zasobniki powinny być uruchomione i przekazywać kontrolę kondycji.

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

Jeśli nie odnaleziono wersji Helm lub nie została ona znaleziona, spróbuj ponownie [połączyć klaster z usługą Azure Arc](./connect-cluster.md) .

Jeśli wersja Helm jest obecna w programie `STATUS: deployed` , sprawdź stan agentów przy użyciu `kubectl` :

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Wszystkie zasobniki powinny być wyświetlane `STATUS` jako `Running` z `3/3` lub `2/2` pod `READY` kolumną. Pobieranie dzienników i opisywanie zasobników zwracających `Error` lub `CrashLoopBackOff` . Jeśli jakieś zasobniki są zablokowane `Pending` , w węzłach klastra mogą znajdować się niewystarczające zasoby. [Skaluj w górę klaster](https://kubernetes.io/docs/tasks/administer-cluster/) może pobrać te zasobniki, aby przejść do `Running` stanu.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Łączenie klastrów Kubernetes z usługą Azure Arc

Łączenie klastrów z platformą Azure wymaga dostępu do subskrypcji platformy Azure i `cluster-admin` dostępu do klastra docelowego. Jeśli nie można skontaktować się z klastrem lub nie masz wystarczających uprawnień, połączenie klastra z usługą Azure Arc zakończy się niepowodzeniem.

### <a name="insufficient-cluster-permissions"></a>Niewystarczające uprawnienia klastra

Jeśli podany plik kubeconfig nie ma wystarczających uprawnień do instalacji agentów usługi Azure ARC, polecenie interfejsu wiersza polecenia platformy Azure zwróci błąd.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

Użytkownik łączący klaster z usługą Azure Arc powinien mieć `cluster-admin` przypisaną rolę w klastrze.

### <a name="installation-timeouts"></a>Limity czasu instalacji

Połączenie klastra Kubernetes z włączoną obsługą usługi Azure Arc Kubernetes wymaga instalacji agentów usługi Azure Arc w klastrze. Jeśli klaster działa za pośrednictwem wolnego połączenia internetowego, ściąganie obrazów kontenera dla agentów może trwać dłużej niż limity czasu interfejsu wiersza polecenia platformy Azure.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Helm problem

`v3.3.0-rc.1`W wersji Helm występuje [problem](https://github.com/helm/helm/pull/8527) polegający na tym, że instalacja/uaktualnienie Helm (używane przez `connectedk8s` rozszerzenie interfejsu wiersza polecenia) powoduje uruchomienie wszystkich punktów zaczepienia prowadzących do następującego błędu:

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Usuń zasób Kubernetes z włączoną funkcją Azure Arc w Azure Portal.
2. Uruchom następujące polecenia na maszynie:
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. [Zainstaluj stabilną wersję](https://helm.sh/docs/intro/install/) Helm 3 na maszynie zamiast wersji Release Candidate.
4. Uruchom `az connectedk8s connect` polecenie z odpowiednimi wartościami, aby połączyć klaster z usługą Azure Arc.

## <a name="configuration-management"></a>Zarządzanie konfiguracją

### <a name="general"></a>Ogólne
Aby pomóc w rozwiązywaniu problemów z zasobem konfiguracji, uruchom polecenie AZ Commands z `--debug` określonym parametrem.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>Utwórz konfiguracje

Uprawnienia do zapisu w ramach zasobu Kubernetes () z włączoną funkcją Azure Arc `Microsoft.Kubernetes/connectedClusters/Write` są niezbędne i wystarczające do tworzenia konfiguracji w tym klastrze.

### <a name="configuration-remains-pending"></a>Konfiguracja pozostaje `Pending`

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
## <a name="monitoring"></a>Monitorowanie

Azure Monitor dla kontenerów wymaga, aby elementu daemonset był uruchamiany w trybie uprzywilejowanym. Aby pomyślnie skonfigurować kanoniczny klaster Kubernetes na potrzeby monitorowania, uruchom następujące polecenie:

```console
juju config kubernetes-worker allow-privileged=true
```