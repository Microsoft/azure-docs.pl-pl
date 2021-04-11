---
title: 'Szybki Start: łączenie istniejącego klastra Kubernetes z usługą Azure Arc'
description: W tym przewodniku szybki start dowiesz się, jak połączyć klaster Kubernetes z obsługą usługi Azure Arc.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 03/03/2021
ms.custom: template-quickstart, references_regions
keywords: Kubernetes, łuk, Azure, klaster
ms.openlocfilehash: b4cbd45f8478674c7c6bacc50f068bc0ec691a14
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449923"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>Szybki Start: łączenie istniejącego klastra Kubernetes z usługą Azure Arc 

W tym przewodniku szybki start skorzystać zalety usługi Azure ARC z włączonym Kubernetes i łączą istniejący klaster Kubernetes z usługą Azure Arc. Aby uzyskać informacje o koncepcyjnym przełączaniu klastrów do usługi Azure ARC, zobacz [artykuł architektura agenta Kubernetes z obsługą usługi Azure Arc](./conceptual-agent-architecture.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* W pełni uruchomiony klaster Kubernetes. Jeśli go nie masz, możesz utworzyć klaster przy użyciu jednej z następujących opcji:
    * [Kubernetes w Docker (rodzaj)](https://kind.sigs.k8s.io/)
    * Tworzenie klastra Kubernetes przy użyciu platformy Docker dla [komputerów Mac](https://docs.docker.com/docker-for-mac/#kubernetes) lub [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    * Samozarządzany klaster Kubernetes przy użyciu [interfejsu API klastra](https://cluster-api.sigs.k8s.io/user/quick-start.html)

    >[!NOTE]
    > Klaster musi mieć co najmniej jeden węzeł systemu operacyjnego i typu architektury `linux/amd64` . Klastry z tylko `linux/arm64` węzłami nie są jeszcze obsługiwane.
    
* `kubeconfig`Plik i kontekst wskazujący klaster.
* Uprawnienia "read" i "Write" dla typu zasobu Kubernetes z włączoną funkcją Azure ARC ( `Microsoft.Kubernetes/connectedClusters` ).

* Zainstaluj [najnowszą wersję programu Helm 3](https://helm.sh/docs/intro/install).

- [Instalowanie lub uaktualnianie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) do wersji >= 2.16.0
* Zainstaluj `connectedk8s` rozszerzenie interfejsu wiersza polecenia platformy Azure w wersji >= 1.0.0:
  
  ```azurecli
  az extension add --name connectedk8s
  ```

>[!TIP]
> Jeśli `connectedk8s` rozszerzenie jest już zainstalowane, zaktualizuj je do najnowszej wersji za pomocą następującego polecenia: `az extension update --name connectedk8s`


>[!NOTE]
>Listę regionów obsługiwanych przez usługę Azure Arc Kubernetes można znaleźć [tutaj](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

>[!NOTE]
> Jeśli chcesz używać lokalizacji niestandardowych w klastrze, użyj regionów Wschodnie stany USA lub Europa Zachodnia do łączenia klastra, ponieważ lokalizacje niestandardowe są dostępne tylko w tych regionach. Wszystkie inne funkcje usługi Azure ARC z włączoną obsługą Kubernetes są dostępne we wszystkich regionach wymienionych powyżej.

## <a name="meet-network-requirements"></a>Spełnianie wymagań sieci

>[!IMPORTANT]
>Agenci usługi Azure Arc wymagają następujących protokołów/portów/wychodzących adresów URL do działania:
>* TCP na porcie 443: `https://:443`
>* TCP na porcie 9418: `git://:9418`
  
| Punkt końcowy (DNS) | Opis |  
| ----------------- | ------------- |  
| `https://management.azure.com`                                                                                 | Wymagane, aby Agent łączył się z platformą Azure i zarejestrował klaster.                                                        |  
| `https://<region>.dp.kubernetesconfiguration.azure.com` | Punkt końcowy płaszczyzny danych dla agenta do wypychania stanu i pobrania informacji o konfiguracji.                                      |  
| `https://login.microsoftonline.com`                                                                            | Wymagane do pobierania i aktualizowania tokenów Azure Resource Manager.                                                                                    |  
| `https://mcr.microsoft.com`                                                                            | Wymagane do ściągania obrazów kontenerów dla agentów usługi Azure Arc.                                                                  |  
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`, `https://wcus.his.arc.azure.com`, `https://scus.his.arc.azure.com`, `https://sea.his.arc.azure.com`, `https://uks.his.arc.azure.com`, `https://wus2.his.arc.azure.com`, `https://ae.his.arc.azure.com`, `https://eus2.his.arc.azure.com`, `https://ne.his.arc.azure.com` |  Wymagany do ściągania certyfikatów tożsamość usługi zarządzanej (MSI) przypisanych do systemu.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Zarejestruj dwóch dostawców z włączoną funkcją Azure Arc Kubernetes

1. Wprowadź następujące polecenia:
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. Monitorowanie procesu rejestracji. Rejestracja może potrwać do 10 minut.
    ```azurecli
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów:  

```console
az group create --name AzureArcTest -l EastUS -o table
```

```output
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-an-existing-kubernetes-cluster"></a>Łączenie istniejącego klastra Kubernetes

1. Połącz klaster Kubernetes z usługą Azure ARC przy użyciu następującego polecenia:
    ```console
    az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
    ```

    ```output
    Helm release deployment succeeded

    {
      "aadProfile": {
        "clientAppId": "",
        "serverAppId": "",
        "tenantId": ""
      },
      "agentPublicKeyCertificate": "xxxxxxxxxxxxxxxxxxx",
      "agentVersion": null,
      "connectivityStatus": "Connecting",
      "distribution": "gke",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
      "identity": {
        "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "type": "SystemAssigned"
      },
      "infrastructure": "gcp",
      "kubernetesVersion": null,
      "lastConnectivityTime": null,
      "location": "eastus",
      "managedIdentityCertificateExpirationTime": null,
      "name": "AzureArcTest1",
      "offering": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "AzureArcTest",
      "tags": {},
      "totalCoreCount": null,
      "totalNodeCount": null,
      "type": "Microsoft.Kubernetes/connectedClusters"
    }
    ```

> [!TIP]
> Powyższe polecenie bez określonego parametru Location tworzy zasób Kubernetes z włączonym Łukem Azure w tej samej lokalizacji co grupa zasobów. Aby utworzyć zasób Kubernetes z obsługą usługi Azure Arc w innej lokalizacji, należy określić albo `--location <region>` `-l <region>` podczas uruchamiania `az connectedk8s connect` polecenia.

## <a name="verify-cluster-connection"></a>Weryfikuj połączenie klastra

Wyświetl listę podłączonych klastrów przy użyciu następującego polecenia:  

```console
az connectedk8s list -g AzureArcTest -o table
```

```output
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

> [!NOTE]
> Po dołączeniu klastra trwa około 5 – 10 minut w przypadku metadanych klastra (wersja klastra, wersja agenta, liczba węzłów itd.) do powierzchni na stronie Przegląd zasobu Kubernetes z włączoną funkcją Azure Arc w Azure Portal.

## <a name="connect-using-an-outbound-proxy-server"></a>Nawiązywanie połączenia przy użyciu serwera proxy wychodzącego

Jeśli klaster znajduje się za wychodzącym serwerem proxy, interfejs wiersza polecenia platformy Azure i agenci Kubernetes z obsługą usługi Azure Arc muszą kierować żądania za pośrednictwem serwera proxy wychodzącego. 


1. Ustaw zmienne środowiskowe, które są używane w interfejsie wiersza polecenia platformy Azure do korzystania z serwera proxy wychodzącego:

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

2. Uruchom polecenie Connect z określonymi parametrami serwera proxy:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * Określ w `excludedCIDR` obszarze `--proxy-skip-range` , aby upewnić się, że komunikacja w klastrze nie jest uszkodzona dla agentów.
> * `--proxy-http`, `--proxy-https` i `--proxy-skip-range` są oczekiwane dla większości środowisk serwera proxy ruchu wychodzącego. `--proxy-cert` jest wymagany *tylko* wtedy, gdy konieczne jest wstrzyknięcie zaufanych certyfikatów oczekiwanych przez serwer proxy do magazynu zaufanych certyfikatów w obszarze agentów.

## <a name="view-azure-arc-agents-for-kubernetes"></a>Wyświetlanie agentów usługi Azure ARC dla Kubernetes

Usługa Azure ARC z włączonym Kubernetes wdraża kilka operatorów w `azure-arc` przestrzeni nazw. 

1. Wyświetl te wdrożenia i narzędzia do użycia:

    ```console
    kubectl -n azure-arc get deployments,pods
    ```

1. Sprawdź, czy wszystkie zasobniki są w `Running` stanie.

    ```output
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

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Można usunąć zasób Kubernetes z obsługą usługi Azure ARC, skojarzone z nim zasoby konfiguracyjne *oraz* wszystkich agentów uruchomionych w klastrze przy użyciu następującego polecenia:

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
>Usunięcie zasobu Kubernetes z obsługą usługi Azure ARC przy użyciu Azure Portal powoduje usunięcie wszystkich skojarzonych zasobów konfiguracji, ale *nie* spowoduje usunięcia żadnych agentów uruchomionych w klastrze. Najlepszym rozwiązaniem jest usunięcie zasobu Kubernetes z włączoną funkcją Azure ARC przy użyciu `az connectedk8s delete` zamiast Azure Portal.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak wdrażać konfiguracje w podłączonym klastrze Kubernetes przy użyciu GitOps.
> [!div class="nextstepaction"]
> [Wdrażanie konfiguracji przy użyciu Gitops](tutorial-use-gitops-connected-cluster.md)
