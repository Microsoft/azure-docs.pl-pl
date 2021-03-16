---
title: Wyświetlanie listy definicji ról usługi Azure AD — Azure AD
description: Dowiedz się, jak wyświetlić listę wbudowanych i niestandardowych ról platformy Azure.
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
ms.openlocfilehash: d37b2988d32c854e4184adee998341ebadcee053
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467886"
---
# <a name="list-azure-ad-role-definitions"></a>Wyświetlanie listy definicji ról usługi Azure AD

Definicja roli to zbiór uprawnień, które mogą być wykonywane, takie jak Odczyt, zapis i usuwanie. Zwykle jest nazywana rolą. Azure Active Directory ma ponad 60 ról wbudowanych lub można utworzyć własne role niestandardowe. Jeśli kiedykolwiek zastanawiasz się, jak naprawdę "Jakie są te role?", zobaczysz szczegółową listę uprawnień dla każdej z ról.

W tym artykule opisano sposób tworzenia listy wbudowanych i niestandardowych ról usługi Azure AD wraz z ich uprawnieniami.

## <a name="list-all-roles"></a>Wyświetl wszystkie role

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) i wybierz pozycję **Azure Active Directory**.

1. Wybierz **role i Administratorzy** , aby wyświetlić listę wszystkich dostępnych ról.

    ![lista ról w portalu usługi Azure AD](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. Po prawej stronie wybierz wielokropek, a następnie **Opis** , aby wyświetlić pełną listę uprawnień dla roli.

    Strona zawiera linki do odpowiedniej dokumentacji ułatwiające zarządzanie rolami.

    ![Zrzut ekranu przedstawiający stronę "Global administrator-Description".](./media/role-definitions-list/role-description.png)

## <a name="next-steps"></a>Następne kroki

* Podziel się z nami na [forum ról administracyjnych usługi Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Aby uzyskać więcej informacji o rolach i przypisaniu roli administratora, zobacz [Przypisywanie ról administratorów](permissions-reference.md).
* Domyślne uprawnienia użytkownika można znaleźć w [porównaniu z domyślnymi uprawnieniami gościa i użytkownika](../fundamentals/users-default-permissions.md).
