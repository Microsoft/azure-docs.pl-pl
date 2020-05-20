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
ms.openlocfilehash: e0583fdeaa0819ec961e87ae89ee3aa7592d1f79
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680701"
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
$ kubectl -n azure-arc get deploy,pods
NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/config-agent         1/1     1            1           53s
deployment.apps/connect-agent        1/1     1            1           53s
deployment.apps/controller-manager   1/1     1            1           53s
deployment.apps/metrics-agent        1/1     1            1           53s

NAME                                      READY   STATUS    RESTARTS   AGE
pod/config-agent-74cf758b5f-cxnhs         2/2     Running   0          53s
pod/connect-agent-bc6b9ff5d-dzkvf         2/2     Running   0          53s
pod/controller-manager-7cf95d5d77-wv5cw   2/2     Running   0          53s
pod/metrics-agent-c77c9dfc7-45n5r         1/1     Running   0          53s
```

Wszystkie zasobniki powinny `STATUS` być wyświetlane jako `Running` i `READY` powinny mieć wartość `2/2` lub `1/1` . Pobieranie dzienników i opisywanie zasobników zwracających `Error` lub `CrashLoopBackOff` .

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

### <a name="incorrect-or-expired-onboarding-credentials"></a>Nieprawidłowe lub wygasłe poświadczenia dołączania

```console
$ kubectl -n azure-arc get deploy,pod
NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/config-agent         1/1     1            1           8m11s
deployment.apps/connect-agent        0/1     1            0           8m11s
deployment.apps/controller-manager   1/1     1            1           8m11s
deployment.apps/metrics-agent        1/1     1            1           8m11s

NAME                                      READY   STATUS             RESTARTS   AGE
pod/config-agent-74cf758b5f-d7qz9         2/2     Running            0          8m11s
pod/connect-agent-bc6b9ff5d-sd9fb         1/2     CrashLoopBackOff   6          8m11s
pod/controller-manager-7cf95d5d77-qlsvs   2/2     Running            0          8m11s
pod/metrics-agent-c77c9dfc7-lp2rf         1/1     Running            1          8m11s
```

Program Connect Agent rejestruje wszystkie błędy komunikacji z platformą Azure i lokalnym serwerem interfejsu API Kubernetes jako standardową dzienników. Pobierz dzienniki przy użyciu polecenia `kubectl` , aby debugować.

```console
$ kubectl -n azure-arc logs -l app.kubernetes.io/component=connect-agent -c connect-agent
2020/04/07 20:52:50 Environment validation :success
2020/04/07 20:52:50 Kubernetes API server access validation :success
2020/04/07 20:52:51 Azure Subscription access token :error :http request failed. Authentication Token URL:https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47/oauth2/token Authentication Token Body:grant_type=client_credentials&client_id=82195c37-7497-458c-b643-f4a3d0a64190&client_secret=9814c84e-59d7-49fc-bef6-17b717d2f5a8&resource=https%3A%2F%2Fmanagement.azure.com%2F ErrorInfo: Response:{"error":"invalid_client","error_description":"AADSTS7000215: Invalid client secret is provided.\r\nTrace ID: b179b7db-c957-4917-a1b6-66fab2042a00\r\nCorrelation ID: 4cfc9c81-660f-4a1a-ba0b-87db205c5461\r\nTimestamp: 2020-04-07 20:52:51Z","error_codes":[7000215],"timestamp":"2020-04-07 20:52:51Z","trace_id":"b179b7db-c957-4917-a1b6-66fab2042a00","correlation_id":"4cfc9c81-660f-4a1a-ba0b-87db205c5461","error_uri":"https://login.microsoftonline.com/error?code=7000215"} HTTPReturnCode:401
```

Aby naprawić nieprawidłowe poświadczenie klienta, sprawdź, czy client_id i klucz tajny są poprawne:

```console
$ kubectl -n azure-arc get cm/azure-clusterconfig -o yaml
  AZURE_CLIENT_ID: 82195c37-7497-458c-b643-f4a3d0a64190
  AZURE_RESOURCE_GROUP: AzureArc
  AZURE_RESOURCE_NAME: AzureArcCluster
```

### <a name="expired-credentials"></a>Wygasłe poświadczenia

Poświadczenia nazwy głównej usługi, które wygasły, powodują, że Agent łączenia z zarejestrowaniem błędu `AADSTS7000222: The provided client secret keys are expired` .

```console
$ kubectl -n azure-arc logs -l app.kubernetes.io/component=connect-agent -c connect-agent
2020/04/13 19:49:19 Environment validation :success
2020/04/13 19:49:19 Kubernetes API server access validation :success
2020/04/13 19:49:19 Azure Subscription access token :error :http request failed. Authentication Token URL:https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47/oauth2/token Authentication Token Body:grant_type=client_credentials&client_id=82195c37-7497-458c-b643-f4a3d0a64190&client_secret=9814c84e-59d7-49fc-bef6-17b717d2f5a8&resource=https%3A%2F%2Fmanagement.azure.com%2F ErrorInfo: Response:{"error":"invalid_client","error_description":"AADSTS7000222: The provided client secret keys are expired.\r\nTrace ID: 69ade0e5-f089-4a9d-b55d-9089e07f6300\r\nCorrelation ID: 10057011-6143-4e87-ad4a-c8256cf0e353\r\nTimestamp: 2020-04-13 19:49:19Z","error_codes":[7000222],"timestamp":"2020-04-13 19:49:19Z","trace_id":"69ade0e5-f089-4a9d-b55d-9089e07f6300","correlation_id":"10057011-6143-4e87-ad4a-c8256cf0e353"} HTTPReturnCode:401
```

Wygasłe poświadczenia mogą być resetowane przy użyciu `az ad sp credential reset` .

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
