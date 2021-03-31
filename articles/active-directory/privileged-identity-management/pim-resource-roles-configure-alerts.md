---
title: Konfigurowanie alertów zabezpieczeń dla ról zasobów platformy Azure w Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak skonfigurować alerty zabezpieczeń dla ról zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
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
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bfca096eb49ee9f1807935de1dac49151cc8ac3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84743766"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Konfigurowanie alertów zabezpieczeń dla ról zasobów platformy Azure w Privileged Identity Management

Privileged Identity Management (PIM) generuje alerty w przypadku podejrzenia lub niebezpiecznego działania w organizacji Azure Active Directory (Azure AD). Po wyzwoleniu alertu zostanie on wyświetlony na stronie alerty.

![Zasoby platformy Azure — alert dotyczący wyświetlania na stronie alertów, poziom ryzyka i liczba](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Przegląd alertów

Wybierz Alert, aby wyświetlić raport zawierający listę użytkowników lub ról, które wyzwoliły alert, oraz wskazówki dotyczące korygowania.

![Raport o alertach przedstawiający czas ostatniego skanowania, opis, kroki zaradcze, typ, ważność, wpływ na zabezpieczenia oraz sposób zapobiegania następnym](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Alerty

| Alerty | Ważność | Wyzwalacz | Zalecenie |
| --- | --- | --- | --- |
| **Zbyt wielu właścicieli przypisanych do zasobu** |Śred. |Zbyt wielu użytkowników ma rolę właściciela. |Zapoznaj się z użytkownikami na liście i przypisz je do mniej uprzywilejowanych ról. |
| **Zbyt wiele stałych właścicieli przypisanych do zasobu** |Śred. |Zbyt wielu użytkowników jest trwale przypisanych do roli. |Przejrzyj użytkowników na liście i przypisz go ponownie, aby wymagać aktywacji do użycia przez rolę. |
| **Utworzono zduplikowaną rolę** |Śred. |Wiele ról ma takie same kryteria. |Użyj tylko jednej z tych ról. |

### <a name="severity"></a>Ważność

- **Wysoka**: wymaga natychmiastowej akcji z powodu naruszenia zasad. 
- **Średni**: nie wymaga natychmiastowego działania, ale sygnalizuje potencjalne naruszenie zasad.
- **Niska**: nie wymaga natychmiastowego działania, ale sugeruje preferowaną zmianę zasad.

## <a name="configure-security-alert-settings"></a>Konfigurowanie ustawień alertów zabezpieczeń

Na stronie Alerty przejdź do pozycji **Ustawienia**.

![Strona alerty z wyróżnionymi ustawieniami](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Dostosuj ustawienia różnych alertów, aby współpracowały ze środowiskiem i celami zabezpieczeń.

![Ustawianie strony alertu do włączania i konfigurowania ustawień](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Następne kroki

- [Skonfiguruj ustawienia roli zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
