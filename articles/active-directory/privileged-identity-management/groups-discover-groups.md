---
title: Identyfikowanie grupy do zarządzania w usłudze Privileged Identity Management — Azure AD | Microsoft Docs
description: Dowiedz się, jak dołączyć grupy, które można przypisać do ról, aby zarządzać grupami dostępu uprzywilejowanego w Privileged Identity Management (PIM).
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
ms.date: 08/03/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76f7b61b6fb065409a2fe4b2ed8f599f321cd1db
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542300"
---
# <a name="bring-a-privileged-access-groups-preview-into-privileged-identity-management"></a>Przenoszenie uprzywilejowanych grup dostępu (wersja zapoznawcza) do Privileged Identity Management

W Azure Active Directory (Azure AD) można przypisać wbudowane role usługi Azure AD do grup w chmurze, aby uprościć zarządzanie przypisaniami ról. Aby chronić role usługi Azure AD i zabezpieczyć dostęp, można teraz używać Privileged Identity Management (PIM) do zarządzania dostępem just in Time dla członków lub właścicieli tych grup. Aby zarządzać grupą umożliwiającą Przypisywanie roli usługi Azure AD jako uprzywilejowaną grupę dostępu w Privileged Identity Management, należy ją w obszarze zarządzania w usługach PIM.

## <a name="identify-groups-to-manage"></a>Identyfikowanie grup do zarządzania

Można utworzyć grupę umożliwiającą Przypisywanie ról w usłudze Azure AD zgodnie z opisem w temacie Tworzenie grupy z możliwością [przypisywania ról w Azure Active Directory](../users-groups-roles/roles-groups-create-eligible.md). Jesteś właścicielem grupy, aby zarządzać nią za pomocą Privileged Identity Management.

1. [Zaloguj się do usługi Azure AD](https://aad.portal.azure.com) przy użyciu uprawnień roli administrator ról uprzywilejowanych.
1. Wybierz pozycję **grupy** , a następnie wybierz grupę, która ma zostać przypisana do roli, którą chcesz zarządzać w usłudze PIM. Można wyszukiwać i filtrować listę.

    ![Znajdowanie grupy przypisanej do roli do zarządzania w usłudze PIM](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Otwórz grupę i wybierz pozycję **dostęp uprzywilejowany (wersja zapoznawcza)**.

    ![Otwórz środowisko Privileged Identity Management](./media/groups-discover-groups/groups-discover-groups.png)

1. Rozpocznij zarządzanie przypisaniami w usłudze PIM.

    ![Zarządzanie przypisaniami w Privileged Identity Management](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> Gdy grupa uprzywilejowanych dostępu jest zarządzana, nie można jej wyprowadzić z zarządzania. Zapobiega to usunięciu przez innego administratora zasobów Privileged Identity Management ustawień.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie przypisań grup dostępu uprzywilejowanego w Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Przypisywanie uprzywilejowanych grup dostępu w Privileged Identity Management](pim-resource-roles-assign-roles.md)
