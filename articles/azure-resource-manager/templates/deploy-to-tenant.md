---
title: Wdrażanie zasobów w dzierżawie
description: Opisuje sposób wdrażania zasobów w zakresie dzierżawy w szablonie Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 854ccbd43509b6c0b5a04357844c78c32b7e6396
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92668703"
---
# <a name="tenant-deployments-with-arm-templates"></a>Wdrożenia dzierżawców przy użyciu szablonów ARM

W miarę dojrzewania organizacji może być konieczne zdefiniowanie i przypisanie [zasad](../../governance/policy/overview.md) lub [kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/overview.md) w ramach dzierżawy usługi Azure AD. Szablony poziomu dzierżawców umożliwiają deklaratywne stosowanie zasad i przypisywanie ról na poziomie globalnym.

## <a name="supported-resources"></a>Obsługiwane zasoby

Nie wszystkie typy zasobów można wdrożyć na poziomie dzierżawy. W tej sekcji przedstawiono typy zasobów, które są obsługiwane.

W przypadku zasad platformy Azure Użyj:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

W przypadku kontroli dostępu opartej na rolach platformy Azure (RBAC) Użyj:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

W przypadku szablonów zagnieżdżonych wdrażanych w grupach zarządzania, subskrypcjach lub grupach zasobów należy użyć:

* [komputerów](/azure/templates/microsoft.resources/deployments)

Aby utworzyć grupy zarządzania, należy użyć:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

Aby zarządzać kosztami, użyj:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [wskazówek](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

## <a name="schema"></a>Schemat

Schemat używany do wdrożeń dzierżawców różni się od schematu dla wdrożeń grup zasobów.

W przypadku szablonów Użyj:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    ...
}
```

Schemat pliku parametrów jest taki sam dla wszystkich zakresów wdrożenia. W przypadku plików parametrów należy użyć:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="required-access"></a>Wymagany dostęp

Podmiot zabezpieczeń wdrażający szablon musi mieć uprawnienia do tworzenia zasobów w zakresie dzierżawy. Podmiot zabezpieczeń musi mieć uprawnienia do wykonywania akcji wdrażania ( `Microsoft.Resources/deployments/*` ) oraz do tworzenia zasobów zdefiniowanych w szablonie. Na przykład, aby utworzyć grupę zarządzania, podmiot zabezpieczeń musi mieć uprawnienie współautor w zakresie dzierżawy. Aby utworzyć przypisania ról, podmiot zabezpieczeń musi mieć uprawnienia właściciela.

Administrator globalny Azure Active Directory nie ma automatycznie uprawnienia do przypisywania ról. Aby włączyć wdrożenia szablonów w zakresie dzierżawy, Administrator globalny musi wykonać następujące czynności:

1. Podnieś poziom dostępu do konta, aby administrator globalny mógł przypisywać role. Aby uzyskać więcej informacji, zobacz [Podnieś poziom dostępu do zarządzania wszystkimi subskrypcjami i grupami zarządzania platformy Azure](../../role-based-access-control/elevate-access-global-admin.md).

1. Przypisz właściciela lub współautora do podmiotu zabezpieczeń, który musi wdrożyć szablony.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

Podmiot zabezpieczeń ma teraz wymagane uprawnienia do wdrożenia szablonu.

## <a name="deployment-commands"></a>Polecenia wdrażania

Polecenia dla wdrożeń dzierżawców są inne niż polecenia dla wdrożeń grup zasobów.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

W przypadku interfejsu wiersza polecenia platformy Azure Użyj polecenia [AZ Deployment dzierżawca Create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aby uzyskać Azure PowerShell, użyj polecenie [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

---

Aby uzyskać bardziej szczegółowe informacje na temat poleceń wdrażania i opcji wdrażania szablonów ARM, zobacz:

* [Wdrażanie zasobów za pomocą szablonów ARM i Azure Portal](deploy-portal.md)
* [Wdrażanie zasobów za pomocą szablonów ARM i interfejsu wiersza polecenia platformy Azure](deploy-cli.md)
* [Wdrażanie zasobów za pomocą szablonów ARM i Azure PowerShell](deploy-powershell.md)
* [Wdrażanie zasobów za pomocą szablonów ARM i interfejsu API REST Azure Resource Manager](deploy-rest.md)
* [Użyj przycisku wdrożenia, aby wdrożyć szablony z repozytorium GitHub](deploy-to-azure-button.md)
* [Wdrażanie szablonów usługi ARM na podstawie Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Zakresy wdrożenia

Podczas wdrażania w grupie zarządzania można wdrożyć zasoby w programie:

* Dzierżawca
* grupy zarządzania w ramach dzierżawy
* opłaty
* grupy zasobów (do dwóch wdrożeń zagnieżdżonych)
* [zasoby rozszerzeń](scope-extension-resources.md) można stosować do zasobów

Użytkownik wdrażający szablon musi mieć dostęp do określonego zakresu.

W tej sekcji pokazano, jak określić różne zakresy. Można połączyć te różne zakresy w jednym szablonie.

### <a name="scope-to-tenant"></a>Zakres do dzierżawy

Zasoby zdefiniowane w sekcji zasobów szablonu są stosowane do dzierżawcy.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>Zakres w grupie zarządzania

Aby wskazać grupę zarządzania w ramach dzierżawy, Dodaj wdrożenie zagnieżdżone i określ `scope` Właściwość.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,22":::

### <a name="scope-to-subscription"></a>Zakres subskrypcji

Możesz również kierować subskrypcje do subskrypcji w ramach dzierżawy. Użytkownik wdrażający szablon musi mieć dostęp do określonego zakresu.

Aby docelowa była subskrypcja w ramach dzierżawy, należy użyć wdrożenia zagnieżdżonego i `subscriptionId` właściwości.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="10,18":::

## <a name="deployment-location-and-name"></a>Lokalizacja i nazwa wdrożenia

W przypadku wdrożeń na poziomie dzierżawy należy podać lokalizację wdrożenia. Lokalizacja wdrożenia jest oddzielona od lokalizacji wdrażanych zasobów. Lokalizacja wdrożenia określa miejsce przechowywania danych wdrożenia.

Możesz podać nazwę wdrożenia lub użyć domyślnej nazwy wdrożenia. Nazwa domyślna to nazwa pliku szablonu. Na przykład wdrożenie szablonu o nazwie **azuredeploy.jsw** programie tworzy domyślną nazwę wdrożenia **azuredeploy** .

Dla każdej nazwy wdrożenia lokalizacja jest niezmienna. Nie można utworzyć wdrożenia w jednej lokalizacji, gdy istnieje wdrożenie o tej samej nazwie w innej lokalizacji. Jeśli zostanie wyświetlony kod błędu `InvalidDeploymentLocation` , użyj innej nazwy lub tej samej lokalizacji co poprzednie wdrożenie dla tej nazwy.

## <a name="create-management-group"></a>Tworzenie grupy zarządzania

[Poniższy szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/new-mg) tworzy grupę zarządzania.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>Przypisywanie roli

[Poniższy szablon](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/tenant-role-assignment) przypisuje rolę w zakresie dzierżawy.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o przypisywaniu ról, zobacz [Dodawanie przypisań ról platformy Azure przy użyciu szablonów Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Szablony można także wdrażać na poziomie [subskrypcji](deploy-to-subscription.md) lub [grupy zarządzania](deploy-to-management-group.md).
