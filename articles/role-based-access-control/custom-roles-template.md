---
title: Tworzenie roli niestandardowej platformy Azure przy użyciu szablonu Azure Resource Manager — RBAC na platformie Azure
description: Dowiedz się, jak utworzyć rolę niestandardową platformy Azure przy użyciu szablonów Azure Resource Manager i kontroli dostępu opartej na rolach (RBAC) na platformie Azure.
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.custom: subject-armqs
ms.workload: identity
ms.date: 06/25/2020
ms.author: rolyon
ms.openlocfilehash: bcf1966ffc326291448cb611d99390fe0d652151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85392981"
---
# <a name="create-an-azure-custom-role-using-an-azure-resource-manager-template"></a>Tworzenie roli niestandardowej platformy Azure przy użyciu szablonu Azure Resource Manager

Jeśli [wbudowane role platformy Azure](built-in-roles.md) nie są zgodne z konkretnymi potrzebami organizacji, możesz utworzyć własne [role niestandardowe](custom-roles.md). W tym artykule opisano sposób tworzenia roli niestandardowej przy użyciu szablonu Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć rolę niestandardową, musisz mieć:

- Uprawnienia do tworzenia ról niestandardowych, takie jak [Właściciel](built-in-roles.md#owner) lub [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator)

## <a name="create-a-custom-role"></a>Tworzenie roli niestandardowej

Aby utworzyć rolę niestandardową, należy określić nazwę roli, uprawnienia i lokalizację, w której może być używana rola. W tym artykule utworzysz rolę o nazwie "Custom role-RG Reader" z uprawnieniami do zasobów, które można przypisać w zakresie subskrypcji lub niższym.

### <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym artykule pochodzi z [szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-role-def). Szablon zawiera cztery parametry i sekcję Resources. Cztery parametry:

- Tablica akcji z wartością domyślną ["Microsoft. resources/subscriptions/resourceGroups/Read"]
- Tablica nostąps z pustą wartością domyślną
- Nazwa roli z wartością domyślną "Custom role-RG Reader"
- Opis roli z wartością domyślną "wdrożenie poziomu subskrypcji definicji roli"

Zasób zdefiniowany w szablonie to:

- [Microsoft. Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

Zakres, do którego można przypisać tę rolę niestandardową, jest ustawiany na bieżącą subskrypcję.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

### <a name="deploy-the-template"></a>Wdrażanie szablonu

Wykonaj następujące kroki, aby wdrożyć poprzedni szablon.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Otwórz Azure Cloud Shell dla programu PowerShell.

1. Skopiuj i wklej następujący skrypt do Cloud Shell.

    ```azurepowershell
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')

    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"

    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. Wprowadź lokalizację wdrożenia, na przykład *centralną*.

1. Wprowadź listę akcji dla roli niestandardowej jako listę rozdzieloną przecinkami, taką jak *Microsoft. resources/Resources/Read, Microsoft. resources/subscriptions/resourceGroups/Read*.

1. W razie potrzeby naciśnij klawisz ENTER, aby uruchomić polecenie New-AzDeployment.

    Polecenie [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) wdraża szablon, aby utworzyć rolę niestandardową.

    Wyświetlone dane wyjściowe powinny przypominać następujące dane:

    ```azurepowershell
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

    ```azurepowershell
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    Wyświetlone dane wyjściowe powinny przypominać następujące dane:

    ```azurepowershell
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

1. W menu po lewej stronie kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **role** .

1. Ustaw listę **Typ** na **CustomRole**.

1. Sprawdź, czy na liście jest wyświetlana rola **niestandardowa rola czytnika RG** .

   ![Nowa rola niestandardowa w Azure Portal](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć rolę niestandardową, wykonaj następujące kroki.

1. Uruchom następujące polecenie, aby usunąć rolę niestandardową.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. Wprowadź wartość **Y** , aby potwierdzić, że chcesz usunąć rolę niestandardową.

## <a name="next-steps"></a>Następne kroki

- [Informacje o definicjach ról platformy Azure](role-definitions.md)
- [Szybki Start: Dodawanie przypisania roli platformy Azure przy użyciu szablonu Azure Resource Manager](quickstart-role-assignments-template.md)
- [Dokumentacja szablonów usługi ARM](../azure-resource-manager/templates/index.yml)
