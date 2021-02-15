---
title: Połącz klaster Kubernetes z włączonym usługą Azure ARC (wersja zapoznawcza)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Łączenie klastra Kubernetes z obsługą usługi Azure ARC przy użyciu usługi Azure Arc
keywords: Kubernetes, łuk, Azure, K8s, kontenery
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: e68eccf998592aa7d1ebfea51e4ca66d577b3c7f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390559"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Połącz klaster Kubernetes z włączonym usługą Azure ARC (wersja zapoznawcza)

W tym artykule opisano proces łączenia z klastrem Kubernetes (CNCF) certyfikowanych rozwiązań w chmurze, takich jak AKS-Engine na platformie Azure, AKS-Engine on Azure Stack Hub, GKE, EKS i VMware vSphere klastra do usługi Azure Arc.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Sprawdź, czy zostały przygotowane następujące wymagania wstępne:

* W pełni uruchomiony klaster Kubernetes. Jeśli nie masz istniejącego klastra Kubernetes, możesz użyć jednej z następujących przewodników, aby utworzyć klaster testowy:
  * Utwórz klaster Kubernetes przy użyciu [Kubernetes w Docker (rodzaj)](https://kind.sigs.k8s.io/).
  * Tworzenie klastra Kubernetes przy użyciu platformy Docker dla [komputerów Mac](https://docs.docker.com/docker-for-mac/#kubernetes) lub [Windows](https://docs.docker.com/docker-for-windows/#kubernetes).
* Plik kubeconfig, który umożliwia dostęp do roli klastra i klastra w klastrze w celu wdrożenia agentów Kubernetes z włączonym łukiem.
* Nazwa główna użytkownika lub usługi używana z `az login` `az connectedk8s connect` poleceniami i musi mieć uprawnienia "read" i "Write" dla typu zasobu "Microsoft. Kubernetes/connectedclusters". Rola "klaster Kubernetes — dołączanie do usługi Azure ARC" ma te uprawnienia i może służyć do przypisywania ról dla użytkownika lub nazwy głównej usługi.
* Helm 3 do dołączania klastra przy użyciu rozszerzenia connectedk8s. [Zainstaluj najnowszą wersję programu Helm 3](https://helm.sh/docs/intro/install) , aby spełnić to wymaganie.
* Interfejs wiersza polecenia platformy Azure w wersji 2.15 + w celu zainstalowania rozszerzeń interfejsu wiersza polecenia Kubernetes. [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) lub zaktualizuj do najnowszej wersji.
* Zainstaluj rozszerzenia Kubernetes CLI z włączonym łukiem:
  
  * Zainstaluj `connectedk8s` rozszerzenie, które ułatwia łączenie klastrów Kubernetes z platformą Azure:
  
  ```azurecli
  az extension add --name connectedk8s
  ```
  
   * Zainstaluj `k8sconfiguration` rozszerzenie:
  
  ```azurecli
  az extension add --name k8sconfiguration
  ```

  * Jeśli chcesz zaktualizować te rozszerzenia później, uruchom następujące polecenia:
  
  ```azurecli
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>Obsługiwane regiony

* East US
* West Europe

## <a name="network-requirements"></a>Wymagania dotyczące sieci

Agenci usługi Azure Arc wymagają następujących protokołów/portów/wychodzących adresów URL do działania:

* TCP na porcie 443: `https://:443`
* TCP na porcie 9418: `git://:9418`

| Punkt końcowy (DNS)                                                                                               | Opis                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Wymagane, aby Agent łączył się z platformą Azure i zarejestrował klaster.                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Punkt końcowy płaszczyzny danych dla agenta do wypychania stanu i pobrania informacji o konfiguracji.                                      |
| `https://login.microsoftonline.com`                                                                            | Wymagane do pobierania i aktualizowania tokenów Azure Resource Manager.                                                                                    |
| `https://mcr.microsoft.com`                                                                            | Wymagane do ściągania obrazów kontenerów dla agentów usługi Azure Arc.                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Wymagane do ściągania certyfikatów tożsamości zarządzanych przypisanych przez system.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Zarejestruj dwóch dostawców z włączoną funkcją Azure Arc Kubernetes:

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

Rejestracja jest procesem asynchronicznym i może potrwać około 10 minut. Proces rejestracji można monitorować przy użyciu następujących poleceń:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
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

Następnie będziemy łączyć nasz klaster Kubernetes z platformą Azure przy użyciu `az connectedk8s connect` :

1. Sprawdź łączność z klastrem Kubernetes za pomocą jednego z następujących elementów:
   * `KUBECONFIG`
   * `~/.kube/config`
   * `--kube-config`
1. Wdróż agentów usługi Azure ARC dla Kubernetes przy użyciu Helm 3 w `azure-arc` przestrzeni nazw:

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

Użyj następującego polecenia, aby wyświetlić listę podłączonych klastrów:

```console
az connectedk8s list -g AzureArcTest -o table
```

**Rozdzielczości**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

Możesz również wyświetlić ten zasób na [Azure Portal](https://portal.azure.com/). Otwórz Portal w przeglądarce i przejdź do grupy zasobów i zasobu Kubernetes z obsługą usługi Azure Arc na podstawie nazwy zasobu i nazwy grupy zasobów używanych wcześniej w `az connectedk8s connect` poleceniu.  
> [!NOTE]
> Po dołączeniu klastra trwa około 5 – 10 minut w przypadku metadanych klastra (wersja klastra, wersja agenta, liczba węzłów itd.) do powierzchni na stronie Przegląd zasobu Kubernetes z włączoną funkcją Azure Arc w Azure Portal.

## <a name="connect-using-an-outbound-proxy-server"></a>Nawiązywanie połączenia przy użyciu serwera proxy wychodzącego

Jeśli klaster znajduje się za wychodzącym serwerem proxy, interfejs wiersza polecenia platformy Azure i agenci Kubernetes muszą kierować żądania za pośrednictwem serwera proxy wychodzącego:

1. Sprawdź wersję `connectedk8s` rozszerzenia zainstalowanego na komputerze:

    ```console
    az -v
    ```

    `connectedk8s`Aby skonfigurować agentów z serwerem proxy wychodzącego, musisz mieć rozszerzenie 0.2.5 +. Jeśli na komputerze jest zainstalowana wersja 0.2.3 lub starsza, wykonaj [kroki aktualizacji](#before-you-begin) , aby pobrać najnowszą wersję rozszerzenia na komputerze.

2. Ustaw zmienne środowiskowe, które są używane w interfejsie wiersza polecenia platformy Azure do korzystania z serwera proxy wychodzącego:

    * Jeśli używasz bash, uruchom następujące polecenie z odpowiednimi wartościami:

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * Jeśli używasz programu PowerShell, uruchom następujące polecenie z odpowiednimi wartościami:

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

3. Uruchom polecenie Connect z określonymi parametrami serwera proxy:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * Określenie `excludedCIDR` w obszarze `--proxy-skip-range` jest ważne, aby zapewnić, że komunikacja w klastrze nie jest uszkodzona dla agentów.
> * Chociaż `--proxy-http` , `--proxy-https` i `--proxy-skip-range` są oczekiwane dla większości środowisk serwera proxy wychodzącego, `--proxy-cert` jest to wymagane tylko w przypadku, gdy zaufane certyfikaty z serwera proxy muszą zostać wprowadzone do magazynu zaufanych certyfikatów w obszarze agentów.
> * Powyższa Specyfikacja serwera proxy jest obecnie stosowana tylko dla agentów ARC, a nie dla zasobników strumieni używanych w sourceControlConfiguration. Zespół z włączonym łukiem Kubernetes aktywnie pracuje nad tą funkcją i będzie dostępny wkrótce.

## <a name="azure-arc-agents-for-kubernetes"></a>Agenci Azure ARC dla Kubernetes

Usługa Azure ARC z włączonym Kubernetes wdraża kilka operatorów w `azure-arc` przestrzeni nazw. Można wyświetlić te wdrożenia i narzędzia do użycia:

```console
kubectl -n azure-arc get deployments,pods
```

**Rozdzielczości**

```console
NAME                                        READY      UP-TO-DATE  AVAILABLE  AGE
deployment.apps/cluster-metadata-operator     1/1             1        1      16h
deployment.apps/clusteridentityoperator       1/1             1        1      16h
deployment.apps/config-agent                  1/1             1        1      16h
deployment.apps/controller-manager            1/1             1        1      16h
deployment.apps/flux-logs-agent               1/1             1        1      16h
deployment.apps/metrics-agent                 1/1             1        1      16h
deployment.apps/resource-sync-agent           1/1             1        1      16h

NAME                                           READY    STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Usługa Azure ARC z włączonym Kubernetes składa się z kilku agentów (operatorów) uruchomionych w klastrze wdrożonym w `azure-arc` przestrzeni nazw.

| Agenci (operatorzy)                                                                                               | Opis                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `deployment.apps/config-agent`                                                                                 | Obserwuje podłączony klaster pod kątem zasobów konfiguracji kontroli źródła zastosowanych w klastrze i aktualizuje stan zgodności.                                                        |
| `deployment.apps/controller-manager` | Operator operatorów, który organizuje interakcje między składnikami usługi Azure Arc.                                      |
| `deployment.apps/metrics-agent`                                                                            | Zbiera metryki wydajności innych agentów Arc.                                                                                    |
| `deployment.apps/cluster-metadata-operator`                                                                            | Zbiera metadane klastra, takie jak wersja klastra, liczba węzłów i wersja agenta usługi Azure Arc.                                                                  |
| `deployment.apps/resource-sync-agent`                                                                            |  Synchronizuje powyższe metadane klastra z platformą Azure.                                                                  |
| `deployment.apps/clusteridentityoperator`                                                                            |  Usługa Azure ARC z włączonym Kubernetes obsługuje obecnie tożsamość przypisaną do systemu. `clusteridentityoperator` zachowuje certyfikat tożsamości usługi zarządzanej (MSI) używany przez innych agentów do komunikacji z platformą Azure.                                                                  |
| `deployment.apps/flux-logs-agent`                                                                            |  Zbiera dzienniki z operatorów strumienia wdrożonych w ramach konfiguracji kontroli źródła.                                                                  |

## <a name="delete-a-connected-cluster"></a>Usuwanie połączonego klastra

Zasób można usunąć `Microsoft.Kubernetes/connectedcluster` przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure Portal.


* **Usuwanie przy użyciu interfejsu wiersza polecenia platformy Azure**: aby zainicjować usuwanie zasobu Kubernetes z włączonym użyciem usługi Azure ARC, należy użyć poniższego narzędzia platformy Azure.
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  To polecenie usuwa `Microsoft.Kubernetes/connectedCluster` zasób i wszystkie skojarzone `sourcecontrolconfiguration` zasoby na platformie Azure. Interfejs wiersza polecenia platformy Azure używa `helm uninstall` również do usuwania agentów uruchomionych w klastrze.

* **Usuwanie na Azure Portal**: usunięcie zasobu Kubernetes z obsługą usługi Azure Arc na Azure Portal powoduje usunięcie `Microsoft.Kubernetes/connectedcluster` zasobu i wszystkich skojarzonych `sourcecontrolconfiguration` zasobów na platformie Azure, ale *nie* spowoduje usunięcia agentów uruchomionych w klastrze. 

  Aby usunąć agentów uruchomionych w klastrze, uruchom następujące polecenie:

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z podejścia GitOps w połączonym klastrze](./use-gitops-connected-cluster.md)
* [Zarządzanie konfiguracją klastra przy użyciu Azure Policy](./use-azure-policy.md)
