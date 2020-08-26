---
title: Zarządzanie grupami, które można przypisać, jako uprzywilejowanych grup dostępu — Azure AD | Microsoft Docs
description: Wyrażanie zgody na dołączenie grup, które można przypisać do ról w celu zarządzania nimi jako uprzywilejowanych grup dostępu w Privileged Identity Management (PIM).
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
ms.date: 08/17/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: a707c66f2562fe442c58ce6292e51c4e67a20ae2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869539"
---
# <a name="manage-privileged-access-groups-preview-in-privileged-identity-management"></a>Zarządzanie uprzywilejowanymi grupami dostępu (wersja zapoznawcza) w Privileged Identity Management

W Azure Active Directory (Azure AD) można przypisać wbudowane role usługi Azure AD do grup w chmurze, aby uprościć zarządzanie przypisaniami ról. Aby chronić role usługi Azure AD i zabezpieczyć dostęp, można teraz używać Privileged Identity Management (PIM) do zarządzania dostępem just in Time dla członków lub właścicieli tych grup. Aby zarządzać grupą umożliwiającą Przypisywanie roli usługi Azure AD jako uprzywilejowaną grupę dostępu w Privileged Identity Management, należy ją w obszarze zarządzania w usługach PIM.

## <a name="identify-groups-to-manage"></a>Identyfikowanie grup do zarządzania

Można utworzyć grupę umożliwiającą Przypisywanie ról w usłudze Azure AD zgodnie z opisem w temacie Tworzenie grupy z możliwością [przypisywania ról w Azure Active Directory](../users-groups-roles/roles-groups-create-eligible.md). Jesteś właścicielem grupy, aby zarządzać nią w Privileged Identity Management.

1. [Zaloguj się do usługi Azure AD](https://aad.portal.azure.com) przy użyciu uprawnień administratora ról uprzywilejowanych.
1. Wybierz pozycję **grupy** , a następnie wybierz grupę, do której chcesz zarządzać. Można wyszukiwać lub filtrować listę.

    ![Znajdowanie grupy przypisanej do roli do zarządzania w usłudze PIM](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Otwórz grupę i wybierz pozycję **dostęp uprzywilejowany (wersja zapoznawcza)**.

    ![Otwórz środowisko Privileged Identity Management](./media/groups-discover-groups/groups-discover-groups.png)

1. Jeśli grupy nie zostały jeszcze objęte zarządzaniem w usłudze PIM, wybierz pozycję **Włącz dostęp uprzywilejowany** , aby wyrazić zgodę na zarządzanie. Tylko administrator globalny lub właściciel grupy może udzielić tej zgody.

    ![wyrażanie zgody na Zarządzanie grupą w Privileged Identity Management, jeśli jest to wymagane](./media/groups-discover-groups/consent-page.png)

1. Rozpocznij zarządzanie przypisaniami w usłudze PIM.

    ![Zarządzanie przypisaniami w Privileged Identity Management](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> Gdy grupa uprzywilejowanych dostępu jest zarządzana, nie można jej wyprowadzić z zarządzania. Zapobiega to usunięciu ustawień Privileged Identity Management przez innego administratora.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie przypisań grup dostępu uprzywilejowanego w Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Przypisywanie uprzywilejowanych grup dostępu w Privileged Identity Management](pim-resource-roles-assign-roles.md)
