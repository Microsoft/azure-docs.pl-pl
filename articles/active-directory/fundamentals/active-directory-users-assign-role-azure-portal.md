---
title: Przypisywanie ról katalogów do użytkowników — Azure Active Directory | Microsoft Docs
description: Instrukcje dotyczące sposobu przypisywania ról administratora i niebędących administratorami do użytkowników z Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df52969ea79e5d1af132aa82c2ec1ceedb92b82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75422900"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Przypisywanie ról administratorów i niebędących administratorami do użytkowników z Azure Active Directory
Jeśli użytkownik w organizacji wymaga uprawnień do zarządzania zasobami usługi Azure Active Directory (Azure AD), należy przypisać użytkownikowi odpowiednią rolę w usłudze Azure AD na podstawie akcji wymaganych przez użytkownika do wykonania.

Aby uzyskać więcej informacji na temat dostępnych ról, zobacz [Przypisywanie ról administratorów w Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Aby uzyskać więcej informacji na temat dodawania użytkowników, zobacz [Dodawanie nowych użytkowników do Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Przypisywanie ról
Typowym sposobem przypisywania ról usługi Azure AD do użytkownika jest strona **roli katalogu** dla użytkownika.

Role można także przypisywać przy użyciu Privileged Identity Management (PIM). Aby uzyskać szczegółowe informacje na temat korzystania z usługi PIM, zobacz [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Aby przypisać rolę do użytkownika
1. Przejdź do [Azure Portal](https://portal.azure.com/) i zaloguj się przy użyciu konta administratora globalnego dla katalogu. 

2. Wyszukaj i wybierz pozycję **Azure Active Directory**.

      ![Azure Portal Wyszukaj Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Wybierz pozycję **Użytkownicy**.

4. Wyszukaj i wybierz użytkownika pobierającego przypisanie roli. Na przykład _Alain Charon_.

      ![Strona wszyscy użytkownicy — Wybieranie użytkownika](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Na stronie **Alain Charon — profil** wybierz pozycję **przypisane role**.

    Zostanie wyświetlona strona **rola usługi Alain Charon** .

6. Wybierz pozycję **Dodaj przypisanie**, wybierz rolę, która ma zostać przypisana do Alain (na przykład _administrator aplikacji_), a następnie wybierz **pozycję Wybierz**.

    ![Strona przypisane role — wyświetlana została wybrana rola](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Rola Administrator aplikacji jest przypisywana do Alain Charon i pojawia się na stronie **roli Charon Alain-Directory** .

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli
Jeśli musisz usunąć przypisanie roli z użytkownika, możesz to zrobić również na stronie **roli Alain Charon-Directory** .

### <a name="to-remove-a-role-assignment-from-a-user"></a>Aby usunąć przypisanie roli z użytkownika

1. Wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wyszukaj i wybierz użytkownika, który usunął przypisanie roli. Na przykład _Alain Charon_.

2. Wybierz pozycję **przypisane role**, wybierz pozycję **administrator aplikacji**, a następnie wybierz pozycję **Usuń przypisanie**.

    ![Strona przypisane role, która wyświetla wybraną rolę i opcję Usuń](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Rola Administrator aplikacji jest usuwana z Alain Charon i nie jest już wyświetlana na stronie **roli Charon Alain-Directory** .

## <a name="next-steps"></a>Następne kroki
- [Dodawanie lub usuwanie użytkowników](add-users-azure-active-directory.md)

- [Dodawanie lub zmiana informacji o profilu](active-directory-users-profile-azure-portal.md)

- [Dodawanie użytkowników-gości z innego katalogu](../b2b/what-is-b2b.md)

Można też wykonywać inne zadania zarządzania użytkownikami, takie jak przypisywanie delegatów, korzystanie z zasad i udostępnianie kont użytkowników. Aby uzyskać więcej informacji na temat innych dostępnych akcji, zobacz [Azure Active Directory dokumentacja dotycząca zarządzania użytkownikami](../users-groups-roles/index.yml).


