---
title: Synchronizacja w zakresie przy użyciu programu PowerShell dla Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować synchronizację z zakresem z usługi Azure AD do domeny zarządzanej Azure Active Directory Domain Services przy użyciu programu Azure AD PowerShell
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: justinha
ms.openlocfilehash: f877a631fd3c89d74b9e3b47cf205bbcf173ebc0
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453414"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-azure-ad-powershell"></a>Konfigurowanie synchronizacji z zakresem z usługi Azure AD do Azure Active Directory Domain Services przy użyciu programu Azure AD PowerShell

Aby zapewnić usługi uwierzytelniania, Azure Active Directory Domain Services (Azure AD DS) synchronizuje użytkowników i grupy z usługi Azure AD. W środowisku hybrydowym Użytkownicy i grupy ze środowiska lokalnego Active Directory Domain Services (AD DS) można najpierw zsynchronizować z usługą Azure AD przy użyciu Azure AD Connect, a następnie zsynchronizowane z usługą Azure AD DS.

Domyślnie wszyscy użytkownicy i grupy z katalogu usługi Azure AD są synchronizowane z domeną zarządzaną AD DS platformy Azure. Jeśli masz określone potrzeby, możesz zamiast tego synchronizować tylko zdefiniowanego zestawu użytkowników.

W tym artykule opisano sposób tworzenia domeny zarządzanej, która używa synchronizacji w zakresie, a następnie zmieniania lub wyłączania zestawu użytkowników z zakresem przy użyciu programu Azure AD PowerShell. [Te kroki można również wykonać przy użyciu Azure Portal][scoped-sync].

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby wykonać ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.
    * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z subskrypcją, zsynchronizowana z katalogiem lokalnym lub katalogiem w chmurze.
    * W razie konieczności [Utwórz dzierżawę Azure Active Directory][create-azure-ad-tenant] lub [skojarz subskrypcję platformy Azure z Twoim kontem][associate-azure-ad-tenant].
* Azure Active Directory Domain Services zarządzana domena włączona i skonfigurowana w dzierżawie usługi Azure AD.
    * W razie potrzeby uzupełnij ten samouczek, aby [utworzyć i skonfigurować domenę zarządzaną Azure Active Directory Domain Services][tutorial-create-instance].
* Aby zmienić zakres synchronizacji AD DS platformy Azure, musisz mieć uprawnienia *administratora globalnego* w dzierżawie usługi Azure AD.

## <a name="scoped-synchronization-overview"></a>Synchronizacja z zakresem — Omówienie

Domyślnie wszyscy użytkownicy i grupy z katalogu usługi Azure AD są synchronizowane z domeną zarządzaną. Jeśli tylko kilku użytkowników potrzebuje dostępu do domeny zarządzanej, można synchronizować tylko te konta użytkowników. Ta synchronizacja w zakresie jest oparta na grupach. Podczas konfigurowania synchronizacji z zakresem opartym na grupach tylko konta użytkowników należące do określonych grup są synchronizowane z domeną zarządzaną. Grupy zagnieżdżone nie są zsynchronizowane, tylko wybrane grupy.

Zakres synchronizacji można zmienić przed lub po utworzeniu domeny zarządzanej. Zakres synchronizacji jest definiowany przez jednostkę usługi z identyfikatorem aplikacji 2565bd9d-DA50-47d4-8b85-4c97f669dc36. Aby zapobiec utracie zakresu, nie usuwaj ani nie zmieniaj nazwy głównej usługi. W razie przypadkowego usunięcia nie można odzyskać zakresu synchronizacji. 

Należy pamiętać o następujących zastrzeżeniach, jeśli zmienisz zakres synchronizacji:

- Występuje pełna synchronizacja.
- Obiekty, które nie są już wymagane w domenie zarządzanej, są usuwane. Nowe obiekty są tworzone w domenie zarządzanej.

Aby dowiedzieć się więcej o procesie synchronizacji, zobacz [Omówienie synchronizacji w Azure AD Domain Services][concepts-sync].

## <a name="powershell-script-for-scoped-synchronization"></a>Skrypt programu PowerShell dotyczący synchronizacji w zakresie

Aby skonfigurować synchronizację z zakresem przy użyciu programu PowerShell, najpierw Zapisz następujący skrypt do pliku o nazwie `Select-GroupsToSync.ps1` .

