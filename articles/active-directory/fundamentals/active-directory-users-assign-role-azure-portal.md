---
title: Przypisywanie ról usługi Azure AD do użytkowników — Azure Active Directory | Microsoft Docs
description: Instrukcje dotyczące sposobu przypisywania ról administratora i niebędących administratorami do użytkowników z Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/15/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a468b5f43e8b024dd1da6e382049f8cce6de295
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797369"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Przypisywanie ról administratorów i niebędących administratorami do użytkowników z Azure Active Directory

W Azure Active Directory (Azure AD), jeśli jeden z użytkowników potrzebuje uprawnień do zarządzania zasobami usługi Azure AD, należy przypisać je do roli, która udostępnia wymagane uprawnienia. Aby uzyskać informacje na temat ról zarządzania zasobami platformy Azure i ról zarządzania zasobami usługi Azure AD, zobacz [role administratora subskrypcji klasycznej, role platformy Azure i role usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

Aby uzyskać więcej informacji na temat dostępnych ról usługi Azure AD, zobacz [Przypisywanie ról administratorów w Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Aby dodać użytkowników, zobacz [Dodawanie nowych użytkowników do Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Przypisywanie ról

Typowym sposobem przypisywania ról usługi Azure AD do użytkownika jest strona **przypisane role** dla użytkownika. Można również uzyskać podwyższony poziom uprawnień w czasie do roli przy użyciu Privileged Identity Management (PIM). Aby uzyskać więcej informacji na temat korzystania z usługi PIM, zobacz [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

> [!Note]
> Jeśli masz plan licencji Azure AD — wersja Premium P2 i już korzystasz z programu PIM, wszystkie zadania związane z zarządzaniem rolami są wykonywane w [Privileged Identity Management środowisku](../users-groups-roles/directory-manage-roles-portal.md).
>
> ![Role usługi Azure AD zarządzane w usłudze PIM dla użytkowników korzystających już z usług PIM i z licencją premium P2](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>Przypisywanie roli do użytkownika

1. Przejdź do [Azure Portal](https://portal.azure.com/) i zaloguj się przy użyciu konta administratora globalnego dla katalogu.

2. Wyszukaj i wybierz pozycję **Azure Active Directory**.

      ![Azure Portal Wyszukaj Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. Wybierz pozycję **Użytkownicy**.

4. Wyszukaj i wybierz użytkownika pobierającego przypisanie roli. Na przykład _Alain Charon_.

      ![Strona wszyscy użytkownicy — Wybieranie użytkownika](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Na stronie **Alain Charon — profil** wybierz pozycję **przypisane role**.

    Zostanie wyświetlona strona **Alain Charon — role administracyjne** .

6. Wybierz pozycję **Dodaj przypisania**, wybierz rolę, która ma zostać przypisana do Alain (na przykład _administrator aplikacji_), a następnie wybierz **pozycję Wybierz**.

    ![Strona przypisane role — wyświetlana została wybrana rola](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Rola Administrator aplikacji jest przypisywana do Alain Charon i pojawia się na stronie **role administracyjne Alain Charon** .

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli

Jeśli musisz usunąć przypisanie roli z użytkownika, możesz to zrobić również na stronie **role administracyjne Alain Charon** .

### <a name="to-remove-a-role-assignment-from-a-user"></a>Aby usunąć przypisanie roli z użytkownika

1. Wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wyszukaj i wybierz użytkownika, który usunął przypisanie roli. Na przykład _Alain Charon_.

2. Wybierz pozycję **przypisane role**, wybierz pozycję **administrator aplikacji**, a następnie wybierz pozycję **Usuń przypisanie**.

    ![Strona przypisane role, która wyświetla wybraną rolę i opcję Usuń](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Rola Administrator aplikacji jest usuwana z Alain Charon i nie jest już wyświetlana na stronie **role administracyjne Alain Charon** .

## <a name="next-steps"></a>Następne kroki

- [Dodawanie lub usuwanie użytkowników](add-users-azure-active-directory.md)

- [Dodawanie lub zmiana informacji o profilu](active-directory-users-profile-azure-portal.md)

- [Dodawanie użytkowników-gości z innego katalogu](../b2b/what-is-b2b.md)

Inne zadania zarządzania użytkownikami, które można wyewidencjonować, są dostępne w dokumentacji dotyczącej [zarządzania użytkownikami Azure Active Directory](../users-groups-roles/index.yml).
