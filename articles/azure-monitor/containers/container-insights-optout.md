---
title: Jak zatrzymać monitorowanie klastra Azure Kubernetes Service klastra | Microsoft Docs
description: W tym artykule opisano, jak można zakończyć monitorowanie klastra usługi Azure AKS za pomocą usługi Container Insights.
ms.topic: conceptual
ms.date: 08/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 619b6fc4cce860e5869fd0b31e303b4a474f8428
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774021"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-container-insights"></a>Jak zatrzymać monitorowanie usługi Azure Kubernetes Service (AKS) za pomocą usługi Container Insights

Po włączeniu monitorowania klastra usługi AKS możesz zatrzymać monitorowanie klastra, jeśli zdecydujesz, że nie chcesz już go monitorować. W tym artykule pokazano, jak to zrobić przy użyciu interfejsu wiersza polecenia platformy Azure lub z Azure Resource Manager szablonów.  


## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Użyj polecenia [az aks disable-addons,](/cli/azure/aks#az_aks_disable_addons) aby wyłączyć usługę Container Insights. Polecenie usuwa agenta z węzłów klastra, nie usuwa rozwiązania ani danych już zebranych i przechowywanych w zasobie Azure Monitor klastra.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Aby ponownie włączyć monitorowanie klastra, zobacz [Włączanie monitorowania przy użyciu interfejsu wiersza polecenia platformy Azure.](container-insights-enable-new-cluster.md#enable-using-azure-cli)

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Dostępne są dwa Azure Resource Manager do obsługi spójnego i wielokrotnego usuwania zasobów rozwiązania w grupie zasobów. Jeden z nich to szablon JSON określający konfigurację do zatrzymania monitorowania, a drugi zawiera wartości parametrów skonfigurowane w celu określenia identyfikatora zasobu klastra usługi AKS i grupy zasobów, w których wdrożono klaster.

Jeśli nie masz pojęcia wdrażania zasobów przy użyciu szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)
* [Wdrażanie zasobów przy użyciu Resource Manager szablonów i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>Szablon należy wdrożyć w tej samej grupie zasobów klastra. Jeśli pominięto jakiekolwiek inne właściwości lub dodatki podczas korzystania z tego szablonu, może to spowodować usunięcie ich z klastra. Na przykład *enableRBAC* dla zasad RBAC kubernetes zaimplementowanych w klastrze lub *aksResourceTagValues,* jeśli tagi są określone dla klastra usługi AKS.  
>

Jeśli zdecydujesz się na korzystanie z interfejsu wiersza polecenia platformy Azure, musisz najpierw zainstalować interfejs wiersza polecenia i używać go lokalnie. Musisz mieć uruchomiony interfejs wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować swoją wersję, `az --version` uruchom . Jeśli musisz zainstalować lub uaktualnić interfejs wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

### <a name="create-template"></a>Tworzenie szablonu

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
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Zapisz ten plik jako **OptOutTemplate.jsw** folderze lokalnym.

3. Wklej następującą składnię JSON do pliku:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterRegion>"
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

4. Edytuj wartości **aksResourceId** **i aksResourceLocation** przy użyciu wartości klastra AKS, które  można znaleźć na stronie Właściwości wybranego klastra.

    ![Strona właściwości kontenera](media/container-insights-optout/container-properties-page.png)

    Na stronie Właściwości skopiuj **również** identyfikator zasobu **obszaru roboczego**. Ta wartość jest wymagana, jeśli zdecydujesz się później usunąć obszar roboczy usługi Log Analytics. Usuwanie obszaru roboczego usługi Log Analytics nie jest wykonywane w ramach tego procesu.

    Edytuj wartości **aksResourceTagValues,** aby dopasować istniejące wartości tagów określone dla klastra usługi AKS.

5. Zapisz ten plik jako **OptOutParam.jsw** folderze lokalnym.

6. Wszystko jest teraz gotowe do wdrożenia tego szablonu.

### <a name="remove-the-solution-using-azure-cli"></a>Usuwanie rozwiązania przy użyciu interfejsu wiersza polecenia platformy Azure

Wykonaj następujące polecenie za pomocą interfejsu wiersza polecenia platformy Azure w systemie Linux, aby usunąć rozwiązanie i wyczyścić konfigurację w klastrze usługi AKS.

```azurecli
az login   
az account set --subscription "Subscription Name"
az deployment group create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie zwrócony komunikat podobny do następującego, który zawiera wynik:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Usuwanie rozwiązania przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wykonaj następujące polecenia programu PowerShell w folderze zawierającym szablon, aby usunąć rozwiązanie i wyczyścić konfigurację z klastra usługi AKS.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie zwrócony komunikat podobny do następującego, który zawiera wynik:

```output
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>Następne kroki

Jeśli obszar roboczy został utworzony tylko w celu obsługi monitorowania klastra i nie jest już potrzebny, należy go usunąć ręcznie. Jeśli nie wiesz, jak usunąć obszar roboczy, zobacz Usuwanie obszaru roboczego [usługi Azure Log Analytics](../logs/delete-workspace.md)za pomocą Azure Portal . Nie zapomnij o **identyfikatorze** zasobu obszaru roboczego skopiowanym wcześniej w kroku 4.