---
title: Tworzenie i wdrażanie specyfikacji szablonu
description: Dowiedz się, jak utworzyć specyfikację szablonu na podstawie szablonu ARM. Następnie wdróż specyfikację szablonu w grupie zasobów w subskrypcji.
author: tfitzmac
ms.date: 12/14/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 28987486726f5a88d20efe9fe8a766e536062c2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889964"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Szybki Start: Tworzenie i wdrażanie specyfikacji szablonu (wersja zapoznawcza)

W tym przewodniku szybki start pokazano, jak spakować szablon Azure Resource Manager (szablon ARM) do [specyfikacji szablonu](template-specs.md). Następnie należy wdrożyć tę specyfikację szablonu. Specyfikacja szablonu zawiera szablon ARM, który wdraża konto magazynu.

## <a name="prerequisites"></a>Wymagania wstępne

Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Specyfikacje szablonu są obecnie w wersji zapoznawczej. Aby można było używać go z Azure PowerShell, należy zainstalować [wersję 5.0.0 lub nowszą](/powershell/azure/install-az-ps). Aby użyć go z interfejsem wiersza polecenia platformy Azure, użyj [wersji 2.14.2 lub nowszej](/cli/azure/install-azure-cli).

## <a name="create-template"></a>Tworzenie szablonu

Utwórz specyfikację szablonu na podstawie szablonu lokalnego. Skopiuj następujący szablon i Zapisz go lokalnie do pliku o nazwie **azuredeploy.jsna**. W tym przewodniku szybki start założono, że Zapisano w ścieżce **c:\Templates\azuredeploy.jsna** , ale można użyć dowolnej ścieżki.

:::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

## <a name="create-template-spec"></a>Utwórz specyfikację szablonu

Specyfikacja szablonu jest typem zasobu o nazwie `Microsoft.Resources/templateSpecs` . Aby utworzyć specyfikację szablonu, użyj programu PowerShell, interfejsu wiersza polecenia platformy Azure, portalu lub szablonu usługi ARM.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Utwórz nową grupę zasobów, aby zawierała specyfikację szablonu.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Utwórz specyfikację szablonu w tej grupie zasobów. Nadaj nowemu szablonowi nazwę **storageSpec**.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

1. Utwórz nową grupę zasobów, aby zawierała specyfikację szablonu.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Utwórz specyfikację szablonu w tej grupie zasobów. Nadaj nowemu szablonowi nazwę **storageSpec**.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj **specyfikacje szablonu**. Wybierz pozycję **specyfikacje szablonu** z dostępnych opcji.

    :::image type="content" source="./media/quickstart-create-template-specs/search-template-spec.png" alt-text="specyfikacje szablonu wyszukiwania":::

1. Wybierz pozycję **Importuj szablon**.

    :::image type="content" source="./media/quickstart-create-template-specs/import-template.png" alt-text="Importuj szablon":::

1. Wybierz ikonę folderu.

    :::image type="content" source="./media/quickstart-create-template-specs/open-folder.png" alt-text="Otwórz folder":::

1. Przejdź do zapisanego przez siebie szablonu lokalnego i wybierz go. Wybierz pozycję **Otwórz**.
1. Wybierz pozycję **Importuj**.

    :::image type="content" source="./media/quickstart-create-template-specs/select-import.png" alt-text="Wybierz opcję importowania":::

1. Podaj następujące wartości:

    - **Nazwa**: Wprowadź nazwę specyfikacji szablonu.  Na przykład **storageSpec**
    - **Subskrypcja**: wybierz subskrypcję platformy Azure używaną do tworzenia specyfikacji szablonu.
    - **Grupa zasobów**: wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę nowej grupy zasobów.  Na przykład **templateSpecRG**.
    - **Lokalizacja**: wybierz lokalizację grupy zasobów. Na przykład  **zachodnie stany USA 2**.
    - **Wersja**: wprowadź wersję specyfikacji szablonu. Użyj **1,0**.

1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz przycisk **Utwórz**.

# <a name="arm-template"></a>[Szablon ARM](#tab/azure-resource-manager)

> [!NOTE]
> Zamiast używać szablonu ARM, zalecamy użycie programu PowerShell lub interfejsu wiersza polecenia w celu utworzenia specyfikacji szablonu. Narzędzia te automatycznie konwertują połączone szablony na artefakty połączone z głównym szablonem. W przypadku tworzenia specyfikacji szablonu przy użyciu szablonu ARM należy ręcznie dodać te połączone szablony jako artefakty, co może być skomplikowane.

1. W przypadku tworzenia specyfikacji szablonu przy użyciu szablonu ARM szablon zostanie osadzony w definicji zasobu. Istnieją pewne zmiany, które należy wprowadzić w szablonie lokalnym. Skopiuj następujący szablon i Zapisz go lokalnie jako **azuredeploy.js**.

    > [!NOTE]
    > W szablonie osadzonym wszystkie [wyrażenia szablonu](template-expressions.md) muszą być wyprowadzane przy użyciu drugiego lewego nawiasu. Użyj `"[[` zamiast `"[` . W tablicach JSON nadal jest używany pojedynczy lewy nawias.

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

Aby wdrożyć specyfikację szablonu, należy użyć tych samych poleceń wdrażania, które są używane do wdrażania szablonu. Przekaż identyfikator zasobu specyfikacji szablonu do wdrożenia.

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
    id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
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

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wybierz utworzoną specyfikację szablonu.

    :::image type="content" source="./media/quickstart-create-template-specs/select-template-spec.png" alt-text="Wybierz specyfikacje szablonu":::

