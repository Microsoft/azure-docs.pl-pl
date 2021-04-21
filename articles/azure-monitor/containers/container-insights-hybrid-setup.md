---
title: Konfigurowanie hybrydowych klastrów Kubernetes za pomocą usługi Container Insights | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania usługi Container Insights do monitorowania klastrów Kubernetes hostowanych Azure Stack lub w innym środowisku.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 90a4c14397df8e70fc8f3d88bc339f826bb1ccc9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767027"
---
# <a name="configure-hybrid-kubernetes-clusters-with-container-insights"></a>Konfigurowanie hybrydowych klastrów Kubernetes za pomocą usługi Container Insights

Usługa Container Insights zapewnia rozbudowane środowisko monitorowania dla usługi Azure Kubernetes Service (AKS) i aparatu AKS na platformie [Azure,](https://github.com/Azure/aks-engine)czyli samodzielnie zarządzanego klastra Kubernetes hostowanej na platformie Azure. W tym artykule opisano sposób włączania monitorowania klastrów Kubernetes hostowanych poza platformą Azure i osiągnięcia podobnego środowiska monitorowania.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

Następujące konfiguracje są oficjalnie obsługiwane przez usługę Container Insights. Jeśli masz inną wersję usługi Kubernetes i wersji systemu operacyjnego, wyślij wiadomość e-mail na adres askcoin@microsoft.com .

- Środowiskach:

    - Lokalna sieć Kubernetes
    - Aparat AKS na platformie Azure i Azure Stack. Aby uzyskać więcej informacji, zobacz [Aparat AKS na Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)
    - [OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) w wersji 4 lub wyższej, lokalnie lub w innych środowiskach w chmurze.

- Wersje środowiska Kubernetes i zasady pomocy technicznej są takie same jak wersje [obsługiwanej przez aKS wersji](../../aks/supported-kubernetes-versions.md).

- Obsługiwane są następujące środowiska uruchomieniowe kontenerów: środowiska uruchomieniowe zgodne z docker, Moby i CRI, takie jak CRI-O i ContainerD.

- Obsługiwane wersje systemu operacyjnego Linux dla węzłów głównych i węzłów roboczych to: Ubuntu (18.04 LTS i 16.04 LTS) oraz Red Hat Enterprise Linux CoreOS 43.81.

- Obsługiwana kontrola dostępu: Kontrola dostępu na kontach kubernetes i nie RBAC

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy:

- [Obszar roboczy usługi Log Analytics](../logs/design-logs-deployment.md).

    Usługa Container Insights obsługuje obszar roboczy usługi Log Analytics w regionach wymienionych w obszarze Produkty platformy Azure [według regionów.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Aby utworzyć własny obszar roboczy, można go utworzyć za pomocą Azure Resource Manager [,](../logs/resource-manager-workspace.md)za pomocą programu [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)lub w Azure Portal [.](../logs/quick-create-workspace.md)

    >[!NOTE]
    >Włączanie monitorowania wielu klastrów o tej samej nazwie w tym samym obszarze roboczym usługi Log Analytics nie jest obsługiwane. Nazwy klastrów muszą być unikatowe.
    >

- Jesteś członkiem roli współautora usługi **Log Analytics, aby** włączyć monitorowanie kontenerów. Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego usługi Log Analytics, zobacz [Zarządzanie dostępem do obszaru roboczego i danych dziennika.](../logs/manage-access.md)

- Aby wyświetlić dane monitorowania, musisz mieć rolę czytelnika usługi [*Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) w obszarze roboczym usługi Log Analytics skonfigurowaną za pomocą usługi Container Insights.

- [Klient HELM do](https://helm.sh/docs/using_helm/) dołączania wykresu szczegółowych informacji o kontenerach dla określonego klastra Kubernetes.

- Następujące informacje o konfiguracji serwera proxy i zapory są wymagane, aby konteneryzowana wersja agenta usługi Log Analytics dla systemu Linux komunikowały się z Azure Monitor:

    |Zasób agenta|Porty |
    |------|---------|
    |*.ods.opinsights.azure.com |port 443 |
    |*.oms.opinsights.azure.com |port 443 |
    |*.dc.services.visualstudio.com |port 443 |

- Konteneryzowany agent wymaga, aby program Kubelet lub był otwarty we wszystkich węzłach w klastrze w celu `cAdvisor secure port: 10250` `unsecure port :10255` zbierania metryk wydajności. Zalecamy skonfigurowanie go na podstawie polecenia c WC, jeśli nie `secure port: 10250` zostało ono jeszcze skonfigurowane.

- Konteneryzowany agent wymaga, aby następujące zmienne środowiskowe zostały określone w kontenerze w celu komunikowania się z usługą interfejsu API Kubernetes w klastrze w celu zbierania danych spisu — `KUBERNETES_SERVICE_HOST` i `KUBERNETES_PORT_443_TCP_PORT` .

>[!IMPORTANT]
>Minimalna wersja agenta obsługiwana do monitorowania hybrydowych klastrów Kubernetes to ciprod10182019 lub nowsza.

## <a name="enable-monitoring"></a>Włączanie monitorowania

Włączenie usługi Container Insights dla hybrydowego klastra Kubernetes polega na wykonaniu następujących kroków w porządek.

1. Skonfiguruj obszar roboczy usługi Log Analytics przy użyciu rozwiązania Container Insights.   

2. Włącz wykres HELM usługi Container Insights w obszarze roboczym usługi Log Analytics.

Dodatkowe informacje na temat rozwiązań do monitorowania w programie Azure Monitor można [znaleźć tutaj.](../../azure-monitor/insights/solutions.md)

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Jak dodać rozwiązanie kontenerów Azure Monitor kontenerów

Rozwiązanie można wdrożyć za pomocą dostarczonego szablonu Azure Resource Manager za pomocą polecenia cmdlet Azure PowerShell `New-AzResourceGroupDeployment` lub interfejsu wiersza polecenia platformy Azure.

Jeśli nie masz pojęcia wdrażania zasobów przy użyciu szablonu, zobacz:

- [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)

- [Wdrażanie zasobów przy użyciu Resource Manager szablonów i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Jeśli zdecydujesz się używać interfejsu wiersza polecenia platformy Azure, musisz najpierw zainstalować interfejs wiersza polecenia i używać go lokalnie. Musisz mieć uruchomiony interfejs wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Aby zidentyfikować swoją wersję, `az --version` uruchom . Jeśli musisz zainstalować lub uaktualnić interfejs wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

Ta metoda zawiera dwa szablony JSON. Jeden szablon określa konfigurację włączania monitorowania, a drugi zawiera wartości parametrów, które można skonfigurować w celu określenia następujących wartości:

- **workspaceResourceId** — pełny identyfikator zasobu obszaru roboczego usługi Log Analytics.
- **workspaceRegion** — region, w którym jest tworzony obszar  roboczy, nazywany również lokalizacją we właściwościach obszaru roboczego podczas wyświetlania z Azure Portal.

Aby najpierw zidentyfikować pełny identyfikator zasobu obszaru roboczego usługi Log Analytics wymagany dla wartości parametru w plikucontainerSolutionParams.js, wykonaj następujące kroki, a następnie uruchom polecenie cmdlet programu PowerShell lub polecenie interfejsu wiersza polecenia platformy Azure, aby dodać `workspaceResourceId` rozwiązanie. 

1. Aby wyświetlić listę wszystkich subskrypcji, do których masz dostęp, należy użyć następującego polecenia:

    ```azurecli
    az account list --all -o table
    ```

    Dane wyjściowe będą wyglądać następująco:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    Skopiuj wartość **subscriptionid**.

2. Przejdź do subskrypcji hostowania obszaru roboczego usługi Log Analytics przy użyciu następującego polecenia:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. W poniższym przykładzie zostanie wyświetlona lista obszarów roboczych w twoich subskrypcjach w domyślnym formacie JSON.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    W danych wyjściowych znajdź nazwę obszaru roboczego, a następnie skopiuj pełny identyfikator zasobu tego obszaru roboczego usługi Log Analytics pod **identyfikatorem pola**.

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

7. Edytuj wartości **workspaceResourceId** przy użyciu wartości skopiowanej w kroku 3, a dla obszaru **roboczegoRegion** skopiuj wartość **Region** po uruchomieniu polecenia interfejsu wiersza polecenia platformy Azure [az monitor log-analytics workspace show](/cli/azure/monitor/log-analytics/workspace#az_monitor-log-analytics-workspace-list&preserve-view=true).

8. Zapisz ten plik jako containerSolutionParams.jsw folderze lokalnym.

9. Wszystko jest teraz gotowe do wdrożenia tego szablonu.

   - Aby wdrożyć Azure PowerShell za pomocą polecenia , użyj następujących poleceń w folderze zawierającym szablon:

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

       Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego i będzie zawierał wynik:

       ```powershell
       provisioningState       : Succeeded
       ```

   - Aby wdrożyć za pomocą interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenia:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego i będzie zawierał wynik:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Po włączeniu monitorowania wyświetlenie metryk kondycji klastra może potrwać około 15 minut.

## <a name="install-the-helm-chart"></a>Instalowanie pakietu HELM

W tej sekcji zainstalujesz konteneryzowanego agenta dla usługi Container Insights. Przed przystąpieniem należy zidentyfikować identyfikator obszaru roboczego wymagany dla parametru i klucz `omsagent.secret.wsid` podstawowy wymagany dla `omsagent.secret.key` parametru. Te informacje można zidentyfikować, wykonując poniższe kroki, a następnie uruchamiać polecenia w celu zainstalowania agenta przy użyciu pakietu HELM.

1. Uruchom następujące polecenie, aby zidentyfikować identyfikator obszaru roboczego:

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    W danych wyjściowych znajdź nazwę obszaru roboczego pod nazwą pola **,** a następnie skopiuj identyfikator tego obszaru roboczego usługi Log Analytics w polu **customerID**.

2. Uruchom następujące polecenie, aby zidentyfikować klucz podstawowy dla obszaru roboczego:

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    W danych wyjściowych znajdź klucz podstawowy w polu **primarySharedKey,** a następnie skopiuj wartość .

>[!NOTE]
>Następujące polecenia mają zastosowanie tylko w przypadku programu Helm w wersji 2. Użycie `--name` parametru nie ma zastosowania w przypadku programu Helm w wersji 3. 

>[!NOTE]
>Jeśli klaster Kubernetes komunikuje się za pośrednictwem serwera proxy, skonfiguruj parametr przy użyciu `omsagent.proxy` adresu URL serwera proxy. Jeśli klaster nie komunikuje się za pośrednictwem serwera proxy, nie trzeba określać tego parametru. Aby uzyskać więcej informacji, zobacz [Konfigurowanie punktu końcowego serwera proxy](#configure-proxy-endpoint) w dalszej części tego artykułu.

3. Dodaj repozytorium Azure Charts do listy lokalnej, uruchamiając następujące polecenie:

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. Zainstaluj wykres, uruchamiając następujące polecenie:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Jeśli obszar roboczy usługi Log Analytics Azure (Chiny) — 21Vianet, uruchom następujące polecenie:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Jeśli obszar roboczy usługi Log Analytics znajduje się w usłudze Azure US Government, uruchom następujące polecenie:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>Włączanie wykresu programu Helm przy użyciu modelu interfejsu API

Dodatek można określić w pliku JSON specyfikacji klastra aparatu AKS, nazywanym również modelem interfejsu API. W tym dodatku podaj zakodowaną w formacie base64 wersję programu i obszaru roboczego usługi Log Analytics, w którym są przechowywane `WorkspaceGUID` `WorkspaceKey` zebrane dane monitorowania. Możesz znaleźć i , `WorkspaceGUID` `WorkspaceKey` korzystając z kroków 1 i 2 w poprzedniej sekcji.

Obsługiwane definicje interfejsu API Azure Stack Hub klastra można znaleźć w tym przykładzie —kubernetes-container-monitoring_existing_workspace_id_and_key.js[ na stronie](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). W szczególności znajdź właściwość **addons** w pliku **kubernetesConfig**:

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

W wersji 1.0.0 wykresu ustawienia zbierania danych agenta są kontrolowane za pomocą narzędzia ConfigMap. Zapoznaj się z dokumentacją dotyczącą ustawień zbierania danych agenta [tutaj.](container-insights-agent-config.md)

Po pomyślnym wdrożeniu wykresu możesz przejrzeć dane dla hybrydowego klastra Kubernetes w chmurze Container insights z Azure Portal.  

>[!NOTE]
>Opóźnienie pozyskiwania od agenta do zatwierdzenia w obszarze roboczym usługi Azure Log Analytics wynosi od około 5 do 10 minut. Stan klastra pokazuje wartość  Brak danych lub **Nieznany,** dopóki wszystkie wymagane dane monitorowania nie będą dostępne w Azure Monitor.

## <a name="configure-proxy-endpoint"></a>Konfigurowanie punktu końcowego serwera proxy

Począwszy od wykresu w wersji 2.7.1, wykres będzie obsługiwać określanie punktu końcowego serwera proxy za pomocą `omsagent.proxy` parametru chart. Dzięki temu może komunikować się za pośrednictwem serwera proxy. Komunikacja między agentem usługi Container Insights Azure Monitor może być serwerem proxy HTTP lub HTTPS, a obsługiwane jest zarówno uwierzytelnianie anonimowe, jak i podstawowe (nazwa użytkownika/hasło).

Wartość konfiguracji serwera proxy ma następującą składnię: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Jeśli serwer proxy nie wymaga uwierzytelniania, nadal musisz określić nazwę użytkownika/hasło psuedo. Może to być dowolna nazwa użytkownika lub hasło.

|Właściwość| Opis |
|--------|-------------|
|Protokół | http lub https |
|użytkownik | Opcjonalna nazwa użytkownika do uwierzytelniania serwera proxy |
|hasło | Opcjonalne hasło do uwierzytelniania serwera proxy |
|proxyhost | Adres lub nazwa FQDN serwera proxy |
|port | Opcjonalny numer portu serwera proxy |

Na przykład: `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

W przypadku określenia protokołu jako **http** żądania HTTP są tworzone przy użyciu bezpiecznego połączenia SSL/TLS. Serwer proxy musi obsługiwać protokoły SSL/TLS.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wystąpi błąd podczas próby włączenia monitorowania hybrydowego klastra Kubernetes, [](https://aka.ms/troubleshoot-non-azure-k8s) skopiuj skrypt programu PowerShellTroubleshootError_nonAzureK8s.ps1i zapisz go w folderze na komputerze. Ten skrypt jest dostarczany, aby ułatwić wykrywanie i rozwiązywanie napotkanych problemów. Problemy, które zaprojektowano pod kątem wykrywania i korekty, są następujące:

- Określony obszar roboczy usługi Log Analytics jest prawidłowy
- Obszar roboczy usługi Log Analytics jest konfigurowany za pomocą rozwiązania Container Insights. Jeśli nie, skonfiguruj obszar roboczy.
- Zasobniki zestawu replik OmsAgent są uruchomione
- Zasobniki demonów OmsAgent są uruchomione
- Usługa OmsAgent Health jest uruchomiona
- Identyfikator i klucz obszaru roboczego usługi Log Analytics skonfigurowane w konteneryzowanych agentach są zgodne z obszarem roboczym, za pomocą których skonfigurowano szczegółowe informacje.
- Sprawdź, czy wszystkie węzły procesu roboczego systemu Linux `kubernetes.io/role=agent` mają etykietę do planowania zasobnika rs. Jeśli nie istnieje, dodaj go.
- `cAdvisor secure port:10250` `unsecure port: 10255` Zweryfikuj lub został otwarty we wszystkich węzłach w klastrze.

Aby wykonać polecenie Azure PowerShell, użyj następujących poleceń w folderze zawierającym skrypt:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Następne kroki

Po włączeniu monitorowania w celu zbierania informacji o kondycji i wykorzystaniu zasobów hybrydowego klastra Kubernetes i uruchomionych na nich obciążeń dowiedz się, jak używać [usługi](container-insights-analyze.md) Container Insights.