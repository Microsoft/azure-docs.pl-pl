---
title: Przypisywanie ról platformy Azure przy użyciu Azure Resource Manager szablonów — RBAC platformy Azure
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure użytkownikom, grupom, jednostkom usługi lub tożsamościom zarządzanym przy użyciu szablonów usługi Azure Resource Manager i kontroli dostępu na podstawie ról (RBAC) platformy Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/21/2021
ms.author: rolyon
ms.openlocfilehash: ba1df23b40de82a8ef901541884ef29ea0b504a1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771879"
---
# <a name="assign-azure-roles-using-azure-resource-manager-templates"></a>Przypisywanie ról platformy Azure przy użyciu Azure Resource Manager szablonów

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)]Oprócz korzystania z usługi Azure PowerShell interfejsu wiersza polecenia platformy Azure można przypisywać role przy [użyciu Azure Resource Manager szablonów.](../azure-resource-manager/templates/template-syntax.md) Szablony mogą być przydatne, jeśli trzeba wdrażać zasoby spójnie i wielokrotnie. W tym artykule opisano sposób przypisywania ról przy użyciu szablonów.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="get-object-ids"></a>Uzyskiwanie identyfikatorów obiektów

Aby przypisać rolę, musisz określić identyfikator użytkownika, grupy lub aplikacji, do której chcesz przypisać rolę. Identyfikator ma format: `11111111-1111-1111-1111-111111111111` . Identyfikator można uzyskać przy użyciu interfejsu wiersza Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

### <a name="user"></a>Użytkownik

