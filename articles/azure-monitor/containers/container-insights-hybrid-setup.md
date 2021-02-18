---
title: Konfigurowanie hybrydowych klastrów Kubernetes za pomocą Azure Monitor dla kontenerów | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania Azure Monitor kontenerów do monitorowania klastrów Kubernetes hostowanych w Azure Stack lub innym środowisku.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 12901b1d2d7edd85fbe1650600856d09105c15b2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616280"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Konfigurowanie hybrydowych klastrów Kubernetes za pomocą Azure Monitor dla kontenerów

Azure Monitor dla kontenerów zapewnia rozbudowane środowisko monitorowania dla usługi Azure Kubernetes Service (AKS) i [aparatu AKS na platformie Azure](https://github.com/Azure/aks-engine), która jest klastrem z własnym zarządzaniem Kubernetes hostowanym na platformie Azure. W tym artykule opisano, jak włączyć monitorowanie klastrów Kubernetes hostowanych poza platformą Azure i osiągnąć podobne środowisko monitorowania.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

Następujące konfiguracje są oficjalnie obsługiwane w przypadku kontenerów Azure Monitor. Jeśli masz inną wersję programu Kubernetes i wersje systemu operacyjnego, Wyślij wiadomość e-mail do programu askcoin@microsoft.com .

- Wiejski

    - Kubernetes lokalnie
    - Aparat AKS na platformie Azure i Azure Stack. Aby uzyskać więcej informacji, zobacz [aparat AKS na Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)
    - [OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) w wersji 4 lub nowszej, lokalnie lub w innych środowiskach w chmurze.

- Wersje programu Kubernetes i zasady pomocy technicznej są takie same, jak wersje programu [AKS obsługiwane](../../aks/supported-kubernetes-versions.md).

- Obsługiwane są następujące środowiska uruchomieniowe kontenera: środowiska Docker, Moby i CRI zgodne z tym CRI-O i kontenera.

- Wydanie systemu operacyjnego Linux dla węzłów głównych i procesów roboczych jest obsługiwane: Ubuntu (18,04 LTS i 16,04 LTS) i Red Hat Enterprise Linux CoreOS 43,81.

- Obsługiwana kontrola dostępu: Kubernetes RBAC i bez kontroli RBAC

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy:

- [Obszar roboczy usługi Log Analytics](../platform/design-logs-deployment.md).

    Azure Monitor dla kontenerów obsługuje obszar roboczy Log Analytics w regionach wymienionych w produktach platformy Azure [według regionów](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Aby utworzyć własny obszar roboczy, można go utworzyć za pomocą [Azure Resource Manager](../samples/resource-manager-workspace.md), za pomocą [programu PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)lub [Azure Portal](../learn/quick-create-workspace.md).

    >[!NOTE]
    >Włącz monitorowanie wielu klastrów o tej samej nazwie klastra w tym samym obszarze roboczym Log Analytics nie jest obsługiwane. Nazwy klastrów muszą być unikatowe.
    >

- Musisz być członkiem **roli współautor log Analytics** , aby umożliwić monitorowanie kontenerów. Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego Log Analytics, zobacz [Zarządzanie dostępem do obszaru roboczego i danych dziennika](../platform/manage-access.md).

- Aby wyświetlić dane monitorowania, należy mieć rolę [*czytnika log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) w obszarze roboczym log Analytics, skonfigurowany przy użyciu Azure monitor dla kontenerów.

- [Helm klient](https://helm.sh/docs/using_helm/) Azure monitor do dołączania wykresu kontenerów dla określonego klastra Kubernetes.

- Następujące informacje o konfiguracji serwera proxy i zapory są wymagane przez kontener Log Analytics agenta dla systemu Linux w celu komunikowania się z Azure Monitor:

    |Zasób agenta|Porty |
    |------|---------|
    |*.ods.opinsights.azure.com |port 443 |
    |*.oms.opinsights.azure.com |port 443 |
    |*. dc.services.visualstudio.com |port 443 |

- Agent kontenera wymaga `cAdvisor secure port: 10250` , aby Kubelet lub `unsecure port :10255` był otwarty na wszystkich węzłach w klastrze w celu zbierania metryk wydajności. Zalecamy skonfigurowanie `secure port: 10250` Kubelet cAdvisor, jeśli nie został on jeszcze skonfigurowany.

- Agent kontenerów wymaga określenia następujących zmiennych środowiskowych w kontenerze w celu komunikowania się z usługą interfejsu API Kubernetes w klastrze w celu zbierania danych spisu — `KUBERNETES_SERVICE_HOST` i `KUBERNETES_PORT_443_TCP_PORT` .

>[!IMPORTANT]
>Minimalna wersja agenta obsługiwana w przypadku monitorowania hybrydowych klastrów Kubernetes to ciprod10182019 lub nowszy.

## <a name="enable-monitoring"></a>Włączanie monitorowania

Włączenie Azure Monitor kontenerów dla klastra hybrydowego Kubernetes składa się z następujących kroków w kolejności.

1. Skonfiguruj obszar roboczy Log Analytics przy użyciu rozwiązania datainsights.   

2. Włącz Azure Monitor dla HELM kontenerów z obszarem roboczym Log Analytics.

Dodatkowe informacje na temat monitorowania rozwiązań w Azure Monitor można znaleźć [tutaj](../../azure-monitor/insights/solutions.md).

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Jak dodać rozwiązanie Azure Monitor Containers

Rozwiązanie można wdrożyć za pomocą podanego szablonu Azure Resource Manager za pomocą polecenia cmdlet Azure PowerShell `New-AzResourceGroupDeployment` lub interfejsu wiersza polecenia platformy Azure.

Jeśli nie znasz koncepcji wdrażania zasobów przy użyciu szablonu, zobacz:

- [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)

- [Wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Jeśli zdecydujesz się na korzystanie z interfejsu wiersza polecenia platformy Azure, musisz najpierw zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Aby zidentyfikować swoją wersję, uruchom polecenie `az --version` . Jeśli konieczne jest zainstalowanie lub uaktualnienie interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Ta metoda obejmuje dwa szablony JSON. Jeden szablon określa konfigurację umożliwiającą monitorowanie, a druga zawiera wartości parametrów, które można skonfigurować, aby określić następujące elementy:

- **workspaceResourceId** — pełny identyfikator zasobu obszaru roboczego log Analytics.
- **workspaceRegion** — region, w którym jest tworzony obszar roboczy, który jest również określany jako **Lokalizacja** we właściwościach obszaru roboczego podczas wyświetlania z Azure Portal.

Aby najpierw określić pełny identyfikator zasobu Log Analytics obszaru roboczego wymagany dla `workspaceResourceId` wartości parametru w **containerSolutionParams.js** pliku, wykonaj następujące kroki, a następnie uruchom polecenie cmdlet programu PowerShell lub interfejs wiersza polecenia platformy Azure, aby dodać rozwiązanie.

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

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    W danych wyjściowych Znajdź nazwę obszaru roboczego, a następnie skopiuj pełny identyfikator zasobu tego Log Analytics obszaru roboczego pod **identyfikatorem** pola.

4. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. Zapisz ten plik jako containerSolution.jsw folderze lokalnym.

6. Wklej następującą składnię JSON do pliku:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Edytuj wartości dla **workspaceResourceId** przy użyciu wartości skopiowanej w kroku 3, a w przypadku **WorkspaceRegion** skopiuj wartość **region** po uruchomieniu polecenia wiersza poleceń platformy Azure [AZ Monitor Log-Analytics Workspace show](/cli/azure/monitor/log-analytics/workspace#az-monitor-log-analytics-workspace-list&preserve-view=true).

8. Zapisz ten plik jako containerSolutionParams.jsw folderze lokalnym.

9. Wszystko jest teraz gotowe do wdrożenia tego szablonu.

   - Aby wdrożyć program przy użyciu Azure PowerShell, użyj następujących poleceń w folderze zawierającym szablon:

       ```powershell
       # configure and login to the cloud of Log Analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of Log Analytics workspace>
       ```

       ```powershell
       # execute deployment command to add Container Insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of Log Analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik:

       ```powershell
       provisioningState       : Succeeded
       ```

   - Aby przeprowadzić wdrożenie przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenia:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Po włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra.

## <a name="install-the-helm-chart"></a>Instalowanie wykresu HELM

W tej części zainstalujesz agenta kontenerowego dla Azure Monitor kontenerów. Przed kontynuowaniem należy określić identyfikator obszaru roboczego wymagany dla tego `omsagent.secret.wsid` parametru i klucz podstawowy wymagany dla tego `omsagent.secret.key` parametru. Możesz zidentyfikować te informacje, wykonując poniższe kroki, a następnie uruchom polecenia, aby zainstalować agenta za pomocą wykresu HELM.

1. Uruchom następujące polecenie, aby zidentyfikować identyfikator obszaru roboczego:

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    W danych wyjściowych Znajdź nazwę obszaru roboczego pod **nazwą** pola, a następnie skopiuj identyfikator obszaru roboczego tego log Analytics obszaru roboczego w polu **IDKlienta**.

2. Uruchom następujące polecenie, aby zidentyfikować klucz podstawowy obszaru roboczego:

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    W danych wyjściowych Znajdź klucz podstawowy w polu **primarySharedKey**, a następnie skopiuj wartość.

>[!NOTE]
>Poniższe polecenia dotyczą tylko programu Helm w wersji 2. Użycie `--name` parametru nie jest stosowane w przypadku Helm w wersji 3. 

>[!NOTE]
>Jeśli klaster Kubernetes komunikuje się za pomocą serwera proxy, należy skonfigurować parametr `omsagent.proxy` przy użyciu adresu URL serwera proxy. Jeśli klaster nie komunikuje się za pomocą serwera proxy, nie trzeba określać tego parametru. Aby uzyskać więcej informacji, zobacz [konfigurowanie punktu końcowego serwera proxy](#configure-proxy-endpoint) w dalszej części tego artykułu.

3. Dodaj repozytorium wykresów platformy Azure do listy lokalnej, uruchamiając następujące polecenie:

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. Zainstaluj wykres, uruchamiając następujące polecenie:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Jeśli obszar roboczy Log Analytics jest na platformie Azure w Chinach, uruchom następujące polecenie:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Jeśli obszar roboczy Log Analytics jest w instytucji rządowych USA platformy Azure, uruchom następujące polecenie:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>Włączanie wykresu Helm przy użyciu modelu interfejsu API

Można określić dodatek w pliku JSON specyfikacji klastra aparatu AKS, nazywany również modelem interfejsu API. W tym dodatku należy udostępnić zakodowane w formacie base64 wersje systemu `WorkspaceGUID` i `WorkspaceKey` log Analytics obszarze roboczym, w którym są przechowywane zebrane dane monitorowania. `WorkspaceGUID` `WorkspaceKey` W poprzedniej sekcji znajdują się instrukcje 1 i 2.

Definicje obsługiwanych interfejsów API dla klastra Azure Stack Hub można znaleźć w tym przykładzie — [kubernetes-container-monitoring_existing_workspace_id_and_key.json](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). Znajdź właściwość **Dodatki** w **kubernetesConfig**:

```json
"orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Id in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="configure-agent-data-collection"></a>Konfigurowanie zbierania danych agenta

W przypadku używania wykresu w wersji 1.0.0 ustawienia zbierania danych agentów są kontrolowane przez ConfigMap. W [tym miejscu](container-insights-agent-config.md)zapoznaj się z dokumentacją dotyczącą ustawień zbierania danych agentów.

Po pomyślnym wdrożeniu wykresu można przejrzeć dane dla hybrydowego klastra Kubernetes w Azure Monitor kontenerów z Azure Portal.  

>[!NOTE]
>Opóźnienie pozyskiwania trwa od pięciu do dziesięciu minut od agenta do zatwierdzenia w obszarze roboczym usługi Azure Log Analytics. Stan klastra pokazuje wartość **Brak danych lub nie** jest **znana** do momentu udostępnienia wszystkich wymaganych danych monitorowania w Azure monitor.

## <a name="configure-proxy-endpoint"></a>Skonfiguruj punkt końcowy serwera proxy

Począwszy od wersji wykresu 2.7.1, wykres będzie obsługiwał Określanie punktu końcowego proxy z `omsagent.proxy` parametrem wykresu. Dzięki temu można komunikować się z serwerem proxy. Komunikacja między Azure Monitor dla agenta kontenerów i Azure Monitor może być serwerem proxy HTTP lub HTTPS, a uwierzytelnianiem anonimowym i podstawowym (nazwa użytkownika/hasło) są obsługiwane.

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

Na przykład: `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

W przypadku określenia protokołu jako **http** żądania HTTP są tworzone przy użyciu bezpiecznego połączenia SSL/TLS. Serwer proxy musi obsługiwać protokoły SSL/TLS.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wystąpi błąd podczas próby włączenia monitorowania dla hybrydowego klastra Kubernetes, skopiuj skrypt programu PowerShell [TroubleshootError_nonAzureK8s.ps1](https://aka.ms/troubleshoot-non-azure-k8s) i Zapisz go w folderze na komputerze. Ten skrypt zapewnia pomoc w wykrywaniu i usuwaniu napotkanych problemów. Te problemy zaprojektowano w celu wykrycia i wykonania korekty są następujące:

- Określony obszar roboczy Log Analytics jest prawidłowy
- W obszarze roboczym Log Analytics jest konfigurowany Azure Monitor rozwiązanie kontenerów. W przeciwnym razie Skonfiguruj obszar roboczy.
- OmsAgent REPLICASET są uruchomione
- OmsAgent elementu daemonset są uruchomione
- Usługa OmsAgent Health jest uruchomiona
- Identyfikator i klucz obszaru roboczego Log Analytics skonfigurowany na kontenerze kontenera jest zgodny z obszarem roboczym, w którym skonfigurowano szczegółowe informacje.
- Sprawdź, czy wszystkie węzły procesów roboczych systemu Linux mają `kubernetes.io/role=agent` etykietę do harmonogramu RS. Jeśli nie istnieje, Dodaj ją.
- Sprawdza poprawność `cAdvisor secure port:10250` lub `unsecure port: 10255` jest otwarta na wszystkich węzłach w klastrze.

Aby wykonać z Azure PowerShell, użyj następujących poleceń w folderze, który zawiera skrypt:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Następne kroki

Po włączeniu monitorowania w celu zbierania informacji o kondycji i użyciu zasobów hybrydowego klastra Kubernetes oraz obciążeń na nich działających należy dowiedzieć się, [jak używać](container-insights-analyze.md) Azure monitor kontenerów.
