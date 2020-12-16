---
title: Dodawanie przypisań ról platformy Azure przy użyciu szablonów Azure Resource Manager — Azure RBAC
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure dla użytkowników, grup, nazw głównych usług lub tożsamości zarządzanych przy użyciu szablonów Azure Resource Manager i kontroli dostępu opartej na rolach (RBAC) na platformie Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1e05ecd162ccb333c6ab29b0185f6ffcb04a6213
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591365"
---
# <a name="add-azure-role-assignments-using-azure-resource-manager-templates"></a>Dodawanie przypisań ról platformy Azure przy użyciu szablonów Azure Resource Manager

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Oprócz używania Azure PowerShell lub interfejsu wiersza polecenia platformy Azure można przypisywać role przy użyciu [szablonów Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Szablony mogą być przydatne, jeśli trzeba spójnie i wielokrotnie wdrażać zasoby. W tym artykule opisano sposób przypisywania ról przy użyciu szablonów.

## <a name="get-object-ids"></a>Pobierz identyfikatory obiektów

Aby przypisać rolę, należy określić identyfikator użytkownika, grupy lub aplikacji, do której ma zostać przypisana rola. Identyfikator ma format: `11111111-1111-1111-1111-111111111111` . Identyfikator można uzyskać, korzystając z Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

### <a name="user"></a>Użytkownik

Aby uzyskać identyfikator użytkownika, można użyć poleceń [Get-AzADUser](/powershell/module/az.resources/get-azaduser) lub [AZ AD User show](/cli/azure/ad/user#az-ad-user-show) .

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Grupa

Aby uzyskać identyfikator grupy, można użyć poleceń [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) lub [AZ AD Group Show](/cli/azure/ad/group#az-ad-group-show) .

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="managed-identities"></a>Tożsamości zarządzane

Aby uzyskać identyfikator tożsamości zarządzanej, można użyć polecenia [Get-AzAdServiceprincipal](/powershell/module/az.resources/get-azadserviceprincipal) lub [AZ AD Sp](/cli/azure/ad/sp) .

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName <Azure resource name>).id
```

```azurecli
objectid=$(az ad sp list --display-name <Azure resource name> --query [].objectId --output tsv)
```

### <a name="application"></a>Aplikacja

Aby uzyskać identyfikator jednostki usługi (tożsamość używana przez aplikację), można użyć poleceń [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) lub [AZ AD Sp list](/cli/azure/ad/sp#az-ad-sp-list) . Dla jednostki usługi Użyj identyfikatora obiektu, a **nie** identyfikatora aplikacji.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>Dodaj przypisanie roli

W celu udzielenia dostępu w usłudze Azure RBAC należy dodać przypisanie roli.

### <a name="resource-group-scope-without-parameters"></a>Zakres grupy zasobów (bez parametrów)

Poniższy szablon przedstawia podstawowy sposób dodawania przypisania roli. Niektóre wartości są określone w szablonie. Poniższy szablon demonstruje:

-  Jak przypisać rolę [czytnika](built-in-roles.md#reader) do użytkownika, grupy lub aplikacji w zakresie grupy zasobów

Aby użyć szablonu, należy wykonać następujące czynności:

- Utwórz nowy plik JSON i skopiuj szablon
- Zamień na `<your-principal-id>` Identyfikator użytkownika, grupy, tożsamości zarządzanej lub aplikacji, do której ma zostać przypisana rola

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

Poniżej przedstawiono przykład [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create) polecenia służące do uruchamiania wdrożenia w grupie zasobów o nazwie example.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json
```

Poniżej przedstawiono przykład przypisania roli czytnika do użytkownika dla grupy zasobów po wdrożeniu szablonu.

![Przypisanie roli w zakresie grupy zasobów](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription-scope"></a>Grupa zasobów lub zakres subskrypcji

Poprzedni szablon nie jest bardzo elastyczny. Następujący szablon używa parametrów i może być używany w różnych zakresach. Poniższy szablon demonstruje:

- Jak przypisać rolę do użytkownika, grupy lub aplikacji w ramach grupy zasobów lub zakresu subskrypcji
- Jak określić role właściciela, współautora i czytnika jako parametr

Aby użyć szablonu, należy określić następujące dane wejściowe:

- Identyfikator użytkownika, grupy, tożsamości zarządzanej lub aplikacji, do której ma zostać przypisana rola
- Unikatowy identyfikator, który będzie używany na potrzeby przypisania roli, lub można użyć identyfikatora domyślnego

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
> Ten szablon nie jest idempotentne, chyba że ta sama `roleNameGuid` wartość jest podana jako parametr dla każdego wdrożenia szablonu. Jeśli nie `roleNameGuid` jest podany, domyślnie nowy identyfikator GUID jest generowany dla każdego wdrożenia, a kolejne wdrożenia zakończą się niepowodzeniem z `Conflict: RoleAssignmentExists` powodu błędu.

Zakres przypisania roli jest określany na podstawie poziomu wdrożenia. Oto przykład polecenia [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create) , aby uruchomić wdrożenie w zakresie grupy zasobów.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Poniżej przedstawiono przykłady polecenia [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) i [AZ Deployment subcreate](/cli/azure/deployment/sub#az_deployment_sub_create) dotyczące sposobu uruchamiania wdrożenia w zakresie subskrypcji i określania lokalizacji.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment sub create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource-scope"></a>Zakres zasobu

Jeśli konieczne jest dodanie przypisania roli na poziomie zasobu, należy ustawić `scope` Właściwość przypisanie roli na nazwę zasobu.

Poniższy szablon demonstruje:

- Jak utworzyć nowe konto magazynu
- Jak przypisać rolę do użytkownika, grupy lub aplikacji w zakresie konta magazynu
- Jak określić role właściciela, współautora i czytnika jako parametr

Aby użyć szablonu, należy określić następujące dane wejściowe:

- Identyfikator użytkownika, grupy, tożsamości zarządzanej lub aplikacji, do której ma zostać przypisana rola

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "scope": "[concat('Microsoft.Storage/storageAccounts', '/', variables('storageName'))]",
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

Aby wdrożyć poprzedni szablon, Użyj poleceń grupy zasobów. Oto przykład polecenia [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create) , aby rozpocząć wdrażanie w zakresie zasobów.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Poniżej przedstawiono przykład przypisania roli współautor do użytkownika dla konta magazynu po wdrożeniu szablonu.

![Przypisanie roli w zakresie zasobów](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Nowa nazwa główna usługi

Jeśli utworzysz nową nazwę główną usługi i natychmiast spróbujesz przypisać rolę do tej jednostki usługi, w niektórych przypadkach przypisanie roli może zakończyć się niepowodzeniem. Jeśli na przykład utworzysz nową tożsamość zarządzaną, a następnie spróbujesz przypisać rolę do tej jednostki usługi w tym samym szablonie Azure Resource Manager, przypisanie roli może zakończyć się niepowodzeniem. Przyczyną tego błędu jest prawdopodobnie opóźnienie replikacji. Nazwa główna usługi jest tworzona w jednym regionie; jednak przypisanie roli może wystąpić w innym regionie, który jeszcze nie replikuje jednostki usługi.

Aby rozwiązać ten scenariusz, należy ustawić `principalType` Właściwość na `ServicePrincipal` przy tworzeniu przypisania roli. Należy również ustawić `apiVersion` przypisanie roli do `2018-09-01-preview` lub nowszego.

Poniższy szablon demonstruje:

- Jak utworzyć nową nazwę główną usługi tożsamości zarządzanej
- Jak określić `principalType`
- Jak przypisać rolę współautor do tej nazwy głównej usługi w zakresie grupy zasobów

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

Oto przykład polecenia [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create) , aby uruchomić wdrożenie w zakresie grupy zasobów.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Poniżej przedstawiono przykład przypisania roli współautor do nowej jednostki usługi tożsamości zarządzanej po wdrożeniu szablonu.

![Przypisanie roli dla nowej nazwy głównej usługi tożsamości zarządzanej](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli

W celu usunięcia dostępu do zasobu platformy Azure w usłudze Azure RBAC należy usunąć przypisanie roli. Nie można usunąć przypisania roli przy użyciu szablonu. Aby usunąć przypisanie roli, należy użyć innych narzędzi, takich jak:

- [Azure Portal](role-assignments-portal.md#remove-a-role-assignment)
- [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)
- [Interfejs wiersza polecenia platformy Azure](role-assignments-cli.md#remove-a-role-assignment)
- [Interfejs API REST](role-assignments-rest.md#remove-a-role-assignment)

## <a name="next-steps"></a>Następne kroki

- [Szybki Start: Tworzenie i wdrażanie szablonów ARM przy użyciu Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Opis struktury i składni szablonów usługi ARM](../azure-resource-manager/templates/template-syntax.md)
- [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
