---
title: Wdrażanie zasobów w dzierżawie
description: Opisuje sposób wdrażania zasobów w zakresie dzierżawy w szablonie Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 48b3fbcedb119ae699624e79f83297f4ecbc9ede
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372395"
---
# <a name="create-resources-at-the-tenant-level"></a>Tworzenie zasobów na poziomie dzierżawy

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
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

Schemat pliku parametrów jest taki sam dla wszystkich zakresów wdrożenia. W przypadku plików parametrów należy użyć:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
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

## <a name="deployment-scopes"></a>Zakresy wdrożenia

Podczas wdrażania w dzierżawie można kierować do dzierżawców lub grup zarządzania, subskrypcji i grup zasobów w dzierżawie. Użytkownik wdrażający szablon musi mieć dostęp do określonego zakresu.

Zasoby zdefiniowane w sekcji zasobów szablonu są stosowane do dzierżawcy.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

Aby wskazać grupę zarządzania w ramach dzierżawy, Dodaj wdrożenie zagnieżdżone i określ `scope` Właściwość.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,22":::

## <a name="deployment-commands"></a>Polecenia wdrażania

Polecenia dla wdrożeń dzierżawców są inne niż polecenia dla wdrożeń grup zasobów.

W przypadku interfejsu wiersza polecenia platformy Azure Użyj polecenia [AZ Deployment dzierżawca Create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

Aby uzyskać Azure PowerShell, użyj polecenie [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

W przypadku interfejsu API REST Użyj [wdrożeń — Utwórz lub zaktualizuj w zakresie dzierżawy](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Lokalizacja i nazwa wdrożenia

W przypadku wdrożeń na poziomie dzierżawy należy podać lokalizację wdrożenia. Lokalizacja wdrożenia jest oddzielona od lokalizacji wdrażanych zasobów. Lokalizacja wdrożenia określa miejsce przechowywania danych wdrożenia.

Możesz podać nazwę wdrożenia lub użyć domyślnej nazwy wdrożenia. Nazwa domyślna to nazwa pliku szablonu. Na przykład wdrożenie szablonu o nazwie **azuredeploy.jsw** programie tworzy domyślną nazwę wdrożenia **azuredeploy**.

Dla każdej nazwy wdrożenia lokalizacja jest niezmienna. Nie można utworzyć wdrożenia w jednej lokalizacji, gdy istnieje wdrożenie o tej samej nazwie w innej lokalizacji. Jeśli zostanie wyświetlony kod błędu `InvalidDeploymentLocation` , użyj innej nazwy lub tej samej lokalizacji co poprzednie wdrożenie dla tej nazwy.

## <a name="use-template-functions"></a>Korzystanie z funkcji szablonu

W przypadku wdrożeń dzierżawców istnieją pewne ważne zagadnienia dotyczące korzystania z funkcji szablonu:

* Funkcja [przesourceing ()](template-functions-resource.md#resourcegroup) **nie** jest obsługiwana.
* Funkcja [Subscription ()](template-functions-resource.md#subscription) **nie** jest obsługiwana.
* Obsługiwane są funkcje [Reference ()](template-functions-resource.md#reference) i [list ()](template-functions-resource.md#list) .
* Nie należy używać [ResourceID ()](template-functions-resource.md#resourceid) w celu uzyskania identyfikatora zasobu dla zasobów wdrożonych na poziomie dzierżawy.

  Zamiast tego należy użyć funkcji [tenantResourceId ()](template-functions-resource.md#tenantresourceid) .

  Na przykład aby uzyskać identyfikator zasobu dla wbudowanej definicji zasad, użyj:

  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```

  Identyfikator zwróconego zasobu ma następujący format:

  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

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
