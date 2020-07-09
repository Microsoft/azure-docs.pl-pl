---
title: Używanie GitOps z Helm dla konfiguracji klastra z obsługą usługi Azure ARC (wersja zapoznawcza)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Używanie GitOps z Helm dla konfiguracji klastra z obsługą usługi Azure ARC (wersja zapoznawcza)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, ARC, AKS, usługa Azure Kubernetes, kontenery
ms.openlocfilehash: 677c5f2b27794ebea9d38e470b5e1a5ba12bff7e
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857227"
---
# <a name="use-gitops-with-helm-for-an-azure-arc-enabled-cluster-configuration-preview"></a>Używanie GitOps z Helm dla konfiguracji klastra z obsługą usługi Azure ARC (wersja zapoznawcza)

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

## <a name="overview-of-using-helm-with-azure-arc-enabled-kubernetes"></a>Omówienie używania Helm z funkcją Azure Arc Kubernetes

 Operator Helm zapewnia rozszerzenie do napływu, który automatyzuje wydawanie wykresów Helm. Wydanie wykresu jest opisane za pomocą niestandardowego zasobu Kubernetes o nazwie HelmRelease. Strumień synchronizuje te zasoby z usługi git do klastra, a operator Helm gwarantuje, że wykresy Helm są zwalniane zgodnie z określonymi w zasobach.

 Poniżej znajduje się przykładowa struktura repozytorium git, która będzie używana w tym samouczku:

```bash
├── charts
│   └── azure-vote
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── prod
        └── azure-vote-app.yaml
```

W repozytorium git mamy dwa katalogi — jeden zawierający wykres Helm i jeden zawierający konfigurację wydań. W `releases/prod` katalogu znajduje `azure-vote-app.yaml` się konfiguracja HelmRelease pokazana poniżej:

```bash
 apiVersion: helm.fluxcd.io/v1
 kind: HelmRelease
 metadata:
   name: azure-vote-app
   namespace: prod
   annotations:
 spec:
   releaseName: azure-vote-app
   chart:
     git: https://github.com/Azure/arc-helm-demo
     path: charts/azure-vote
     ref: master
   values:
     image:
       repository: dstrebel/azurevote
       tag: v1
     replicaCount: 3
```

Konfiguracja Helm Release zawiera następujące pola:

- `metadata.name`jest obowiązkowy i musi przestrzegać konwencji nazewnictwa Kubernetes
- `metadata.namespace`jest opcjonalne i określa, gdzie jest tworzona wersja
- `spec.releaseName`jest opcjonalne i jeśli nie zostanie podana nazwa wydania będzie $namespace-$name
- `spec.chart.path`jest katalogiem zawierającym wykres względem katalogu głównego repozytorium
- `spec.values`czy domyślne wartości parametrów są dostosowywane przez użytkownika

Opcje określone w specyfikacji HelmRelease. wartości spowodują zastąpienie opcji określonych w wartości. YAML ze źródła wykresu.

Więcej informacji na temat HelmRelease można znaleźć w oficjalnej [dokumentacji operatora Helm](https://docs.fluxcd.io/projects/helm-operator/en/1.0.0-rc9/references/helmrelease-custom-resource.html)

## <a name="create-a-configuration"></a>Utwórz konfigurację

Za pomocą rozszerzenia interfejsu wiersza polecenia platformy Azure dla programu `k8sconfiguration` Połączmy nasz połączony klaster z przykładowym repozytorium git. Ta konfiguracja zostanie nadana nazwie `azure-voting-app` i zostanie wdrożony operator strumień w `prod` przestrzeni nazw.

```bash
az k8sconfiguration create --name azure-voting-app --resource-group  $RESOURCE_GROUP --cluster-name $CLUSTER_NAME --operator-instance-name azure-voting-app --operator-namespace prod --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --operator-params='--git-readonly --git-path=releases/prod' --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Parametry konfiguracji

Aby dostosować Tworzenie konfiguracji, [zapoznaj się z dodatkowymi parametrami, które mogą być używane](./use-gitops-connected-cluster.md#additional-parameters).


## <a name="validate-the-configuration"></a>Weryfikowanie konfiguracji

Za pomocą interfejsu wiersza polecenia platformy Azure Sprawdź, czy `sourceControlConfiguration` został pomyślnie utworzony.

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-voting-app --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
```

Należy pamiętać, że `sourceControlConfiguration` zasób jest aktualizowany ze stanem zgodności, komunikatami i informacjami o debugowaniu.

**Rozdzielczości**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "azure-vote-app",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "prod",
  "operatorParams": "--git-readonly --git-path=releases/prod",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Weryfikuj aplikację

Teraz sprawdzimy, czy aplikacja jest uruchomiona, pobierając adres IP usługi.

```bash
kubectl get svc/azure-vote-front -n prod
```

**Rozdzielczości**

```bash
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.14.161   52.186.160.216   80:30372/TCP   4d22h
```

Znajdź zewnętrzny adres IP z danych wyjściowych powyżej i otwórz go w przeglądarce.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie konfiguracją klastra przy użyciu Azure Policy](./use-azure-policy.md)
