---
title: Wdrażanie wykresów Helm przy użyciu GitOps na łuku z włączoną obsługą klastra Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Używanie GitOps z Helm dla konfiguracji klastra z obsługą usługi Azure Arc
keywords: GitOps, Kubernetes, K8s, Azure, Helm, ARC, AKS, usługa Azure Kubernetes, kontenery
ms.openlocfilehash: 117fc8dabdce2fdf23cbc2b9fe78137db1c656a5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101647646"
---
# <a name="deploy-helm-charts-using-gitops-on-an-arc-enabled-kubernetes-cluster"></a>Wdrażanie wykresów Helm przy użyciu GitOps w klastrze z włączonym łukiem Kubernetes

Helm to narzędzie do tworzenia pakietów typu open source, które ułatwia instalowanie cyklu życia aplikacji Kubernetes i zarządzanie nim. Podobnie jak w przypadku menedżerów pakietów systemu Linux, takich jak APT i yum, Helm służy do zarządzania wykresami Kubernetes, które są pakietami wstępnie skonfigurowanych zasobów Kubernetes.

W tym artykule opisano sposób konfigurowania i używania Helm z usługą Azure ARC z włączonym Kubernetes.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Upewnij się, że masz już podłączony połączony klaster usługi Azure Arc Kubernetes. Jeśli potrzebujesz podłączonego klastra, zobacz temat [nawiązywanie połączenia z usługą Azure ARC z włączoną obsługą klastra Kubernetes](./quickstart-connect-cluster.md).

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Omówienie korzystania z GitOps i Helm z włączonym usługą Azure Arc Kubernetes

 Operator Helm zapewnia rozszerzenie do napływu, który automatyzuje wydawanie wykresów Helm. Wydanie wykresu Helm jest opisane za pośrednictwem zasobu niestandardowego Kubernetes o nazwie HelmRelease. Strumień synchronizuje te zasoby z usługi git do klastra, podczas gdy operator Helm gwarantuje, że wykresy Helm są zwalniane zgodnie z określonymi w zasobach.

 [Przykładowe repozytorium](https://github.com/Azure/arc-helm-demo) używane w tym artykule są uporządkowane w następujący sposób:

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

W repozytorium git mamy dwa katalogi: jeden zawierający wykres Helm i jeden zawierający konfigurację wydań. W `releases` katalogu `app.yaml` znajduje się konfiguracja HelmRelease, pokazana poniżej:

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

Konfiguracja Helm Release zawiera następujące pola:

| Pole | Opis |
| ------------- | ------------- | 
| `metadata.name` | Pole wymagane. Musi przestrzegać konwencji nazewnictwa Kubernetes. |
| `metadata.namespace` | Pole opcjonalne. Określa miejsce tworzenia wersji. |
| `spec.releaseName` | Pole opcjonalne. Jeśli nie podano nazwy wydania, będzie to możliwe `$namespace-$name` . |
| `spec.chart.path` | Katalog zawierający wykres (względem katalogu głównego repozytorium). |
| `spec.values` | Dostosowanie wartości domyślnych parametrów przez użytkownika do samego wykresu. |

Opcje określone w HelmRelease `spec.values` zastępują opcje określone w `values.yaml` źródle wykresu.

Więcej informacji na temat HelmRelease można znaleźć w oficjalnym [dokumencie dotyczącym operatora Helm](https://docs.fluxcd.io/projects/helm-operator/en/stable/).

## <a name="create-a-configuration"></a>Utwórz konfigurację

Za pomocą rozszerzenia interfejsu wiersza polecenia platformy Azure dla programu `k8s-configuration` Połącz podłączony klaster z przykładowym repozytorium git. Nadaj tej konfiguracji nazwę `azure-arc-sample` i Wdróż operatora strumienia w `arc-k8s-demo` przestrzeni nazw.

```console
az k8s-configuration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-version='1.2.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Parametry konfiguracji

Aby dostosować Tworzenie konfiguracji, [zapoznaj się z dodatkowymi parametrami](./tutorial-use-gitops-connected-cluster.md#additional-parameters).

## <a name="validate-the-configuration"></a>Weryfikowanie konfiguracji

Upewnij się, że konfiguracja została pomyślnie utworzona przy użyciu interfejsu wiersza polecenia platformy Azure.

```console
az k8s-configuration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Zasób konfiguracji jest aktualizowany ze stanem zgodności, komunikatami i informacjami o debugowaniu.

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "1.2.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Weryfikuj aplikację

Uruchom następujące polecenie i przejdź do `localhost:8080` przeglądarki, aby sprawdzić, czy aplikacja jest uruchomiona.

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie konfiguracją klastra przy użyciu Azure Policy](./use-azure-policy.md)
