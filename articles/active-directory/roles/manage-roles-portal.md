---
title: Przypisywanie ról usługi Azure AD do użytkowników — Azure Active Directory
description: Dowiedz się, jak udzielić dostępu użytkownikom w Azure Active Directory, przypisując role usługi Azure AD.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ced586db1b4e417e623431c137c43dac8ba56f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466648"
---
# <a name="assign-azure-ad-roles-to-users"></a>Przypisywanie ról usługi Azure AD do użytkowników

Teraz można wyświetlać wszystkich członków ról administratorów w centrum administracyjnym usługi Azure AD i zarządzać nimi. Jeśli często zarządzasz przypisaniami ról, prawdopodobnie wolisz skorzystać z tego środowiska. W tym artykule opisano sposób przypisywania ról usługi Azure AD przy użyciu Centrum administracyjnego usługi Azure AD.

## <a name="assign-a-role"></a>Przypisywanie roli

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) z uprawnieniami administratora globalnego lub administratora roli uprzywilejowanej.

1. Wybierz pozycję **Azure Active Directory**.

1. Wybierz **role i Administratorzy** , aby wyświetlić listę wszystkich dostępnych ról.

    ![Zrzut ekranu strony role i Administratorzy](./media/manage-roles-portal/roles-and-administrators.png)

1. Wybierz rolę, aby wyświetlić jej przypisania.

    Aby ułatwić znalezienie potrzebnej roli, usługa Azure AD może wyświetlać podzbiory ról na podstawie kategorii ról. Sprawdź filtr **typu** , aby wyświetlić tylko role w wybranym typie.

1. Wybierz pozycję **Dodaj przypisania** , a następnie wybierz użytkowników, których chcesz przypisać do tej roli.

    Jeśli widzisz coś innego niż na poniższej ilustracji, zapoznaj się z uwagą w [Privileged Identity Management (PIM)](#privileged-identity-management-pim) , aby sprawdzić, czy używasz programu PIM.

    ![Lista uprawnień dla roli administratora](./media/manage-roles-portal/add-assignments.png)

1. Wybierz pozycję **Dodaj** , aby przypisać rolę.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Możesz wybrać pozycję **Zarządzaj w usłudze PIM** , aby uzyskać dodatkowe możliwości zarządzania przy użyciu [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md). Administratorzy ról uprzywilejowanych mogą zmieniać przypisania "trwałe" (zawsze aktywne w roli) do "kwalifikującego się" (w roli tylko w przypadku podniesionych uprawnień). Jeśli nie masz Privileged Identity Management, nadal możesz wybrać pozycję **Zarządzaj w usłudze PIM** , aby zarejestrować się w celu korzystania z wersji próbnej. Privileged Identity Management wymaga [planu licencji Azure AD — wersja Premium P2](../privileged-identity-management/subscription-requirements.md).

![Zrzut ekranu przedstawiający stronę "przydziały administratora użytkownika" z wybraną akcją "Zarządzaj w usłudze PIM"](./media/manage-roles-portal/member-list-pim.png)

Jeśli jesteś administratorem globalnym lub administratorem roli uprzywilejowanej, możesz łatwo dodać lub usunąć członków, odfiltrować listę lub wybrać element członkowski, aby wyświetlić aktywne przypisane role.

> [!Note]
> Jeśli masz licencję usługi Azure AD Premium P2 i używasz już Privileged Identity Management, wszystkie zadania związane z zarządzaniem rolami są wykonywane w ramach uprawnień do zarządzania tożsamościami, a nie w usłudze Azure AD.
>
> ![Role usługi Azure AD zarządzane w usłudze PIM dla użytkowników korzystających już z usług PIM i z licencją premium P2](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="next-steps"></a>Następne kroki

* Podziel się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Aby uzyskać więcej informacji na temat ról, zobacz [wbudowane role usługi Azure AD](permissions-reference.md).
* Domyślne uprawnienia użytkownika można znaleźć w [porównaniu z domyślnymi uprawnieniami gościa i użytkownika](../fundamentals/users-default-permissions.md).
