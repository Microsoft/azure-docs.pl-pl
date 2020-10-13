---
title: Zatwierdzanie żądań aktywacji dla członków i właścicieli grupy w Privileged Identity Management — Azure AD
description: Dowiedz się, jak zatwierdzać lub odrzucać żądania dotyczące grup przypisanych do ról w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a3b6dad33ce678478a201dc8b7b612f4b8507c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536991"
---
# <a name="approve-activation-requests-for-privileged-access-group-members-and-owners-preview"></a>Zatwierdzanie żądań aktywacji dla członków i właścicieli grupy uprzywilejowanego dostępu (wersja zapoznawcza)

Za pomocą Privileged Identity Management (PIM) w usłudze Azure Active Directory (Azure AD) można skonfigurować członków i właścicieli grup dostępu uprzywilejowanego w celu wymagania zatwierdzenia dla aktywacji, a następnie wybrać użytkowników lub grupy z organizacji usługi Azure AD jako delegowane osoby zatwierdzające. Zalecamy wybranie co najmniej dwóch osób zatwierdzających dla każdej grupy w celu ograniczenia obciążenia dla administratora ról uprzywilejowanych. Delegowane osoby zatwierdzające mają 24 godziny na zatwierdzanie żądań. Jeśli żądanie nie zostanie zatwierdzone w ciągu 24 godzin, wówczas uprawniony użytkownik musi ponownie przesłać nowe żądanie. Nie można skonfigurować okna czasu zatwierdzania 24-godzinnego.

Wykonaj kroki opisane w tym artykule, aby zatwierdzić lub odrzucić żądania dotyczące ról zasobów platformy Azure.

## <a name="view-pending-requests"></a>Wyświetlanie oczekujących żądań

W przypadku delegowanej osoby zatwierdzającej otrzymasz powiadomienie e-mail, gdy żądanie roli zasobu platformy Azure oczekuje na zatwierdzenie. Oczekujące żądania można wyświetlić w Privileged Identity Management.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **Zatwierdź żądania**.

    ![Zatwierdź żądania — na stronie zasobów platformy Azure zostanie wyświetlona prośba o przejrzenie](./media/groups-approval-workflow/groups-select-request.png)

    W sekcji **żądania aktywacji roli** zobaczysz listę żądań oczekujących na zatwierdzenie.

## <a name="approve-requests"></a>Zatwierdzanie żądań

1. Znajdź i wybierz żądanie, które chcesz zatwierdzić, a następnie wybierz pozycję **Zatwierdź**.

    ![Zrzut ekranu pokazujący stronę "Zatwierdź żądania" z wyróżnionymi przyciskami "Zatwierdź" i "Potwierdź".](./media/groups-approval-workflow/groups-confirm-approval.png)

1. W polu **uzasadnienie** wprowadź uzasadnienie biznesowe.

1. Wybierz pozycję **Potwierdź**. Powiadomienie platformy Azure zostanie wygenerowane na podstawie zatwierdzenia.

## <a name="deny-requests"></a>Odmów żądań

1. Znajdź i wybierz żądanie, które chcesz odmówić, i wybierz pozycję **Odmów**.

    ![Okienko zatwierdzanie żądań — zatwierdzanie lub odrzucanie z użyciem pola szczegóły i uzasadnienia](./media/groups-approval-workflow/groups-confirm-denial.png)

1. W polu **uzasadnienie** wprowadź uzasadnienie biznesowe.

1. Wybierz pozycję **Potwierdź**. Powiadomienie platformy Azure jest generowane przez odmowę.

## <a name="workflow-notifications"></a>Powiadomienia przepływu pracy

Oto kilka informacji o powiadomieniach dotyczących przepływu pracy:

- Osoby zatwierdzające są powiadamiane za pośrednictwem poczty e-mail, gdy żądanie przypisania grupy oczekuje na ich przegląd. Powiadomienia e-mail zawierają bezpośredni link do żądania, gdzie osoba zatwierdzająca może zatwierdzić lub odmówić.
- Żądania są rozwiązywane przez pierwszej osoby zatwierdzającej, która zatwierdza lub odmówi.
- Gdy osoba zatwierdzająca odpowie na żądanie, wszyscy osoby zatwierdzające są powiadamiani o akcji.

>[!Note]
>Administrator, który uważa, że zatwierdzony użytkownik nie powinien być aktywny, może usunąć aktywne przypisanie grupy w Privileged Identity Management. Mimo że administratorzy zasobów nie są powiadamiani o oczekujących żądaniach, chyba że są osobami zatwierdzającymi, mogą wyświetlać i anulować oczekujące żądania dla wszystkich użytkowników, wyświetlając oczekujące żądania w Privileged Identity Management.

## <a name="next-steps"></a>Następne kroki

- [Przedłuż lub Odnów przypisania grup w Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Powiadomienia e-mail w Privileged Identity Management](pim-email-notifications.md)
- [Zatwierdzanie lub odrzucanie żądań przypisania grupy w Privileged Identity Management](azure-ad-pim-approval-workflow.md)