Aby uzyskać identyfikator użytkownika, możesz użyć poleceń [Get-AzADUser](/powershell/module/az.resources/get-azaduser) lub [az ad user show.](/cli/azure/ad/user#az_ad_user_show)

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Group (Grupa)

Aby uzyskać identyfikator grupy, możesz użyć poleceń [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) lub [az ad group show.](/cli/azure/ad/group#az_ad_group_show)

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="managed-identities"></a>Tożsamości zarządzane

Aby uzyskać identyfikator tożsamości zarządzanej, możesz użyć poleceń [Get-AzAdServiceprincipal](/powershell/module/az.resources/get-azadserviceprincipal) lub [az ad sp.](/cli/azure/ad/sp)

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName <Azure resource name>).id
```

```azurecli
objectid=$(az ad sp list --display-name <Azure resource name> --query [].objectId --output tsv)
```

### <a name="application"></a>Aplikacja

Aby uzyskać identyfikator jednostki usługi (tożsamości używanej przez aplikację), możesz użyć poleceń [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) lub [az ad sp list.](/cli/azure/ad/sp#az_ad_sp_list) W przypadku jednostki usługi użyj identyfikatora obiektu, a **nie** identyfikatora aplikacji.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="assign-an-azure-role"></a>Przypisywanie roli platformy Azure

Aby udzielić dostępu, na platformie Azure RBAC należy przypisać rolę.

### <a name="resource-group-scope-without-parameters"></a>Zakres grupy zasobów (bez parametrów)

Poniższy szablon przedstawia podstawowy sposób przypisywania roli. Niektóre wartości są określone w szablonie. Poniższy szablon przedstawia następujące elementy:

-  Jak przypisać rolę [Czytelnik](built-in-roles.md#reader) do użytkownika, grupy lub aplikacji w zakresie grupy zasobów

Aby użyć szablonu, należy wykonać następujące czynności:

- Tworzenie nowego pliku JSON i kopiowanie szablonu
- Zastąp `<your-principal-id>` identyfikatorem użytkownika, grupy, tożsamości zarządzanej lub aplikacji, do których chcesz przypisać rolę

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

Poniżej znajdują się przykładowe [polecenia New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) umożliwiające rozpoczęcie wdrażania w grupie zasobów o nazwie ExampleGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json
```

Poniżej przedstawiono przykład przypisania roli Czytelnik do użytkownika dla grupy zasobów po wdrożeniu szablonu.

![Przypisanie roli w zakresie grupy zasobów](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription-scope"></a>Zakres grupy zasobów lub subskrypcji

Poprzedni szablon nie jest zbyt elastyczny. Poniższy szablon używa parametrów i może być używany w różnych zakresach. Poniższy szablon przedstawia następujące elementy:

- Jak przypisać rolę do użytkownika, grupy lub aplikacji w grupie zasobów lub w zakresie subskrypcji
- Jak określić rolę właściciela, współautora i czytelnika jako parametr

Aby użyć szablonu, należy określić następujące dane wejściowe:

- Identyfikator użytkownika, grupy, tożsamości zarządzanej lub aplikacji, do których ma zostać przypisana rola
- Unikatowy identyfikator, który będzie używany do przypisania roli. Można też użyć identyfikatora domyślnego

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
> Ten szablon nie jest idempotentny, chyba że ta sama wartość jest dostarczana `roleNameGuid` jako parametr dla każdego wdrożenia szablonu. Jeśli nie zostanie podany, domyślnie nowy identyfikator GUID jest generowany dla każdego wdrożenia i kolejne wdrożenia nie powiodą `roleNameGuid` się z `Conflict: RoleAssignmentExists` błędem.

Zakres przypisania roli zależy od poziomu wdrożenia. Poniżej znajdują się [przykładowe polecenia New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) umożliwiające rozpoczęcie wdrażania w zakresie grupy zasobów.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Oto przykładowe [polecenia New-AzDeployment](/powershell/module/az.resources/new-azdeployment) i [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create) umożliwiające rozpoczęcie wdrażania w zakresie subskrypcji i określenie lokalizacji.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment sub create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource-scope"></a>Zakres zasobu

Jeśli musisz przypisać rolę na poziomie zasobu, ustaw właściwość przypisania roli `scope` na nazwę zasobu.

Poniższy szablon przedstawia następujące elementy:

- Jak utworzyć nowe konto magazynu
- Jak przypisać rolę do użytkownika, grupy lub aplikacji w zakresie konta magazynu
- Jak określić role Właściciel, Współautor i Czytelnik jako parametr

Aby użyć szablonu, należy określić następujące dane wejściowe:

- Identyfikator użytkownika, grupy, tożsamości zarządzanej lub aplikacji, do których ma zostać przypisana rola

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

Aby wdrożyć poprzedni szablon, użyj poleceń grupy zasobów. Poniżej znajdują się [przykładowe polecenia New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) umożliwiające rozpoczęcie wdrażania w zakresie zasobów.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Poniżej przedstawiono przykład przypisania roli Współautor do użytkownika dla konta magazynu po wdrożeniu szablonu.

![Przypisanie roli w zakresie zasobów](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Nowa jednostka usługi

Jeśli utworzysz nową jednostkę usługi i natychmiast spróbujesz przypisać rolę do tej jednostki usługi, w niektórych przypadkach przypisanie roli może się nie powieść. Jeśli na przykład utworzysz nową tożsamość zarządzaną, a następnie spróbujesz przypisać rolę do tej jednostki usługi w tym samym szablonie Azure Resource Manager, przypisanie roli może się nie powieść. Przyczyną tego błędu jest prawdopodobnie opóźnienie replikacji. Jednostkę usługi tworzy się w jednym regionie. Jednak przypisanie roli może wystąpić w innym regionie, który nie zreplikował jeszcze jednostki usługi.

Aby rozwiązać ten scenariusz, należy ustawić `principalType` właściwość na podczas tworzenia przypisania `ServicePrincipal` roli. Należy również ustawić przypisanie roli `apiVersion` na lub `2018-09-01-preview` nowsze.

Poniższy szablon przedstawia następujące elementy:

- Jak utworzyć nową jednostkę usługi tożsamości zarządzanej
- Jak określić `principalType`
- Jak przypisać rolę Współautor do tej jednostki usługi w zakresie grupy zasobów

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
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Poniżej znajdują się przykładowe [polecenia New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) i [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) umożliwiające rozpoczęcie wdrażania w zakresie grupy zasobów.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Poniżej przedstawiono przykład przypisania roli Współautor do nowej jednostki usługi tożsamości zarządzanej po wdrożeniu szablonu.

![Przypisanie roli dla nowej jednostki usługi tożsamości zarządzanej](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Następne kroki

- [Szybki start: tworzenie i wdrażanie szablonów usługi ARM przy użyciu Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Opis struktury i składni szablonów usługi ARM](../azure-resource-manager/templates/template-syntax.md)
- [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