1. Wybierz pozycję **Wdróż**.

    :::image type="content" source="./media/quickstart-create-template-specs/deploy-template-spec.png" alt-text="Wdróż specyfikacje szablonu":::

1. Podaj następujące wartości:

    - **Subskrypcja**: wybierz subskrypcję platformy Azure na potrzeby tworzenia zasobu.
    - **Grupa zasobów**: wybierz pozycję **Utwórz nową** , a następnie wprowadź **storageRG**.
    - **Typ konta magazynu**: wybierz pozycję **Standard_GRS**.

1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.

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
          "apiVersion": "2020-10-01",
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

## <a name="update-template"></a>Aktualizuj szablon

Załóżmy, że zidentyfikowano zmianę, która ma zostać wprowadzona dla szablonu w specyfikacji szablonu. Następujący szablon jest podobny do wcześniejszego szablonu, z tą różnicą, że dodaje prefiks nazwy konta magazynu. Skopiuj następujący szablon i zaktualizuj azuredeploy.jspliku.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/templatespecs/azuredeploy2.json":::

## <a name="update-template-spec-version"></a>Aktualizuj wersję specyfikacji szablonu

Zamiast tworzyć nową specyfikację szablonu dla zweryfikowanego szablonu, Dodaj nową wersję o nazwie `2.0` do istniejącej specyfikacji szablonu. Użytkownicy mogą wybrać jedną z wersji do wdrożenia.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Utwórz nową wersję specyfikacji szablonu.

   ```powershell
   New-AzTemplateSpec `
     -Name storageSpec `
     -Version "2.0" `
     -ResourceGroupName templateSpecRG `
     -Location westus2 `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

1. Aby wdrożyć nową wersję, Pobierz identyfikator zasobu dla `2.0` wersji.

   ```powershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "2.0").Versions.Id
   ```

1. Wdróż tę wersję. Podaj prefiks dla nazwy konta magazynu.

   ```powershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG `
     -namePrefix "demoaccount"
   ```

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/azure-cli)

1. Utwórz nową wersję specyfikacji szablonu.

   ```azurecli
   az ts create \
     --name storageSpec \
     --version "2.0" \
     --resource-group templateSpecRG \
     --location "westus2" \
     --template-file "c:\Templates\azuredeploy.json"
   ```

1. Aby wdrożyć nową wersję, Pobierz identyfikator zasobu dla `2.0` wersji.

   ```azurecli
   id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "2.0" --query "id")
   ```

1. Wdróż tę wersję. Podaj prefiks dla nazwy konta magazynu.

   ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters namePrefix='demoaccount'
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W specyfikacji szablonu wybierz pozycję **Utwórz nową wersję**.

   :::image type="content" source="./media/quickstart-create-template-specs/select-versions.png" alt-text="Utwórz nową wersję":::

1. Nazwij nową wersję `2.0` i opcjonalnie Dodaj notatki. Wybierz pozycję **Edytuj szablon**.

   :::image type="content" source="./media/quickstart-create-template-specs/add-version-name.png" alt-text="Nazwa Nowa wersja":::

1. Zastąp zawartość szablonu zaktualizowanym szablonem. Wybierz pozycję **Przejrzyj i zapisz**.
1. Wybierz pozycję **Save changes** (Zapisz zmiany).

1. Aby wdrożyć nową wersję, wybierz pozycję **wersje**

   :::image type="content" source="./media/quickstart-create-template-specs/see-versions.png" alt-text="Wyświetl wersje":::

1. Dla wersji, którą chcesz wdrożyć, wybierz trzy kropki i **Wdróż**.

   :::image type="content" source="./media/quickstart-create-template-specs/deploy-version.png" alt-text="Wybierz wersję do wdrożenia":::

1. Wypełnij pola, tak jak podczas wdrażania wcześniejszej wersji.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.

# <a name="arm-template"></a>[Szablon ARM](#tab/azure-resource-manager)

1. Ponownie musisz wprowadzić pewne zmiany w szablonie lokalnym, aby działały z specyfikacją szablonu. Skopiuj następujący szablon i Zapisz go lokalnie jako azuredeploy.js.

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
              "name": "2.0",
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
                    },
                    "namePrefix": {
                      "type": "string",
                      "maxLength": 11,
                      "defaultValue": "store",
                      "metadata": {
                        "description": "Prefix for storage account name"
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat(parameters('namePrefix'), uniquestring(resourceGroup().id))]"
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

1. Aby dodać nową wersję do specyfikacji szablonu, wdróż szablon przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

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
          "apiVersion": "2020-10-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '2.0')]"
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

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasób wdrożony w tym przewodniku Szybki Start, Usuń grupy zasobów, które zostały utworzone.

1. W witrynie Azure Portal wybierz pozycję Grupa zasobów z menu po lewej stronie.

1. Wprowadź nazwę grupy zasobów (templateSpecRG i storageRG) w polu Filtruj według nazwy.

1. Wybierz nazwę grupy zasobów.

1. Z górnego menu wybierz pozycję Usuń grupę zasobów.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tworzeniu specyfikacji szablonu zawierającej połączone szablony, zobacz [Tworzenie specyfikacji szablonu połączonego szablonu](template-specs-create-linked.md).
