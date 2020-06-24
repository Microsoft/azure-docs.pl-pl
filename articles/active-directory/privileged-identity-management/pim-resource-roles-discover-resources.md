---
title: Odnajdywanie zasobów platformy Azure do zarządzania w usłudze PIM — Azure AD | Microsoft Docs
description: Dowiedz się, jak odnajdywać zasoby platformy Azure do zarządzania w usłudze Azure AD Privileged Identity Management (PIM).
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
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4852f841fc9ac8ebea586d5e24967a1db20b8044
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84743715"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Odnajdywanie zasobów platformy Azure w celu zarządzania nimi w Privileged Identity Management

Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) można poprawić ochronę zasobów platformy Azure. Jest to przydatne w przypadku organizacji, które już używają Privileged Identity Management do ochrony ról usługi Azure AD oraz do grup zarządzania i właścicieli subskrypcji, którzy chcą zabezpieczyć zasoby produkcyjne.

Podczas pierwszego konfigurowania Privileged Identity Management dla zasobów platformy Azure należy odnaleźć i wybrać zasoby, które mają być chronione przez Privileged Identity Management. Nie ma limitu liczby zasobów, którymi można zarządzać za pomocą Privileged Identity Management. Zalecamy jednak rozpoczęcie od najbardziej krytycznych zasobów (produkcyjnych).

## <a name="discover-resources"></a>Odnajdywanie zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **zasoby platformy Azure**.

    Jeśli korzystasz z Privileged Identity Management dla zasobów platformy Azure po raz pierwszy, zobaczysz stronę **odnajdywanie zasobów** .

    ![Okienko odnajdywanie zasobów bez zasobów wymienionych na potrzeby pierwszego uruchomienia](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Jeśli innym administratorem w organizacji już zarządzasz zasobami platformy Azure w Privileged Identity Management, zostanie wyświetlona lista zasobów, które są aktualnie zarządzane.

    ![Okienko odnajdywania zasobów zawierające listę aktualnie zarządzanych zasobów](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Wybierz pozycję **odkryj zasoby** , aby uruchomić środowisko odnajdywania.

    ![Okienko odnajdywania zawierające listę zasobów, którymi można zarządzać, takich jak subskrypcje i grupy zarządzania](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. Na stronie **odnajdywanie** Użyj **filtru stanu zasobów** i **Wybierz typ zasobu** , aby odfiltrować grupy zarządzania lub subskrypcje, do których masz uprawnienia do zapisu. Prawdopodobnie najłatwiej **zacząć od początku** .

    Za pomocą Privileged Identity Management można wyszukiwać i wybierać tylko zasoby grupy zarządzania lub subskrypcji. W przypadku zarządzania grupą zarządzania lub subskrypcją w programie Privileged Identity Management można także zarządzać swoimi zasobami podrzędnymi.

1. Zaznacz pole wyboru obok wszystkich niezarządzanych zasobów, którymi chcesz zarządzać.

1. Wybierz pozycję **Zarządzaj zasobem** , aby rozpocząć zarządzanie wybranymi zasobami.

    > [!NOTE]
    > Zarządzanie grupą lub subskrypcją nie jest możliwe, ponieważ nie jest ona zarządzana. Zapobiega to usunięciu przez innego administratora zasobów Privileged Identity Management ustawień.

    ![Okienko odnajdywania z wybranym zasobem i z wyróżnioną opcją Zarządzaj zasobem](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Jeśli zostanie wyświetlony komunikat z prośbą o potwierdzenie dołączenia wybranego zasobu do zarządzania, wybierz pozycję **tak**.

    ![Komunikat potwierdzający dołączenie wybranych zasobów do zarządzania](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Następne kroki

- [Skonfiguruj ustawienia roli zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Przypisywanie ról zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-assign-roles.md)
