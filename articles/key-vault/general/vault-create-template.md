---
title: Tworzenie magazynu kluczy platformy Azure i zasad dostępu do magazynu przy użyciu szablonu usługi ARM
description: W tym artykule pokazano, jak tworzyć magazyny kluczy platformy Azure i zasady dostępu do magazynu przy użyciu Azure Resource Manager szablonu.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/14/2021
ms.author: mbaldwin
ms.openlocfilehash: e70906cbf26c899744bfbe137da4ce9cfa651b20
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753150"
---
# <a name="how-to-create-an-azure-key-vault-and-vault-access-policy-by-using-a-resource-manager-template"></a>Jak utworzyć zasady dostępu magazynu kluczy platformy Azure i magazynu przy użyciu szablonu Resource Manager magazynu

[Azure Key Vault](../general/overview.md) to usługa w chmurze, która zapewnia bezpieczny magazyn wpisów tajnych, takich jak klucze, hasła i certyfikaty. W tym artykule opisano proces wdrażania szablonu Azure Resource Manager (szablonu USŁUGI ARM) w celu utworzenia magazynu kluczy.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule:

* Jeśli nie masz subskrypcji platformy Azure, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem utwórz bezpłatne konto.


## <a name="create-a-key-vault-resource-manager-template"></a>Tworzenie szablonu Key Vault Resource Manager szablonu

Poniższy szablon przedstawia podstawowy sposób tworzenia magazynu kluczy. Niektóre wartości są określone w szablonie.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
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
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
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
    }
  ]
}

```

Aby uzyskać więcej informacji na Key Vault szablonu, zobacz [Key Vault szablonu usługi ARM](/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Jeśli szablon zostanie ponownielodowany, wszelkie istniejące zasady dostępu w magazynie kluczy zostaną zastąpione. Zalecamy wypełnienie właściwości istniejącymi zasadami dostępu, aby `accessPolicies` uniknąć utraty dostępu do magazynu kluczy. 

## <a name="add-an-access-policy-to-a-key-vault-resource-manager-template"></a>Dodawanie zasad dostępu do Key Vault Resource Manager szablonu

Zasady dostępu można wdrożyć w istniejącym magazynie kluczy bez ponownego wdrażania całego szablonu magazynu kluczy. Poniższy szablon przedstawia podstawowy sposób tworzenia zasad dostępu:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": "[parameters('certificatePermissions')]"
            }
          }
        ]
      }
    }
  ]
}

```

Aby uzyskać więcej informacji na temat Key Vault szablonu, zobacz Key Vault szablonu [usługi ARM](/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="more-key-vault-resource-manager-templates"></a>Więcej Key Vault Resource Manager szablonów

Istnieją inne szablony Resource Manager dostępne dla Key Vault obiektów:

| Wpisy tajne | Klucze | Certyfikaty |
|--|--|--|
|<ul><li>[Szybki start](../secrets/quick-create-template.md)<li>[Odwołanie](/azure/templates/microsoft.keyvault/vaults/secrets)|NIE DOTYCZY|NIE DOTYCZY|

Więcej szablonów Key Vault znaleźć tutaj: [Key Vault Resource Manager referencyjny .](/azure/templates/microsoft.keyvault/allversions)

## <a name="deploy-the-templates"></a>Wdrażanie szablonów

Aby wdrożyć Azure Portal szablonów, możesz użyć opcji Build **your own template in editor** (Tworzenie własnego szablonu w edytorze) zgodnie z opisem w tym miejscu: Deploy resources from a custom template (Wdrażanie zasobów na podstawie szablonu [niestandardowego).](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)

Możesz również zapisać poprzednie szablony w plikach i użyć tych poleceń:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki start i samouczkami, możesz pozostawić te zasoby na miejscu. Jeśli nie potrzebujesz już zasobów, usuń grupę zasobów. Jeśli usuniesz grupę, magazyn kluczy i powiązane zasoby również zostaną usunięte. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure Azure PowerShell, wykonaj następujące kroki:

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

## <a name="resources"></a>Zasoby

- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md).
- Dowiedz się więcej o [Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Zapoznaj się z [omówieniem Azure Key Vault zabezpieczeń](security-overview.md)

## <a name="next-steps"></a>Następne kroki

- [Bezpieczny dostęp do magazynu kluczy](security-overview.md)
- [Uwierzytelnianie w magazynie kluczy](authentication.md)
- [Przewodnik dewelopera usługi Azure Key Vault](developers-guide.md)
