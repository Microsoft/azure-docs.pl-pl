---
title: Aktywowanie ról zasobów platformy Azure w programie PIM — Azure AD | Microsoft Docs
description: Dowiedz się, jak aktywować role zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
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
ms.date: 07/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f38ef7db114705392bd1d3dc6f9a4562a809e20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86023883"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Aktywuj moje role zasobów platformy Azure w Privileged Identity Management

Użyj Privileged Identity Management (PIM), aby zezwolić uprawnionym członkom roli zasobów platformy Azure na planowanie aktywacji na przyszłą datę i godzinę. Mogą również wybrać określony czas trwania aktywacji w maksymalnym (skonfigurowanym przez administratorów).

Ten artykuł dotyczy członków, którzy muszą aktywować swoją rolę zasobów platformy Azure w Privileged Identity Management.

## <a name="activate-a-role"></a>Aktywuj rolę

Gdy musisz przejąć rolę zasobów platformy Azure, możesz zażądać aktywacji przy użyciu opcji nawigacji **Moje role** w Privileged Identity Management.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**. Aby uzyskać informacje na temat dodawania kafelka Privileged Identity Management do pulpitu nawigacyjnego, zobacz [Rozpoczynanie korzystania z Privileged Identity Management](pim-getting-started.md).

1. Wybierz pozycję **Moje role**.

    ![Strona Moje role pokazujące role, które można uaktywnić](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Wybierz pozycję **role zasobów platformy Azure** , aby wyświetlić listę kwalifikujących się ról zasobów platformy Azure.

   ![Moje role — strona ról zasobów platformy Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png)

1. Na liście **role zasobów platformy Azure** Znajdź rolę, którą chcesz aktywować.

    ![Role zasobów platformy Azure — lista wszystkich uprawnionych ról](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Wybierz pozycję **Aktywuj** , aby otworzyć stronę Aktywacja.

     ![Otwarte okienko Aktywuj z zakresem, czasem rozpoczęcia, czasem trwania i przyczynie](./media/pim-resource-roles-activate-your-roles/azure-role-eligible-activate.png)

1. Jeśli Twoja rola wymaga uwierzytelniania wieloskładnikowego, wybierz opcję **Weryfikuj swoją tożsamość przed kontynuowaniem**. Musisz tylko raz uwierzytelnić się na sesję.

    ![Weryfikuj moją tożsamość z użyciem usługi MFA przed aktywacją roli](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Wybierz pozycję **Weryfikuj moją tożsamość** i postępuj zgodnie z instrukcjami, aby zapewnić dodatkową weryfikację zabezpieczeń.

    ![Ekran zapewniający weryfikację zabezpieczeń, np. kod PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Jeśli chcesz określić ograniczony zakres, wybierz pozycję **zakres** , aby otworzyć okienko filtr zasobów.

    Najlepszym rozwiązaniem jest zażądanie dostępu do zasobów, które są potrzebne. W okienku Filtr zasobów można określić grupy zasobów lub zasoby, do których jest wymagany dostęp.

    ![Aktywuj okienko filtru zasobów, aby określić zakres](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. W razie potrzeby określ czas rozpoczęcia aktywacji niestandardowej. Członek zostanie aktywowany po wybranym czasie.

1. W polu **Przyczyna** wprowadź przyczynę żądania aktywacji.

    ![Okienko ukończono aktywację z zakresem, czasem rozpoczęcia, czasem trwania i przyczynie](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Wybierz pozycję **Aktywuj**.

    Jeśli [rola wymaga zatwierdzenia](pim-resource-roles-approval-workflow.md) do aktywacji, w prawym górnym rogu przeglądarki zostanie wyświetlone powiadomienie z informacją, że żądanie oczekuje na zatwierdzenie.

    ![Żądanie aktywacji oczekuje na powiadomienie o zatwierdzeniu](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Wyświetlanie stanu żądań

Można wyświetlić stan oczekujących żądań, które należy aktywować.

1. Otwórz Azure AD Privileged Identity Management.

1. Wybierz pozycję **Moje żądania** , aby wyświetlić listę ról usługi Azure AD i żądań ról zasobów platformy Azure.

    ![Moje żądania — Strona zasobów platformy Azure zawierająca oczekujące żądania](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Przewiń w prawo, aby wyświetlić kolumnę **stan żądania** .

## <a name="cancel-a-pending-request"></a>Anulowanie oczekującego żądania

Jeśli nie wymagasz aktywacji roli wymagającej zatwierdzenia, możesz w dowolnym momencie anulować oczekujące żądanie.

1. Otwórz Azure AD Privileged Identity Management.

1. Wybierz pozycję **Moje żądania**.

1. Dla roli, którą chcesz anulować, wybierz łącze **Anuluj** .

    Po wybraniu przycisku Anuluj żądanie zostanie anulowane. Aby ponownie aktywować rolę, trzeba będzie przesłać nowe żądanie aktywacji.

   ![Lista moje żądania z wyróżnioną akcją anulowania](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Nie udzielono uprawnień po aktywowaniu roli

Po aktywowaniu roli w Privileged Identity Management aktywacja może nie być natychmiast propagowana do wszystkich portali, które wymagają roli uprzywilejowanej. Czasami nawet po rozpropagowaniu zmiany buforowanie internetowe w portalu może spowodować, że zmiany nie zostaną natychmiast uwzględnione. Jeśli aktywacja jest opóźniona, Oto co należy zrobić.

1. Wyloguj się z witryny Azure Portal, a następnie zaloguj się ponownie.
1. W Privileged Identity Management Sprawdź, czy jesteś członkiem roli.

## <a name="next-steps"></a>Następne kroki

- [Przedłuż lub Odnów role zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Aktywuj moje role usługi Azure AD w Privileged Identity Management](pim-how-to-activate-role.md)
