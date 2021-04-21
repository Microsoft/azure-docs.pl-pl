---
title: Przewodnik Szybki start platformy Azure — tworzenie magazynu kluczy platformy Azure i klucza przy użyciu Azure Resource Manager szablonu | Microsoft Docs
description: Przewodnik Szybki start przedstawiający sposób tworzenia magazynów kluczy platformy Azure i dodawania klucza do magazynów przy użyciu Azure Resource Manager szablonu (szablonu usługi ARM).
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 10/14/2020
ms.author: sebansal
ms.openlocfilehash: 048482c6b52d3fd9225224bd3b4ff3ee66bf24fd
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815364"
---
# <a name="quickstart-create-an-azure-key-vault-and-a-key-by-using-arm-template"></a>Szybki start: tworzenie magazynu kluczy platformy Azure i klucza przy użyciu szablonu usługi ARM 

[Azure Key Vault](../general/overview.md) to usługa w chmurze, która zapewnia bezpieczny magazyn wpisów tajnych, takich jak klucze, hasła, certyfikaty i inne wpisy tajne. Ten przewodnik Szybki start koncentruje się na procesie wdrażania szablonu usługi Azure Resource Manager (arm) w celu utworzenia magazynu kluczy i klucza.

> [!NOTE]
> Ta funkcja nie jest dostępna dla Azure Government.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł:

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Użytkownik musi mieć przypisaną wbudowaną rolę platformy Azure, np. Współautorów. [Dowiedz się więcej tutaj](../../role-based-access-control/role-assignments-portal.md)
- Szablon wymaga Twojego identyfikatora obiektu użytkownika z usługi Azure Active Directory w celu skonfigurowania uprawnień. Poniższa procedura pozwala pobrać identyfikator obiektu (GUID).

    1. Uruchom następujące polecenie Azure PowerShell interfejsu wiersza polecenia platformy Azure, wybierz pozycję **Wypróbuj,** a następnie wklej skrypt w okienku powłoki. Aby wkleić skrypt, kliknij prawym przyciskiem myszy powłokę, a następnie wybierz pozycję **Wklej**.

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

    1. Zanotuj identyfikator obiektu. Będzie on potrzebny w następnej sekcji tego przewodnika Szybki start.

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
      "defaultValue": 2048,
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
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
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
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('vaultName'), '/', parameters('keyName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('vaultName'))]"
      ],
      "properties": {
        "kty": "[parameters('keyType')]",
        "keyOps": "[parameters('keyOps')]",
        "keySize": "[parameters('keySize')]",
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

W szablonie zdefiniowano dwa zasoby:

- [Microsoft.KeyVault/vaults](/azure/templates/microsoft.keyvault/vaults)
- Microsoft.KeyVault/vaults/keys

Więcej Azure Key Vault szablonów można znaleźć w tesłudze [Azure Quickstart Templates (Szablony szybkiego startu platformy Azure).](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Wdrożenie szablonu
Możesz użyć [interfejsu Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal), Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub interfejsu API REST. Aby dowiedzieć się więcej o metodach wdrażania, [zobacz Wdrażanie szablonów](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell).

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

Możesz użyć polecenia Azure Portal, aby sprawdzić magazyn kluczy i klucz, albo użyć następującego interfejsu wiersza polecenia platformy Azure Azure PowerShell skryptu, aby wyświetlić listę utworzonych kluczy.

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

W tym przewodniku Szybki start utworzono magazyn kluczy i klucz przy użyciu szablonu usługi ARM oraz zweryfikowano wdrożenie. Aby dowiedzieć się więcej Key Vault i Azure Resource Manager, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Key Vault](../general/overview.md)
- Dowiedz się więcej o usłudze [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Przejrzyj omówienie [Key Vault zabezpieczeń](../general/security-features.md)
