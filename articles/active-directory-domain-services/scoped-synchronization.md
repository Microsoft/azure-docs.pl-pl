---
title: Synchronizacja z zakresami dla Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować synchronizację z zakresem z usługi Azure AD do domeny zarządzanej Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 5f2c823b0932db42876be6ab04ebcd82783729aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84734425"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Konfigurowanie synchronizacji z zakresem z usługi Azure AD do Azure Active Directory Domain Services

Aby zapewnić usługi uwierzytelniania, Azure Active Directory Domain Services (Azure AD DS) synchronizuje użytkowników i grupy z usługi Azure AD. W środowisku hybrydowym Użytkownicy i grupy ze środowiska lokalnego Active Directory Domain Services (AD DS) można najpierw zsynchronizować z usługą Azure AD przy użyciu Azure AD Connect, a następnie zsynchronizowane z usługą Azure AD DS.

Domyślnie wszyscy użytkownicy i grupy z katalogu usługi Azure AD są synchronizowane z domeną zarządzaną AD DS platformy Azure. Jeśli masz określone potrzeby, możesz zamiast tego synchronizować tylko zdefiniowanego zestawu użytkowników.

W tym artykule opisano sposób tworzenia domeny zarządzanej, która używa synchronizacji w zakresie, a następnie zmieniania lub wyłączania zestawu użytkowników z zakresem.

## <a name="scoped-synchronization-overview"></a>Synchronizacja z zakresem — Omówienie

Domyślnie wszyscy użytkownicy i grupy z katalogu usługi Azure AD są synchronizowane z domeną zarządzaną. Jeśli tylko kilku użytkowników potrzebuje dostępu do domeny zarządzanej, można synchronizować tylko te konta użytkowników. Ta synchronizacja w zakresie jest oparta na grupach. Podczas konfigurowania synchronizacji z zakresem opartym na grupach tylko konta użytkowników należące do określonych grup są synchronizowane z domeną zarządzaną.

W poniższej tabeli opisano sposób używania synchronizacji w zakresie:

| Bieżący stan | Żądany stan | Wymagana konfiguracja |
| --- | --- | --- |
| Istniejąca domena zarządzana jest skonfigurowana do synchronizacji wszystkich kont użytkowników i grup. | Chcesz synchronizować tylko konta użytkowników należące do określonych grup. | Nie można zmienić synchronizowania wszystkich użytkowników w celu korzystania z synchronizacji z zakresem. [Usuń istniejącą domenę zarządzaną](delete-aadds.md), a następnie wykonaj kroki opisane w tym artykule, aby ponownie utworzyć domenę zarządzaną z skonfigurowaną synchronizacją o określonym zakresie. |
| Brak istniejącej domeny zarządzanej. | Chcesz utworzyć nową domenę zarządzaną i zsynchronizować tylko konta użytkowników należące do określonych grup. | Wykonaj kroki opisane w tym artykule, aby utworzyć domenę zarządzaną z skonfigurowaną synchronizacją o określonym zakresie. |
| Istniejąca domena zarządzana jest skonfigurowana tak, aby synchronizować tylko konta należące do określonych grup. | Chcesz zmodyfikować listę grup, których użytkownicy mają być zsynchronizowani z domeną zarządzaną. | Wykonaj kroki opisane w tym artykule, aby zmodyfikować synchronizację z zakresem. |

Użyj Azure Portal lub PowerShell, aby skonfigurować ustawienia synchronizacji z zakresem:

