---
title: Tworzenie magazynu kluczy platformy Azure i zasad dostępu do magazynu przy użyciu szablonu ARM
description: W tym artykule pokazano, jak utworzyć magazyny kluczy Azure i zasady dostępu do magazynu przy użyciu szablonu Azure Resource Manager.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: 1ab382ec10d932b94961c73b7d7ac9082125a88c
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704524"
---
# <a name="how-to-create-an-azure-key-vault-and-vault-access-policy-by-using-a-resource-manager-template"></a>Jak utworzyć zasady dostępu magazynu i magazynu kluczy platformy Azure przy użyciu szablonu Menedżer zasobów

[Azure Key Vault](../general/overview.md) to usługa w chmurze, która zapewnia bezpieczny magazyn dla wpisów tajnych, takich jak klucze, hasła i certyfikaty. W tym artykule opisano proces wdrażania szablonu Azure Resource Manager (szablon ARM) w celu utworzenia magazynu kluczy.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .


## <a name="create-a-key-vault-resource-manager-template"></a>Tworzenie szablonu Menedżer zasobów Key Vault

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

Aby uzyskać więcej informacji na temat Key Vault ustawień szablonu, zobacz sekcję [Key Vault informacje o szablonie usługi ARM](/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Jeśli szablon zostanie ponownie wdrożony, wszystkie istniejące zasady dostępu w magazynie kluczy zostaną zastąpione. Zalecamy wypełnienie `accessPolicies` Właściwości istniejącymi zasadami dostępu, aby uniknąć utraty dostępu do magazynu kluczy. 

## <a name="add-an-access-policy-to-a-key-vault-resource-manager-template"></a>Dodawanie zasad dostępu do szablonu Menedżer zasobów Key Vault

Zasady dostępu można wdrożyć do istniejącego magazynu kluczy bez ponownego wdrażania całego szablonu magazynu kluczy. Poniższy szablon przedstawia podstawowy sposób tworzenia zasad dostępu:

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
              "certificates": [parameters('certificatesPermissions')]
            }
          }
        ]
      }
    }
  ]
}

```
Aby uzyskać więcej informacji na temat ustawień szablonu Key Vault, zobacz sekcję [Key Vault informacje o szablonie usługi ARM](/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="more-key-vault-resource-manager-templates"></a>Więcej szablonów Menedżer zasobów Key Vault

Istnieją inne szablony Menedżer zasobów dostępne dla obiektów Key Vault:

| Wpisy tajne | Klucze | Certyfikaty |
|--|--|--|
|<ul><li>[Szybki start](../secrets/quick-create-template.md)<li>[Odwołanie](/azure/templates/microsoft.keyvault/vaults/secrets)|NIE DOTYCZY|NIE DOTYCZY|

Więcej Key Vault szablonów można znaleźć tutaj: [Key Vault Menedżer zasobów Reference](/azure/templates/microsoft.keyvault/allversions).

## <a name="deploy-the-templates"></a>Wdrażanie szablonów

Możesz użyć Azure Portal do wdrożenia powyższych szablonów przy użyciu opcji **Kompiluj własny szablon w edytorze** , jak opisano tutaj: [Wdróż zasoby z szablonu niestandardowego](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

Można również zapisać powyższe szablony w plikach i użyć następujących poleceń:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować korzystanie z kolejnych przewodników Szybki Start i samouczków, możesz pozostawić te zasoby na miejscu. Jeśli zasoby nie są już potrzebne, Usuń grupę zasobów. Usunięcie grupy spowoduje również usunięcie magazynu kluczy i powiązanych zasobów. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, wykonaj następujące kroki:

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
- Zapoznaj się z [najlepszymi rozwiązaniami Azure Key Vault](../general/best-practices.md).

## <a name="next-steps"></a>Następne kroki

- [Bezpieczny dostęp do magazynu kluczy](secure-your-key-vault.md)
- [Uwierzytelnianie w magazynie kluczy](authentication.md)
- [Przewodnik dewelopera usługi Azure Key Vault](developers-guide.md)
