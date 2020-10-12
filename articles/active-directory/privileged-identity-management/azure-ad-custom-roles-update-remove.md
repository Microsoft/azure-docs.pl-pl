---
title: Aktualizowanie lub usuwanie roli niestandardowej usługi Azure AD — Privileged Identity Management (PIM)
description: Jak zaktualizować lub usunąć przypisanie roli niestandardowej usługi Azure AD Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f9e52dc28d2834b479ac0580ff990119a56772a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87422032"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Aktualizowanie lub usuwanie przypisanej roli niestandardowej usługi Azure AD w Privileged Identity Management

W tym artykule przedstawiono sposób używania Privileged Identity Management (PIM) do aktualizowania lub usuwania przypisywania just in Time i Time do ról niestandardowych utworzonych na potrzeby zarządzania aplikacjami w środowisku administracyjnym usługi Azure Active Directory (Azure AD). 

- Aby uzyskać więcej informacji na temat tworzenia ról niestandardowych w celu delegowania zarządzania aplikacjami w usłudze Azure AD, zobacz [Niestandardowe role administratorów w Azure Active Directory (wersja zapoznawcza)](../users-groups-roles/roles-custom-overview.md). 
- Jeśli jeszcze nie korzystasz Privileged Identity Management, uzyskaj więcej informacji na temat [rozpoczynania pracy przy użyciu Privileged Identity Management](pim-getting-started.md).

> [!NOTE]
> Role niestandardowe usługi Azure AD nie są zintegrowane z wbudowanymi rolami katalogu w wersji zapoznawczej. Gdy funkcja jest ogólnie dostępna, zarządzanie rolami będzie odbywać się w interfejsie wbudowanym. Jeśli zobaczysz poniższy transparent, te role powinny być zarządzane [przy użyciu wbudowanej roli środowisko](pim-how-to-add-role-to-user.md) i ten artykuł nie ma zastosowania:
>
> [![Wybierz Privileged Identity Management usługi Azure AD >.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="update-or-remove-an-assignment"></a>Aktualizowanie lub usuwanie przypisania

Wykonaj następujące kroki, aby zaktualizować lub usunąć istniejące niestandardowe przypisanie roli.

1. Zaloguj się do [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) w Azure Portal przy użyciu konta użytkownika przypisanego do roli administrator ról uprzywilejowanych.
1. Wybierz **role niestandardowe usługi Azure AD (wersja zapoznawcza)**.

    ![Wybieranie ról niestandardowych usługi Azure AD w wersji zapoznawczej, aby zobaczyć kwalifikujące się przypisania ról](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Wybierz pozycję **role** , aby wyświetlić listę **przypisań** ról niestandardowych dla aplikacji usługi Azure AD.

    ![Wybierz role Zobacz listę kwalifikujących się przypisań ról](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. Wybierz rolę, którą chcesz zaktualizować lub usunąć.
1. Znajdź przypisanie roli na kartach **kwalifikujące się role** lub **role aktywne** .
1. Wybierz pozycję **Aktualizuj** lub **Usuń** , aby zaktualizować lub usunąć przypisanie roli.

    ![Wybierz pozycję Usuń lub zaktualizuj w uprawnionym przypisaniu roli](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>Następne kroki

- [Aktywuj rolę niestandardową usługi Azure AD](azure-ad-custom-roles-assign.md)
- [Przypisywanie roli niestandardowej usługi Azure AD](azure-ad-custom-roles-assign.md)
- [Skonfiguruj niestandardowe przypisanie roli usługi Azure AD](azure-ad-custom-roles-configure.md)
