---
title: Wdrażanie zasobów w dzierżawie
description: Opisuje sposób wdrażania zasobów w zakresie dzierżawy w Azure Resource Manager szablonu.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 0b17b8741d1701720de86d8039be3b6cd28ace5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781905"
---
# <a name="tenant-deployments-with-arm-templates"></a>Wdrożenia dzierżawy z szablonami usługi ARM

W trakcie rozwoju organizacji może być konieczne [](../../governance/policy/overview.md) zdefiniowanie i przypisanie zasad lub kontroli dostępu na podstawie ról [(RBAC)](../../role-based-access-control/overview.md) platformy Azure w dzierżawie usługi Azure AD. Za pomocą szablonów na poziomie dzierżawy można deklaratywnie stosować zasady i przypisywać role na poziomie globalnym.

## <a name="supported-resources"></a>Obsługiwane zasoby

Nie wszystkie typy zasobów można wdrożyć na poziomie dzierżawy. W tej sekcji wymieniono obsługiwane typy zasobów.

W przypadku zasad platformy Azure użyj:

* [policyAssignments (przypisania zasad)](/azure/templates/microsoft.authorization/policyassignments)
* [Policydefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

W przypadku kontroli dostępu opartej na rolach (RBAC) na platformie Azure użyj:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

W przypadku zagnieżdżonych szablonów wdrażanych w grupach zarządzania, subskrypcjach lub grupach zasobów użyj:

* [Wdrożeń](/azure/templates/microsoft.resources/deployments)

Do tworzenia grup zarządzania użyj:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

Aby utworzyć subskrypcje, użyj:

* [Aliasy](/azure/templates/microsoft.subscription/aliases)

Aby zarządzać kosztami, użyj:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [Instrukcje](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

Aby skonfigurować portal, użyj:

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

## <a name="schema"></a>Schemat

Schemat wdrożeń dzierżawy różni się od schematu wdrożeń grup zasobów.

W przypadku szablonów użyj:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    ...
}
```

Schemat pliku parametrów jest taki sam dla wszystkich zakresów wdrożenia. W przypadku plików parametrów użyj:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="required-access"></a>Wymagany dostęp

Podmiot zabezpieczeń wdrażający szablon musi mieć uprawnienia do tworzenia zasobów w zakresie dzierżawy. Podmiot zabezpieczeń musi mieć uprawnienia do wykonywania akcji wdrażania ( ) i do `Microsoft.Resources/deployments/*` tworzenia zasobów zdefiniowanych w szablonie. Aby na przykład utworzyć grupę zarządzania, podmiot zabezpieczeń musi mieć uprawnienie Współautor w zakresie dzierżawy. Aby utworzyć przypisania ról, podmiot zabezpieczeń musi mieć uprawnienia właściciela.

Administrator globalny aplikacji Azure Active Directory automatycznie nie ma uprawnień do przypisywania ról. Aby włączyć wdrożenia szablonów w zakresie dzierżawy, administrator globalny musi wykonać następujące czynności:

1. Podnieś uprawnienia dostępu do konta, aby administrator globalny mógł przypisywać role. Aby uzyskać więcej informacji, zobacz [Podniesienie dostępu w celu zarządzania wszystkimi subskrypcjami i](../../role-based-access-control/elevate-access-global-admin.md)grupami zarządzania platformy Azure.

1. Przypisz właściciela lub współautora do podmiotu zabezpieczeń, który musi wdrożyć szablony.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

Podmiot zabezpieczeń ma teraz uprawnienia wymagane do wdrożenia szablonu.

## <a name="deployment-commands"></a>Polecenia wdrażania

Polecenia dla wdrożeń dzierżawy różnią się od poleceń dla wdrożeń grup zasobów.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

W przypadku interfejsu wiersza polecenia platformy Azure użyj [polecenia az deployment tenant create:](/cli/azure/deployment/tenant#az_deployment_tenant_create)

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Na Azure PowerShell użyj [new-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

---

Aby uzyskać bardziej szczegółowe informacje na temat poleceń wdrażania i opcji wdrażania szablonów usługi ARM, zobacz:

* [Wdrażanie zasobów za pomocą szablonów usługi ARM i Azure Portal](deploy-portal.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi ARM i interfejsu wiersza polecenia platformy Azure](deploy-cli.md)
* [Wdrażanie zasobów za pomocą szablonów usługi ARM i Azure PowerShell](deploy-powershell.md)
* [Wdrażanie zasobów za pomocą szablonów usługi ARM i Azure Resource Manager API REST](deploy-rest.md)
* [Używanie przycisku wdrażania do wdrażania szablonów z repozytorium GitHub](deploy-to-azure-button.md)
* [Wdrażanie szablonów usługi ARM z Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Lokalizacja i nazwa wdrożenia

W przypadku wdrożeń na poziomie dzierżawy należy podać lokalizację wdrożenia. Lokalizacja wdrożenia jest oddzielona od lokalizacji wdrażanych zasobów. Lokalizacja wdrożenia określa miejsce przechowywania danych wdrożenia. [Wdrożenia](deploy-to-subscription.md) subskrypcji [i grup](deploy-to-management-group.md) zarządzania również wymagają lokalizacji. W [przypadku wdrożeń](deploy-to-resource-group.md) grup zasobów lokalizacja grupy zasobów jest używana do przechowywania danych wdrożenia.

Możesz podać nazwę wdrożenia lub użyć domyślnej nazwy wdrożenia. Domyślna nazwa to nazwa pliku szablonu. Na przykład wdrożenie szablonu o nazwie _azuredeploy.jsna powoduje_ utworzenie domyślnej nazwy wdrożenia **azuredeploy**.

Dla każdej nazwy wdrożenia lokalizacja jest niezmienna. Nie można utworzyć wdrożenia w jednej lokalizacji, jeśli istnieje wdrożenie o takiej samej nazwie w innej lokalizacji. Jeśli na przykład utworzysz wdrożenie dzierżawy o nazwie **deployment1** w lokalizacji **centralus**, nie możesz później utworzyć kolejnego wdrożenia o nazwie **deployment1,** ale w lokalizacji **westus**. Jeśli zostanie wyświetlany kod błędu , użyj dla tej nazwy innej nazwy lub tej samej lokalizacji co w poprzednim `InvalidDeploymentLocation` wdrożeniu.

## <a name="deployment-scopes"></a>Zakresy wdrożenia

Podczas wdrażania w dzierżawie można wdrożyć zasoby w:

* dzierżawy
* grupy zarządzania w ramach dzierżawy
* Subskrypcji
* grupy zasobów

Zasób [rozszerzenia może](scope-extension-resources.md) być ograniczony do obiektu docelowego, który jest inny niż cel wdrożenia.

Użytkownik wdrażający szablon musi mieć dostęp do określonego zakresu.

W tej sekcji przedstawiono sposób określania różnych zakresów. Te różne zakresy można połączyć w jednym szablonie.

### <a name="scope-to-tenant"></a>Zakres do dzierżawy

Zasoby zdefiniowane w sekcji zasobów szablonu są stosowane do dzierżawy.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>Zakres do grupy zarządzania

Aby określić docelową grupę zarządzania w dzierżawie, dodaj wdrożenie zagnieżdżone i określ `scope` właściwość .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Zakres do subskrypcji

Można również wybrać subskrypcje docelowe w ramach dzierżawy. Użytkownik wdrażający szablon musi mieć dostęp do określonego zakresu.

Aby ukierunkować subskrypcję w ramach dzierżawy, użyj zagnieżdżonych wdrożeń i `subscriptionId` właściwości .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Zakres do grupy zasobów

Można również kierować grupy zasobów w ramach dzierżawy. Użytkownik wdrażający szablon musi mieć dostęp do określonego zakresu.

Aby ukierunkować grupę zasobów w ramach dzierżawy, użyj wdrożenia zagnieżdżone. Ustaw właściwości `subscriptionId` `resourceGroup` i . Nie ustawiaj lokalizacji dla zagnieżdżonych wdrożeń, ponieważ jest ono wdrażane w lokalizacji grupy zasobów.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-rg.json" highlight="9,10,18":::

## <a name="create-management-group"></a>Tworzenie grupy zarządzania

Poniższy szablon tworzy grupę zarządzania.

:::code language="json" source="~/quickstart-templates/tenant-deployments/new-mg/azuredeploy.json":::

Jeśli Twoje konto nie ma uprawnień do wdrażania w dzierżawie, nadal możesz tworzyć grupy zarządzania, wdrażając je w innym zakresie. Aby uzyskać więcej informacji, zobacz [Grupa zarządzania](deploy-to-management-group.md#management-group).

## <a name="assign-role"></a>Przypisywanie roli

Poniższy szablon przypisuje rolę w zakresie dzierżawy.

:::code language="json" source="~/quickstart-templates/tenant-deployments/tenant-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat przypisywania ról, zobacz [Dodawanie przypisań ról platformy Azure przy użyciu Azure Resource Manager szablonów.](../../role-based-access-control/role-assignments-template.md)
* Szablony można również wdrażać na [poziomie subskrypcji](deploy-to-subscription.md) lub [grupy zarządzania.](deploy-to-management-group.md)
