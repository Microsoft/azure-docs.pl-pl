---
title: Jak zatrzymać monitorowanie klastra Red Hat OpenShift v3 platformy Azure | Microsoft Docs
description: W tym artykule opisano, jak można zatrzymać monitorowanie klastra Red Hat OpenShift platformy Azure za pomocą Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 7e6ab46940ed29a98b3988c00c92d6c691d6e0f0
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695627"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-v3-cluster"></a>Jak zatrzymać monitorowanie klastra Red Hat OpenShift v3 platformy Azure

>[!IMPORTANT]
> Usługa Azure Red Hat OpenShift 3,11 zostanie wycofana z czerwca 2022.
>
> Od października 2020 nie będzie już można tworzyć nowych klastrów 3,11.
> Istniejące klastry 3,11 będą nadal działać do 2022 czerwca, ale nie będą już obsługiwane po tej dacie.
>
> Postępuj zgodnie z tym przewodnikiem, aby [utworzyć klaster usługi Azure Red Hat OpenShift 4](../../openshift/tutorial-create-cluster.md).
> Jeśli masz określone pytania, [skontaktuj się z nami](mailto:aro-feedback@microsoft.com).

Po włączeniu monitorowania klastra Red Hat OpenShift w wersji 3. x można zatrzymać monitorowanie klastra przy użyciu Azure Monitor dla kontenerów, jeśli zdecydujesz, że nie chcesz już ich monitorować. W tym artykule pokazano, jak to zrobić za pomocą podanego szablonu Azure Resource Manager.  

## <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Udostępniane są dwa Azure Resource Manager szablonu do obsługi usuwania zasobów rozwiązania spójnie i wielokrotnie w grupie zasobów. Jeden to szablon JSON określający konfigurację, aby zatrzymać monitorowanie, a drugi zawiera wartości parametrów, które można skonfigurować w celu określenia identyfikatora zasobu klastra OpenShift i regionu świadczenia usługi Azure, w którym wdrożono klaster.

Jeśli nie znasz koncepcji wdrażania zasobów przy użyciu szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)
* [Wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Jeśli zdecydujesz się na korzystanie z interfejsu wiersza polecenia platformy Azure, musisz najpierw zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie. Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Aby zidentyfikować swoją wersję, uruchom polecenie `az --version` . Jeśli konieczne jest zainstalowanie lub uaktualnienie interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="create-template"></a>Tworzenie szablonu

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
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
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. Edytuj wartości dla **aroResourceId** i **aroResourceLocation** przy użyciu wartości klastra OpenShift, który można znaleźć na stronie **Właściwości** wybranego klastra.

    ![Strona właściwości kontenera](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Zapisz ten plik jako **OptOutParam.jsw** folderze lokalnym.

6. Wszystko jest teraz gotowe do wdrożenia tego szablonu.

### <a name="remove-the-solution-using-azure-cli"></a>Usuwanie rozwiązania przy użyciu interfejsu wiersza polecenia platformy Azure

Wykonaj następujące polecenie w interfejsie wiersza polecenia platformy Azure w systemie Linux, aby usunąć rozwiązanie i wyczyścić konfigurację w klastrze.

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

Wykonaj następujące polecenia programu PowerShell w folderze zawierającym szablon, aby usunąć rozwiązanie i wyczyścić konfigurację z klastra.    

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

Jeśli obszar roboczy został utworzony tylko w celu obsługi monitorowania klastra i nie jest już wymagany, trzeba go ręcznie usunąć. Jeśli nie wiesz, jak usunąć obszar roboczy, zobacz temat [Usuwanie obszaru roboczego usługi Azure log Analytics](../platform/delete-workspace.md).
