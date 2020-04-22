---
title: Konfigurowanie klastrów kubernetów hybrydowych za pomocą usługi Azure Monitor dla kontenerów | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak skonfigurować usługę Azure Monitor dla kontenerów do monitorowania klastrów kubernetes hostowanych w usłudze Azure Stack lub innym środowisku.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 6c2782fdd810403a793f6ef682d1112fadc22465
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769043"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Konfigurowanie hybrydowych klastrów kubernetes za pomocą usługi Azure Monitor dla kontenerów

Usługa Azure Monitor dla kontenerów zapewnia zaawansowane środowisko monitorowania usługi Azure Kubernetes Service (AKS) i [AKS Engine na platformie Azure](https://github.com/Azure/aks-engine), która jest samodzielnie zarządzanym klastrem Kubernetes hostowanym na platformie Azure. W tym artykule opisano, jak włączyć monitorowanie klastrów kubernetes hostowanych poza platformą Azure i osiągnąć podobne środowisko monitorowania.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy:

* Obszar roboczy usługi Log Analytics.

    Usługa Azure Monitor dla kontenerów obsługuje obszar roboczy usługi Log Analytics w regionach wymienionych w usłudze Azure [Products według regionów.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Aby utworzyć własny obszar roboczy, można go utworzyć za pomocą [usługi Azure Resource Manager](../platform/template-workspace-configuration.md), za pośrednictwem programu [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)lub w [portalu Azure.](../learn/quick-create-workspace.md)

    >[!NOTE]
    >Włącz monitorowanie wielu klastrów o tej samej nazwie klastra do tego samego obszaru roboczego usługi Log Analytics nie jest obsługiwane. Nazwy klastrów muszą być unikatowe.
    >

* Jesteś członkiem **roli współautora usługi Log Analytics,** aby włączyć monitorowanie kontenera. Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego usługi Log Analytics, zobacz [Zarządzanie dostępem do obszaru roboczego i danych dziennika](../platform/manage-access.md)

* [Helm klienta](https://helm.sh/docs/using_helm/) do dołączania wykresu usługi Azure Monitor dla kontenerów dla określonego klastra Kubernetes.

* Następujące informacje o konfiguracji serwera proxy i zapory są wymagane dla konteneryzowanej wersji agenta usługi Log Analytics dla systemu Linux do komunikowania się z usługą Azure Monitor:

    |Zasób agenta|Porty |
    |------|---------|   
    |*.ods.opinsights.azure.com |port 443 |  
    |*.oms.opinsights.azure.com |port 443 |  
    |*.blob.core.windows.net |port 443 |  
    |*.dc.services.visualstudio.com |port 443 |

* Konteneryzowany agent wymaga `cAdvisor secure port: 10250` Kubelet `unsecure port :10255` lub mają być otwarte na wszystkich węzłach w klastrze do zbierania metryk wydajności. Zalecamy skonfigurowanie `secure port: 10250` w cAdvisor Kubelet, jeśli nie jest już skonfigurowany.

* Konteneryzowany agent wymaga, aby następujące zmienne środowiskowe były określone w kontenerze w celu komunikowania się `KUBERNETES_SERVICE_HOST` `KUBERNETES_PORT_443_TCP_PORT`z usługą interfejsu API kubernetes w klastrze w celu zbierania danych zapasów — i .

>[!IMPORTANT]
>Minimalna wersja agenta obsługiwana do monitorowania hybrydowych klastrów kubernetes jest ciprod10182019 lub nowsza.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

Następujące są oficjalnie obsługiwane w usłudze Azure Monitor dla kontenerów.

- Środowiska: Kubernetes lokalnie, aparat AKS na platformie Azure i usługi Azure Stack. Aby uzyskać więcej informacji, zobacz [Aparat AKS w usłudze Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Wersje usługi Kubernetes i zasady pomocy technicznej są takie same jak wersje [obsługiwanych przez program AKS](../../aks/supported-kubernetes-versions.md).
- Środowisko uruchomieniowe kontenera: Docker i Moby
- Wydanie systemu operacyjnego Linux dla węzłów głównych i roboczych: Ubuntu (18.04 LTS i 16.04 LTS)
- Kontrola dostępu obsługiwana: Kubernetes RBAC i non-RBAC

## <a name="enable-monitoring"></a>Włączanie monitorowania

Włączenie usługi Azure Monitor dla kontenerów dla hybrydowego klastra Kubernetes polega na wykonaniu następujących kroków w kolejności.

1. Skonfiguruj obszar roboczy usługi Log Analytics za pomocą rozwiązania Usługi Container Insights.

2. Włącz wykres usługi Azure Monitor dla kontenerów HELM z obszarem roboczym usługi Log Analytics.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Jak dodać rozwiązanie Kontenery usługi Azure Monitor

Rozwiązanie można wdrożyć za pomocą szablonu usługi Azure Resource Manager `New-AzResourceGroupDeployment` przy użyciu polecenia cmdlet programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

Jeśli nie znasz pojęcia wdrażania zasobów przy użyciu szablonu, zobacz:

* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)

* [Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Jeśli zdecydujesz się korzystać z interfejsu wiersza polecenia platformy Azure, najpierw należy zainstalować i używać interfejsu wiersza polecenia lokalnie. Musi być uruchomiony interfejsu wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Aby zidentyfikować swoją `az --version`wersję, uruchom polecenie . Jeśli chcesz zainstalować lub uaktualnić platformę Azure CLI, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

Ta metoda zawiera dwa szablony JSON. Jeden szablon określa konfigurację włączania monitorowania, a drugi zawiera wartości parametrów skonfigurowane w celu określenia następujących wartości:

- **workspaceResourceId** — pełny identyfikator zasobu obszaru roboczego usługi Log Analytics.
- **workspaceRegion** — region, w którym tworzony jest obszar roboczy, który jest również określany jako **Lokalizacja** we właściwościach obszaru roboczego podczas wyświetlania z witryny Azure portal.

Aby najpierw zidentyfikować pełny identyfikator zasobu obszaru roboczego `workspaceResourceId` usługi Log Analytics wymagany dla wartości parametru w pliku **containerSolutionParams.json,** wykonaj następujące kroki, a następnie uruchom polecenie cmdlet programu PowerShell lub narzędzie interfejsu wiersza polecenia platformy Azure, aby dodać rozwiązanie.

1. Wyświetl listę wszystkich subskrypcji, do których masz dostęp, używając następującego polecenia:

    ```azurecli
    az account list --all -o table
    ```

    Dane wyjściowe będą podobne do następujących:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Skopiuj wartość **identyfikatora subscriptionid**.

2. Przełącz się do subskrypcji obsługującej obszar roboczy usługi Log Analytics za pomocą następującego polecenia:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. W poniższym przykładzie jest wyświetlana lista obszarów roboczych w subskrypcjach w domyślnym formacie JSON.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    W danych wyjściowych znajdź nazwę obszaru roboczego, a następnie skopiuj pełny identyfikator zasobu tego obszaru roboczego usługi Log Analytics pod **identyfikatorem**pola .

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

5. Zapisz ten plik jako containerSolution.json w folderze lokalnym.

6. Wklej do pliku następującą składnię JSON:

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

7. Edytuj wartości dla **workspaceResourceId** przy użyciu wartości skopiowanej w kroku 3, a dla **obszaru roboczegoRegion** skopiować wartość **regionu** po uruchomieniu polecenia interfejsu wiersza polecenia Azure [CLI az monitor obszaru roboczego analizy dziennika show](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list).

8. Zapisz ten plik jako containerSolutionParams.json w folderze lokalnym.

9. Wszystko jest teraz gotowe do wdrożenia tego szablonu.

   * Aby wdrożyć za pomocą programu Azure PowerShell, należy użyć następujących poleceń w folderze, który zawiera szablon:

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```

       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       Zmiana konfiguracji może potrwać kilka minut. Po jego zakończeniu zostanie wyświetlony komunikat podobny do następującego i zawiera wynik:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Aby wdrożyć za pomocą interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenia:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Zmiana konfiguracji może potrwać kilka minut. Po jego zakończeniu zostanie wyświetlony komunikat podobny do następującego i zawiera wynik:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Po włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra.

## <a name="install-the-chart"></a>Instalowanie wykresu

>[!NOTE]
>Poniższe polecenia mają zastosowanie tylko do wersji 2 helm. Użycie parametru --name nie ma zastosowania do helm w wersji 3.

Aby włączyć wykres HELM, wykonaj następujące czynności:

1. Dodaj repozytorium wykresów platformy Azure do listy lokalnej, uruchamiając następujące polecenie:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Zainstaluj wykres, uruchamiając następujące polecenie:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Jeśli obszar roboczy usługi Log Analytics znajduje się w Chinach platformy Azure, uruchom następujące polecenie:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Jeśli obszar roboczy usługi Log Analytics znajduje się na platformie Azure US GOVERNMENT, uruchom następujące polecenie:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Konfigurowanie zbierania danych agenta

Patrząc z wykresu w wersji 1.0.0, ustawienia zbierania danych agenta są kontrolowane z ConfigMap. Zapoznaj się z dokumentacją dotyczącą ustawień zbierania danych [agenta tutaj](container-insights-agent-config.md).

Po pomyślnym wdrożeniu wykresu można przejrzeć dane klastra hybrydowych kubernetes w usłudze Azure Monitor dla kontenerów z witryny Azure portal.  

>[!NOTE]
>Opóźnienie pozyskiwania wynosi około pięciu do dziesięciu minut od agenta do zatwierdzenia w obszarze roboczym usługi Azure Log Analytics. Stan klastra pokazuje wartość **Brak danych** lub **Nieznany,** dopóki wszystkie wymagane dane monitorowania nie będą dostępne w usłudze Azure Monitor.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli podczas próby włączenia monitorowania hybrydowego klastra kubernetes wystąpi błąd, skopiuj skrypt programu PowerShell [TroubleshootError_nonAzureK8s.ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) i zapisz go w folderze na komputerze. Ten skrypt jest dostarczany, aby pomóc wykryć i rozwiązać napotkane problemy. Problemy, które jest przeznaczony do wykrywania i próby korekty są następujące:

* Określony obszar roboczy usługi Log Analytics jest prawidłowy
* Obszar roboczy usługi Log Analytics jest skonfigurowany przy użyciu rozwiązania Usługi Azure Monitor dla kontenerów. Jeśli nie, skonfiguruj obszar roboczy.
* OmsAgent repliki zestaw zasobników są uruchomione
* Zasobniki demonów OmsAgent są uruchomione
* Usługa OmsAgent Health działa
* Identyfikator obszaru roboczego usługi Log Analytics i klucz skonfigurowany w konteneryzowanym agencie są zgodne z obszarem roboczym skonfigurowanym przez program Insight.
* Sprawdź poprawność wszystkie węzły `kubernetes.io/role=agent` procesu roboczego systemu Linux mają etykietę do planowania rs zasobnika. Jeśli nie istnieje, dodaj go.
* Sprawdź `cAdvisor secure port:10250` poprawność lub `unsecure port: 10255` jest otwarty na wszystkich węzłach w klastrze.

Aby wykonać za pomocą programu Azure PowerShell, należy użyć następujących poleceń w folderze, który zawiera skrypt:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Następne kroki

Dzięki możliwości monitorowania włączonemu do zbierania kondycji i wykorzystania zasobów hybrydowego klastra kubernetes i uruchomionych na nich obciążeń, dowiedz się, [jak używać](container-insights-analyze.md) usługi Azure Monitor dla kontenerów.
