---
title: Nawiązywanie połączenia z klastrem Kubernetes z włączoną usługą Azure Arc (wersja zapoznawcza)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Łączenie klastra Kubernetes z obsługą usługi Azure ARC przy użyciu usługi Azure Arc
keywords: Kubernetes, łuk, Azure, K8s, kontenery
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 8155ed709045626dee44fb499304ff5244a61b54
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060252"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Nawiązywanie połączenia z klastrem Kubernetes z włączoną usługą Azure Arc (wersja zapoznawcza)

W tym dokumencie opisano proces łączenia z klastrem Kubernetes, który jest certyfikowany w chmurze CNCF, na przykład AKS-Engine na platformie Azure, AKS-Engine on Azure Stack Hub, GKE, EKS i VMware vSphere do usługi Azure Arc.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, czy masz gotowe do spełnienia następujące wymagania:

* Klaster Kubernetes, który jest uruchomiony. Jeśli nie masz istniejącego klastra Kubernetes, możesz użyć jednej z następujących przewodników, aby utworzyć klaster testowy:
  * Tworzenie klastra Kubernetes przy użyciu [Kubernetes w Docker (rodzaj)](https://kind.sigs.k8s.io/)
  * Tworzenie klastra Kubernetes przy użyciu platformy Docker dla [komputerów Mac](https://docs.docker.com/docker-for-mac/#kubernetes) lub [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
* Musisz mieć plik kubeconfig, aby uzyskać dostęp do roli klastra i klastra w klastrze w celu wdrożenia agentów Kubernetes z włączonym łukiem.
* Nazwa główna użytkownika lub usługi używana z `az login` `az connectedk8s connect` poleceniami i musi mieć uprawnienia "read" i "Write" dla typu zasobu "Microsoft. Kubernetes/connectedclusters". Rola "klaster Kubernetes — dołączanie do usługi Azure ARC" ma te uprawnienia i może służyć do przypisywania ról dla użytkownika lub nazwy głównej usługi.
* Do dołączania klastra przy użyciu rozszerzenia connectedk8s jest wymagany Helm 3. [Zainstaluj najnowszą wersję programu Helm 3](https://helm.sh/docs/intro/install) , aby spełnić to wymaganie.
* Interfejs wiersza polecenia platformy Azure w wersji 2.15 + jest wymagany do zainstalowania rozszerzeń interfejsu wiersza polecenia Kubernetes z funkcją Azure Arc. [Zainstaluj interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) lub zaktualizuj do najnowszej wersji, aby upewnić się, że masz interfejs wiersza polecenia platformy Azure w wersji 2.15 +.
* Zainstaluj rozszerzenia Kubernetes CLI z włączonym łukiem:
  
  Zainstaluj `connectedk8s` rozszerzenie, które ułatwia łączenie klastrów Kubernetes z platformą Azure:
  
  ```console
  az extension add --name connectedk8s
  ```
  
  Zainstaluj `k8sconfiguration` rozszerzenie:
  
  ```console
  az extension add --name k8sconfiguration
  ```
  
  Jeśli chcesz zaktualizować te rozszerzenia później, uruchom następujące polecenia:
  
  ```console
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>Obsługiwane regiony

* East US
* West Europe

## <a name="network-requirements"></a>Wymagania dotyczące sieci

Agenci usługi Azure Arc potrzebują następujących protokołów/portów/wychodzących adresów URL do działania.

* TCP na porcie 443--> `https://:443`
* TCP na porcie 9418--> `git://:9418`

| Punkt końcowy (DNS)                                                                                               | Opis                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Wymagane przez agenta do łączenia się z platformą Azure i rejestrowania klastra                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Punkt końcowy płaszczyzny danych dla agenta do wypychania stanu i pobrania informacji o konfiguracji                                      |
| `https://login.microsoftonline.com`                                                                            | Wymagane do pobierania i aktualizowania tokenów Azure Resource Manager                                                                                    |
| `https://mcr.microsoft.com`                                                                            | Wymagane do ściągania obrazów kontenerów dla agentów usługi Azure Arc                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Wymagany do ściągania certyfikatów tożsamości zarządzanych przypisanych przez system                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Zarejestruj dwóch dostawców z włączoną funkcją Azure Arc Kubernetes:

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

Rejestracja jest procesem asynchronicznym. Rejestracja może potrwać około 10 minut. Proces rejestracji można monitorować przy użyciu następujących poleceń:

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

Następnie będziemy łączyć nasz klaster Kubernetes z platformą Azure. Przepływ pracy dla programu `az connectedk8s connect` jest następujący:

1. Weryfikowanie łączności z klastrem Kubernetes: za pośrednictwem `KUBECONFIG` , `~/.kube/config` lub `--kube-config`
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
az connectedk8s list -g AzureArcTest -o table
```

**Rozdzielczości**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

Możesz również wyświetlić ten zasób na [Azure Portal](https://portal.azure.com/). Gdy Portal zostanie otwarty w przeglądarce, przejdź do grupy zasobów i zasobu Kubernetes z włączoną funkcją Azure Arc na podstawie nazwy zasobu i nazwy grupy zasobów użytych wcześniej w `az connectedk8s connect` poleceniu.

> [!NOTE]
> Po dołączeniu klastra trwa około 5 – 10 minut, aby metadane klastra (wersja klastra, wersja agenta, liczba węzłów) były dostępne na stronie Przegląd zasobu Kubernetes w usłudze Azure Arc w Azure Portal.

## <a name="connect-using-an-outbound-proxy-server"></a>Nawiązywanie połączenia przy użyciu serwera proxy wychodzącego

Jeśli klaster znajduje się za wychodzącym serwerem proxy, interfejs wiersza polecenia platformy Azure i agenci Kubernetes muszą kierować żądania za pośrednictwem serwera proxy wychodzącego. Następująca konfiguracja umożliwia:

1. Sprawdź wersję `connectedk8s` rozszerzenia zainstalowanego na komputerze, uruchamiając następujące polecenie:

    ```console
    az -v
    ```

    Wymagana `connectedk8s` wersja rozszerzenia >= 0.2.5, aby skonfigurować agentów z serwerem proxy wychodzącego. Jeśli masz wersję < 0.2.3 na maszynie, wykonaj [kroki aktualizacji](#before-you-begin) , aby pobrać najnowszą wersję rozszerzenia na komputerze.

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
> 1. Określenie excludedCIDR w obszarze--proxy-Skip-Range jest ważne, aby zapewnić, że komunikacja w klastrze nie jest uszkodzona dla agentów.
> 2. While--proxy-HTTP,--proxy-HTTPS i--proxy-Range-zakres jest oczekiwany w przypadku większości środowisk serwera proxy wychodzącego.--certyfikat proxy jest wymagany tylko wtedy, gdy istnieją zaufane certyfikaty z serwera proxy, które muszą zostać wprowadzone do magazynu zaufanych certyfikatów.
> 3. Powyższa Specyfikacja serwera proxy jest obecnie stosowana tylko dla agentów ARC, a nie dla zasobników strumieni używanych w sourceControlConfiguration. Zespół z włączonym łukiem Kubernetes aktywnie pracuje nad tą funkcją i będzie dostępny wkrótce.

## <a name="azure-arc-agents-for-kubernetes"></a>Agenci Azure ARC dla Kubernetes

Usługa Azure ARC z włączonym Kubernetes wdraża kilka operatorów w `azure-arc` przestrzeni nazw. Te wdrożenia i zasobniki można wyświetlić tutaj:

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

* `deployment.apps/config-agent`: Obserwujący połączony klaster dla zasobów konfiguracji kontroli źródła zastosowanych w klastrze i aktualizacji stanu zgodności
* `deployment.apps/controller-manager`: jest operatorem operatorów i organizuje interakcje między składnikami usługi Azure Arc
* `deployment.apps/metrics-agent`: zbiera metryki innych agentów ARC, aby upewnić się, że te agenci wykazują optymalną wydajność
* `deployment.apps/cluster-metadata-operator`: zbiera metadane klastra — wersję klastra, liczbę węzłów i wersję agenta usługi Azure Arc
* `deployment.apps/resource-sync-agent`: synchronizuje powyższe metadane klastra z platformą Azure
* `deployment.apps/clusteridentityoperator`: Usługa Azure ARC z włączonym Kubernetes obecnie obsługuje tożsamość przypisaną do systemu. clusteridentityoperator zachowuje certyfikat tożsamości usługi zarządzanej (MSI) używany przez innych agentów do komunikacji z platformą Azure.
* `deployment.apps/flux-logs-agent`: zbiera dzienniki z operatorów strumienia wdrożonych w ramach konfiguracji kontroli źródła

## <a name="delete-a-connected-cluster"></a>Usuwanie połączonego klastra

Zasób można usunąć `Microsoft.Kubernetes/connectedcluster` przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure Portal.


* **Usuwanie przy użyciu interfejsu wiersza polecenia platformy Azure**: następujące polecenie interfejsu wiersza poleceń platformy Azure może służyć do inicjowania usuwania zasobu Kubernetes z włączoną funkcją Azure Arc.
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  To polecenie usuwa `Microsoft.Kubernetes/connectedCluster` zasób i wszystkie skojarzone `sourcecontrolconfiguration` zasoby na platformie Azure. W interfejsie wiersza polecenia platformy Azure jest używana Dezinstalacja Helm w celu usunięcia agentów uruchomionych w klastrze.

* **Usuwanie na Azure Portal**: usunięcie zasobu Kubernetes z obsługą usługi Azure Arc na Azure Portal powoduje usunięcie `Microsoft.Kubernetes/connectedcluster` zasobu i wszystkich skojarzonych `sourcecontrolconfiguration` zasobów na platformie Azure, ale nie spowoduje usunięcia agentów uruchomionych w klastrze. Aby usunąć agentów uruchomionych w klastrze, uruchom następujące polecenie.

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z podejścia GitOps w połączonym klastrze](./use-gitops-connected-cluster.md)
* [Zarządzanie konfiguracją klastra przy użyciu Azure Policy](./use-azure-policy.md)
