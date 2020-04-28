---
title: Dodawanie przypisań ról za pomocą szablonów RBAC i Usługi Azure Resource Manager
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure użytkownikom, grupom, jednostkom usług lub tożsamościom zarządzanym przy użyciu szablonów kontroli dostępu opartej na rolach platformy Azure (RBAC) i usługi Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9f817880f938f5d03024e3aacd9b84817a5ac721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77138298"
---
# <a name="add-role-assignments-using-azure-rbac-and-azure-resource-manager-templates"></a>Dodawanie przypisań ról przy użyciu szablonów usługi Azure RBAC i usługi Azure Resource Manager

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Oprócz korzystania z usługi Azure PowerShell lub interfejsu wiersza polecenia platformy Azure można przypisać role przy użyciu [szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Szablony mogą być przydatne, jeśli trzeba wdrożyć zasoby konsekwentnie i wielokrotnie. W tym artykule opisano sposób przypisywania ról przy użyciu szablonów.

## <a name="get-object-ids"></a>Pobierz identyfikatory obiektów

Aby przypisać rolę, należy określić identyfikator użytkownika, grupy lub aplikacji, do której chcesz przypisać rolę. Identyfikator ma format: `11111111-1111-1111-1111-111111111111`. Identyfikator można uzyskać za pomocą witryny Azure portal, usługi Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

### <a name="user"></a>Użytkownik

Aby uzyskać identyfikator użytkownika, możesz użyć poleceń [get-AzADUser](/powershell/module/az.resources/get-azaduser) lub [az ad user show.](/cli/azure/ad/user#az-ad-user-show)

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Grupa

Aby uzyskać identyfikator grupy, możesz użyć poleceń [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) lub [az ad group show.](/cli/azure/ad/group#az-ad-group-show)

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="application"></a>Aplikacja

Aby uzyskać identyfikator jednostki usługi (tożsamość używana przez aplikację), można użyć polecenia [listy Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) lub [az ad sp.](/cli/azure/ad/sp#az-ad-sp-list) Dla jednostki usługi należy użyć identyfikatora obiektu, a **nie** identyfikatora aplikacji.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>Dodaj przypisanie roli

W RBAC, aby udzielić dostępu, należy dodać przypisanie roli.

### <a name="resource-group-without-parameters"></a>Grupa zasobów (bez parametrów)

Poniższy szablon przedstawia podstawowy sposób dodawania przypisania roli. Niektóre wartości są określone w szablonie. Następujący szablon pokazuje:

-  Jak przypisać rolę [czytelnika](built-in-roles.md#reader) do użytkownika, grupy lub aplikacji w zakresie grupy zasobów

Aby użyć szablonu, należy wykonać następujące czynności:

- Tworzenie nowego pliku JSON i kopiowanie szablonu
- Zastąp `<your-principal-id>` identyfikatorem użytkownika, grupy lub aplikacji, aby przypisać rolę do

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

Poniżej przedstawiono przykład [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) commands for how to start the deployment in a resource group named ExampleGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

Poniżej przedstawiono przykład przypisania roli czytnika do użytkownika dla grupy zasobów po wdrożeniu szablonu.

![Przypisanie roli w zakresie grupy zasobów](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription"></a>Grupa zasobów lub subskrypcja

Poprzedni szablon nie jest zbyt elastyczny. Poniższy szablon używa parametrów i może być używany w różnych zakresach. Następujący szablon pokazuje:

- Jak przypisać rolę do użytkownika, grupy lub aplikacji w grupie zasobów lub zakresie subskrypcji
- Jak określić role właściciela, współautora i czytelnika jako parametru

Aby użyć szablonu, należy określić następujące dane wejściowe:

- Identyfikator użytkownika, grupy lub aplikacji, aby przypisać rolę do
- Unikatowy identyfikator, który będzie używany do przypisania roli lub można użyć domyślnego identyfikatora

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

> [!NOTE]
> Ten szablon nie jest idempotentne, chyba że ta sama `roleNameGuid` wartość jest podana jako parametr dla każdego wdrożenia szablonu. Jeśli `roleNameGuid` nie jest pod warunkiem, domyślnie nowy identyfikator GUID jest generowany `Conflict: RoleAssignmentExists` na każdym wdrożeniu i kolejnych wdrożeń zakończy się niepowodzeniem z powodu błędu.

Zakres przypisania roli jest określany na podstawie poziomu wdrożenia. Poniżej przedstawiono przykład [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) commands for how to start the deployment at a resource group scope.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Poniżej [przedstawiono przykład New-AzDeployment](/powershell/module/az.resources/new-azdeployment) i [az wdrożenia utworzyć](/cli/azure/deployment#az-deployment-create) polecenia, jak rozpocząć wdrożenie w zakresie subskrypcji i określić lokalizację.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource"></a>Zasób

Jeśli chcesz dodać przypisanie roli na poziomie zasobu, format przypisania roli jest inny. Udostępnić obszar nazw dostawcy zasobów i typ zasobu do przypisania roli. Należy również podać nazwę zasobu w nazwie przypisania roli.

W przypadku typu i nazwy przypisania roli należy użyć następującego formatu:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Następujący szablon pokazuje:

- Jak utworzyć nowe konto magazynu
- Jak przypisać rolę do użytkownika, grupy lub aplikacji w zakresie konta magazynu
- Jak określić role właściciela, współautora i czytelnika jako parametru

Aby użyć szablonu, należy określić następujące dane wejściowe:

- Identyfikator użytkownika, grupy lub aplikacji, aby przypisać rolę do

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', guid(uniqueString(variables('storageName'))))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Aby wdrożyć poprzedni szablon, należy użyć poleceń grupy zasobów. Poniżej przedstawiono przykład [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [az group deployment utworzyć](/cli/azure/group/deployment#az-group-deployment-create) polecenia, jak rozpocząć wdrażanie w zakresie zasobów.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Poniżej przedstawiono przykład przypisania roli współautora do użytkownika dla konta magazynu po wdrożeniu szablonu.

![Przypisanie roli w zakresie zasobów](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Nowy dyrektor serwisu

Jeśli utworzysz nową jednostkę usługi i natychmiast spróbujesz przypisać rolę do tej jednostki usługi, to przypisanie roli może w niektórych przypadkach zakończyć się niepowodzeniem. Na przykład jeśli utworzysz nową tożsamość zarządzaną, a następnie spróbujesz przypisać rolę do tego podmiotu usługi w tym samym szablonie usługi Azure Resource Manager, przypisanie roli może zakończyć się niepowodzeniem. Przyczyną tego błędu jest prawdopodobnie opóźnienie replikacji. Podmiot usługi jest tworzony w jednym regionie; jednak przypisanie roli może wystąpić w innym regionie, który nie replikował jeszcze jednostki usługi. Aby rozwiązać ten scenariusz, `principalType` należy `ServicePrincipal` ustawić właściwość podczas tworzenia przypisania roli.

Następujący szablon pokazuje:

- Jak utworzyć nową jednostkę usługi tożsamości zarządzanej
- Jak określić`principalType`
- Jak przypisać rolę współautora do tego podmiotu usługi w zakresie grupy zasobów

Aby użyć szablonu, należy określić następujące dane wejściowe:

- Podstawowa nazwa tożsamości zarządzanej lub można użyć ciągu domyślnego

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Poniżej przedstawiono przykład [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) commands for how to start the deployment at a resource group scope.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Poniżej przedstawiono przykład przypisania roli współautora do nowego podmiotu usługi tożsamości zarządzanej po wdrożeniu szablonu.

![Przypisanie roli dla nowego podmiotu usługi tożsamości zarządzanej](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Następne kroki

- [Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Understand the structure and syntax of Azure Resource Manager Templates (Omówienie struktury i składni szablonów usługi Azure Resource Manager)](../azure-resource-manager/templates/template-syntax.md)
- [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Szablony przewodników Szybki start platformy Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
