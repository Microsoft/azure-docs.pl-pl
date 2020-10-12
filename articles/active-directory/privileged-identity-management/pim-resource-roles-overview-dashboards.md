---
title: Pulpity nawigacyjne zasobów do przeglądów dostępu w usłudze PIM — Azure AD | Microsoft Docs
description: Opisuje sposób używania pulpitu nawigacyjnego zasobów do przeprowadzania przeglądu dostępu w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e95eaa5b0e86a7470fc48edc23b2dbfb47e4b10c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84743732"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Użyj pulpitu nawigacyjnego zasobów, aby przeprowadzić przegląd dostępu w Privileged Identity Management

Za pomocą pulpitu nawigacyjnego zasobów można wykonać przegląd dostępu w Privileged Identity Management (PIM). Pulpit nawigacyjny widoku administratora w Azure Active Directory (Azure AD) ma trzy składniki podstawowe:

- Graficzna reprezentacja aktywacji ról zasobów
- Wykresy przedstawiające dystrybucję przypisań ról według typu przypisania
- Obszar danych zawierający informacje o nowych przypisaniach ról

![Zrzut ekranu pulpitu nawigacyjnego widoku administratora, przedstawiający wykresy i wykresy](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Zrzut ekranu przedstawiający pulpit nawigacyjny widoku administratora, pokazujący listy danych](media/pim-resource-roles-overview-dashboards/role-settings.png)

Graficzna reprezentacja aktywacji ról zasobów obejmuje ostatnie siedem dni. Te dane są objęte zakresem wybranego zasobu i są wyświetlane aktywacje dla najbardziej typowych ról (właściciel, współautor, administrator dostępu użytkowników) i dla wszystkich połączonych ról.

Po jednej stronie wykresu aktywacji dwa wykresy przedstawiają rozkład przypisań ról według typu przypisania dla użytkowników i grup. Możesz zmienić wartość na procent (lub odwrotnie), wybierając wycinek wykresu.

Poniżej wykresów przedstawiono liczbę użytkowników i grup z nowymi przypisaniami ról w ciągu ostatnich 30 dni i role posortowane według całkowitej liczby przypisań w kolejności malejącej.

## <a name="next-steps"></a>Następne kroki

- [Rozpocznij przegląd dostępu dla ról zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-start-access-review.md)
