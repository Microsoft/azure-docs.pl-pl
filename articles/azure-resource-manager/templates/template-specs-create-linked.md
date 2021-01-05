---
title: Tworzenie specyfikacji szablonu z połączonymi szablonami
description: Dowiedz się, jak utworzyć specyfikację szablonu z połączonymi szablonami.
ms.topic: conceptual
ms.date: 01/05/2021
ms.openlocfilehash: e5725ece165f5716480afbcb4ef9098274c09993
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900641"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>Samouczek: Tworzenie specyfikacji szablonu z połączonymi szablonami (wersja zapoznawcza)

Dowiedz się, jak utworzyć [specyfikację szablonu](template-specs.md) z szablonem głównym i [połączonym szablonem](linked-templates.md#linked-template). Specyfikacje szablonu są używane do udostępniania szablonów ARM innym użytkownikom w organizacji. W tym artykule opisano sposób tworzenia specyfikacji szablonu do pakowania szablonu głównego i jego połączonych szablonów przy użyciu `relativePath` właściwości [zasobu wdrożenia](/azure/templates/microsoft.resources/deployments).

## <a name="prerequisites"></a>Wymagania wstępne

Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Specyfikacje szablonu są obecnie w wersji zapoznawczej. Aby można było używać go z Azure PowerShell, należy zainstalować [wersję 5.0.0 lub nowszą](/powershell/azure/install-az-ps). Aby użyć go z interfejsem wiersza polecenia platformy Azure, użyj [wersji 2.14.2 lub nowszej](/cli/azure/install-azure-cli).

## <a name="create-linked-templates"></a>Tworzenie połączonych szablonów

Utwórz szablon główny i połączony szablon.

Aby połączyć szablon, Dodaj [zasób wdrożenia](/azure/templates/microsoft.resources/deployments) do szablonu głównego. We `templateLink` Właściwości określ ścieżkę względną dla połączonego szablonu zgodnie ze ścieżką szablonu nadrzędnego.

Połączony szablon jest nazywany **linkedTemplate.json** i jest przechowywany w podfolderze o nazwie **artefakty** w ścieżce, w której jest przechowywany szablon główny.  Możesz użyć jednej z następujących wartości dla relativePath:

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

`relativePath`Właściwość jest zawsze względna w stosunku do pliku szablonu `relativePath` , gdzie jest zadeklarowany, więc jeśli istnieje inny linkedTemplate2.js, który jest wywoływany z linkedTemplate.json i linkedTemplate2.jsjest przechowywany w tym samym podfolderze artefaktów, RelativePath określona w linkedTemplate.jsjest tylko `linkedTemplate2.json` .

1. Utwórz szablon główny przy użyciu poniższego kodu JSON. Zapisz szablon główny jako **azuredeploy.jsna** komputerze lokalnym. W tym samouczku założono, że Zapisano w ścieżce **c:\Templates\linkedTS\azuredeploy.jsna** , ale można użyć dowolnej ścieżki.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
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
          "apiVersion": "2020-06-01",
          "name": "createStorage",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > ApiVersion `Microsoft.Resources/deployments` musi mieć wartość 2020-06-01 lub nowszą.

1. Utwórz katalog o nazwie **artefakty** w folderze, w którym jest zapisany szablon główny.
1. Utwórz połączony szablon przy użyciu następującego kodu JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS",
            "Premium_LRS"
          ],
          "metadata": {
            "description": "Storage Account type"
          }
        },
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        }
      },
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. Zapisz szablon jako **linkedTemplate.js** w folderze **artefaktów** .

## <a name="create-template-spec"></a>Utwórz specyfikację szablonu

Specyfikacje szablonów są przechowywane w grupach zasobów.  Utwórz grupę zasobów, a następnie utwórz specyfikację szablonu przy użyciu następującego skryptu. Nazwa specyfikacji szablonu to **webspec**.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -Name webSpec `
  -Version "1.0.0.0" `
  -ResourceGroupName templateSpecRG `
  -Location westus2 `
  -TemplateFile "c:\Templates\linkedTS\azuredeploy.json"
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

```azurecli
az group create \
  --name templateSpecRG \
  --location westus2

az ts create \
  --name webSpec \
  --version "1.0.0.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "<path-to-main-template>"
```

---

Gdy skończysz, możesz wyświetlić specyfikację szablonu z poziomu Azure Portal lub przy użyciu następującego polecenia cmdlet:

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

```azurecli
az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0"
```

---

## <a name="deploy-template-spec"></a>Wdróż specyfikację szablonu

Teraz można wdrożyć specyfikację szablonu. Wdrożenie specyfikacji szablonu jest tak samo samo jak wdrażanie szablonu, który zawiera, z tą różnicą, że identyfikator zasobu specyfikacji szablonu jest przekazywany. Używasz tych samych poleceń wdrażania i w razie potrzeby Przekaż wartości parametrów dla specyfikacji szablonu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Versions.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

id = $(az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0" --query "id")

az deployment group create \
  --resource-group webRG \
  --template-spec $id
```

> [!NOTE]
> Istnieje znany problem z pobraniem identyfikatora specyfikacji szablonu i przypisaniem go do zmiennej w programie Windows PowerShell.

---

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wdrażaniu specyfikacji szablonu jako połączonego szablonu, zobacz [Samouczek: wdrażanie specyfikacji szablonu jako połączonego szablonu](template-specs-deploy-linked-template.md).
