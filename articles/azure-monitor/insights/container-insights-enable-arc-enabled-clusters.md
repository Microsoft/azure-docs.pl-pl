---
title: Konfigurowanie klastra Kubernetes z obsługą usługi Azure ARC przy użyciu Azure Monitor dla kontenerów | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania monitorowania za pomocą Azure Monitor dla kontenerów w klastrach Kubernetes z obsługą usługi Azure Arc.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 79a534e4f37fb0154115e43402f031752a603ccb
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91620294"
---
# <a name="enable-monitoring-of-azure-arc-enabled-kubernetes-cluster"></a>Włącz monitorowanie klastra Kubernetes z obsługą usługi Azure Arc

Azure Monitor dla kontenerów zapewnia rozbudowane środowisko monitorowania dla klastrów usługi Azure Kubernetes Service (AKS) i AKS Engine. W tym artykule opisano, jak włączyć monitorowanie klastrów Kubernetes hostowanych poza platformą Azure, które są włączone przy użyciu usługi Azure ARC, aby osiągnąć podobne środowisko monitorowania.

Azure Monitor dla kontenerów można włączyć dla co najmniej jednego istniejącego wdrożenia Kubernetes przy użyciu skryptu PowerShell lub bash.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

Azure Monitor dla kontenerów obsługuje monitorowanie Kubernetes usługi Azure ARC (wersja zapoznawcza) zgodnie z opisem w artykule [Omówienie](container-insights-overview.md) , z wyjątkiem następujących funkcji:

- Dane dynamiczne (wersja zapoznawcza)

Następujące elementy są oficjalnie obsługiwane w Azure Monitor dla kontenerów:

- Wersje programu Kubernetes i zasady pomocy technicznej są takie same, jak wersje programu [AKS obsługiwane](../../aks/supported-kubernetes-versions.md).

- Obsługiwane są następujące środowiska uruchomieniowe kontenera: środowiska Docker, Moby i CRI zgodne z tym CRI-O i kontenera.

- Wydanie systemu operacyjnego Linux dla węzłów głównych i procesów roboczych jest obsługiwane: Ubuntu (18,04 LTS i 16,04 LTS).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy:

