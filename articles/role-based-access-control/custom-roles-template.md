---
title: Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu szablonu Azure Resource Manager — RBAC na platformie Azure
description: Dowiedz się, jak tworzyć lub aktualizować role niestandardowe platformy Azure przy użyciu szablonu Azure Resource Manager (szablon ARM) i kontroli dostępu opartej na rolach (Azure RBAC).
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.custom: subject-armqs
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: beea0c5cecd7bb99973a4692a4cce17e7a69d708
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631316"
---
# <a name="create-or-update-azure-custom-roles-using-an-arm-template"></a>Tworzenie lub aktualizowanie ról niestandardowych platformy Azure przy użyciu szablonu ARM

Jeśli [wbudowane role platformy Azure](built-in-roles.md) nie są zgodne z konkretnymi potrzebami organizacji, możesz utworzyć własne [role niestandardowe](custom-roles.md). W tym artykule opisano, jak utworzyć lub zaktualizować rolę niestandardową przy użyciu szablonu Azure Resource Manager (szablon ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Aby utworzyć rolę niestandardową, należy określić nazwę roli, uprawnienia i lokalizację, w której może być używana rola. W tym artykule utworzysz rolę o nazwie _Custom role-RG Reader_ z uprawnieniami do zasobów, które można przypisać w zakresie subskrypcji lub niższym.

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsubscription-deployments%2Fcreate-role-def%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć rolę niestandardową, musisz mieć:

- Uprawnienia do tworzenia ról niestandardowych, takich jak [właściciel](built-in-roles.md#owner) lub [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym artykule pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/create-role-def). Szablon zawiera cztery parametry i sekcję Resources. Cztery parametry:

- Tablica akcji z wartością domyślną `["Microsoft.Resources/subscriptions/resourceGroups/read"]` .
- Tablica `notActions` z pustą wartością domyślną.
- Nazwa roli z wartością domyślną `Custom Role - RG Reader` .
- Opis roli z wartością domyślną `Subscription Level Deployment of a Role Definition` .

Zakres, do którego można przypisać tę rolę niestandardową, jest ustawiany na bieżącą subskrypcję.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

Zasób zdefiniowany w szablonie to:

- [Microsoft. Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wykonaj następujące kroki, aby wdrożyć poprzedni szablon.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Otwórz Azure Cloud Shell dla programu PowerShell.

1. Skopiuj i wklej następujący skrypt do Cloud Shell.

    ```azurepowershell-interactive
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"
    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. Wprowadź lokalizację wdrożenia, na przykład `centralus` .

1. Wprowadź listę akcji dla roli niestandardowej jako listę rozdzieloną przecinkami, taką jak `Microsoft.Resources/resources/read,Microsoft.Resources/subscriptions/resourceGroups/read` .

1. W razie potrzeby naciśnij klawisz ENTER, aby uruchomić `New-AzDeployment` polecenie.

    Polecenie [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) wdraża szablon, aby utworzyć rolę niestandardową.

    Wyświetlone dane wyjściowe powinny przypominać następujące dane:

    ```azurepowershell-interactive
    PS> New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions

    Id                      : /subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/azuredeploy
    DeploymentName          : azuredeploy
    Location                : centralus
    ProvisioningState       : Succeeded
    Timestamp               : 6/25/2020 8:08:32 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json
                              ContentVersion : 1.0.0.0

    Parameters              :
                              Name               Type                       Value
                              =================  =========================  ==========
                              actions            Array                      [
                                "Microsoft.Resources/resources/read",
                                "Microsoft.Resources/subscriptions/resourceGroups/read"
                              ]
                              notActions         Array                      []
                              roleName           String                     Custom Role - RG Reader
                              roleDescription    String                     Subscription Level Deployment of a Role Definition

    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Wykonaj następujące kroki, aby sprawdzić, czy rola niestandardowa została utworzona.

1. Uruchom polecenie [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) , aby wyświetlić listę roli niestandardowej.

    ```azurepowershell-interactive
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    Wyświetlone dane wyjściowe powinny przypominać następujące dane:

    ```azurepowershell-interactive
    {
      "Name": "Custom Role - RG Reader",
      "Id": "11111111-1111-1111-1111-111111111111",
      "IsCustom": true,
      "Description": "Subscription Level Deployment of a Role Definition",
      "Actions": [
        "Microsoft.Resources/resources/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId}"
      ]
    }
    ```

1. W Azure Portal Otwórz swoją subskrypcję.

1. W menu po lewej stronie wybierz pozycję **Kontrola dostępu (IAM)**.

1. Wybierz kartę **role** .

1. Ustaw listę **Typ** na **CustomRole**.

1. Sprawdź, czy na liście jest wyświetlana rola **niestandardowa rola czytnika RG** .

   ![Nowa rola niestandardowa w Azure Portal](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="update-a-custom-role"></a>Aktualizacja roli niestandardowej

Podobnie jak w przypadku tworzenia roli niestandardowej, można zaktualizować istniejącą rolę niestandardową przy użyciu szablonu. Aby zaktualizować rolę niestandardową, należy określić rolę, która ma zostać zaktualizowana.

Poniżej przedstawiono zmiany, które należy wprowadzić w poprzednim szablonie szybkiego startu, aby zaktualizować rolę niestandardową.

- Dołącz identyfikator roli jako parametr.
    ```json
        ...
        "roleDefName": {
          "type": "string",
          "metadata": {
            "description": "ID of the role definition"
          }
        ...
    ```

- Dołącz parametr identyfikatora roli w definicji roli.

    ```json
      ...
      "resources": [
        {
          "type": "Microsoft.Authorization/roleDefinitions",
          "apiVersion": "2018-07-01",
          "name": "[parameters('roleDefName')]",
          "properties": {
            ...
    ```

Oto przykład sposobu wdrażania szablonu.

```azurepowershell
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
[string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
$actions = $actions.Split(',')
$roleDefName = Read-Host -Prompt "Enter the role ID to update"
$templateFile = "rg-reader-update.json"
New-AzDeployment -Location $location -TemplateFile $templateFile -actions $actions -roleDefName $roleDefName
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć rolę niestandardową, wykonaj następujące kroki.

1. Uruchom następujące polecenie, aby usunąć rolę niestandardową.

    ```azurepowershell-interactive
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. Wprowadź wartość **Y** , aby potwierdzić, że chcesz usunąć rolę niestandardową.

## <a name="next-steps"></a>Następne kroki

- [Informacje o definicjach ról platformy Azure](role-definitions.md)
- [Szybki Start: Dodawanie przypisania roli platformy Azure przy użyciu szablonu Azure Resource Manager](quickstart-role-assignments-template.md)
- [Dokumentacja szablonów usługi ARM](../azure-resource-manager/templates/index.yml)
