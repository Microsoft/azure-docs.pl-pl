---
title: Jak zatrzymać monitorowanie hybrydowego klastra Kubernetes | Microsoft Docs
description: W tym artykule opisano, jak można zatrzymać monitorowanie hybrydowego klastra Kubernetes za pomocą Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 2754649cd990b015162be158effa2b85aa1fe27e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986053"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>Jak zatrzymać monitorowanie klastra hybrydowego

Po włączeniu monitorowania klastra Kubernetes można zatrzymać monitorowanie klastra przy użyciu Azure Monitor dla kontenerów, jeśli zdecydujesz, że nie chcesz już go monitorować. W tym artykule pokazano, jak to zrobić w następujących środowiskach:

- Aparat AKS na platformie Azure i Azure Stack
- OpenShift w wersji 4 lub nowszej
- Platforma Kubernetes z obsługą usługi Azure Arc (wersja zapoznawcza)

## <a name="how-to-stop-monitoring-using-helm"></a>Jak zatrzymać monitorowanie za pomocą Helm

Poniższe kroki dotyczą następujących środowisk:

- Aparat AKS na platformie Azure i Azure Stack
- OpenShift w wersji 4 lub nowszej

1. Aby najpierw zidentyfikować Azure Monitor kontenerów Helm wersja wykresu zainstalowana w klastrze, uruchom następujące polecenie Helm.

    ```
    helm list
    ```

    Dane wyjściowe będą wyglądać następująco:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-Containers-Release-1* reprezentuje wersję wykresu helm dla Azure monitor dla kontenerów.

2. Aby usunąć wersję wykresu, uruchom następujące polecenie Helm.

    `helm delete <releaseName>`

    Przykład:

    `helm delete azmon-containers-release-1`

    Spowoduje to usunięcie wersji z klastra. Możesz sprawdzić, uruchamiając `helm list` polecenie:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Zmiana konfiguracji może potrwać kilka minut. Ponieważ usługa Helm śledzi wersje nawet po ich usunięciu, można przeprowadzić inspekcję historii klastra, a nawet cofnąć usunięcie wersji za pomocą programu `helm rollback` .

## <a name="how-to-stop-monitoring-on-arc-enabled-kubernetes"></a>Jak zatrzymać monitorowanie na Kubernetes z włączonym łukiem

### <a name="using-powershell"></a>Korzystanie z programu PowerShell

1. Pobierz i Zapisz skrypt do folderu lokalnego, który konfiguruje klaster przy użyciu dodatku do monitorowania za pomocą następujących poleceń:

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. Skonfiguruj `$azureArcClusterResourceId` zmienną ustawiając odpowiednie wartości dla `subscriptionId` `resourceGroupName` i `clusterName` reprezentującą identyfikator zasobu dla zasobu klastra Kubernetes z obsługą usługi Azure Arc.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Skonfiguruj `$kubeContext` zmienną za pomocą **kontekstu polecenia** klastra, uruchamiając polecenie `kubectl config get-contexts` . Jeśli chcesz użyć bieżącego kontekstu, ustaw wartość na `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Uruchom następujące polecenie, aby zatrzymać monitorowanie klastra.

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

#### <a name="using-service-principal"></a>Korzystanie z nazwy głównej usługi
Skrypt *disable-monitoring.ps1* używa logowania interakcyjnego urządzenia. Jeśli wolisz nieinteraktywną logowanie, możesz użyć istniejącej nazwy głównej usługi lub utworzyć nową, która ma wymagane uprawnienia zgodnie z opisem w sekcji [wymagania wstępne](container-insights-enable-arc-enabled-clusters.md#prerequisites). Aby można było użyć jednostki usługi, należy przekazać parametry $servicePrincipalClientId, $servicePrincipalClientSecret i $tenantId z wartościami jednostki usługi, która ma być używana do enable-monitoring.ps1 skryptu.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

Na przykład:

```powershell
\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId
```


### <a name="using-bash"></a>Korzystanie z bash

1. Pobierz i Zapisz skrypt do folderu lokalnego, który konfiguruje klaster przy użyciu dodatku do monitorowania za pomocą następujących poleceń:

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. Skonfiguruj `azureArcClusterResourceId` zmienną ustawiając odpowiednie wartości dla `subscriptionId` `resourceGroupName` i `clusterName` reprezentującą identyfikator zasobu dla zasobu klastra Kubernetes z obsługą usługi Azure Arc.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Skonfiguruj `kubeContext` zmienną za pomocą **kontekstu polecenia** klastra, uruchamiając polecenie `kubectl config get-contexts` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Aby zatrzymać monitorowanie klastra, dostępne są różne polecenia w oparciu o scenariusz wdrażania.

    Uruchom następujące polecenie, aby zatrzymać monitorowanie klastra przy użyciu bieżącego kontekstu.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Uruchom następujące polecenie, aby zatrzymać monitorowanie klastra przez określenie kontekstu

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

#### <a name="using-service-principal"></a>Korzystanie z nazwy głównej usługi
Skrypt bash *disable-monitoring.sh* używa logowania interakcyjnego urządzenia. Jeśli wolisz nieinteraktywną logowanie, możesz użyć istniejącej nazwy głównej usługi lub utworzyć nową, która ma wymagane uprawnienia zgodnie z opisem w sekcji [wymagania wstępne](container-insights-enable-arc-enabled-clusters.md#prerequisites). Aby można było użyć jednostki usługi, należy przekazać wartości--Client-ID,--Client-Secret i--ID dla jednostki usługi, która ma zostać użyta do *enable-monitoring.sh* bash skryptu.

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

Na przykład:

```bash
bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId
```

## <a name="next-steps"></a>Następne kroki

Jeśli obszar roboczy Log Analytics został utworzony tylko w celu obsługi monitorowania klastra i nie jest już wymagany, trzeba go ręcznie usunąć. Jeśli nie wiesz, jak usunąć obszar roboczy, zobacz temat [Usuwanie obszaru roboczego usługi Azure log Analytics](../platform/delete-workspace.md).
