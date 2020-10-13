---
title: Przewodnik dotyczący platformy Azure — tworzenie magazynu kluczy platformy Azure i zasad dostępu do magazynu przy użyciu szablonu Azure Resource Manager | Microsoft Docs
description: Pokazuje, jak tworzyć magazyny kluczy Azure i zasady dostępu do magazynu przy użyciu szablonu Azure Resource Manager.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: cf19561005fe2e98b7b5cf6812ff9224fd9474dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91804397"
---
# <a name="how-to-create-azure-key-vault-and-vault-access-policy-using-a-resource-manager-template"></a>Jak utworzyć zasady dostępu Azure Key Vault i magazynu przy użyciu szablonu Menedżer zasobów

[Azure Key Vault](../general/overview.md) to usługa w chmurze, która zapewnia bezpieczny magazyn dla wpisów tajnych, takich jak klucze, hasła, certyfikaty i inne wpisy tajne. Ten przewodnik koncentruje się na procesie wdrażania szablonu Azure Resource Manager (szablon ARM) w celu utworzenia magazynu kluczy.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="create-key-vault-resource-manager-template"></a>Utwórz szablon Menedżer zasobów Key Vault

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

Aby uzyskać więcej informacji na temat Key Vault ustawień szablonu, zobacz sekcję [Key Vault informacje o szablonie usługi ARM](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Jeśli szablon zostanie wdrożony ponownie, spowoduje zastąpienie wszystkich istniejących zasad dostępu w magazynie kluczy. Zalecane jest wypełnienie `accessPolicies` Właściwości istniejącymi zasadami dostępu, aby uniknąć utracie dostępu do magazynu kluczy. 

## <a name="add-access-policy-to-key-vault-resource-manager-template"></a>Dodawanie zasad dostępu do szablonu Menedżer zasobów Key Vault

Zasady dostępu można wdrożyć do istniejącego magazynu kluczy bez ponownego wdrożenia całego szablonu magazynu kluczy. Poniższy szablon przedstawia podstawowy sposób tworzenia zasad dostępu.

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
Aby uzyskać więcej informacji na temat Key Vault ustawień szablonu, zobacz sekcję [Key Vault informacje o szablonie usługi ARM](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="other-available-key-vault-resource-manager-templates"></a>Inne dostępne szablony Menedżer zasobów Key Vault

Istnieją inne szablony Menedżer zasobów dostępne dla obiektów Key Vault:

| Wpisy tajne | Klucze | Certyfikaty |
|--|--|--|
|[Szybki start](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-template)<br>[Odwołanie](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/secrets)|NIE DOTYCZY|NIE DOTYCZY|

Więcej szablonów Key Vault można znaleźć tutaj: [Key Vault Menedżer zasobów odwołania](https://docs.microsoft.com/azure/templates/microsoft.keyvault/allversions)

## <a name="deploy-the-templates"></a>Wdrażanie szablonów

Azure Portal można użyć do wdrożenia powyższych szablonów przy użyciu opcji "Kompiluj własny szablon w edytorze" w przewodniku poniżej: [Wdróż zasoby z szablonu niestandardowego](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)

Można również zapisać powyższe szablony do plików i użyć następujących poleceń:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki Start i samouczkami, możesz pozostawić te zasoby na miejscu. Gdy zasoby nie będą już potrzebne, Usuń grupę zasobów, która spowoduje usunięcie magazynu kluczy i powiązanych zasobów. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, wykonaj następujące czynności.

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

- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Dowiedz się więcej o usłudze [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Przegląd [Azure Key Vault najlepszych](../general/best-practices.md) rozwiązań

## <a name="next-steps"></a>Następne kroki

- [Bezpieczny dostęp do magazynu kluczy](secure-your-key-vault.md)
- [Uwierzytelnianie w magazynie kluczy](authentication.md)
- [Przewodnik dewelopera Azure Key Vault](developers-guide.md)
