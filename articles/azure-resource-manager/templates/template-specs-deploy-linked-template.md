---
title: Wdróż specyfikację szablonu jako połączony szablon
description: Dowiedz się, jak wdrożyć istniejącą specyfikację szablonu w połączonym wdrożeniu.
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 8d4ccd77c8b37a696fab7494a8d3f8052fc89b35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889267"
---
# <a name="tutorial-deploy-a-template-spec-as-a-linked-template-preview"></a>Samouczek: wdrażanie specyfikacji szablonu jako połączonego szablonu (wersja zapoznawcza)

Dowiedz się, jak wdrożyć istniejącą [specyfikację szablonu](template-specs.md) przy użyciu [połączonego wdrożenia](linked-templates.md#linked-template). Specyfikacje szablonu są używane do udostępniania szablonów ARM innym użytkownikom w organizacji. Po utworzeniu specyfikacji szablonu można wdrożyć specyfikację szablonu przy użyciu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Specyfikacja szablonu można również wdrożyć jako część rozwiązania przy użyciu połączonego szablonu.

## <a name="prerequisites"></a>Wymagania wstępne

Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Specyfikacje szablonu są obecnie w wersji zapoznawczej. Aby można było używać go z Azure PowerShell, należy zainstalować [wersję 5.0.0 lub nowszą](/powershell/azure/install-az-ps). Aby użyć go z interfejsem wiersza polecenia platformy Azure, użyj [wersji 2.14.2 lub nowszej](/cli/azure/install-azure-cli).

## <a name="create-a-template-spec"></a>Utwórz specyfikację szablonu

Postępuj zgodnie z [przewodnikiem Szybki Start: Tworzenie i wdrażanie specyfikacji szablonu](quickstart-create-template-specs.md) , aby utworzyć specyfikację szablonu służącą do wdrażania konta magazynu. W następnej sekcji wymagana jest nazwa grupy zasobów specyfikacji szablonu, Nazwa specyfikacji szablonu i wersja specyfikacji szablonu.

## <a name="create-the-main-template"></a>Utwórz szablon główny

Aby wdrożyć specyfikację szablonu w szablonie ARM, Dodaj [zasób wdrożenia](/azure/templates/microsoft.resources/deployments) do głównego szablonu. We `templateLink` Właściwości Określ identyfikator zasobu specyfikacji szablonu. Utwórz szablon z następującym kodem JSON o nazwie **azuredeploy.json**. W tym samouczku założono, że Zapisano w ścieżce **c:\Templates\deployTS\azuredeploy.jsna** , ale można użyć dowolnej ścieżki.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tsResourceGroup":{
      "type": "string",
      "metadata": {
        "Description": "Specifies the resource group name of the template spec."
      }
    },
    "tsName": {
      "type": "string",
      "metadata": {
        "Description": "Specifies the name of the template spec."
      }
    },
    "tsVersion": {
      "type": "string",
      "defaultValue": "1.0.0.0",
      "metadata": {
        "Description": "Specifies the version the template spec."
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "metadata": {
        "Description": "Specifies the storage account type required by the template spec."
      }
    }
  },
  "variables": {
    "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2016-09-01",
      "name": "[variables('appServicePlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "B1",
        "tier": "Basic",
        "size": "B1",
        "family": "B",
        "capacity": 1
      },
      "kind": "linux",
      "properties": {
        "perSiteScaling": false,
        "reserved": true,
        "targetWorkerCount": 0,
        "targetWorkerSizeId": 0
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "createStorage",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
        },
        "parameters": {
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }
  ],
  "outputs": {
    "templateSpecId": {
      "type": "string",
      "value": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
    }
  }
}
```

Identyfikator specyfikacji szablonu jest generowany przy użyciu [`resourceID()`](template-functions-resource.md#resourceid) funkcji. Argument grupy zasobów w funkcji resourceID () jest opcjonalny, jeśli templateSpec znajduje się w tej samej grupie zasobów bieżącego wdrożenia.  Możesz również przekazać bezpośrednio identyfikator zasobu jako parametr. Aby uzyskać identyfikator, użyj:

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$id = (Get-AzTemplateSpec -ResourceGroupName $resourceGroupName -Name $templateSpecName -Version $templateSpecVersion).Versions.Id
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

```azurecli-interactive
id = $(az ts show --name $templateSpecName --resource-group $resourceGroupName --version $templateSpecVersion --query "id")
```

> [!NOTE]
> Istnieje znany problem z pobraniem identyfikatora specyfikacji szablonu i przypisaniem go do zmiennej w programie Windows PowerShell.

---

Składnia przekazywania parametrów do specyfikacji szablonu jest następująca:

```json
"parameters": {
  "storageAccountType": {
    "value": "[parameters('storageAccountType')]"
  }
}
```

> [!NOTE]
> ApiVersion `Microsoft.Resources/deployments` musi mieć wartość 2020-06-01 lub nowszą.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Po wdrożeniu połączonego szablonu wdrażana jest zarówno aplikacja sieci Web, jak i konto magazynu. Wdrożenie jest takie samo jak wdrażanie innych szablonów ARM.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

New-AzResourceGroupDeployment `
  -ResourceGroupName webRG `
  -TemplateFile "c:\Templates\deployTS\azuredeploy.json" `
  -tsResourceGroup templateSpecRg `
  -tsName storageSpec `
  -tsVersion 1.0
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

az deployment group create \
  --resource-group webRG \
  --template-file "c:\Templates\deployTS\azuredeploy.json" \
  --parameters tsResourceGroup=templateSpecRG tsName=storageSpec tsVersion=1.0
```

---

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tworzeniu specyfikacji szablonu zawierającej połączone szablony, zobacz [Tworzenie specyfikacji szablonu połączonego szablonu](template-specs-create-linked.md).
