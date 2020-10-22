---
title: PowerShell dla ról usługi Azure AD w usłudze PIM — Azure AD | Microsoft Docs
description: Zarządzanie rolami usługi Azure AD przy użyciu poleceń cmdlet programu PowerShell w programie Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7d04a2941888592ffa37acfe6cba52a33fda528
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92365514"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>Program PowerShell dla ról usługi Azure AD w Privileged Identity Management

Ten artykuł zawiera instrukcje dotyczące korzystania z poleceń cmdlet programu PowerShell w usłudze Azure Active Directory (Azure AD) w celu zarządzania rolami usługi Azure AD w Privileged Identity Management (PIM). Zawarto również informacje, jak skonfigurować program przy użyciu modułu Azure AD PowerShell.

> [!Note]
> Nasze oficjalne środowisko PowerShell jest obsługiwane tylko w przypadku, gdy korzystasz z nowej wersji Azure AD Privileged Identity Management. Przejdź do Privileged Identity Management i upewnij się, że masz Poniższy transparent w bloku Szybki Start.
> [![Sprawdź wersję Privileged Identity Management masz](media/pim-how-to-add-role-to-user/pim-new-version.png "Wybierz > usługi Azure AD Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) Jeśli nie masz tego transparentu, zaczekaj, aż trwa proces wdrażania tego zaktualizowanego środowiska w ciągu najbliższych kilku tygodni.
> Polecenia cmdlet programu PowerShell Privileged Identity Management są obsługiwane za pomocą modułu Azure AD Preview. Jeśli używasz innego modułu i ten moduł zwróci komunikat o błędzie, zacznij korzystać z tego nowego modułu. Jeśli masz jakiekolwiek systemy produkcyjne utworzone w oparciu o inny moduł, skontaktuj się z Tobą [pim_preview@microsoft.com](mailto:pim_preview@microsoft.com) .

## <a name="installation-and-setup"></a>Instalacja i konfiguracja

1. Instalowanie modułu usługi Azure AD w wersji zapoznawczej

    ```powershell
    Install-module AzureADPreview
    ```

1. Przed kontynuowaniem upewnij się, że masz wymagane uprawnienia roli. Jeśli próbujesz wykonać zadania zarządzania, takie jak podawanie roli lub aktualizowanie ustawienia roli, upewnij się, że masz rolę Administrator globalny lub administrator ról uprzywilejowanych. Jeśli użytkownik próbuje aktywować własne przypisanie, nie są wymagane żadne uprawnienia poza domyślnymi uprawnieniami użytkownika.

1. Nawiązywanie połączenia z usługą Azure AD.

    ```powershell
    $AzureAdCred = Get-Credential  
    Connect-AzureAD -Credential $AzureAdCred
    ```

1. Znajdź identyfikator dzierżawy dla organizacji usługi Azure AD, przechodząc do **Azure Active Directory**  >  **Właściwości**  >  **katalogu**. W sekcji poleceń cmdlet Użyj tego identyfikatora zawsze wtedy, gdy musisz podać identyfikator zasobu.

    ![Znajdź identyfikator organizacji we właściwościach organizacji usługi Azure AD](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> Poniższe sekcje stanowią proste przykłady, które mogą pomóc Ci w rozpoczęciu pracy. Więcej szczegółowej dokumentacji dotyczącej następujących poleceń cmdlet można znaleźć pod adresem [https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management&preserve-view=true](/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management&preserve-view=true) . Należy jednak zamienić ciąg "azureResources" w parametrze identyfikatorem na wartość "aadRoles". Należy również pamiętać, aby użyć identyfikatora dzierżawy dla organizacji usługi Azure AD jako parametru resourceId.

## <a name="retrieving-role-definitions"></a>Pobieranie definicji ról

Użyj poniższego polecenia cmdlet, aby pobrać wszystkie wbudowane i niestandardowe role usługi Azure AD w organizacji usługi Azure AD. Ten ważny krok zapewnia mapowanie między nazwą roli a zduplikowanych. Zduplikowanych jest używany w tych poleceniach cmdlet, aby można było odwołać się do określonej roli.

Zduplikowanych jest specyficzna dla organizacji usługi Azure AD i różni się od zduplikowanych zwróconego przez interfejs API zarządzania rolami.

```powershell
Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26
```

Wynik:

![Pobierz wszystkie role dla organizacji usługi Azure AD](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Pobieranie przypisań ról

Użyj poniższego polecenia cmdlet, aby pobrać wszystkie przypisania ról w organizacji usługi Azure AD.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"
```

Użyj następującego polecenia cmdlet, aby pobrać wszystkie przypisania ról dla określonego użytkownika. Ta lista jest również znana jako "Moje role" w portalu usługi Azure AD. Jedyną różnicą jest to, że dodano filtr dla identyfikatora podmiotu. Identyfikator podmiotu w tym kontekście jest identyfikatorem użytkownika lub IDENTYFIKATORem grupy.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 
```

Użyj następującego polecenia cmdlet, aby pobrać wszystkie przypisania ról dla określonej roli. Zduplikowanych tutaj to identyfikator, który jest zwracany przez poprzednie polecenie cmdlet.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"
```

Polecenia cmdlet powodują wyświetlenie listy obiektów przypisania roli przedstawionych poniżej. Identyfikator podmiotu jest IDENTYFIKATORem użytkownika, do którego przypisano rolę. Stan przypisania może być aktywny lub być uprawniony. Jeśli użytkownik jest aktywny i istnieje identyfikator w polu LinkedEligibleRoleAssignmentId, oznacza to, że rola jest obecnie aktywowana.

Wynik:

![Pobieranie wszystkich przypisań ról dla organizacji usługi Azure AD](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Przypisywanie roli

Aby utworzyć kwalifikujące się przypisanie, użyj następującego polecenia cmdlet.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 
```

Harmonogram, który definiuje godzinę rozpoczęcia i zakończenia przypisania, jest obiektem, który można utworzyć jak w poniższym przykładzie:

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
$schedule.endDateTime = "2020-07-25T20:49:11.770Z"
```
> [!Note]
> Jeśli wartość Końcowawartośćdatetime jest ustawiona na wartość null, wskazuje stałe przypisanie.

## <a name="activate-a-role-assignment"></a>Aktywuj przypisanie roli

Aby aktywować kwalifikujące się przypisanie, użyj następującego polecenia cmdlet.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas"
``` 

To polecenie cmdlet jest niemal identyczne z poleceniem cmdlet do tworzenia przypisania roli. Kluczową różnicą między poleceniami cmdlet jest, że dla parametru – type aktywacja ma wartość "userAdd", a nie "adminAdd". Druga różnica polega na tym, że parametr – AssignmentState jest "aktywny" zamiast "kwalifikujący się".

> [!Note]
> Istnieją dwa ograniczenia dotyczące aktywacji roli za poorednictwem programu PowerShell.
> 1. Jeśli w ustawieniach roli jest wymagany system biletu/numer biletu, nie ma możliwości podawania ich jako parametru. W rezultacie nie będzie możliwe uaktywnienie roli poza Azure Portal. Ta funkcja jest wdrażana w programie PowerShell w ciągu następnych kilku miesięcy.
> 1. W przypadku korzystania z uwierzytelniania wieloskładnikowego w ramach aktywacji roli obecnie nie jest możliwe, aby program PowerShell zakwestionował użytkownika podczas aktywowania jego roli. Zamiast tego użytkownicy będą musieli wyzwolić wyzwanie usługi MFA, gdy łączą się z usługą Azure AD, wykonując [ten wpis w blogu](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) od jednego z naszych inżynierów. Jeśli tworzysz aplikację dla usługi PIM, jedną z możliwych implementacji jest zażądanie użytkowników i ponowne połączenie ich z modułem po otrzymaniu błędu "MfaRule".

## <a name="retrieving-and-updating-role-settings"></a>Pobieranie i aktualizowanie ustawień roli

Użyj poniższego polecenia cmdlet, aby pobrać wszystkie ustawienia roli w organizacji usługi Azure AD.

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'"
```

Ustawienie obejmuje cztery główne obiekty. Tylko trzy z tych obiektów są obecnie używane przez program PIM. UserMemberSettings to ustawienia aktywacji, AdminEligibleSettings to ustawienia przypisywania dla kwalifikujących się przypisań, a AdminmemberSettings to ustawienia przypisywania dla aktywnych przypisań.

[![Pobierz i zaktualizuj ustawienia roli.](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png)](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

Aby zaktualizować ustawienie roli, należy uzyskać istniejący obiekt ustawień dla określonej roli i wprowadzić w nim zmiany:

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq 'tenant id' and RoleDefinitionId eq 'role id'"
$settinga = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting
$settinga.RuleIdentifier = "JustificationRule"
$settinga.Setting = '{"required":false}'
```

Następnie można zastosować ustawienie do jednego z obiektów dla określonej roli, jak pokazano poniżej. Ten identyfikator jest IDENTYFIKATORem ustawienia roli, który można pobrać z wyniku polecenia cmdlet listy ustawień roli.

```powershell
Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $settinga 
```

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli niestandardowej usługi Azure AD](azure-ad-custom-roles-assign.md)
- [Usuń lub zaktualizuj niestandardowe przypisanie roli usługi Azure AD](azure-ad-custom-roles-update-remove.md)
- [Skonfiguruj niestandardowe przypisanie roli usługi Azure AD](azure-ad-custom-roles-configure.md)
- [Definicje ról w usłudze Azure AD](../roles/permissions-reference.md)