Ten skrypt służy do konfigurowania usługi Azure AD DS do synchronizowania wybranych grup z usługi Azure AD. Wszystkie konta użytkowników, które są częścią określonych grup, są synchronizowane z domeną zarządzaną.

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

## <a name="enable-scoped-synchronization"></a>Włącz synchronizację z zakresem

Aby włączyć synchronizację z zakresem opartym na grupach dla domeny zarządzanej, wykonaj następujące czynności:

1. Najpierw ustaw wartość *"filteredSync" = "Enabled"* w zasobie AD DS platformy Azure, a następnie zaktualizuj domenę zarządzaną.

    Po wyświetleniu monitu podaj poświadczenia *administratora globalnego* , aby zalogować się do dzierżawy usługi Azure AD przy użyciu polecenia cmdlet [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    # Connect to your Azure AD tenant
    Connect-AzureAD

    # Retrieve the Azure AD DS resource.
    $DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

    # Enable group-based scoped synchronization.
    $enableScopedSync = @{"filteredSync" = "Enabled"}

    # Update the Azure AD DS resource
    Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
    ```

1. Teraz Określ listę grup, których użytkownicy mają być zsynchronizowani z domeną zarządzaną.

    Uruchom `Select-GroupsToSync.ps1` skrypt i określ listę grup do zsynchronizowania. W poniższym przykładzie grupy do synchronizowania to *GroupName1* i *GroupName2*.

    > [!WARNING]
    > Należy uwzględnić grupę *administratorów kontrolera domeny usługi AAD* na liście grup dla synchronizacji z zakresem. Jeśli nie dołączysz tej grupy, domena zarządzana stanie się niezdatna do użytku.

    ```powershell
    .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
    ```

Zmiana zakresu synchronizacji powoduje, że zarządzana domena ponownie zsynchronizuje wszystkie dane. Obiekty, które nie są już wymagane w domenie zarządzanej, są usuwane, a ponowna synchronizacja może zająć dużo czasu.

## <a name="modify-scoped-synchronization"></a>Modyfikuj synchronizację w zakresie

Aby zmodyfikować listę grup, których użytkownicy powinni synchronizować z domeną zarządzaną, uruchom `Select-GroupsToSync.ps1` skrypt i określ nową listę grup do zsynchronizowania.

W poniższym przykładzie grupy do zsynchronizowania nie zawierają już *GroupName2* i teraz zawierają *GroupName3*.

> [!WARNING]
> Należy uwzględnić grupę *administratorów kontrolera domeny usługi AAD* na liście grup dla synchronizacji z zakresem. Jeśli nie dołączysz tej grupy, domena zarządzana stanie się niezdatna do użytku.

Po wyświetleniu monitu podaj poświadczenia *administratora globalnego* , aby zalogować się do dzierżawy usługi Azure AD przy użyciu polecenia cmdlet [Connect-AzureAD][Connect-AzureAD] :

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Zmiana zakresu synchronizacji powoduje, że zarządzana domena ponownie zsynchronizuje wszystkie dane. Obiekty, które nie są już wymagane w domenie zarządzanej, są usuwane, a ponowna synchronizacja może zająć dużo czasu.

## <a name="disable-scoped-synchronization"></a>Wyłącz synchronizację z zakresem

Aby wyłączyć synchronizację z zakresem opartym na grupach dla domeny zarządzanej, ustaw wartość *"filteredSync" = "Disabled"* w zasobie AD DS platformy Azure, a następnie zaktualizuj domenę zarządzaną. Po zakończeniu wszyscy użytkownicy i grupy są ustawiani do synchronizacji z usługi Azure AD.

Po wyświetleniu monitu podaj poświadczenia *administratora globalnego* , aby zalogować się do dzierżawy usługi Azure AD przy użyciu polecenia cmdlet [Connect-AzureAD][Connect-AzureAD] :

```powershell
# Connect to your Azure AD tenant
Connect-AzureAD

# Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

# Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

# Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Zmiana zakresu synchronizacji powoduje, że zarządzana domena ponownie zsynchronizuje wszystkie dane. Obiekty, które nie są już wymagane w domenie zarządzanej, są usuwane, a ponowna synchronizacja może zająć dużo czasu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o procesie synchronizacji, zobacz [Omówienie synchronizacji w Azure AD Domain Services](synchronization.md).

<!-- INTERNAL LINKS -->
[scoped-sync]: scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md

<!-- EXTERNAL LINKS -->
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
