---
title: Samouczek `:` Korzystanie z tożsamości zarządzanej w celu uzyskania dostępu do Azure Resource Manager-Windows-Azure AD
description: Samouczek przedstawiający proces użycia przypisanej przez użytkownika tożsamości zarządzanej na maszynie wirtualnej z systemem Windows do uzyskiwania dostępu do usługi Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9757a322922524f181b1fa3f48850efbb7a18dd
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546781"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>Samouczek: używanie tożsamości zarządzanej przypisanej przez użytkownika na maszynie wirtualnej z systemem Windows w celu uzyskania dostępu do Azure Resource Manager

W tym samouczku przedstawiono sposób tworzenia tożsamości przypisanej przez użytkownika, przypisywania jej do maszyny wirtualnej z systemem Windows, a następnie używania tej tożsamości w celu uzyskania dostępu do interfejsu API usługi Azure Resource Manager. Tożsamości usługi zarządzanej są zarządzane automatycznie przez platformę Azure. Umożliwiają one uwierzytelnianie do usług, które obsługują uwierzytelnianie usługi Azure AD bez konieczności osadzania poświadczeń w kodzie. 

Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie tożsamości zarządzanej przypisanej przez użytkownika
> * Przypisywanie tożsamości przypisanej przez użytkownika do maszyny wirtualnej z systemem Windows
> * Udzielanie tożsamości przypisanej przez użytkownika dostępu do grupy zasobów w usłudze Azure Resource Manager 
> * Uzyskiwanie tokenu dostępu przy użyciu tożsamości przypisanej przez użytkownika oraz używanie go do wywołania usługi Azure Resource Manager 
> * Odczytywanie właściwości grupy zasobów

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Zaloguj się do Azure Portal](https://portal.azure.com)

- [Tworzenie maszyny wirtualnej z systemem Windows](../../virtual-machines/windows/quick-create-portal.md)

- Aby przeprowadzić tworzenie wymaganych zasobów i wykonać kroki zarządzania rolami opisane w tym samouczku, Twoje konto musi mieć uprawnienia „Właściciel” w odpowiednim zakresie (subskrypcji lub grupy zasobów). Jeśli potrzebujesz pomocy dotyczącej przypisania roli, zobacz [Korzystanie z kontroli dostępu opartej na rolach do zarządzania dostępem do zasobów subskrypcji platformy Azure](../../role-based-access-control/role-assignments-portal.md).

- Do uruchomienia przykładowych skryptów są dostępne dwie opcje:
    - Użyj [Azure Cloud Shell](../../cloud-shell/overview.md), którą można otworzyć za pomocą przycisku **Wypróbuj** w prawym górnym rogu bloków kodu.
    - Uruchom skrypty lokalnie przy użyciu Azure PowerShell, zgodnie z opisem w następnej sekcji.

### <a name="configure-azure-powershell-locally"></a>Skonfiguruj lokalnie Azure PowerShell

Aby używać Azure PowerShell lokalnie w tym artykule (zamiast używać Cloud Shell), wykonaj następujące czynności:

1. Zainstaluj [najnowszą wersję programu Azure PowerShell](/powershell/azure/install-az-ps) , jeśli jeszcze tego nie zrobiono.

1. Zaloguj się do platformy Azure:

    ```azurepowershell
    Connect-AzAccount
    ```

1. Zainstaluj [najnowszą wersję modułu PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    `Exit`Po uruchomieniu tego polecenia w następnym kroku może być konieczne wyjście z bieżącej sesji programu PowerShell.

1. Zainstaluj wersję wstępną `Az.ManagedServiceIdentity` modułu, aby wykonać operacje zarządzanej tożsamości przypisane przez użytkownika w tym artykule:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="enable"></a>Włącz

W przypadku scenariusza opartego na tożsamości przypisanej do użytkownika należy wykonać następujące czynności:

- Tworzenie tożsamości
- Przypisz nowo utworzoną tożsamość

### <a name="create-identity"></a>Utwórz tożsamość

W tej sekcji przedstawiono sposób tworzenia tożsamości przypisanej do użytkownika. Tożsamość przypisana przez użytkownika jest tworzona jako autonomiczny zasób platformy Azure. Za pomocą polecenia [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity) platforma Azure tworzy tożsamość w dzierżawie usługi Azure AD, którą można przypisać do co najmniej jednego wystąpienia usługi platformy Azure.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

Odpowiedź zawiera szczegóły utworzonej tożsamości przypisanej przez użytkownika, podobne do poniższego przykładu. Zwróć uwagę na wartości `Id` i `ClientId` tożsamości przypisanej przez użytkownika, ponieważ są one używane w kolejnych krokach:

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

### <a name="assign-identity"></a>Przypisywanie tożsamości

W tej sekcji przedstawiono sposób przypisywania tożsamości przypisanej przez użytkownika do maszyny wirtualnej z systemem Windows. Tożsamość przypisana przez użytkownika może być używana przez klientów w obrębie wielu zasobów platformy Azure. Użyj poniższych poleceń, aby przypisać tożsamość przypisaną przez użytkownika do pojedynczej maszyny wirtualnej. Użyj właściwości `Id` zwróconej w poprzednim kroku dla parametru `-IdentityID`.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-access"></a>Udzielanie dostępu 

W tej sekcji pokazano, jak udzielić użytkownikowi dostępu do tożsamości przypisanej do grupy zasobów w Azure Resource Manager. Tożsamości zarządzane dla zasobów platformy Azure udostępniają tożsamości, których kod może używać do żądania tokenów dostępu w celu uwierzytelniania w interfejsach API zasobów obsługujących uwierzytelnianie usługi Azure AD. W tym samouczku kod będzie uzyskiwać dostęp do interfejsu API usługi Azure Resource Manager. 

Aby kod mógł uzyskać dostęp do interfejsu API, musisz udzielić dostępu tożsamości do zasobu w usłudze Azure Resource Manager. W tym przypadku jest to grupa zasobów, w której znajduje się maszyna wirtualna. Zaktualizuj wartość `<SUBSCRIPTION ID>` zgodnie z wymaganiami środowiska.

```azurepowershell-interactive
$spID = (Get-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

Odpowiedź zawiera szczegóły utworzonego przypisania roli, podobne do poniższego przykładu:

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="access-data"></a>Uzyskiwanie dostępu do danych

### <a name="get-an-access-token"></a>Pobranie tokenu dostępu 

W pozostałej części tego samouczka będziemy pracować z poziomu wcześniej utworzonej maszyny wirtualnej.

1. Zaloguj się do Azure Portal w [https://portal.azure.com](https://portal.azure.com)

2. W portalu przejdź do pozycji **Maszyny wirtualne**, a następnie przejdź do maszyny wirtualnej z systemem Windows i w pozycji **Przegląd** kliknij przycisk **Połącz**.

3. Wprowadź **nazwę użytkownika** i **hasło** użyte podczas tworzenia maszyny wirtualnej z systemem Windows.

4. Teraz, po utworzeniu **Podłączanie pulpitu zdalnego** z maszyną wirtualną, Otwórz program **PowerShell** w sesji zdalnej.

5. Używając polecenia `Invoke-WebRequest` programu PowerShell, wyślij żądanie do lokalnego punktu końcowego tożsamości zarządzanych dla zasobów platformy Azure, aby uzyskać token dostępu na potrzeby usługi Azure Resource Manager.  `client_id`Wartość jest wartością zwracaną podczas tworzenia tożsamości zarządzanej przypisanej przez użytkownika.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

### <a name="read-properties"></a>Właściwości odczytu

Użyj tokenu dostępu pobranego w poprzednim kroku, aby uzyskać dostęp do usługi Azure Resource Manager i odczytaj właściwości grupy zasobów, której udzielono dostępu tożsamości przypisanej przez użytkownika. Zastąp element `<SUBSCRIPTION ID>` identyfikatorem subskrypcji danego środowiska.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
Odpowiedź zawiera informacje o określonej grupie zasobów, podobne do poniższego przykładu:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia tożsamości przypisanej przez użytkownika i dołączenia jej do maszyny wirtualnej platformy Azure w celu uzyskania dostępu do interfejsu API Azure Resource Manager.  Aby dowiedzieć się więcej o usłudze Azure Resource Manager, zobacz:

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)
