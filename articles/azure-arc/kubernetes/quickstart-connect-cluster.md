---
title: 'Szybki start: łączenie istniejącego klastra Kubernetes z Azure Arc'
description: W tym przewodniku Szybki start dowiesz się, jak połączyć Azure Arc Kubernetes z włączonym klastrem.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 03/03/2021
ms.custom: template-quickstart, references_regions, devx-track-azurecli
keywords: Kubernetes, Arc, Azure, cluster
ms.openlocfilehash: 21ec5000ed7ef9df1805fa6ec43e20efc0f82182
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481260"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>Szybki start: łączenie istniejącego klastra Kubernetes z Azure Arc 

W tym przewodniku Szybki start będziemy czerpać korzyści z Azure Arc Kubernetes i połączyć istniejący klaster Kubernetes z Azure Arc. Koncepcyjne omówienie łączenia klastrów z klastrami Azure Arc można znaleźć w Azure Arc architektura agenta [Kubernetes z włączoną obsługą usługi](./conceptual-agent-architecture.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* Uruchomiony klaster Kubernetes. Jeśli go nie masz, możesz utworzyć klaster przy użyciu jednej z tych opcji:
    * [Kubernetes na platformy Docker (KIND)](https://kind.sigs.k8s.io/)
    * Tworzenie klastra Kubernetes przy użyciu platformy Docker dla komputerów [Mac lub](https://docs.docker.com/docker-for-mac/#kubernetes) systemu [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    * Samodzielnie zarządzany klaster Kubernetes przy użyciu [interfejsu API klastra](https://cluster-api.sigs.k8s.io/user/quick-start.html)

    >[!NOTE]
    > Klaster musi mieć co najmniej jeden węzeł systemu operacyjnego i typu architektury `linux/amd64` . Klastry z tylko `linux/arm64` węzłami nie są jeszcze obsługiwane.
    
* Plik `kubeconfig` i kontekst, które wskazują klaster.
* Uprawnienia "Odczyt" i "Zapis" Azure Arc typu zasobu Kubernetes z włączoną obsługą ( `Microsoft.Kubernetes/connectedClusters` ).

* Zainstaluj [najnowszą wersję programu Helm 3.](https://helm.sh/docs/intro/install)

- [Instalowanie lub uaktualnianie interfejsu](https://docs.microsoft.com/cli/azure/install-azure-cli) wiersza polecenia platformy Azure do wersji >= 2.16.0
* Zainstaluj rozszerzenie `connectedk8s` interfejsu wiersza polecenia platformy Azure w wersji >= 1.0.0:
  
  ```azurecli
  az extension add --name connectedk8s
  ```

>[!TIP]
> Jeśli rozszerzenie jest już zainstalowane, zaktualizuj je do najnowszej wersji przy `connectedk8s` użyciu następującego polecenia : `az extension update --name connectedk8s`


>[!NOTE]
>Listę regionów obsługiwanych przez Azure Arc Kubernetes można [znaleźć tutaj.](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)

>[!NOTE]
> Jeśli chcesz używać lokalizacji niestandardowych w klastrze, użyj regionów Wschodnie stany USA lub Europa Zachodnia, aby połączyć klaster, ponieważ lokalizacje niestandardowe są obecnie dostępne tylko w tych regionach. Wszystkie inne Azure Arc kubernetes z włączoną obsługą usługi Kubernetes są dostępne we wszystkich regionach wymienionych powyżej.

## <a name="meet-network-requirements"></a>Spełnianie wymagań sieciowych

>[!IMPORTANT]
>Azure Arc agentów do działania wymagają następujących protokołów/portów/adresów URL ruchu wychodzącego:
>* Protokół TCP na porcie 443: `https://:443`
>* Protokół TCP na porcie 9418: `git://:9418`
  
| Punkt końcowy (DNS) | Opis |  
| ----------------- | ------------- |  
| `https://management.azure.com`                                                                                 | Wymagane, aby agent łączył się z platformą Azure i rejestrł klaster.                                                        |  
| `https://<region>.dp.kubernetesconfiguration.azure.com` | Punkt końcowy płaszczyzny danych dla agenta w celu wypychania stanu i pobierania informacji o konfiguracji.                                      |  
| `https://login.microsoftonline.com`                                                                            | Wymagane do pobierania i aktualizowania Azure Resource Manager tokenów.                                                                                    |  
| `https://mcr.microsoft.com`                                                                            | Wymagane do ściągania obrazów kontenerów dla Azure Arc agentów.                                                                  |  
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`, `https://wcus.his.arc.azure.com`, `https://scus.his.arc.azure.com`, `https://sea.his.arc.azure.com`, `https://uks.his.arc.azure.com`, `https://wus2.his.arc.azure.com`, `https://ae.his.arc.azure.com`, `https://eus2.his.arc.azure.com`, `https://ne.his.arc.azure.com` |  Wymagane do ściągania certyfikatów tożsamości usługi zarządzanej (MSI) przypisanych przez system.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Rejestrowanie dwóch dostawców na Azure Arc Kubernetes

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

1. Połącz klaster Kubernetes z Azure Arc za pomocą następującego polecenia:
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
> Powyższe polecenie bez określonego parametru lokalizacji tworzy Azure Arc usługi Kubernetes w tej samej lokalizacji co grupa zasobów. Aby utworzyć zasób Azure Arc Kubernetes w innej lokalizacji, określ lub `--location <region>` `-l <region>` podczas uruchamiania `az connectedk8s connect` polecenia.

## <a name="verify-cluster-connection"></a>Weryfikowanie połączenia klastra

Wyświetl listę połączonych klastrów za pomocą następującego polecenia:  

```console
az connectedk8s list -g AzureArcTest -o table
```

```output
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

> [!NOTE]
> Po dołączaniu klastra metadane klastra (wersja klastra, wersja agenta, liczba węzłów itp.) mogą być dostępne na stronie przeglądu zasobu Kubernetes z włączoną obsługą usługi Azure Arc w programie Azure Portal.

## <a name="connect-using-an-outbound-proxy-server"></a>Nawiązywanie połączenia przy użyciu serwera proxy ruchu wychodzącego

Jeśli klaster znajduje się za serwerem proxy ruchu wychodzącego, interfejs wiersza polecenia platformy Azure Azure Arc agentów Kubernetes z włączoną obsługą ruchu wychodzącego muszą rozsyłać żądania za pośrednictwem serwera proxy ruchu wychodzącego. 


1. Ustaw zmienne środowiskowe potrzebne interfejsowi wiersza polecenia platformy Azure do korzystania z serwera proxy ruchu wychodzącego:

    * Jeśli używasz powłoki bash, uruchom następujące polecenie z odpowiednimi wartościami:

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

2. Uruchom polecenie connect z określonymi parametrami serwera proxy:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * Określ wartość w obszarze , aby upewnić `excludedCIDR` `--proxy-skip-range` się, że komunikacja w klastrze nie zostanie przerwana dla agentów.
> * `--proxy-http`W przypadku większości środowisk serwera proxy ruchu wychodzącego oczekiwane są `--proxy-https` `--proxy-skip-range` , i . `--proxy-cert` jest *wymagany* tylko wtedy, gdy trzeba wstrzyknąć zaufane certyfikaty oczekiwane przez serwer proxy do magazynu zaufanych certyfikatów zasobników agentów.

## <a name="view-azure-arc-agents-for-kubernetes"></a>Wyświetlanie Azure Arc agentów dla kubernetes

Azure Arc usługi Kubernetes wdraża kilka operatorów w przestrzeni `azure-arc` nazw . 

1. Wyświetl te wdrożenia i zasobniki przy użyciu:

    ```console
    kubectl -n azure-arc get deployments,pods
    ```

1. Sprawdź, czy wszystkie zasobniki są `Running` w stanie.

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

Za pomocą następującego polecenia Azure Arc kubernetes z włączoną obsługą usługi Kubernetes, wszelkie skojarzone zasoby konfiguracji i dowolnych agentów uruchomionych w klastrze: 

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
>Usunięcie zasobu Azure Arc kubernetes przy użyciu usługi Azure Portal powoduje usunięcie wszystkich  skojarzonych zasobów konfiguracji, ale nie powoduje usunięcia żadnych agentów uruchomionych w klastrze. Najlepszym rozwiązaniem jest usunięcie zasobu kubernetes Azure Arc z włączoną obsługą usługi, zamiast `az connectedk8s delete` Azure Portal.

## <a name="next-steps"></a>Następne kroki

W następnym artykule dowiesz się, jak wdrażać konfiguracje w połączonym klastrze Kubernetes przy użyciu usługi GitOps.
> [!div class="nextstepaction"]
> [Wdrażanie konfiguracji przy użyciu narzędzia Gitops](tutorial-use-gitops-connected-cluster.md)