| Akcja | | |
|--|--|--|
| Tworzenie domeny zarządzanej i Konfigurowanie synchronizacji w zakresie | [Azure Portal](#enable-scoped-synchronization-using-the-azure-portal) | [Program PowerShell](#enable-scoped-synchronization-using-powershell) |
| Modyfikuj synchronizację w zakresie | [Azure Portal](#modify-scoped-synchronization-using-the-azure-portal) | [Program PowerShell](#modify-scoped-synchronization-using-powershell) |
| Wyłącz synchronizację z zakresem | [Azure Portal](#disable-scoped-synchronization-using-the-azure-portal) | [Program PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> Zmiana zakresu synchronizacji powoduje, że zarządzana domena ponownie zsynchronizuje wszystkie dane. Obowiązują następujące zastrzeżenia:
> 
>  * W przypadku zmiany zakresu synchronizacji dla domeny zarządzanej następuje pełna ponowna synchronizacja.
>  * Obiekty, które nie są już wymagane w domenie zarządzanej, są usuwane. Nowe obiekty są tworzone w domenie zarządzanej.
>  * Ponowna synchronizacja może zająć dużo czasu. Czas synchronizacji zależy od liczby obiektów, takich jak użytkownicy, grupy i członkostwa w grupach w domenie zarządzanej i w katalogu usługi Azure AD. W przypadku dużych katalogów z wieloma tysiącami obiektów ponowna synchronizacja może potrwać kilka dni.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Włącz synchronizację z zakresem przy użyciu Azure Portal

Aby włączyć synchronizację w zakresie w Azure Portal, wykonaj następujące czynności:

1. Postępuj zgodnie z [samouczkiem, aby utworzyć i skonfigurować domenę zarządzaną](tutorial-create-instance-advanced.md). Wykonaj wszystkie czynności opisane w sekcji wymagania wstępne i wdrożenia inne niż dla zakresu synchronizacji.
1. Wybierz **zakres** w kroku synchronizacji, a następnie wybierz grupy usługi Azure AD, które mają być synchronizowane z domeną zarządzaną.

Wdrożenie domeny zarządzanej może potrwać do godziny. W Azure Portal strona **Przegląd** dla domeny zarządzanej pokazuje bieżący stan w tym etapie wdrażania.

Gdy Azure Portal pokazuje, że domena zarządzana zakończyła Inicjowanie obsługi administracyjnej, należy wykonać następujące zadania:

* Zaktualizuj ustawienia DNS dla sieci wirtualnej, aby maszyny wirtualne mogły znaleźć domenę zarządzaną do przyłączania do domeny lub uwierzytelniania.
    * Aby skonfigurować serwer DNS, wybierz domenę zarządzaną w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne skonfigurowanie tych ustawień DNS.
* [Włącz synchronizację haseł w Azure AD Domain Services](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) , aby użytkownicy końcowi mogli logować się do domeny zarządzanej przy użyciu swoich poświadczeń firmowych.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Modyfikowanie synchronizacji w zakresie przy użyciu Azure Portal

Aby zmodyfikować listę grup, których użytkownicy powinni synchronizować z domeną zarządzaną, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD Domain Services**. Wybierz domenę zarządzaną, taką jak *aaddscontoso.com*.
1. Z menu po lewej stronie wybierz pozycję **Synchronizacja** .
1. Aby dodać grupę, wybierz pozycję **+ Wybierz grupy** u góry, a następnie wybierz grupy do dodania.
1. Aby usunąć grupę z zakresu synchronizacji, wybierz ją z listy aktualnie zsynchronizowanych grup i wybierz pozycję **Usuń grupy**.
1. Po wprowadzeniu wszystkich zmian wybierz pozycję **Zapisz zakres synchronizacji**.

Zmiana zakresu synchronizacji powoduje, że zarządzana domena ponownie zsynchronizuje wszystkie dane. Obiekty, które nie są już wymagane w domenie zarządzanej, są usuwane, a ponowna synchronizacja może zająć dużo czasu.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Wyłącz synchronizację z zakresem przy użyciu Azure Portal

Aby wyłączyć synchronizację z zakresem opartym na grupach dla domeny zarządzanej, wykonaj następujące czynności:

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD Domain Services**. Wybierz domenę zarządzaną, taką jak *aaddscontoso.com*.
1. Z menu po lewej stronie wybierz pozycję **Synchronizacja** .
1. Ustaw zakres synchronizacji z **zakresu** na **wszystkie**, a następnie wybierz pozycję **Zapisz zakres synchronizacji**.

Zmiana zakresu synchronizacji powoduje, że zarządzana domena ponownie zsynchronizuje wszystkie dane. Obiekty, które nie są już wymagane w domenie zarządzanej, są usuwane, a ponowna synchronizacja może zająć dużo czasu.

## <a name="powershell-script-for-scoped-synchronization"></a>Skrypt programu PowerShell dotyczący synchronizacji w zakresie

Aby skonfigurować synchronizację z zakresem przy użyciu programu PowerShell, najpierw Zapisz następujący skrypt do pliku o nazwie `Select-GroupsToSync.ps1` . Ten skrypt służy do konfigurowania usługi Azure AD DS do synchronizowania wybranych grup z usługi Azure AD. Wszystkie konta użytkowników, które są częścią określonych grup, są synchronizowane z domeną zarządzaną.

Ten skrypt jest używany w dodatkowych krokach przedstawionych w tym artykule.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="enable-scoped-synchronization-using-powershell"></a>Włącz synchronizację z zakresem przy użyciu programu PowerShell

Użyj programu PowerShell, aby wykonać poniższy zestaw kroków. Zapoznaj się z instrukcjami, aby [włączyć Azure Active Directory Domain Services przy użyciu programu PowerShell](powershell-create-instance.md). Kilka kroków tego artykułu jest nieco nieznacznie modyfikowanych w celu skonfigurowania synchronizacji z zakresem.

1. Wykonaj następujące zadania w artykule, aby włączyć usługę Azure AD DS przy użyciu programu PowerShell. Zatrzymaj w kroku, aby faktycznie utworzyć domenę zarządzaną. Należy skonfigurować synchronizację w zakresie utworzoną przez Ciebie domenę zarządzaną.

   * [Zainstaluj wymagane moduły programu PowerShell](powershell-create-instance.md#prerequisites).
   * [Utwórz wymaganą jednostkę usługi i grupę usługi Azure AD na potrzeby dostępu administracyjnego](powershell-create-instance.md#create-required-azure-ad-resources).
   * [Twórz pomocnicze zasoby platformy Azure, takie jak sieć wirtualna i podsieci](powershell-create-instance.md#create-supporting-azure-resources).

1. Określ grupy i użytkowników, których mają dotyczyć synchronizacja z usługi Azure AD. Utwórz listę nazw wyświetlanych grup do synchronizacji z usługą Azure AD DS.

1. Uruchom [skrypt z poprzedniej sekcji](#powershell-script-for-scoped-synchronization) i użyj parametru *-groupsToAdd* , aby przekazać listę grup do zsynchronizowania.

   > [!WARNING]
   > Należy uwzględnić grupę *administratorów kontrolera domeny usługi AAD* na liście grup dla synchronizacji z zakresem. Jeśli nie dołączysz tej grupy, domena zarządzana stanie się niezdatna do użytku.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Teraz Utwórz domenę zarządzaną i Włącz synchronizację z zakresem opartym na grupach. Dołącz *wartość "filteredSync" = "Enabled"* w parametrze *-Properties* .

    Ustaw identyfikator subskrypcji platformy Azure, a następnie podaj nazwę domeny zarządzanej, na przykład *aaddscontoso.com*. Identyfikator subskrypcji można uzyskać za pomocą polecenia cmdlet [Get-AzSubscription][Get-AzSubscription] . Ustaw nazwę grupy zasobów, nazwę sieci wirtualnej i region na wartości używane w poprzednich krokach, aby utworzyć pomocnicze zasoby platformy Azure:

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "aaddscontoso.com"
   $ResourceGroupName = "myResourceGroup"
   $VnetName = "myVnet"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -Force -Verbose
   ```

Utworzenie zasobu i zwrócenie kontroli do wiersza polecenia programu PowerShell trwa kilka minut. Domena zarządzana jest w dalszym ciągu obsługiwana w tle, a ukończenie wdrożenia może potrwać do godziny. W Azure Portal strona **Przegląd** dla domeny zarządzanej pokazuje bieżący stan w tym etapie wdrażania.

Gdy Azure Portal pokazuje, że domena zarządzana zakończyła Inicjowanie obsługi administracyjnej, należy wykonać następujące zadania:

* Zaktualizuj ustawienia DNS dla sieci wirtualnej, aby maszyny wirtualne mogły znaleźć domenę zarządzaną do przyłączania do domeny lub uwierzytelniania.
    * Aby skonfigurować serwer DNS, wybierz domenę zarządzaną w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne skonfigurowanie tych ustawień DNS.
* Jeśli utworzono domenę zarządzaną w regionie, który obsługuje Strefy dostępności, Utwórz sieciową grupę zabezpieczeń, aby ograniczyć ruch w sieci wirtualnej dla domeny zarządzanej. Tworzony jest standardowy moduł równoważenia obciążenia platformy Azure, który wymaga wprowadzenia tych reguł. Ta sieciowa Grupa zabezpieczeń zabezpiecza AD DS platformy Azure i jest wymagana do poprawnego działania domeny zarządzanej.
    * Aby utworzyć grupę zabezpieczeń sieci i wymagane reguły, wybierz domenę zarządzaną w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne utworzenie i skonfigurowanie sieciowej grupy zabezpieczeń.
* [Włącz synchronizację haseł w Azure AD Domain Services](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) , aby użytkownicy końcowi mogli logować się do domeny zarządzanej przy użyciu swoich poświadczeń firmowych.

## <a name="modify-scoped-synchronization-using-powershell"></a>Modyfikowanie synchronizacji w zakresie przy użyciu programu PowerShell

Aby zmodyfikować listę grup, których użytkownicy powinni synchronizować z domeną zarządzaną, uruchom ponownie [skrypt programu PowerShell](#powershell-script-for-scoped-synchronization) i określ nową listę grup. W poniższym przykładzie grupy do zsynchronizowania nie zawierają już *GroupName2*i teraz zawierają *GroupName3*.

> [!WARNING]
> Należy uwzględnić grupę *administratorów kontrolera domeny usługi AAD* na liście grup dla synchronizacji z zakresem. Jeśli nie dołączysz tej grupy, domena zarządzana stanie się niezdatna do użytku.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Zmiana zakresu synchronizacji powoduje, że zarządzana domena ponownie zsynchronizuje wszystkie dane. Obiekty, które nie są już wymagane w domenie zarządzanej, są usuwane, a ponowna synchronizacja może zająć dużo czasu.

## <a name="disable-scoped-synchronization-using-powershell"></a>Wyłącz synchronizację z zakresem przy użyciu programu PowerShell

Aby wyłączyć synchronizację z zakresem opartym na grupach dla domeny zarządzanej, ustaw wartość *"filteredSync" = "Disabled"* w zasobie AD DS platformy Azure, a następnie zaktualizuj domenę zarządzaną. Po zakończeniu wszyscy użytkownicy i grupy są ustawiani do synchronizacji z usługi Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Zmiana zakresu synchronizacji powoduje, że zarządzana domena ponownie zsynchronizuje wszystkie dane. Obiekty, które nie są już wymagane w domenie zarządzanej, są usuwane, a ponowna synchronizacja może zająć dużo czasu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o procesie synchronizacji, zobacz [Omówienie synchronizacji w Azure AD Domain Services](synchronization.md).

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
