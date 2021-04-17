---
title: Przypisywanie użytkownikom ról usługi Azure AD — Azure Active Directory | Microsoft Docs
description: Instrukcje dotyczące sposobu przypisywania ról administratorów i użytkowników niebędących administratorami do użytkowników z Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/31/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4ffcad6f6be16ba7ac3674d710dd543f729f0c3
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575415"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Przypisywanie ról administratora i osób niebędących administratorami do użytkowników z Azure Active Directory

W Azure Active Directory (Azure AD), jeśli jeden z użytkowników potrzebuje uprawnień do zarządzania zasobami usługi Azure AD, musisz przypisać go do roli, która zapewnia im uprawnienia, których potrzebują. Aby uzyskać informacje na temat ról, które zarządzają zasobami platformy Azure i które role zarządzają zasobami usługi Azure AD, zobacz Role klasycznego administratora subskrypcji, role platformy [Azure i role usługi Azure AD.](../../role-based-access-control/rbac-and-directory-admin-roles.md)

Aby uzyskać więcej informacji na temat dostępnych ról usługi Azure AD, zobacz [Przypisywanie ról administratorów w usłudze Azure Active Directory](../roles/permissions-reference.md). Aby dodać użytkowników, zobacz [Dodawanie nowych użytkowników do Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Przypisywanie ról

Częstym sposobem przypisywania ról usługi Azure AD do użytkownika jest strona **Przypisane role** dla użytkownika. Można również skonfigurować uprawnienie użytkownika, aby poziom uprawnień just in time został podwyższony do roli przy użyciu Privileged Identity Management (PIM). Aby uzyskać więcej informacji na temat używania funkcji PIM, [zobacz Privileged Identity Management](../privileged-identity-management/index.yml).

> [!Note]
> Jeśli masz plan licencjonowania Azure AD — wersja Premium P2 i już korzystasz z pim, wszystkie zadania zarządzania rolami są wykonywane w [Privileged Identity Management użytkownika.](../roles/manage-roles-portal.md) Ta funkcja jest obecnie ograniczona do przypisywania tylko jednej roli na raz. Obecnie nie można wybrać wielu ról i przypisać ich do użytkownika jednocześnie.
>
> ![Role usługi Azure AD zarządzane w usłudze PIM dla użytkowników, którzy już korzystają z usługi PIM i mają licencję Premium P2](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>Przypisywanie roli do użytkownika

1. Przejdź do [Azure Portal](https://portal.azure.com/) i zaloguj się przy użyciu administrator globalny dla katalogu.

2. Wyszukaj i wybierz pozycję **Azure Active Directory**.

      ![Azure Portal wyszukaj Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. Wybierz pozycję **Użytkownicy**.

4. Wyszukaj i wybierz użytkownika, który ma przypisanie roli. Na przykład _Alain Charon_.

      ![Strona Wszyscy użytkownicy — wybierz użytkownika](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Na stronie Alain Charon - Profile (Użytkownik **Alain Charon — profil)** **wybierz pozycję Assigned roles (Przypisane role).**

    Zostanie **wyświetlona strona Alain Charon — role** administracyjne.

6. Wybierz **pozycję Dodaj przypisania,** wybierz rolę, która ma zostać przypisana do użytkownika Alain (na przykład administrator aplikacji ), _a_ następnie wybierz **pozycję Wybierz**.

    ![Strona Przypisane role — wyświetlanie wybranej roli](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Rola administrator aplikacji jest przypisana do użytkownika Alain Charon i pojawia się na stronie **Alain Charon - Administrative roles (Alain Charon — role** administracyjne).

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli

Jeśli musisz usunąć przypisanie roli z użytkownika, możesz to również zrobić na stronie **Alain Charon — role administracyjne.**

### <a name="to-remove-a-role-assignment-from-a-user"></a>Aby usunąć przypisanie roli z użytkownika

1. Wybierz **Azure Active Directory**, wybierz **pozycję Użytkownicy,** a następnie wyszukaj i wybierz użytkownika, który usunął przypisanie roli. Na przykład _Alain Charon_.

2. Wybierz **pozycję Przypisane role,** wybierz **pozycję administrator aplikacji**, a następnie wybierz pozycję **Usuń przypisanie.**

    ![Strona Przypisane role z wybraną rolą i opcją usuwania](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Rola administrator aplikacji została usunięta z roli Alain Charon i nie jest już wyświetlana na stronie **Alain Charon - Administrative roles (Alain Charon — role** administracyjne).

## <a name="next-steps"></a>Następne kroki

- [Dodawanie lub usuwanie użytkowników](add-users-azure-active-directory.md)

- [Dodawanie lub zmienianie informacji o profilu](active-directory-users-profile-azure-portal.md)

- [Dodawanie użytkowników-gości z innego katalogu](../external-identities/what-is-b2b.md)

Inne zadania zarządzania użytkownikami, które można wyewidencjonić, są Azure Active Directory [dokumentacji zarządzania użytkownikami.](../enterprise-users/index.yml)
