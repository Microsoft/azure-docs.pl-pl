---
title: Monitoruj wdrożony klaster usługi Azure Kubernetes Service (AKS) | Microsoft Docs
description: Dowiedz się, jak włączyć monitorowanie klastra usługi Azure Kubernetes Service (AKS) przy użyciu szczegółowych informacji o kontenerach już wdrożonych w Twojej subskrypcji.
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: e84e1c4ad3aa3950a433218255ccac3d91435231
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717676"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Włącz monitorowanie już wdrożonego klastra usługi Azure Kubernetes Service (AKS)

W tym artykule opisano sposób konfigurowania usługi Container Insights w celu monitorowania zarządzanego klastra Kubernetes hostowanego w [usłudze Azure Kubernetes Service](../../aks/index.yml) , która została już wdrożona w ramach subskrypcji.

Można włączyć monitorowanie klastra AKS, który jest już wdrożony przy użyciu jednej z obsługiwanych metod:

* Interfejs wiersza polecenia platformy Azure
* Terraform
* [Z Azure monitor](#enable-from-azure-monitor-in-the-portal) lub [bezpośrednio z klastra AKS](#enable-directly-from-aks-cluster-in-the-portal) w Azure Portal
* Za pomocą [podanego szablonu Azure Resource Manager](#enable-using-an-azure-resource-manager-template) przy użyciu polecenia cmdlet Azure PowerShell `New-AzResourceGroupDeployment` lub interfejsu wiersza polecenia platformy Azure.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="enable-using-azure-cli"></a>Włączanie przy użyciu interfejsu wiersza polecenia platformy Azure

Poniższy krok umożliwia monitorowanie klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure. W tym przykładzie nie trzeba wstępnie tworzyć ani określać istniejącego obszaru roboczego. To polecenie upraszcza proces przez utworzenie domyślnego obszaru roboczego w domyślnej grupie zasobów subskrypcji klastra AKS, jeśli jeszcze nie istnieje w regionie.  Utworzony domyślny obszar roboczy jest podobny do formatu *DefaultWorkspace- \<GUID> - \<Region>*.

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Dane wyjściowe będą wyglądać następująco:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Integracja z istniejącym obszarem roboczym

Jeśli wolisz zintegrować z istniejącym obszarem roboczym, wykonaj następujące kroki, aby najpierw zidentyfikować pełny identyfikator zasobu Log Analytics obszaru roboczego, który jest wymagany dla tego `--workspace-resource-id` parametru, a następnie uruchom polecenie, aby włączyć dodatek monitorowania względem określonego obszaru roboczego.

1. Wyświetl listę wszystkich subskrypcji, do których masz dostęp, za pomocą następującego polecenia:

    ```azurecli
    az account list --all -o table
    ```

    Dane wyjściowe będą wyglądać następująco:

    ```output
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
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

4. Uruchom następujące polecenie, aby włączyć dodatek monitorowania, zastępując wartość `--workspace-resource-id` parametru. Wartość ciągu musi znajdować się w podwójnych cudzysłowach:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    Dane wyjściowe będą wyglądać następująco:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Włącz korzystanie z Terraform

1. Dodawanie profilu dodatku **oms_agent** do istniejącego [zasobu azurerm_kubernetes_cluster](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Dodaj [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) , wykonując czynności opisane w dokumentacji Terraform.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Włączanie z Azure Monitor w portalu

Aby włączyć monitorowanie klastra AKS w Azure Portal z Azure Monitor, wykonaj następujące czynności:

1. W Azure Portal wybierz pozycję **Monitoruj**.

2. Z listy wybierz **kontenery** .

3. Na stronie **monitorowanie kontenerów** wybierz opcję **Niemonitorowane klastry**.

4. Na liście niemonitorowanych klastrów Znajdź kontener na liście i kliknij pozycję **Włącz**.

5. Na stronie Dołączanie **do usługi Container Insights** , jeśli masz istniejący obszar roboczy log Analytics w tej samej subskrypcji co klaster, wybierz go z listy rozwijanej.
    Na tej liście jest wybierany domyślny obszar roboczy i lokalizacja, w ramach której jest wdrażany kontener AKS.

    ![Włącz monitorowanie usługi AKS Container Insights](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Jeśli chcesz utworzyć nowy obszar roboczy Log Analytics do przechowywania danych monitorowania z klastra, postępuj zgodnie z instrukcjami w temacie [tworzenie log Analytics obszaru roboczego](../logs/quick-create-workspace.md). Upewnij się, że obszar roboczy jest tworzony w tej samej subskrypcji, w której jest wdrażany kontener AKS.

Po włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Włącz bezpośrednio z klastra AKS w portalu

Aby włączyć monitorowanie bezpośrednio z jednego z klastrów AKS w Azure Portal, wykonaj następujące czynności:

1. W Azure Portal wybierz pozycję **wszystkie usługi**.

2. Na liście zasobów Rozpocznij wpisywanie **kontenerów**.  Lista filtruje się na podstawie danych wejściowych.

3. Wybierz pozycję **Kubernetes Services**.
    
4. Na liście usług Kubernetes wybierz usługę.

5. Na stronie Przegląd usługi Kubernetes wybierz pozycję **monitorowanie — szczegółowe informacje**.

6. Na stronie Dołączanie **do usługi Container Insights** , jeśli masz istniejący obszar roboczy log Analytics w tej samej subskrypcji co klaster, wybierz go na liście rozwijanej.
    Na tej liście jest wybierany domyślny obszar roboczy i lokalizacja, w ramach której jest wdrażany kontener AKS.

    ![Włącz monitorowanie kondycji kontenera AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Jeśli chcesz utworzyć nowy obszar roboczy Log Analytics do przechowywania danych monitorowania z klastra, postępuj zgodnie z instrukcjami w temacie [tworzenie log Analytics obszaru roboczego](../logs/quick-create-workspace.md). Upewnij się, że obszar roboczy jest tworzony w tej samej subskrypcji, w której jest wdrażany kontener AKS.

Po włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić dane operacyjne dla klastra.

## <a name="enable-using-an-azure-resource-manager-template"></a>Włączanie przy użyciu szablonu Azure Resource Manager

Ta metoda obejmuje dwa szablony JSON. Jeden szablon określa konfigurację umożliwiającą monitorowanie, a druga zawiera wartości parametrów, które można skonfigurować, aby określić następujące elementy:

* Identyfikator zasobu kontenera AKS.
* Grupa zasobów, w której jest wdrażany klaster.

>[!NOTE]
>Szablon musi zostać wdrożony w tej samej grupie zasobów co klaster.
>

Aby można było włączyć monitorowanie za pomocą Azure PowerShell lub interfejsu wiersza polecenia, należy utworzyć obszar roboczy Log Analytics. Aby utworzyć obszar roboczy, można go skonfigurować za pomocą [Azure Resource Manager](../logs/resource-manager-workspace.md), za pomocą [programu PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)lub [Azure Portal](../logs/quick-create-workspace.md).

Jeśli nie znasz koncepcji wdrażania zasobów przy użyciu szablonu, zobacz:

* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)

* [Wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Jeśli zdecydujesz się na korzystanie z interfejsu wiersza polecenia platformy Azure, musisz najpierw zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Aby zidentyfikować swoją wersję, uruchom polecenie `az --version` . Jeśli konieczne jest zainstalowanie lub uaktualnienie interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Tworzenie i wykonywanie szablonu

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "type": "string",
          "metadata": {
            "description": "AKS Cluster Resource ID"
          }
        },
        "aksResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the AKS resource e.g. \"East US\""
          }
        },
        "aksResourceTagValues": {
          "type": "object",
          "metadata": {
            "description": "Existing all tags on AKS Cluster Resource"
          }
        },
        "workspaceResourceId": {
          "type": "string",
          "metadata": {
            "description": "Azure Monitor Log Analytics Resource ID"
          }
        }
      },
      "resources": [
        {
          "name": "[split(parameters('aksResourceId'),'/')[8]]",
          "type": "Microsoft.ContainerService/managedClusters",
          "location": "[parameters('aksResourceLocation')]",
          "tags": "[parameters('aksResourceTagValues')]",
          "apiVersion": "2018-03-31",
          "properties": {
            "mode": "Incremental",
            "id": "[parameters('aksResourceId')]",
            "addonProfiles": {
              "omsagent": {
                "enabled": true,
                "config": {
                  "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
                }
              }
            }
          }
        }
      ]
    }
    ```

2. Zapisz ten plik jako **existingClusterOnboarding.jsw** folderze lokalnym.

3. Wklej następującą składnię JSON do pliku:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterLocation>"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Edytuj wartości dla **aksResourceId** i **aksResourceLocation** przy użyciu wartości na stronie **Przegląd AKS** dla klastra AKS. Wartość **workspaceResourceId** to pełny identyfikator zasobu obszaru roboczego log Analytics, który obejmuje nazwę obszaru roboczego.

    Edytuj wartości dla **aksResourceTagValues** , aby pasowały do istniejących wartości tagów określonych dla klastra AKS.

5. Zapisz ten plik jako **existingClusterParam.jsw** folderze lokalnym.

6. Wszystko jest teraz gotowe do wdrożenia tego szablonu.

   * Aby wdrożyć program przy użyciu Azure PowerShell, użyj następujących poleceń w folderze zawierającym szablon:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik:

       ```output
       provisioningState       : Succeeded
       ```

   * Aby przeprowadzić wdrożenie przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenia:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az deployment group create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik:

       ```output
       provisioningState       : Succeeded
       ```

       Po włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra.

## <a name="verify-agent-and-solution-deployment"></a>Weryfikowanie wdrożenia agenta i rozwiązania

W przypadku agenta w wersji *06072018* lub nowszej można sprawdzić, czy zarówno Agent, jak i rozwiązanie zostały pomyślnie wdrożone. W przypadku wcześniejszych wersji agenta można zweryfikować tylko wdrożenie agenta.

### <a name="agent-version-06072018-or-later"></a>Agent w wersji 06072018 lub nowszej

Uruchom następujące polecenie, aby sprawdzić, czy Agent został wdrożony pomyślnie.

```
kubectl get ds omsagent --namespace=kube-system
```

Dane wyjściowe powinny wyglądać podobnie do poniższego, co oznacza, że zostało prawidłowo wdrożone:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```

Jeśli w klastrze znajdują się węzły systemu Windows Server, możesz uruchomić następujące polecenie, aby sprawdzić, czy Agent został wdrożony pomyślnie.

```
kubectl get ds omsagent-win --namespace=kube-system
```

Dane wyjściowe powinny wyglądać podobnie do poniższego, co oznacza, że zostało prawidłowo wdrożone:

```output
User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
```

Aby zweryfikować wdrożenie rozwiązania, uruchom następujące polecenie:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Dane wyjściowe powinny wyglądać podobnie do poniższego, co oznacza, że zostało prawidłowo wdrożone:

```output
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Wersja agenta wcześniejsza niż 06072018

Aby sprawdzić, czy wersja agenta Log Analytics wydana przed *06072018* zostanie wdrożona prawidłowo, uruchom następujące polecenie:

```
kubectl get ds omsagent --namespace=kube-system
```

Dane wyjściowe powinny wyglądać podobnie do poniższego, co oznacza, że zostało prawidłowo wdrożone:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```

## <a name="view-configuration-with-cli"></a>Wyświetl konfigurację przy użyciu interfejsu wiersza polecenia

Użyj `aks show` polecenia, aby uzyskać szczegółowe informacje, takie jak rozwiązanie włączone lub nie, co to jest identyfikator zasobu obszaru roboczego log Analytics i szczegóły podsumowania klastra.

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Po kilku minutach polecenie zostanie wykonane i zwróci informacje w formacie JSON o rozwiązaniu.  Wyniki polecenia powinny zawierać profil dodatku monitorowania i podobne do następujących przykładowych danych wyjściowych:

```output
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Następne kroki

* Jeśli wystąpią problemy podczas próby dołączenia rozwiązania, zapoznaj się z [przewodnikiem rozwiązywania problemów](container-insights-troubleshoot.md)

* Dzięki monitorowaniu z możliwością zbierania danych o kondycji i obciążeniu zasobów klastra AKS oraz uruchomionych na nich obciążeń zapoznaj się z [tematem korzystanie z](container-insights-analyze.md) usługi Container Insights.