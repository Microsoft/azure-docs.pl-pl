---
title: Przypisywanie roli do grupy przy użyciu Privileged Identity Management w usłudze Azure AD | Microsoft Docs
description: Wyświetl podgląd niestandardowych ról usługi Azure AD na potrzeby delegowania zarządzania tożsamościami. Zarządzanie rolami platformy Azure w Azure Portal, PowerShell lub interfejs API programu Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343da87048cf43c04a137376e9a7f24270ce729a
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476098"
---
# <a name="assign-a-role-to-a-group-using-privileged-identity-management"></a>Przypisywanie roli do grupy przy użyciu Privileged Identity Management

W tym artykule opisano sposób przypisywania roli Azure Active Directory (Azure AD) do grupy przy użyciu Azure AD Privileged Identity Management (PIM).

> [!NOTE]
> Aby można było przypisać grupę do roli usługi Azure AD przy użyciu programu PIM, należy użyć zaktualizowanej wersji programu Privileged Identity Management. Jeśli Twoja organizacja usługi Azure AD korzysta z interfejsu API Privileged Identity Management, może się zdarzyć, że masz starszą wersję programu PIM. Jeśli tak, skontaktuj się z aliasem, pim_preview@microsoft.com Aby przenieść swoją organizację i zaktualizować interfejs API. Dowiedz się więcej na temat [ról i funkcji usługi Azure AD w programie PIM](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="using-azure-ad-admin-center"></a>Korzystanie z centrum administracyjnego usługi Azure AD

1. Zaloguj się do [Azure AD Privileged Identity Management](https://ms.portal.azure.com/?Microsoft_AAD_IAM_GroupRoles=true&Microsoft_AAD_IAM_userRolesV2=true&Microsoft_AAD_IAM_enablePimIntegration=true#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) jako administrator ról uprzywilejowanych lub Administrator globalny w organizacji.

1. Wybierz **Privileged Identity Management**  >  **ról ról usługi Azure AD**  >  **Roles**  >  **Dodaj przypisania**

1. Wybierz rolę, a następnie wybierz grupę. Wyświetlane są tylko grupy kwalifikujące się do przypisywania ról (grupy z możliwością przypisywania ról), a nie wszystkie grupy.

    ![Wybierz użytkownika, do którego przypiszesz rolę](./media/roles-groups-pim-eligible/select-member.png)

1. Wybierz odpowiednie ustawienie członkostwa. W przypadku ról wymagających aktywacji wybierz pozycję **kwalifikujące się**. Domyślnie użytkownik będzie ostatecznie uprawniony, ale można również ustawić godzinę rozpoczęcia i zakończenia dla uprawnień użytkownika. Po zakończeniu kliknij przycisk Zapisz i Dodaj, aby zakończyć przypisanie roli.

    ![Wybierz użytkownika, do którego przypiszesz rolę](./media/roles-groups-pim-eligible/set-assignment-settings.png)

## <a name="using-powershell"></a>Korzystanie z programu PowerShell

### <a name="download-the-azure-ad-preview-powershell-module"></a>Pobierz moduł programu PowerShell w wersji zapoznawczej usługi Azure AD

Aby zainstalować moduł #PowerShell usługi Azure AD, użyj następujących poleceń cmdlet:

```powershell
install-module azureadpreview
import-module azureadpreview
```

Aby sprawdzić, czy moduł jest gotowy do użycia, użyj następującego polecenia cmdlet:

```powershell
get-module azureadpreview
```

### <a name="assign-a-group-as-an-eligible-member-of-a-role"></a>Przypisywanie grupy jako uprawnionego członka roli

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = "2019-04-26T20:49:11.770Z"
$schedule.endDateTime = "2019-07-25T20:49:11.770Z"
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId aadRoles -Schedule $schedule -ResourceId "[YOUR TENANT ID]" -RoleDefinitionId "9f8c1837-f885-4dfd-9a75-990f9222b21d" -SubjectId "[YOUR GROUP ID]" -AssignmentState "Eligible" -Type "AdminAdd"
```

## <a name="using-microsoft-graph-api"></a>Korzystanie z interfejsu API Microsoft Graph

```powershell
POST
https://graph.microsoft.com/beta/privilegedAccess/aadroles/roleAssignmentRequests  

{

 "roleDefinitionId": {roleDefinitionId},

 "resourceId": {tenantId},

 "subjectId": {GroupId},

 "assignmentState": "Eligible",

 "type": "AdminAdd",

 "reason": "reason string",

 "schedule": {

   "startDateTime": {DateTime},

   "endDateTime": {DateTime},

   "type": "Once"

 }

}
```

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie przypisaniami ról przy użyciu grup chmur](roles-groups-concept.md)
- [Rozwiązywanie problemów z rolami przypisanymi do grup chmury](roles-groups-faq-troubleshooting.md)
- [Skonfiguruj ustawienia roli administratora usługi Azure AD w Privileged Identity Management](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [Przypisywanie ról zasobów platformy Azure w Privileged Identity Management](../privileged-identity-management/pim-resource-roles-assign-roles.md)
