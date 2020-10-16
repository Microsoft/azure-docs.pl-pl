---
title: Przewodnik Szybki Start platformy Azure — tworzenie magazynu kluczy platformy Azure i klucza przy użyciu szablonu Azure Resource Manager | Microsoft Docs
description: Przewodnik Szybki Start przedstawiający sposób tworzenia magazynów kluczy Azure i dodawania klucza do magazynów przy użyciu szablonu Azure Resource Manager.
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 10/14/2020
ms.author: sebansal
ms.openlocfilehash: 5f8c1436b7e1a4155a99b20ced5c35614f1966f9
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128650"
---
# <a name="quickstart-create-an-azure-key-vault-and-a-key-by-using-arm-template"></a>Szybki Start: Tworzenie magazynu kluczy platformy Azure i klucza przy użyciu szablonu ARM 

[Azure Key Vault](../general/overview.md) to usługa w chmurze, która zapewnia bezpieczny magazyn dla wpisów tajnych, takich jak klucze, hasła, certyfikaty i inne wpisy tajne. Ten przewodnik Szybki Start koncentruje się na procesie wdrażania szablonu Azure Resource Manager (szablon ARM) w celu utworzenia magazynu kluczy i klucza.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Szablon wymaga Twojego identyfikatora obiektu użytkownika z usługi Azure Active Directory w celu skonfigurowania uprawnień. Poniższa procedura pozwala pobrać identyfikator obiektu (GUID).

    1. Uruchom następujące Azure PowerShell lub polecenie interfejsu wiersza polecenia platformy Azure, wybierając pozycję **Wypróbuj**, a następnie wklej skrypt do okienka powłoki. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz polecenie **Wklej**.

        # <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Zanotuj identyfikator obiektu. Będzie ona potrzebna w następnej sekcji tego przewodnika Szybki Start.

## <a name="review-the-template"></a>Przegląd szablonu

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "The name of the key vault to be created."
            }
        },
        "skuName": {
            "type": "string",
            "defaultValue": "Standard",
            "allowedValues": [
                "Standard",
                "Premium"
            ],
            "metadata": {
                "description": "The SKU of the vault to be created."
            }
        },
        "keyName": {
            "type": "string",
            "metadata": {
                "description": "The name of the key to be created."
            }
        },
        "keyType": {
            "type": "string",
            "metadata": {
                "description": "The JsonWebKeyType of the key to be created."
            }
        },
        "keyOps": {
            "type": "array",
            "defaultValue": [],
            "metadata": {
                "description": "The permitted JSON web key operations of the key to be created."
            }
        },
        "keySize": {
            "type": "int",
            "defaultValue": -1,
            "metadata": {
                "description": "The size in bits of the key to be created."
            }
        },
        "curveName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The JsonWebKeyCurveName of the key to be created."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('vaultName')]",
            "apiVersion": "2019-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "enableRbacAuthorization": false,
                "enableSoftDelete": false,
                "enabledForDeployment": false,
                "enabledForDiskEncryption": false,
                "enabledForTemplateDeployment": false,
                "tenantId": "[subscription().tenantId]",
                "accessPolicies": [],
                "sku": {
                    "name": "[parameters('skuName')]",
                    "family": "A"
                },
                "networkAcls": {
                    "defaultAction": "Allow",
                    "bypass": "AzureServices"
                }
            }
        },
        {
            "type": "Microsoft.KeyVault/vaults/keys",
            "name": "[concat(parameters('vaultName'), '/', parameters('keyName'))]",
            "apiVersion": "2019-09-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.KeyVault/vaults', parameters('vaultName'))]"
            ],
            "properties": {
                "kty": "[parameters('keyType')]",
                "keyOps": "[parameters('keyOps')]",
                "keySize": "[if(equals(parameters('keySize'), -1), json('null'), parameters('keySize'))]",
                "curveName": "[parameters('curveName')]"
            }
        }
    ],
    "outputs": {
        "proxyKey": {
            "type": "object",
            "value": "[reference(resourceId('Microsoft.KeyVault/vaults/keys', parameters('vaultName'), parameters('keyName')))]"
        }
    }
}

```

Więcej przykładów szablonów Azure Key Vault można znaleźć w [szablonach szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular).


## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Możesz użyć Azure Portal do sprawdzenia magazynu kluczy i klucza lub użyć następującego interfejsu wiersza polecenia platformy Azure lub skryptu Azure PowerShell, aby utworzyć listę utworzonego klucza.

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault key list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultKey -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne przewodniki Szybki start i samouczki usługi Key Vault bazują na tym przewodniku. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.
Jeśli nie będą Ci one już potrzebne, usuń grupę zasobów, a zostanie także usunięta usługa Key Vault i powiązane zasoby. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell:

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Magazyn kluczy i klucz tajny przy użyciu szablonu usługi ARM oraz zweryfikowano wdrożenie. Aby dowiedzieć się więcej na temat Key Vault i Azure Resource Manager, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Dowiedz się więcej o usłudze [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Przegląd [Azure Key Vault najlepszych](../general/best-practices.md) rozwiązań
