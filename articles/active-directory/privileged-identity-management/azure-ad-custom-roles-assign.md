---
title: Przypisywanie roli niestandardowej usługi Azure AD — Privileged Identity Management (PIM)
description: Jak przypisać rolę niestandardową usługi Azure AD na potrzeby Privileged Identity Management przypisywania (PIM)
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
ms.openlocfilehash: 75e004980ada23a616b57e3c7eb1afb241e09598
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371651"
---
# <a name="assign-an-azure-ad-custom-role-in-privileged-identity-management"></a>Przypisywanie roli niestandardowej usługi Azure AD w Privileged Identity Management

W tym artykule przedstawiono sposób użycia Privileged Identity Management (PIM) do tworzenia przypisywania just-in-Time i Time do ról niestandardowych utworzonych na potrzeby zarządzania aplikacjami w środowisku administracyjnym usługi Azure Active Directory (Azure AD).

- Aby uzyskać więcej informacji na temat tworzenia ról niestandardowych w celu delegowania zarządzania aplikacjami w usłudze Azure AD, zobacz [Niestandardowe role administratorów w Azure Active Directory (wersja zapoznawcza)](../roles/custom-overview.md).
- Jeśli jeszcze nie korzystasz Privileged Identity Management, uzyskaj więcej informacji na temat [rozpoczynania pracy przy użyciu Privileged Identity Management](pim-getting-started.md).
- Aby uzyskać informacje na temat udzielania innym administratorom dostępu do zarządzania Privileged Identity Management, zobacz [udzielanie dostępu innym administratorom w celu zarządzania Privileged Identity Management](pim-how-to-give-access-to-pim.md).

> [!NOTE]
> Role niestandardowe usługi Azure AD nie są zintegrowane z wbudowanymi rolami katalogu w wersji zapoznawczej. Gdy funkcja jest ogólnie dostępna, zarządzanie rolami będzie odbywać się w interfejsie wbudowanym. Jeśli zobaczysz poniższy transparent, te role powinny być zarządzane [przy użyciu wbudowanej roli środowisko](pim-how-to-activate-role.md) i ten artykuł nie ma zastosowania:
>
> [![Wybierz Privileged Identity Management usługi Azure AD >.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="assign-a-role"></a>Przypisywanie roli

Privileged Identity Management może zarządzać rolami niestandardowymi, które można tworzyć w usłudze Azure Active Directory (Azure AD) Zarządzanie aplikacjami.  Poniższe kroki umożliwiają odpowiednie przypisanie do niestandardowej roli katalogu.

1. Zaloguj się do [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) w Azure Portal przy użyciu konta użytkownika przypisanego do roli administrator ról uprzywilejowanych.
1. Wybierz **role niestandardowe usługi Azure AD (wersja zapoznawcza)**.

    ![Wybieranie ról niestandardowych usługi Azure AD w wersji zapoznawczej, aby zobaczyć kwalifikujące się przypisania ról](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Wybierz pozycję **role** , aby wyświetlić listę ról niestandardowych dla aplikacji usługi Azure AD.

    ![Wybierz role Zobacz listę kwalifikujących się przypisań ról](./media/azure-ad-custom-roles-assign/view-roles.png)

1. Wybierz pozycję **Dodaj członka** , aby otworzyć stronę przypisanie.
1. Aby ograniczyć zakres przypisania roli do pojedynczej aplikacji, wybierz pozycję **zakres** , aby określić zakres aplikacji.

    ![Ograniczanie zakresu kwalifikujących się przypisań ról w usłudze Azure AD](./media/azure-ad-custom-roles-assign/set-scope.png)

1. Wybierz pozycję **Wybierz rolę** , aby otworzyć listę **Wybierz rolę** .

    ![Wybierz kwalifikującą się rolę do przypisania do użytkownika](./media/azure-ad-custom-roles-assign/select-role.png)

1. Wybierz rolę, którą chcesz przypisać, a następnie kliknij przycisk **Wybierz**. Zostanie otwarta lista **Wybierz element członkowski** .

    ![Wybierz użytkownika, do którego przypiszesz rolę](./media/azure-ad-custom-roles-assign/select-member.png)

1. Wybierz użytkownika, który chcesz przypisać do roli, a następnie kliknij przycisk **Wybierz**. Zostanie otwarta lista **ustawień członkostwa** .

    ![Ustaw typ przypisania roli na kwalifikujące się lub aktywne](./media/azure-ad-custom-roles-assign/membership-settings.png)

1. Na stronie **Ustawienia członkostwa** wybierz pozycję **kwalifikujące się** lub **aktywne**:

    - **Kwalifikujące się** przypisania wymagają, aby użytkownik przypisany do roli przeprowadził akcję, zanim będzie mógł korzystać z roli. Akcje mogą obejmować przekazanie kontroli uwierzytelniania wieloskładnikowego, zapewnienie uzasadnienia biznesowego lub zażądanie zatwierdzenia od wyznaczenia osób zatwierdzających.
    - **Aktywne** przypisania nie wymagają przypisania użytkownikowi przypisanego do roli. Aktywni użytkownicy mają uprawnienia przypisane do roli przez cały czas.

1. Jeśli **trwałe** pole wyboru jest obecne i dostępne (w zależności od ustawień roli), można określić, czy przydział jest trwały. Zaznacz pole wyboru, aby przydzielić trwale kwalifikujące się lub trwale przypisane. Usuń zaznaczenie tego pola wyboru, aby określić czas trwania przypisywania.
1. Aby utworzyć nowe przypisanie roli, kliknij przycisk **Zapisz** , a następnie **Dodaj**. Zostanie wyświetlony komunikat z informacją o stanie procesu przypisywania.

Aby sprawdzić przypisanie roli, w otwartej roli wybierz pozycję **przypisania**  >  **Przypisz** i sprawdź, czy przypisanie roli jest prawidłowo zidentyfikowane jako kwalifikujące się lub aktywne.

 ![Sprawdź, czy przypisanie roli jest widoczne jako kwalifikujące się lub aktywne](./media/azure-ad-custom-roles-assign/verify-assignments.png)

## <a name="next-steps"></a>Następne kroki

- [Aktywuj rolę niestandardową usługi Azure AD](azure-ad-custom-roles-assign.md)
- [Usuń lub zaktualizuj niestandardowe przypisanie roli usługi Azure AD](azure-ad-custom-roles-update-remove.md)
- [Skonfiguruj niestandardowe przypisanie roli usługi Azure AD](azure-ad-custom-roles-configure.md)
- [Definicje ról w usłudze Azure AD](../roles/permissions-reference.md)
