---
title: 'ML Studio (klasyczny): wdrażanie obszarów roboczych z Azure Resource Manager — Azure'
description: Jak wdrożyć obszar roboczy dla Azure Machine Learning Studio (klasyczny) przy użyciu szablonu Azure Resource Manager
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-azurepowershell
ms.date: 02/05/2018
ms.openlocfilehash: ee0bee4857d410781561e6f7f6a7eae0509d1138
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520038"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Wdróż obszar roboczy Azure Machine Learning Studio (klasyczny) przy użyciu Azure Resource Manager

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Użycie szablonu wdrażania Azure Resource Manager umożliwia zaoszczędzenie czasu, zapewniając skalowalny sposób wdrażania połączonych składników przy użyciu walidacji i mechanizmu ponawiania prób. Aby skonfigurować obszary robocze Azure Machine Learning Studio (klasyczne), na przykład należy najpierw skonfigurować konto usługi Azure Storage, a następnie wdrożyć obszar roboczy. Wyobraź sobie to ręcznie dla setek obszarów roboczych. Łatwiejszym rozwiązaniem jest użycie szablonu Azure Resource Manager do wdrożenia obszaru roboczego programu Studio (klasycznego) i jego wszystkich zależności. W tym artykule przedstawiono krok po kroku. Aby uzyskać więcej informacji na temat Azure Resource Manager, zobacz [Azure Resource Manager omówienie](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Krok po kroku: Tworzenie Obszar roboczy usługi Machine Learning
Utworzymy grupę zasobów platformy Azure, a następnie wdrożono nowe konto usługi Azure Storage i nowy obszar roboczy Azure Machine Learning Studio (klasyczny) przy użyciu szablonu Menedżer zasobów. Po zakończeniu wdrażania zostaną wydrukowane ważne informacje o utworzonych obszarach roboczych (klucz podstawowy, identyfikator obszaru roboczego i adres URL do obszaru roboczego).

### <a name="create-an-azure-resource-manager-template"></a>Tworzenie szablonu Azure Resource Manager

Obszar roboczy usługi Machine Learning wymaga konta usługi Azure Storage do przechowywania połączonego z nim zestawu danych.
Następujący szablon używa nazwy grupy zasobów do wygenerowania nazwy konta magazynu i nazwy obszaru roboczego.  Używa również nazwy konta magazynu jako właściwości podczas tworzenia obszaru roboczego.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Zapisz ten szablon jako mlworkspace.jsw pliku w obszarze c:\Temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Wdróż grupę zasobów na podstawie szablonu

* Otwórz program PowerShell.
* Instalowanie modułów dla Azure Resource Manager i zarządzania usługami platformy Azure

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press "A")
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press "A")
Install-Module Azure -Scope CurrentUser
```

   Te kroki umożliwiają pobranie i zainstalowanie modułów niezbędnych do wykonania pozostałych kroków. Należy to zrobić tylko raz w środowisku, w którym wykonywane są polecenia programu PowerShell.

* Uwierzytelnianie na platformie Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Ten krok należy powtórzyć dla każdej sesji. Po uwierzytelnieniu należy wyświetlić informacje o subskrypcji.

![Konto platformy Azure](./media/deploy-with-resource-manager-template/azuresubscription.png)

Teraz, gdy mamy dostęp do platformy Azure, możemy utworzyć grupę zasobów.

* Tworzenie grupy zasobów

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Sprawdź, czy grupa zasobów została prawidłowo zainicjowana. **ProvisioningState** powinna mieć wartość "powodzenie".
Nazwa grupy zasobów jest używana przez szablon do generowania nazwy konta magazynu. Nazwa konta magazynu musi mieć długość od 3 do 24 znaków i może zawierać tylko cyfry i małe litery.

![Grupa zasobów](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Przy użyciu wdrożenia grupy zasobów Wdróż nowe Obszar roboczy usługi Machine Learning.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Po zakończeniu wdrażania można uzyskać dostęp do właściwości wdrożonego obszaru roboczego. Na przykład można uzyskać dostęp do tokenu klucza podstawowego.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Innym sposobem pobierania tokenów istniejącego obszaru roboczego jest użycie polecenia Invoke-AzResourceAction. Na przykład można wyświetlić listę głównych i pomocniczych tokenów wszystkich obszarów roboczych.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Po aprowizacji obszaru roboczego można także zautomatyzować wiele zadań Azure Machine Learning Studio (klasycznych) przy użyciu [modułu programu PowerShell dla Azure Machine Learning Studio (klasyczny)](https://aka.ms/amlps).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [tworzenia szablonów Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).
* Zapoznaj się z [repozytorium szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates).
* Obejrzyj to wideo dotyczące [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Zobacz [Pomoc dotyczącą szablonu Menedżer zasobów](/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->