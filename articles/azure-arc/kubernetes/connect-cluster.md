---
title: Połącz klaster Kubernetes z obsługą usługi Azure ARC (wersja zapoznawcza)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Łączenie klastra Kubernetes z obsługą usługi Azure ARC przy użyciu usługi Azure Arc
keywords: Kubernetes, łuk, Azure, K8s, kontenery
ms.openlocfilehash: 690955f0e7d18f9a784b4c9a2fa1733442cb70dc
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780014"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Połącz klaster Kubernetes z obsługą usługi Azure ARC (wersja zapoznawcza)

Połącz klaster Kubernetes z usługą Azure Arc. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, czy masz gotowe do spełnienia następujące wymagania:

* Klaster Kubernetes, który jest uruchomiony
* Będziesz potrzebować dostępu z kubeconfig i dostępem do administratora klastra. 
* Nazwa główna użytkownika lub usługi używana z `az login` `az connectedk8s connect` poleceniami i musi mieć uprawnienia "read" i "Write" dla typu zasobu "Microsoft. Kubernetes/connectedclusters". Rola "Azure Arc for Kubernetes dołączania" z tymi uprawnieniami może służyć do przypisywania ról dla użytkownika lub nazwy głównej usługi używanej z interfejsem wiersza polecenia platformy Azure do dołączania.
* Najnowsza wersja rozszerzeń *connectedk8s* i *k8sconfiguration*

## <a name="supported-regions"></a>Obsługiwane regiony

* Wschodnie stany USA
* Europa Zachodnia

## <a name="network-requirements"></a>Wymagania dotyczące sieci

Agenci usługi Azure Arc potrzebują następujących protokołów/portów/wychodzących adresów URL do działania.

* TCP na porcie 443-->`https://:443`
* TCP na porcie 9418-->`git://:9418`

| Punkt końcowy (DNS)                                                                                               | Opis                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Wymagane przez agenta do łączenia się z platformą Azure i rejestrowania klastra                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Punkt końcowy płaszczyzny danych dla agenta do wypychania stanu i pobrania informacji o konfiguracji                                      |
| `https://docker.io`                                                                                            | Wymagane do ściągania obrazów kontenerów                                                                                         |
| `https://github.com`, git://github.com                                                                         | Przykładowe repozytoria GitOps są hostowane w serwisie GitHub. Agent konfiguracji wymaga łączności z określonym punktem końcowym git. |
| `https://login.microsoftonline.com`                                                                            | Wymagane do pobierania i aktualizowania tokenów Azure Resource Manager                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | Wymagane do ściągania obrazów kontenerów dla agentów usługi Azure Arc                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Zarejestruj dwóch dostawców z włączoną funkcją Azure Arc Kubernetes:

```console
az provider register --namespace Microsoft.Kubernetes
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.Kubernetes'

az provider register --namespace Microsoft.KubernetesConfiguration
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.KubernetesConfiguration'
```

Rejestracja jest procesem asynchronicznym. Rejestracja może potrwać około 10 minut. Proces rejestracji można monitorować przy użyciu następujących poleceń:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="install-azure-cli-extensions"></a>Instalowanie rozszerzeń interfejsu wiersza polecenia platformy Azure

Zainstaluj `connectedk8s` rozszerzenie, które ułatwia łączenie klastrów Kubernetes z platformą Azure:

```console
az extension add --name connectedk8s
```

Zainstaluj `k8sconfiguration` rozszerzenie:

```console
az extension add --name k8sconfiguration
```

Uruchom następujące polecenia, aby zaktualizować rozszerzenia do najnowszych wersji.

```console
az extension update --name connectedk8s
az extension update --name k8sconfiguration
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Użyj grupy zasobów do przechowywania metadanych klastra.

Najpierw utwórz grupę zasobów w celu przechowywania połączonego zasobu klastra.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**Rozdzielczości**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>Łączenie klastra

Następnie będziemy łączyć nasz klaster Kubernetes z platformą Azure. Przepływ pracy dla programu `az connectedk8s connect` jest następujący:

1. Weryfikowanie łączności z klastrem Kubernetes: za pośrednictwem `KUBECONFIG` , `~/.kube/config` lub`--kube-config`
1. Wdróż agentów usługi Azure ARC dla Kubernetes przy użyciu Helm 3 w `azure-arc` przestrzeni nazw

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**Rozdzielczości**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>Weryfikuj podłączony klaster

Utwórz listę podłączonych klastrów:

```console
az connectedk8s list -g AzureArcTest
```

**Rozdzielczości**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

Usługa Azure ARC z włączonym Kubernetes wdraża kilka operatorów w `azure-arc` przestrzeni nazw. Te wdrożenia i zasobniki można wyświetlić tutaj:

```console
kubectl -n azure-arc get deployments,pods
```

**Rozdzielczości**

```console
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

## <a name="azure-arc-agents-for-kubernetes"></a>Agenci Azure ARC dla Kubernetes

Usługa Azure ARC z włączonym Kubernetes składa się z kilku agentów (operatorów) uruchomionych w klastrze wdrożonym w `azure-arc` przestrzeni nazw.

* `deployment.apps/config-agent`: Obserwujący połączony klaster dla zasobów konfiguracji kontroli źródła zastosowanych w klastrze i aktualizacji stanu zgodności
* `deployment.apps/controller-manager`: jest operatorem operatorów i organizuje interakcje między składnikami usługi Azure Arc
* `deployment.apps/metrics-agent`: zbiera metryki innych agentów ARC, aby upewnić się, że te agenci wykazują optymalną wydajność
* `deployment.apps/cluster-metadata-operator`: zbiera metadane klastra — wersję klastra, liczbę węzłów i wersję agenta Arc
* `deployment.apps/resource-sync-agent`: synchronizuje powyższe metadane klastra z platformą Azure
* `deployment.apps/clusteridentityoperator`: utrzymuje certyfikat tożsamości usługi zarządzanej (MSI) używany przez innych agentów do komunikacji z platformą Azure
* `deployment.apps/flux-logs-agent`: zbiera dzienniki z operatorów strumienia wdrożonych w ramach konfiguracji kontroli źródła

## <a name="delete-a-connected-cluster"></a>Usuwanie połączonego klastra

Zasób można usunąć `Microsoft.Kubernetes/connectedcluster` przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure Portal.

Polecenie interfejsu wiersza polecenia platformy Azure `az connectedk8s delete` usuwa `Microsoft.Kubernetes/connectedCluster` zasób na platformie Azure. Interfejs wiersza polecenia platformy Azure usuwa wszystkie skojarzone `sourcecontrolconfiguration` zasoby na platformie Azure. Interfejs wiersza polecenia platformy Azure używa dezinstalacji Helm do usuwania agentów w klastrze.

Azure Portal usuwa `Microsoft.Kubernetes/connectedcluster` zasób na platformie Azure i usuwa wszystkie skojarzone `sourcecontrolconfiguration` zasoby na platformie Azure.

Aby usunąć agentów w klastrze, należy uruchomić `az connectedk8s delete` lub `helm uninstall azurearcfork8s` .

## <a name="next-steps"></a>Następne kroki

* [Używanie GitOps w podłączonym klastrze](./use-gitops-connected-cluster.md)
* [Zarządzanie konfiguracją klastra przy użyciu Azure Policy](./use-azure-policy.md)