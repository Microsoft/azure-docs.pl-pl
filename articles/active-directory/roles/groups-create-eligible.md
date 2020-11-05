---
title: Tworzenie grupy do przypisywania ról w Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak utworzyć grupę z możliwością przypisywania ról w usłudze Azure AD. Zarządzanie rolami platformy Azure w Azure Portal, PowerShell lub interfejs API programu Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc2c42f407ee31a792a6634a813358d428120b46
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378622"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>Utwórz grupę z możliwością przypisania w Azure Active Directory

Rolę można przypisać tylko do grupy, która została utworzona z właściwością "isAssignableToRole" o wartości "true" lub została utworzona w portalu usługi Azure AD przy użyciu **ról usługi Azure AD, które można przypisać do grupy** . Ten atrybut grupy tworzy grupę, która może być przypisana do roli w Azure Active Directory (Azure AD). W tym artykule opisano sposób tworzenia tego specjalnego rodzaju grupy. **Uwaga:** Grupa z właściwością isAssignableToRole ustawioną na wartość true nie może być typu członkostwa dynamicznego. Aby uzyskać więcej informacji, zobacz [używanie grupy do zarządzania przypisaniami ról usługi Azure AD](groups-concept.md).

## <a name="using-azure-ad-admin-center"></a>Korzystanie z centrum administracyjnego usługi Azure AD

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) przy użyciu uprawnień administrator ról uprzywilejowanych lub Administrator globalny w organizacji usługi Azure AD.
1. Wybierz kolejno pozycje **grupy**  >  **wszystkie grupy**  >  **Nowa grupa**.

    [![Otwórz Azure Active Directory i Utwórz nową grupę.](./media/groups-create-eligible/new-group.png "Otwórz Azure Active Directory i Utwórz nową grupę.")](./media/groups-create-eligible/new-group.png#<lightbox>)

1. Na karcie **Nowa grupa** Podaj typ grupy, nazwę i opis.
1. Włącz **role usługi Azure AD, które można przypisać do grupy**. Ten przełącznik jest widoczny tylko dla administratorów ról uprzywilejowanych i administratorów globalnych, ponieważ są to tylko dwie role, które mogą ustawiać przełącznik.

    [![Utwórz nową grupę uprawniającą do przypisania roli](./media/groups-create-eligible/eligible-switch.png "Utwórz nową grupę uprawniającą do przypisania roli")](./media/groups-create-eligible/eligible-switch.png#<lightbox>)

1. Wybierz członków i właścicieli dla grupy. Istnieje również możliwość przypisania ról do grupy, ale przypisanie roli nie jest wymagane w tym miejscu.

    [![Dodaj członków do grupy przypisanej do roli i przypisz role.](./media/groups-create-eligible/specify-members.png "Dodaj członków do grupy przypisanej do roli i przypisz role.")](./media/groups-create-eligible/specify-members.png#<lightbox>)

1. Po określeniu członków i właścicieli wybierz pozycję **Utwórz**.

    [![Przycisk Utwórz znajduje się u dołu strony.](./media/groups-create-eligible/create-button.png "Przycisk Utwórz znajduje się u dołu strony.")](./media/groups-create-eligible/create-button.png#<lightbox>)

Grupa jest tworzona z dowolnymi przypisanymi rolami.

## <a name="using-powershell"></a>Korzystanie z programu PowerShell

### <a name="install-the-azure-ad-preview-module"></a>Instalowanie modułu usługi Azure AD w wersji zapoznawczej

```powershell
install-module azureadpreview
import-module azureadpreview
```

Aby sprawdzić, czy moduł jest gotowy do użycia, wydaj następujące polecenie:

```powershell
get-module azureadpreview
```

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Tworzenie grupy, która może być przypisana do roli

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

W przypadku tego typu grupy `isPublic` zawsze będzie to wartość false i `isSecurityEnabled` zawsze będzie ona prawdziwa.

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>Kopiowanie użytkowników i jednostek usługi jednej grupy do grupy z możliwością przydzielenia roli

```powershell
#Basic set up
install-module azureadpreview
import-module azureadpreview
get-module azureadpreview

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating a Microsoft 365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="using-microsoft-graph-api"></a>Korzystanie z interfejsu API Microsoft Graph

### <a name="create-a-role-assignable-group-in-azure-ad"></a>Tworzenie grupy do przypisywania ról w usłudze Azure AD

```powershell
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

W przypadku tego typu grupy `isPublic` zawsze będzie to wartość false i `isSecurityEnabled` zawsze będzie ona prawdziwa.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli do grupy w chmurze](groups-assign-role.md)
- [Zarządzanie przypisaniami ról za pomocą grup w chmurze](groups-concept.md)
- [Rozwiązywanie problemów z rolami przypisanymi do grup w chmurze](groups-faq-troubleshooting.md)
