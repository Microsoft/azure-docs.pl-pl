---
title: Zatwierdzanie żądań dla ról zasobów platformy Azure w usłudze PIM — Azure AD | Microsoft Docs
description: Dowiedz się, jak zatwierdzać lub odrzucać żądania dotyczące ról zasobów platformy Azure w programie Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 096da0547806680f62b569f7c7809a7bbb04cb86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84742083"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>Zatwierdzanie lub odrzucanie żądań dotyczących ról zasobów platformy Azure w Privileged Identity Management

Za pomocą Privileged Identity Management (PIM) w usłudze Azure Active Directory (Azure AD) można skonfigurować role, aby wymagały zatwierdzenia dla aktywacji, i wybrać użytkowników lub grupy z organizacji usługi Azure AD jako delegowane osoby zatwierdzające. Zalecamy wybranie co najmniej dwóch osób zatwierdzających dla każdej roli w celu ograniczenia obciążenia dla administratora ról uprzywilejowanych. Delegowane osoby zatwierdzające mają 24 godziny na zatwierdzanie żądań. Jeśli żądanie nie zostanie zatwierdzone w ciągu 24 godzin, wówczas uprawniony użytkownik musi ponownie przesłać nowe żądanie. Nie można skonfigurować okna czasu zatwierdzania 24-godzinnego.

Wykonaj kroki opisane w tym artykule, aby zatwierdzić lub odrzucić żądania dotyczące ról zasobów platformy Azure.

## <a name="view-pending-requests"></a>Wyświetlanie oczekujących żądań

W przypadku delegowanej osoby zatwierdzającej otrzymasz powiadomienie e-mail, gdy żądanie roli zasobu platformy Azure oczekuje na zatwierdzenie. Te oczekujące żądania można wyświetlić w Privileged Identity Management.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **Zatwierdź żądania**.

    ![Zatwierdź żądania — na stronie zasobów platformy Azure zostanie wyświetlona prośba o przejrzenie](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    W sekcji **żądania aktywacji roli** zobaczysz listę żądań oczekujących na zatwierdzenie.

## <a name="approve-requests"></a>Zatwierdzanie żądań

1. Znajdź i wybierz żądanie, które chcesz zatwierdzić. Zostanie wyświetlona strona zatwierdzanie lub odmowa.

    ![Okienko zatwierdzanie żądań — zatwierdzanie lub odrzucanie z użyciem pola szczegóły i uzasadnienia](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. W polu **uzasadnienie** wprowadź uzasadnienie biznesowe.

1. Wybierz pozycję **Zatwierdź**. Otrzymasz powiadomienie o zatwierdzeniu na platformie Azure.

    ![Zatwierdź powiadomienie z zatwierdzeniem żądania](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Odmów żądań

1. Znajdź i wybierz żądanie, które chcesz odmówić. Zostanie wyświetlona strona zatwierdzanie lub odmowa.

    ![Okienko zatwierdzanie żądań — zatwierdzanie lub odrzucanie z użyciem pola szczegóły i uzasadnienia](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. W polu **uzasadnienie** wprowadź uzasadnienie biznesowe.

1. Wybierz pozycję **Odmów**. Zostanie wyświetlone powiadomienie z odmową.

## <a name="workflow-notifications"></a>Powiadomienia przepływu pracy

Oto kilka informacji o powiadomieniach dotyczących przepływu pracy:

- Osoby zatwierdzające są powiadamiane za pośrednictwem poczty e-mail, gdy żądanie dotyczące roli oczekuje na przegląd. Powiadomienia e-mail zawierają bezpośredni link do żądania, gdzie osoba zatwierdzająca może zatwierdzić lub odmówić.
- Żądania są rozwiązywane przez pierwszej osoby zatwierdzającej, która zatwierdza lub odmówi.
- Gdy osoba zatwierdzająca odpowie na żądanie, wszyscy osoby zatwierdzające są powiadamiani o akcji.
- Administratorzy zasobów są powiadamiani, gdy zatwierdzony użytkownik zostanie uaktywniony w roli.

>[!Note]
>Administrator zasobów, który uważa, że zatwierdzona osoba nie powinna być aktywna, może usunąć przypisanie aktywnej roli w Privileged Identity Management. Mimo że administratorzy zasobów nie są powiadamiani o oczekujących żądaniach, chyba że są osobami zatwierdzającymi, mogą wyświetlać i anulować oczekujące żądania dla wszystkich użytkowników, wyświetlając oczekujące żądania w Privileged Identity Management.

## <a name="next-steps"></a>Następne kroki

- [Przedłuż lub Odnów role zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Powiadomienia e-mail w Privileged Identity Management](pim-email-notifications.md)
- [Zatwierdzanie lub odrzucanie żądań dla ról usługi Azure AD w Privileged Identity Management](azure-ad-pim-approval-workflow.md)
