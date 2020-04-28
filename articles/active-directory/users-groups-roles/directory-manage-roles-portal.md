---
title: Wyświetlanie i przypisywanie uprawnień roli administratora — usługa Azure AD | Microsoft Docs
description: Teraz można wyświetlać członków roli administratora usługi Azure AD w portalu i zarządzać nimi. Dla osób, które często zarządzają przypisaniami ról.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2815b496545ca5f920c00df7b2b5b7efe15c12a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74900911"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Wyświetlanie i przypisywanie ról administratorów w Azure Active Directory

Teraz można wyświetlać wszystkich członków ról administratorów w portalu Azure Active Directory i zarządzać nimi. Jeśli często zarządzasz przypisaniami ról, prawdopodobnie wolisz skorzystać z tego środowiska. A jeśli kiedykolwiek zastanawiasz się, że "czego dotyczy ta rola"? ", możesz wyświetlić szczegółową listę uprawnień dla każdej roli administratora usługi Azure AD.

## <a name="view-all-roles"></a>Wyświetl wszystkie role

Wyszukaj i wybierz pozycję **Azure Active Directory**. Wybierz **role i Administratorzy** , aby wyświetlić listę wszystkich dostępnych ról.

Kliknij przycisk wielokropka po prawej stronie każdego wiersza, aby otworzyć szczegółowy opis roli.

![lista ról w portalu usługi Azure AD](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Wyświetl moje role

Można również łatwo wyświetlić swoje uprawnienia. Wybierz **rolę** na stronie **role i Administratorzy** , aby wyświetlić role, które są aktualnie przypisane do użytkownika.

## <a name="view-assignments-for-a-role"></a>Wyświetlanie przypisań roli

Kliknij rolę, aby wyświetlić użytkowników przypisanych do roli. Możesz wybrać pozycję **Zarządzaj w usłudze PIM** , aby uzyskać dodatkowe możliwości zarządzania. Administratorzy ról uprzywilejowanych mogą zmieniać przypisania "trwałe" (zawsze aktywne w roli) do "kwalifikującego się" (w roli tylko w przypadku podniesionych uprawnień). Jeśli nie masz usługi PIM, nadal możesz wybrać pozycję **Zarządzaj w usłudze PIM** , aby zarejestrować się w celu korzystania z wersji próbnej. Privileged Identity Management wymaga [planu licencji Azure AD — wersja Premium P2](../privileged-identity-management/subscription-requirements.md).

![Lista członków roli administratora](./media/directory-manage-roles-portal/member-list.png)

Jeśli jesteś administratorem globalnym lub administratorem roli uprzywilejowanej, możesz łatwo dodać lub usunąć członków, odfiltrować listę lub wybrać element członkowski, aby wyświetlić aktywne przypisane role.

## <a name="view-a-users-role-permissions"></a>Wyświetlanie uprawnień roli użytkownika

Gdy przeglądasz członków roli, wybierz pozycję **Opis** , aby wyświetlić pełną listę uprawnień przyznanych przez przypisanie roli. Strona zawiera linki do odpowiedniej dokumentacji ułatwiające zarządzanie rolami w katalogu.

![Lista uprawnień dla roli administratora](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Następne kroki

* Podziel się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Aby uzyskać więcej informacji o rolach i przypisaniu roli administratora, zobacz [Przypisywanie ról administratorów](directory-assign-admin-roles.md).
* Domyślne uprawnienia użytkownika można znaleźć w [porównaniu z domyślnymi uprawnieniami gościa i użytkownika](../fundamentals/users-default-permissions.md).