- Obszar roboczy usługi Log Analytics.

    Azure Monitor dla kontenerów obsługuje obszar roboczy Log Analytics w regionach wymienionych w produktach platformy Azure [według regionów](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Aby utworzyć własny obszar roboczy, można go utworzyć za pomocą [Azure Resource Manager](../platform/template-workspace-configuration.md), za pomocą [programu PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)lub [Azure Portal](../learn/quick-create-workspace.md).

- Aby włączyć i uzyskać dostęp do funkcji w Azure Monitor dla kontenerów, musisz być członkiem roli *współautor* platformy Azure w ramach subskrypcji platformy Azure i członkiem roli [*współautor Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) obszaru roboczego log Analytics skonfigurowanym przy użyciu Azure monitor dla kontenerów.

- Jesteś członkiem roli [współautor](../../role-based-access-control/built-in-roles.md#contributor) w zasobie klastra Azure Arc.

- Aby wyświetlić dane monitorowania, należy być członkiem uprawnienia roli [*czytelnik log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) z obszarem roboczym log Analytics skonfigurowanym za pomocą Azure monitor dla kontenerów.

- [Helm klient](https://helm.sh/docs/using_helm/) Azure monitor do dołączania wykresu kontenerów dla określonego klastra Kubernetes.

- Następujące informacje o konfiguracji serwera proxy i zapory są wymagane przez kontener Log Analytics agenta dla systemu Linux w celu komunikowania się z Azure Monitor:

    |Zasób agenta|Porty |
    |------|---------|
    |`*.ods.opinsights.azure.com` |port 443 |
    |`*.oms.opinsights.azure.com` |port 443 |
    |`*.dc.services.visualstudio.com` |port 443 |

- Agent kontenera wymaga `cAdvisor secure port: 10250` , aby Kubelet lub `unsecure port :10255` był otwarty na wszystkich węzłach w klastrze w celu zbierania metryk wydajności. Zalecamy skonfigurowanie `secure port: 10250` Kubelet cAdvisor, jeśli nie został on jeszcze skonfigurowany.

- Agent kontenerów wymaga określenia następujących zmiennych środowiskowych w kontenerze w celu komunikowania się z usługą interfejsu API Kubernetes w klastrze w celu zbierania danych spisu — `KUBERNETES_SERVICE_HOST` i `KUBERNETES_PORT_443_TCP_PORT` .

    >[!IMPORTANT]
    >Minimalna wersja agenta obsługiwana na potrzeby monitorowania klastrów Kubernetes z włączoną funkcją Arc to ciprod04162020 lub nowsza.

- Program [PowerShell Core](/powershell/scripting/install/installing-powershell?view=powershell-6&preserve-view=true) jest wymagany w przypadku włączenia monitorowania przy użyciu metody skryptu programu PowerShell.

- [Bash w wersji 4](https://www.gnu.org/software/bash/) jest wymagana, jeśli monitor zostanie włączony przy użyciu metody skryptu bash.

## <a name="identify-workspace-resource-id"></a>Identyfikator zasobu obszaru roboczego

Aby włączyć monitorowanie klastra przy użyciu pobranego wcześniej skryptu PowerShell lub bash i zintegrować go z istniejącym obszarem roboczym Log Analytics, wykonaj następujące kroki, aby najpierw zidentyfikować pełny identyfikator zasobu Log Analytics obszaru roboczego. Jest to wymagane dla `workspaceResourceId` parametru po uruchomieniu polecenia, aby włączyć dodatek monitorowania względem określonego obszaru roboczego. Jeśli nie masz obszaru roboczego do określenia, możesz pominąć łącznie z `workspaceResourceId` parametrem i pozwolić skryptowi na utworzenie nowego obszaru roboczego.

1. Wyświetl listę wszystkich subskrypcji, do których masz dostęp, za pomocą następującego polecenia:

    ```azurecli
    az account list --all -o table
    ```

    Dane wyjściowe będą wyglądać następująco:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    Skopiuj wartość identyfikatora **subskrypcji**.

2. Przejdź do subskrypcji, w której znajduje się obszar roboczy Log Analytics, przy użyciu następującego polecenia:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Poniższy przykład wyświetla listę obszarów roboczych w Twoich subskrypcjach w domyślnym formacie JSON.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    W danych wyjściowych Znajdź nazwę obszaru roboczego, a następnie skopiuj pełny identyfikator zasobu tego Log Analytics obszaru roboczego pod **identyfikatorem**pola.

## <a name="enable-monitoring-using-powershell"></a>Włączanie monitorowania przy użyciu programu PowerShell

1. Pobierz i Zapisz skrypt do folderu lokalnego, który konfiguruje klaster przy użyciu dodatku do monitorowania za pomocą następujących poleceń:

    ```powershell
    Invoke-WebRequest https://aka.ms/enable-monitoring-powershell-script -OutFile enable-monitoring.ps1
    ```

2. Skonfiguruj `$azureArcClusterResourceId` zmienną ustawiając odpowiednie wartości dla `subscriptionId` `resourceGroupName` i `clusterName` reprezentującą identyfikator zasobu dla zasobu klastra Kubernetes z obsługą usługi Azure Arc.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Skonfiguruj `$kubeContext` zmienną za pomocą **kontekstu polecenia** klastra, uruchamiając polecenie `kubectl config get-contexts` . Jeśli chcesz użyć bieżącego kontekstu, ustaw wartość na `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Jeśli chcesz użyć istniejącego obszaru roboczego Log Analytics Azure Monitor, skonfiguruj zmienną `$logAnalyticsWorkspaceResourceId` z odpowiadającą jej wartością reprezentującą identyfikator zasobu obszaru roboczego. W przeciwnym razie Ustaw zmienną na, `""` a skrypt utworzy domyślny obszar roboczy w domyślnej grupie zasobów subskrypcji klastra, jeśli jeszcze nie istnieje w regionie. Utworzony domyślny obszar roboczy jest podobny do formatu *DefaultWorkspace- \<SubscriptionID> - \<Region> *.

    ```powershell
    $logAnalyticsWorkspaceResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Jeśli klaster Kubernetes z obsługą Arc komunikuje się za pomocą serwera proxy, należy skonfigurować zmienną `$proxyEndpoint` przy użyciu adresu URL serwera proxy. Jeśli klaster nie komunikuje się za pomocą serwera proxy, można ustawić wartość na `""` .  Aby uzyskać więcej informacji, zobacz [konfigurowanie punktu końcowego serwera proxy](#configure-proxy-endpoint) w dalszej części tego artykułu.

6. Uruchom następujące polecenie, aby włączyć monitorowanie.

    ```powershell
    .\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
    ```

Po włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra.

### <a name="using-service-principal"></a>Korzystanie z nazwy głównej usługi
Skrypt *enable-monitoring.ps1* używa logowania interakcyjnego urządzenia. Jeśli wolisz nieinteraktywną logowanie, możesz użyć istniejącej nazwy głównej usługi lub utworzyć nową, która ma wymagane uprawnienia zgodnie z opisem w sekcji [wymagania wstępne](#prerequisites). Aby można było użyć jednostki usługi, należy przekazać parametry $servicePrincipalClientId, $servicePrincipalClientSecret i $tenantId z wartościami jednostki usługi, która ma być używana do *enable-monitoring.ps1* skryptu.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"
```

Przypisanie roli poniżej ma zastosowanie tylko w przypadku korzystania z istniejącego obszaru roboczego Log Analytics w innej subskrypcji platformy Azure niż zasób połączonego klastra usługi Arc K8s.

```powershell
$logAnalyticsWorkspaceResourceId = "<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
New-AzRoleAssignment -RoleDefinitionName 'Log Analytics Contributor'  -ObjectId $servicePrincipal.Id -Scope  $logAnalyticsWorkspaceResourceId

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

Na przykład:

```powershell
.\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
```



## <a name="enable-using-bash-script"></a>Włączanie przy użyciu skryptu bash

Wykonaj następujące kroki, aby włączyć monitorowanie za pomocą podanego skryptu bash.

1. Pobierz i Zapisz skrypt do folderu lokalnego, który konfiguruje klaster przy użyciu dodatku do monitorowania za pomocą następujących poleceń:

    ```bash
    curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script
    ```

2. Skonfiguruj `azureArcClusterResourceId` zmienną ustawiając odpowiednie wartości dla `subscriptionId` `resourceGroupName` i `clusterName` reprezentującą identyfikator zasobu dla zasobu klastra Kubernetes z obsługą usługi Azure Arc.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Skonfiguruj `kubeContext` zmienną za pomocą **kontekstu polecenia** klastra, uruchamiając polecenie `kubectl config get-contexts` . Jeśli chcesz użyć bieżącego kontekstu, ustaw wartość na `""` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Jeśli chcesz użyć istniejącego obszaru roboczego Log Analytics Azure Monitor, skonfiguruj zmienną `logAnalyticsWorkspaceResourceId` z odpowiadającą jej wartością reprezentującą identyfikator zasobu obszaru roboczego. W przeciwnym razie Ustaw zmienną na, `""` a skrypt utworzy domyślny obszar roboczy w domyślnej grupie zasobów subskrypcji klastra, jeśli jeszcze nie istnieje w regionie. Utworzony domyślny obszar roboczy jest podobny do formatu *DefaultWorkspace- \<SubscriptionID> - \<Region> *.

    ```bash
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Jeśli klaster Kubernetes z obsługą Arc komunikuje się za pomocą serwera proxy, należy skonfigurować zmienną `proxyEndpoint` przy użyciu adresu URL serwera proxy. Jeśli klaster nie komunikuje się za pomocą serwera proxy, można ustawić wartość na `""` . Aby uzyskać więcej informacji, zobacz [konfigurowanie punktu końcowego serwera proxy](#configure-proxy-endpoint) w dalszej części tego artykułu.

6. Aby włączyć monitorowanie w klastrze, dostępne są różne polecenia w oparciu o scenariusz wdrażania.

    Uruchom następujące polecenie, aby włączyć monitorowanie z opcjami domyślnymi, takimi jak korzystanie z bieżącego poleceniaego kontekstu, tworzenie domyślnego obszaru roboczego Log Analytics i bez określania serwera proxy:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Uruchom następujące polecenie, aby utworzyć domyślny obszar roboczy Log Analytics i bez określania serwera proxy:

    ```bash
   bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

    Uruchom następujące polecenie, aby użyć istniejącego obszaru roboczego Log Analytics i bez określania serwera proxy:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId
    ```

    Uruchom następujące polecenie, aby użyć istniejącego obszaru roboczego Log Analytics i określić serwer proxy:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
    ```

Po włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra.

### <a name="using-service-principal"></a>Korzystanie z nazwy głównej usługi
Skrypt bash *enable-monitoring.sh* używa logowania interakcyjnego urządzenia. Jeśli wolisz nieinteraktywną logowanie, możesz użyć istniejącej nazwy głównej usługi lub utworzyć nową, która ma wymagane uprawnienia zgodnie z opisem w sekcji [wymagania wstępne](#prerequisites). Aby można było użyć jednostki usługi, należy przekazać wartości--Client-ID,--Client-Secret i--ID dla jednostki usługi, która ma zostać użyta do *enable-monitoring.sh* bash skryptu.

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')
```

Przypisanie roli poniżej ma zastosowanie tylko w przypadku korzystania z istniejącego obszaru roboczego Log Analytics w innej subskrypcji platformy Azure niż zasób połączonego klastra usługi Arc K8s.

```bash
logAnalyticsWorkspaceResourceId="<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
az role assignment create --role 'Log Analytics Contributor' --assignee $servicePrincipalClientId --scope $logAnalyticsWorkspaceResourceId

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

Na przykład:

```bash
bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
```

## <a name="configure-proxy-endpoint"></a>Skonfiguruj punkt końcowy serwera proxy

Za pomocą agenta kontenerowego dla Azure Monitor kontenerów można skonfigurować punkt końcowy serwera proxy, aby umożliwić mu komunikowanie się za pomocą serwera proxy. Komunikacja między agentem kontenera a Azure Monitor może być serwerem proxy HTTP lub HTTPS, a uwierzytelnianiem anonimowym i podstawowym (nazwa użytkownika/hasło) są obsługiwane.

Wartość konfiguracji serwera proxy ma następującą składnię: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Jeśli serwer proxy nie wymaga uwierzytelniania, nadal trzeba określić nazwę użytkownika/hasło psuedo. Może to być dowolna nazwa użytkownika lub hasło.

|Właściwość| Opis |
|--------|-------------|
|Protokół | http lub https |
|użytkownik | Opcjonalna nazwa użytkownika dla uwierzytelniania serwera proxy |
|hasło | Opcjonalne hasło do uwierzytelniania serwera proxy |
|proxyhost | Adres lub nazwa FQDN serwera proxy |
|port | Opcjonalny numer portu serwera proxy |

Na przykład: `http://user01:password@proxy01.contoso.com:3128`

W przypadku określenia protokołu jako **http**żądania HTTP są tworzone przy użyciu bezpiecznego połączenia SSL/TLS. Serwer proxy musi obsługiwać protokoły SSL/TLS.

### <a name="configure-using-powershell"></a>Konfigurowanie przy użyciu programu PowerShell

Określ nazwę użytkownika i hasło, adres IP lub nazwę FQDN i numer portu serwera proxy. Na przykład:

```powershell
$proxyEndpoint = https://<user>:<password>@<proxyhost>:<port>
```

### <a name="configure-using-bash"></a>Konfigurowanie przy użyciu bash

Określ nazwę użytkownika i hasło, adres IP lub nazwę FQDN i numer portu serwera proxy. Na przykład:

```bash
export proxyEndpoint=https://<user>:<password>@<proxyhost>:<port>
```

## <a name="next-steps"></a>Następne kroki

- Po włączeniu monitorowania w celu zbierania informacji o kondycji i użyciu zasobów w klastrze Kubernetes z włączonym łukiem i obciążeniami, Dowiedz się, [jak używać](container-insights-analyze.md) Azure monitor do kontenerów.

- Domyślnie agent kontenera zbiera dzienniki kontenerów stdout/stderr wszystkich kontenerów uruchomionych we wszystkich przestrzeniach nazw z wyjątkiem polecenia-system. Aby skonfigurować zbieranie dzienników kontenerów specyficzne dla konkretnej przestrzeni nazw lub przestrzeni nazw, przejrzyj [konfigurację agenta usługi Container Insights](container-insights-agent-config.md) , aby skonfigurować żądane ustawienia zbierania danych do pliku konfiguracji ConfigMap.

- Aby wyrównać odpadków i analizować metryki Prometheus z klastra, zapoznaj się z tematem [Konfigurowanie wycinków metryk Prometheus](container-insights-prometheus-integration.md)

- Aby dowiedzieć się, jak zatrzymać monitorowanie klastra Kubernetes z włączonym łukiem przy użyciu Azure Monitor dla kontenerów, zobacz [Jak zatrzymać monitorowanie klastra hybrydowego](container-insights-optout-hybrid.md#how-to-stop-monitoring-on-arc-enabled-kubernetes).
