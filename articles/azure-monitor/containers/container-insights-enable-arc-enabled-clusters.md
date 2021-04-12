---
title: Monitorowanie klastrów Kubernetes z obsługą usługi Azure Arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Zbieraj metryki i dzienniki klastrów Kubernetes z obsługą usługi Azure ARC przy użyciu Azure Monitor
ms.openlocfilehash: 0a983f6d7032310d02d35e713482de942bfbfd70
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443854"
---
# <a name="azure-monitor-container-insights-for-azure-arc-enabled-kubernetes-clusters"></a>Azure Monitor szczegółowych informacji o kontenerach dla klastrów Kubernetes z obsługą usługi Azure Arc

Usługa [Azure monitor Insights](container-insights-overview.md) oferuje rozbudowane środowisko monitorowania dla klastrów Kubernetes z włączoną funkcją Azure Arc.

[!INCLUDE [preview features note](../../azure-arc/kubernetes/includes/preview/preview-callout.md)]

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

- Usługa Azure Monitor Insights obsługuje monitorowanie Kubernetes (wersja zapoznawcza) usługi Azure ARC, zgodnie z opisem w artykule [Przegląd](container-insights-overview.md) , z wyjątkiem funkcji Live Data (wersja zapoznawcza). Ponadto użytkownicy nie muszą mieć uprawnień [właściciela](../../role-based-access-control/built-in-roles.md#owner) , aby [włączyć metryki](container-insights-update-metrics.md)
- `Docker`, `Moby` i CRI zgodne środowiska uruchomieniowe kontenerów, takie jak `CRI-O` i `containerd` .
- Obsługiwane są wychodzące serwery proxy bez uwierzytelniania i wychodzącego serwera proxy z uwierzytelnianiem podstawowym. Wychodzący serwer proxy, który oczekuje zaufanych certyfikatów, nie jest obecnie obsługiwany.

## <a name="prerequisites"></a>Wymagania wstępne

- Zostały spełnione wymagania wstępne wymienione w [dokumentacji ogólnych rozszerzeń klastra](../../azure-arc/kubernetes/extensions.md#prerequisites).
- Obszar roboczy Log Analytics: usługa Azure Monitor Insights obsługuje obszar roboczy Log Analytics w regionach wymienionych na [stronie produktów platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Możesz utworzyć własny obszar roboczy za poorednictwem [Azure Resource Manager](../logs/resource-manager-workspace.md), [programu PowerShell](../logs/powershell-sample-create-workspace.md)lub [Azure Portal](../logs/quick-create-workspace.md).
- Musisz mieć przypisanie roli [współautor](../../role-based-access-control/built-in-roles.md#contributor) w subskrypcji platformy Azure zawierającej zasób Kubernetes z włączoną funkcją Azure Arc. Jeśli obszar roboczy Log Analytics należy do innej subskrypcji, w obszarze roboczym Log Analytics jest wymagany Log Analytics przypisanie roli [współautor](../logs/manage-access.md#manage-access-using-azure-permissions) .
- Aby wyświetlić dane monitorowania, należy mieć Log Analytics przypisanie roli [czytnika](../logs/manage-access.md#manage-access-using-azure-permissions) do log Analytics obszaru roboczego.
- Następujące punkty końcowe muszą zostać włączone dla dostępu wychodzącego oprócz tych wymienionych w obszarze [łączenie klastra Kubernetes z usługą Azure Arc](../../azure-arc/kubernetes/quickstart-connect-cluster.md#meet-network-requirements).

    | Punkt końcowy | Port |
    |----------|------|
    | `*.ods.opinsights.azure.com` | 443 |
    | `*.oms.opinsights.azure.com` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    | `*.monitoring.azure.com` | 443 |
    | `login.microsoftonline.com` | 443 |

    Jeśli włączony jest Kubernetes zasobów platformy Azure dla instytucji rządowych USA, dla dostępu wychodzącego należy włączyć następujące punkty końcowe:

    | Punkt końcowy | Port |
    |----------|------|
    | `*.ods.opinsights.azure.us` | 443 |
    | `*.oms.opinsights.azure.us` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    

- Jeśli wcześniej wdrożono Azure Monitor usługi Container Insights w tym klastrze przy użyciu skryptu bez rozszerzeń klastra, postępuj zgodnie z instrukcjami wymienionymi w [tym miejscu](container-insights-optout-hybrid.md) , aby usunąć ten wykres Helm. Następnie można kontynuować tworzenie wystąpienia rozszerzenia klastra na potrzeby Azure Monitor informacji o kontenerze.

    >[!NOTE]
    > Oparta na skrypcie wersja wdrożenia usługi Azure Monitor Container Insights (wersja zapoznawcza) jest zastępowana przez [rozszerzenie klastra](../../azure-arc/kubernetes/extensions.md) w postaci wdrożenia. Azure Monitor wdrożone wcześniej za pośrednictwem skryptu są obsługiwane tylko w czerwcu 2021 i dlatego zaleca się migrację do rozszerzenia klastra w postaci wdrożenia w najkrótszym czasie.

### <a name="identify-workspace-resource-id"></a>Identyfikator zasobu obszaru roboczego

Uruchom następujące polecenia, aby zlokalizować pełny Azure Resource Manager identyfikator obszaru roboczego Log Analytics. 

1. Wyświetl listę wszystkich subskrypcji, do których masz dostęp, za pomocą następującego polecenia:

    ```azurecli
    az account list --all -o table
    ```

2. Przejdź do subskrypcji, w której znajduje się obszar roboczy Log Analytics, przy użyciu następującego polecenia:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Poniższy przykład wyświetla listę obszarów roboczych w Twoich subskrypcjach w domyślnym formacie JSON.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    W danych wyjściowych Znajdź nazwę obszaru roboczego zainteresowania. `id`Pole reprezentujące Azure Resource Manager identyfikator tego log Analytics obszaru roboczego.

    >[!TIP]
    > `id`Można to również znaleźć w bloku *Przegląd* obszaru roboczego Log Analytics za pomocą Azure Portal.

## <a name="create-extension-instance-using-azure-cli"></a>Tworzenie wystąpienia rozszerzenia przy użyciu interfejsu wiersza polecenia platformy Azure

### <a name="option-1---with-default-values"></a>Opcja 1 — z wartościami domyślnymi

Ta opcja używa następujących wartości domyślnych:

- Tworzy lub używa istniejącego domyślnego obszaru roboczego usługi log Analytics odpowiadającego regionowi klastra.
- Funkcja autouaktualnienia jest włączona dla rozszerzenia klastra Azure Monitor

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers
```

### <a name="option-2---with-existing-azure-log-analytics-workspace"></a>Opcja 2 — z istniejącym obszarem roboczym usługi Azure Log Analytics

Możesz użyć istniejącego obszaru roboczego usługi Azure Log Analytics w dowolnej subskrypcji, w której masz *współautora* lub większą liczbę przypisań ról.

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings logAnalyticsWorkspaceResourceID=<armResourceIdOfExistingWorkspace>
```

### <a name="option-3---with-advanced-configuration"></a>Opcja 3 — z konfiguracją zaawansowaną

Jeśli chcesz dostosować domyślne żądania zasobów i limity, możesz użyć ustawień zaawansowanych konfiguracji:

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings  omsagent.resources.daemonset.limits.cpu=150m omsagent.resources.daemonset.limits.memory=600Mi omsagent.resources.deployment.limits.cpu=1 omsagent.resources.deployment.limits.memory=750Mi
```

Wyewidencjonuj [sekcję żądania zasobów i limity wykresu Helm](https://github.com/helm/charts/blob/master/incubator/azuremonitor-containers/values.yaml) , aby znaleźć dostępne ustawienia konfiguracji.

### <a name="option-4---on-azure-stack-edge"></a>Opcja 4 — na Azure Stack Edge

Jeśli klaster Kubernetes z włączoną funkcją Azure Arc jest na Azure Stack Edge, należy użyć niestandardowej ścieżki instalacji `/home/data/docker` .

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings omsagent.logsettings.custommountpath=/home/data/docker
```

>[!NOTE]
> Jeśli jawnie określisz wersję rozszerzenia, która ma zostać zainstalowana w poleceniu Create, a następnie upewnij się, że określona wersja jest >= 2.8.2.

## <a name="create-extension-instance-using-azure-portal"></a>Utwórz wystąpienie rozszerzenia przy użyciu Azure Portal

>[!IMPORTANT]
>  W przypadku wdrażania Azure Monitor w klastrze Kubernetes działającym na górze Azure Stack Edge należy zastosować opcję interfejsu wiersza polecenia platformy Azure zamiast opcji Azure Portal, ponieważ dla tych klastrów należy ustawić niestandardową ścieżkę instalacji.    

### <a name="onboarding-from-the-azure-arc-enabled-kubernetes-resource-blade"></a>Dołączanie z bloku zasobów Kubernetes z funkcją Azure Arc

1. W Azure Portal wybierz klaster Kubernetes z włączonym Łukem, który ma być monitorowany.

2. Wybierz element "Insights (wersja zapoznawcza)" w sekcji "monitorowanie" bloku zasób.

3. Na stronie Dołączanie wybierz przycisk "Konfiguruj Azure Monitor"

4. Teraz możesz wybrać [obszar roboczy log Analytics](../logs/quick-create-workspace.md) , do którego mają być wysyłane metryki i do których dane mają być rejestrowane.

5. Wybierz przycisk "Konfiguruj", aby wdrożyć rozszerzenie klastra Azure Monitor usługi Application Insights.

### <a name="onboarding-from-azure-monitor-blade"></a>Dołączanie z bloku Azure Monitor

1. W Azure Portal przejdź do bloku "Monitor" i wybierz opcję "Containers" w menu "Insights".

2. Wybierz kartę "niemonitorowane klastry", aby wyświetlić klastry Kubernetes z obsługą usługi Azure ARC, w których można włączyć monitorowanie.

3. Kliknij link "Włącz" obok klastra, dla którego chcesz włączyć monitorowanie.

4. Wybierz obszar roboczy Log Analytics i wybierz przycisk "Konfiguruj", aby kontynuować.

## <a name="create-extension-instance-using-azure-resource-manager"></a>Utwórz wystąpienie rozszerzenia przy użyciu Azure Resource Manager

1. Pobierz szablon Azure Resource Manager i parametr:

    ```console
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template -o arc-k8s-azmon-extension-arm-template.json
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template-params -o  arc-k8s-azmon-extension-arm-template-params.json
    ```

2. Zaktualizuj wartości parametrów w arc-k8s-azmon-extension-arm-template-params.jspliku. W przypadku chmury publicznej Azure należy `opinsights.azure.com` użyć wartości workspaceDomain.

3. Wdróż szablon, aby utworzyć rozszerzenie usługi Azure Monitor Insights 

    ```console
    az login
    az account set --subscription "Subscription Name"
    az deployment group create --resource-group <resource-group> --template-file ./arc-k8s-azmon-extension-arm-template.json --parameters @./arc-k8s-azmon-extension-arm-template-params.json
    ```

## <a name="delete-extension-instance"></a>Usuń wystąpienie rozszerzenia

Następujące polecenie usuwa tylko wystąpienie rozszerzenia, ale nie usuwa obszaru roboczego Log Analytics. Dane w ramach zasobu Log Analytics pozostaną bez zmian.

```bash
az k8s-extension delete --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group>
```

## <a name="disconnected-cluster"></a>Odłączony klaster
Jeśli klaster zostanie odłączony od platformy Azure dla > 48 godzin, wykres zasobów platformy Azure nie będzie zawierał informacji o klastrze. W rezultacie w bloku Insights mogą być wyświetlane nieprawidłowe informacje o stanie klastra.

## <a name="next-steps"></a>Następne kroki

- Po włączeniu monitorowania w celu zbierania informacji o kondycji i użyciu zasobów w klastrze Kubernetes z włączonym łukiem i obciążeniami, Dowiedz się, [jak używać](container-insights-analyze.md) usługi Container Insights.

- Domyślnie agent kontenera zbiera dzienniki kontenerów stdout/stderr wszystkich kontenerów uruchomionych we wszystkich przestrzeniach nazw z wyjątkiem polecenia-system. Aby skonfigurować zbieranie dzienników kontenerów specyficzne dla konkretnej przestrzeni nazw lub przestrzeni nazw, przejrzyj [konfigurację agenta usługi Container Insights](container-insights-agent-config.md) , aby skonfigurować żądane ustawienia zbierania danych do pliku konfiguracji ConfigMap.

- Aby wyrównać odpadków i analizować metryki Prometheus z klastra, zapoznaj się z tematem [Konfigurowanie wycinków metryk Prometheus](container-insights-prometheus-integration.md)
