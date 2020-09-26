---
title: Tworzenie i wdrażanie specyfikacji szablonu
description: Dowiedz się, jak utworzyć specyfikację szablonu na podstawie szablonu ARM. Następnie wdróż specyfikację szablonu w grupie zasobów w subskrypcji.
author: tfitzmac
ms.date: 09/25/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: a4f24e69f29614de27947573d968d817dce4a57b
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369219"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Szybki Start: Tworzenie i wdrażanie specyfikacji szablonu (wersja zapoznawcza)

W tym przewodniku szybki start pokazano, jak spakować szablon Azure Resource Manager (szablon ARM) do [specyfikacji szablonu](template-specs.md) , a następnie wdrożyć tę specyfikację szablonu. Specyfikacja szablonu zawiera szablon ARM, który wdraża konto magazynu.

## <a name="prerequisites"></a>Wymagania wstępne

Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Specyfikacje szablonu są obecnie w wersji zapoznawczej. Aby go użyć, musisz [zarejestrować się w celu uzyskania listy oczekiwania](https://aka.ms/templateSpecOnboarding).
>
> Po zatwierdzeniu z listy oczekiwania uzyskasz instrukcje dotyczące instalowania modułu programu PowerShell w wersji zapoznawczej oraz modułu interfejsu wiersza polecenia w wersji zapoznawczej.

## <a name="create-template-spec"></a>Utwórz specyfikację szablonu

Specyfikacja szablonu jest nowym typem zasobu o nazwie **Microsoft. resources/templateSpecs**. Aby utworzyć specyfikację szablonu, można użyć Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonu ARM. We wszystkich opcjach wymagany jest szablon ARM, który jest spakowany w ramach specyfikacji szablonu.

Przy użyciu programu PowerShell i interfejsu wiersza polecenia szablon ARM jest przekazaniem jako parametr w poleceniu. W przypadku szablonu ARM szablon ARM do spakowania w specyfikacji szablonu jest osadzony w definicji specyfikacji szablonu.

Te opcje są pokazane poniżej.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Podczas tworzenia specyfikacji szablonu przy użyciu programu PowerShell można przekazać szablon lokalny. Skopiuj następujący szablon i Zapisz go lokalnie do pliku o nazwie **azuredeploy.jsna**. W tym przewodniku szybki start założono, że Zapisano w ścieżce **c:\Templates\azuredeploy.jsna** , ale można użyć dowolnej ścieżki.

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Utwórz nową grupę zasobów, aby zawierała specyfikację szablonu.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Następnie utwórz specyfikację szablonu w tej grupie zasobów. Nowy szablon daje nazwę **storageSpec**.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

1. Podczas tworzenia specyfikacji szablonu przy użyciu interfejsu wiersza polecenia można przekazać szablon lokalny. Skopiuj następujący szablon i Zapisz go lokalnie do pliku o nazwie **azuredeploy.jsna**. W tym przewodniku szybki start założono, że Zapisano w ścieżce **c:\Templates\azuredeploy.jsna** , ale można użyć dowolnej ścieżki.

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Utwórz nową grupę zasobów, aby zawierała specyfikację szablonu.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Następnie utwórz specyfikację szablonu w tej grupie zasobów. Nowy szablon daje nazwę **storageSpec**.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="arm-template"></a>[Szablon ARM](#tab/azure-resource-manager)

1. W przypadku tworzenia specyfikacji szablonu przy użyciu szablonu ARM szablon zostanie osadzony w definicji zasobu. Skopiuj następujący szablon i Zapisz go lokalnie jako **azuredeploy.js**. W tym przewodniku szybki start założono, że Zapisano w ścieżce **c:\Templates\azuredeploy.jsna** , ale można użyć dowolnej ścieżki.

    > [!NOTE]
    > W szablonie osadzonym wszystkie nawiasy klamrowe muszą zostać zmienione przy użyciu drugiego lewego nawiasu. Użyj `[[` zamiast `[` .

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
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
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Utwórz nową grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Wdróż szablon przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>Wdróż specyfikację szablonu

Teraz można wdrożyć specyfikację szablonu. Wdrożenie specyfikacji szablonu jest tak samo samo jak wdrażanie szablonu, który zawiera, z tą różnicą, że identyfikator zasobu specyfikacji szablonu jest przekazywany. Używasz tych samych poleceń wdrażania i w razie potrzeby Przekaż wartości parametrów dla specyfikacji szablonu.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Utwórz grupę zasobów, która będzie zawierać nowe konto magazynu.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. Pobierz identyfikator zasobu specyfikacji szablonu.

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. Wdróż specyfikację szablonu.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. Parametry należy podawać dokładnie tak samo, jak w przypadku szablonu ARM. Wdróż ponownie specyfikację szablonu z parametrem dla typu konta magazynu.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

1. Utwórz grupę zasobów, która będzie zawierać nowe konto magazynu.

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Pobierz identyfikator zasobu specyfikacji szablonu.

    ```azurecli
    id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > Istnieje znany problem z pobraniem identyfikatora specyfikacji szablonu i przypisaniem go do zmiennej w programie Windows PowerShell.

1. Wdróż specyfikację szablonu.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. Parametry należy podawać dokładnie tak samo, jak w przypadku szablonu ARM. Wdróż ponownie specyfikację szablonu z parametrem dla typu konta magazynu.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="arm-template"></a>[Szablon ARM](#tab/azure-resource-manager)

1. Skopiuj następujący szablon i Zapisz go lokalnie do pliku o nazwie **storage.jsna**.

    ```json
       {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Użyj interfejsu wiersza polecenia platformy Azure lub programu PowerShell, aby utworzyć nową grupę zasobów dla konta magazynu.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Wdróż szablon przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>Udzielanie dostępu

Jeśli chcesz zezwolić innym użytkownikom w organizacji na wdrożenie specyfikacji szablonu, musisz udzielić im dostępu do odczytu. Rolę czytelnika można przypisać do grupy zasobów, która zawiera specyfikacje szablonu, które mają być udostępniane. Aby uzyskać więcej informacji, zobacz [Samouczek: udzielanie grupie dostępu do zasobów platformy Azure przy użyciu Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasób wdrożony w tym przewodniku Szybki Start, Usuń grupy zasobów, które zostały utworzone.

1. W witrynie Azure Portal wybierz pozycję Grupa zasobów z menu po lewej stronie.

1. Wprowadź nazwę grupy zasobów (templateSpecRG i storageRG) w polu Filtruj według nazwy.

1. Wybierz nazwę grupy zasobów.

1. Z górnego menu wybierz pozycję Usuń grupę zasobów.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tworzeniu specyfikacji szablonu zawierającej połączone szablony, zobacz [Tworzenie specyfikacji szablonu połączonego szablonu](template-specs-create-linked.md).
