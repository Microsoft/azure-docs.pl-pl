---
title: Jak zatrzymać monitorowanie klastra usługi Azure Kubernetes Service | Microsoft Docs
description: W tym artykule opisano, jak można przestać kontynuować monitorowanie klastra usługi Azure AKS za pomocą szczegółowych informacji o kontenerze.
ms.topic: conceptual
ms.date: 08/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: eab34f27d1e33b166971203082cce99fb2b0e106
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101723541"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-container-insights"></a>Jak zatrzymać monitorowanie usługi Azure Kubernetes Service (AKS) za pomocą szczegółowych informacji o kontenerze

Po włączeniu monitorowania klastra AKS można zatrzymać monitorowanie klastra, jeśli zdecydujesz, że nie chcesz już go monitorować. W tym artykule pokazano, jak to zrobić za pomocą interfejsu wiersza polecenia platformy Azure lub z podanymi szablonami Azure Resource Manager.  


## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby wyłączyć usługi Container Insights, użyj polecenia [AZ AKS Disable-dodatkis](/cli/azure/aks#az-aks-disable-addons) . Polecenie usuwa agenta z węzłów klastra, nie usuwa rozwiązania lub dane są już zbierane i przechowywane w zasobie Azure Monitor.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Aby ponownie włączyć monitorowanie klastra, zobacz [Włączanie monitorowania za pomocą interfejsu wiersza polecenia platformy Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Udostępniane są dwa Azure Resource Manager szablonu do obsługi usuwania zasobów rozwiązania spójnie i wielokrotnie w grupie zasobów. Jeden to szablon JSON określający konfigurację, aby zatrzymać monitorowanie, a drugi zawiera wartości parametrów, które można skonfigurować, aby określić identyfikator zasobu klastra AKS i grupę zasobów, w której jest wdrażany klaster.

Jeśli nie znasz koncepcji wdrażania zasobów przy użyciu szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)
* [Wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>Szablon musi zostać wdrożony w tej samej grupie zasobów klastra. Jeśli pominięto wszystkie inne właściwości lub dodatki podczas korzystania z tego szablonu, może to spowodować usunięcie z klastra. Na przykład *enableRBAC* dla zasad RBAC Kubernetes wdrożonych w klastrze lub *aksResourceTagValues* , jeśli Tagi są określone dla klastra AKS.  
>

Jeśli zdecydujesz się na korzystanie z interfejsu wiersza polecenia platformy Azure, musisz najpierw zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować swoją wersję, uruchom polecenie `az --version` . Jeśli konieczne jest zainstalowanie lub uaktualnienie interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

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

4. Edytuj wartości dla **aksResourceId** i **aksResourceLocation** przy użyciu wartości klastra AKS, który można znaleźć na stronie **Właściwości** wybranego klastra.

    ![Strona właściwości kontenera](media/container-insights-optout/container-properties-page.png)

    Gdy jesteś na stronie **Właściwości** , skopiuj również **Identyfikator zasobu obszaru roboczego**. Ta wartość jest wymagana, jeśli zdecydujesz, że chcesz usunąć obszar roboczy Log Analytics później. Usuwanie obszaru roboczego Log Analytics nie jest wykonywane w ramach tego procesu.

    Edytuj wartości dla **aksResourceTagValues** , aby pasowały do istniejących wartości tagów określonych dla klastra AKS.

5. Zapisz ten plik jako **OptOutParam.jsw** folderze lokalnym.

6. Wszystko jest teraz gotowe do wdrożenia tego szablonu.

### <a name="remove-the-solution-using-azure-cli"></a>Usuwanie rozwiązania przy użyciu interfejsu wiersza polecenia platformy Azure

Wykonaj następujące polecenie w interfejsie wiersza polecenia platformy Azure w systemie Linux, aby usunąć rozwiązanie i wyczyścić konfigurację w klastrze AKS.

```azurecli
az login   
az account set --subscription "Subscription Name"
az deployment group create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie zwrócony komunikat podobny do następującego zawierającego wynik:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Usuwanie rozwiązania przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wykonaj następujące polecenia programu PowerShell w folderze zawierającym szablon, aby usunąć rozwiązanie i wyczyścić konfigurację z klastra AKS.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie zwrócony komunikat podobny do następującego zawierającego wynik:

```output
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>Następne kroki

Jeśli obszar roboczy został utworzony tylko w celu obsługi monitorowania klastra i nie jest już wymagany, trzeba go ręcznie usunąć. Jeśli nie wiesz, jak usunąć obszar roboczy, zobacz sekcję [Usuwanie obszaru roboczego usługi Azure log Analytics przy użyciu Azure Portal](../logs/delete-workspace.md). Nie zapomnij o **identyfikatorze zasobu obszaru roboczego** skopiowanym wcześniej w kroku 4, który będzie potrzebny.