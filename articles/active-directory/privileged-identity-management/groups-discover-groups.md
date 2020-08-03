---
title: Odnajdywanie grup przypisanych do ról w celu zarządzania nimi w usłudze PIM — Azure AD | Microsoft Docs
description: Informacje o odnajdywaniu grup z uprawnieniami do przypisywania ról w celu zarządzania nimi w Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b66a41f0f1aa48433dac77fca56d5e2877989a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506006"
---
# <a name="discover-privileged-access-groups-preview-to-manage-in-privileged-identity-management"></a>Odnajdywanie uprzywilejowanych grup dostępu (wersja zapoznawcza) w celu zarządzania nimi w Privileged Identity Management

W Azure Active Directory (Azure AD) można przypisać wbudowane role usługi Azure AD do grup w chmurze, aby uprościć zarządzanie przypisaniami ról. Teraz można użyć Privileged Identity Management (PIM), aby przypisać uprawnienia do członkostwa lub własności tych grup, chronić role usługi Azure AD i uzyskiwać zabezpieczenia dostępu. Aby można było zarządzać grupą umożliwiającą Przypisywanie roli usługi Azure AD jako uprzywilejowaną grupę dostępu w Privileged Identity Management, należy ją odnaleźć i zarządzać w usłudze PIM.

## <a name="discover-resources"></a>Odnajdywanie zasobów

1. [Zaloguj się do usługi Azure AD](https://aad.portal.azure.com) przy użyciu uprawnień roli administrator ról uprzywilejowanych.
1. Utwórz grupę z możliwością przypisywania ról w usłudze Azure AD. Musisz być właścicielem grupy, aby odnajdywać ją i zarządzać nią przy użyciu Privileged Identity Management.
1. Otwórz **Privileged Identity Management**.
1. Wybierz pozycję **dostęp uprzywilejowany (wersja zapoznawcza)**.

    ![Polecenie odnajdywania grup po raz pierwszy](./media/groups-discover-groups/groups-discover-groups.png)

1. Wybierz pozycję **odnajdywanie grup**.
1. Wyszukaj według nazwy grupy.
1. Wybierz grupę i wybierz pozycję **Zarządzaj grupami** , aby wprowadzić ją w obszarze Zarządzanie PIM.

    ![Odnajdywanie grup bez zasobów wymienionych na potrzeby pierwszego uruchomienia](./media/groups-discover-groups/groups-bring-under-management.png)

    > [!NOTE]
    > Gdy grupa uprzywilejowanych dostępu jest zarządzana, nie można jej wyprowadzić z zarządzania. Zapobiega to usunięciu przez innego administratora zasobów Privileged Identity Management ustawień.

1. Jeśli zostanie wyświetlony komunikat z prośbą o potwierdzenie dołączenia wybranego zasobu do zarządzania, wybierz pozycję **tak**.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie przypisań grup dostępu uprzywilejowanego w Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Przypisywanie uprzywilejowanych grup dostępu w Privileged Identity Management](pim-resource-roles-assign-roles.md)
