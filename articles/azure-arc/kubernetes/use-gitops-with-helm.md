---
title: Wdrażanie wykresów Helm przy użyciu GitOps na łuku z włączoną obsługą klastra Kubernetes (wersja zapoznawcza)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Używanie GitOps z Helm dla konfiguracji klastra z obsługą usługi Azure ARC (wersja zapoznawcza)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, ARC, AKS, usługa Azure Kubernetes, kontenery
ms.openlocfilehash: cca48910b679ff8f72ee06f4ed990bd480fb2200
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723643"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Wdrażanie wykresów Helm przy użyciu GitOps na łuku z włączoną obsługą klastra Kubernetes (wersja zapoznawcza)

Helm to narzędzie do tworzenia pakietów typu "open source", które ułatwia Instalowanie i zarządzanie cyklem życia aplikacji Kubernetes. Podobnie jak w przypadku menedżerów pakietów systemu Linux, takich jak APT i yum, Helm służy do zarządzania wykresami Kubernetes, które są pakietami wstępnie skonfigurowanych zasobów Kubernetes.

W tym artykule opisano sposób konfigurowania i używania Helm z usługą Azure ARC z włączonym Kubernetes.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący Kubernetes połączony klaster usługi Azure Arc. Jeśli potrzebny jest podłączony klaster, zobacz temat [łączenie się z klastrem szybki start](./connect-cluster.md).

Najpierw Ustaw zmienne środowiskowe do użycia w tym samouczku. Wymagana jest nazwa grupy zasobów i nazwa klastra dla połączonego klastra.

```bash
export RESOURCE_GROUP=<Resource_Group_Name>
export CLUSTER_NAME=<ClusterName>
```

## <a name="verify-your-cluster-is-enabled-with-arc"></a>Sprawdź, czy klaster jest włączony przy użyciu łuku

```bash
az connectedk8s list -g $RESOURCE_GROUP -o table
```

Dane wyjściowe:
```bash
Name           Location    ResourceGroup
-------------  ----------  ---------------
arc-helm-demo  eastus      k8s-clusters
```

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Omówienie korzystania z GitOps i Helm z włączonym usługą Azure Arc Kubernetes

 Operator Helm zapewnia rozszerzenie do napływu, który automatyzuje wydawanie wykresów Helm. Wydanie wykresu jest opisane za pomocą niestandardowego zasobu Kubernetes o nazwie HelmRelease. Strumień synchronizuje te zasoby z usługi git do klastra, a operator Helm gwarantuje, że wykresy Helm są zwalniane zgodnie z określonymi w zasobach.

 Poniżej znajduje się przykładowa struktura repozytorium git, która będzie używana w tym samouczku:

```bash
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

W repozytorium git mamy dwa katalogi — jeden zawierający wykres Helm i jeden zawierający konfigurację wydań. W `releases` katalogu `app.yaml` znajduje się HelmRelease konfiguracja pokazana poniżej:

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

- `metadata.name` jest obowiązkowy i musi przestrzegać konwencji nazewnictwa Kubernetes
- `metadata.namespace` jest opcjonalne i określa, gdzie jest tworzona wersja
- `spec.releaseName` jest opcjonalne i jeśli nie zostanie podana nazwa wydania będzie $namespace-$name
- `spec.chart.path` jest katalogiem zawierającym wykres względem katalogu głównego repozytorium
- `spec.values` czy domyślne wartości parametrów są dostosowywane przez użytkownika

Opcje określone w specyfikacji HelmRelease. wartości spowodują zastąpienie opcji określonych w wartości. YAML ze źródła wykresu.

Więcej informacji na temat HelmRelease można znaleźć w oficjalnej [dokumentacji operatora Helm](https://docs.fluxcd.io/projects/helm-operator/en/stable/)

## <a name="create-a-configuration"></a>Utwórz konfigurację

Za pomocą rozszerzenia interfejsu wiersza polecenia platformy Azure dla programu `k8sconfiguration` Połączmy nasz połączony klaster z przykładowym repozytorium git. Ta konfiguracja zostanie nadana nazwie `azure-arc-sample` i zostanie wdrożony operator strumień w `arc-k8s-demo` przestrzeni nazw.

```bash
az k8sconfiguration create --name azure-arc-sample \
  --resource-group $RESOURCE_GROUP --cluster-name $CLUSTER_NAME \
  --operator-instance-name flux --operator-namespace arc-k8s-demo \
  --operator-params='--git-readonly --git-path=releases' \
  --enable-helm-operator --helm-operator-version='0.6.0' \
  --helm-operator-params='--set helm.versions=v3' \
  --repository-url https://github.com/Azure/arc-helm-demo.git  \
  --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Parametry konfiguracji

Aby dostosować Tworzenie konfiguracji, [zapoznaj się z dodatkowymi parametrami, które mogą być używane](./use-gitops-connected-cluster.md#additional-parameters).

## <a name="validate-the-configuration"></a>Weryfikowanie konfiguracji

Za pomocą interfejsu wiersza polecenia platformy Azure Sprawdź, czy `sourceControlConfiguration` został pomyślnie utworzony.

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-arc-sample --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
```

`sourceControlConfiguration`Zasób jest aktualizowany ze stanem zgodności, komunikatami i informacjami o debugowaniu.

**Rozdzielczości**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
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
    "chartVersion": "0.6.0"
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

```bash
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie konfiguracją klastra przy użyciu Azure Policy](./use-azure-policy.md)
