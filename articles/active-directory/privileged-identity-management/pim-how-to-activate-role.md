---
title: Aktywuj moje role usługi Azure AD w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak aktywować role usługi Azure AD w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/18/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 079dcdc9d1667809779489c5d1126bc85cafb881
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511343"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Aktywowanie ról usługi Azure AD w usłudze PIM

Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) upraszcza zarządzanie dostępem uprzywilejowanym do zasobów w usłudze Azure AD oraz innych Usługi online takich jak Microsoft 365 i Microsoft Intune.  

Jeśli masz uprawnienia *kwalifikujące się* do roli administracyjnej, należy *aktywować* przypisanie roli, gdy konieczne jest wykonanie uprzywilejowanych akcji. Na przykład w przypadku okresowego zarządzania funkcjami Microsoft 365 Administratorzy ról uprzywilejowanych w organizacji mogą nie nakonywać stałego administratora globalnego, ponieważ rola ta ma wpływ na inne usługi. Zamiast tego mogą oni kwalifikować się do ról usługi Azure AD, takich jak administrator usługi Exchange Online. Możesz zażądać aktywowania tej roli, gdy będzie potrzebne jej uprawnienia, a następnie będziesz mieć kontrolę administratora dla wstępnie wyznaczonych okresów.

Ten artykuł jest przeznaczony dla administratorów, którzy muszą aktywować swoją rolę usługi Azure AD w Privileged Identity Management.

## <a name="determine-your-version-of-pim"></a>Określanie używanej wersji programu PIM

Od listopada 2019 część Privileged Identity Management ról usługi Azure AD jest aktualizowana do nowej wersji, która jest zgodna z środowiskami dla ról zasobów platformy Azure. Spowoduje to utworzenie dodatkowych funkcji, a także [zmian w istniejącym interfejsie API](azure-ad-roles-features.md#api-changes). Gdy nowa wersja jest wdrażana, procedury, które należy wykonać w tym artykule, zależą od aktualnie posiadanej wersji Privileged Identity Management. Wykonaj kroki opisane w tej sekcji, aby określić, która wersja Privileged Identity Management masz. Po uzyskaniu informacji o wersji Privileged Identity Management możesz wybrać procedury w tym artykule, które pasują do tej wersji.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu roli [administrator ról uprzywilejowanych](../roles/permissions-reference.md#privileged-role-administrator) .
1. Otwórz **Azure AD Privileged Identity Management**. Jeśli masz baner w górnej części strony przegląd, postępuj zgodnie z instrukcjami wyświetlanymi na karcie **Nowa wersja** tego artykułu. W przeciwnym razie postępuj zgodnie z instrukcjami podanymi w **poprzedniej wersji** karty.

    [![Wybierz Privileged Identity Management usługi Azure AD >.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nowa wersja](#tab/new)

## <a name="activate-a-role-for-new-version"></a>Aktywuj rolę dla nowej wersji

Jeśli musisz założyć rolę usługi Azure AD, możesz poprosić o aktywację, otwierając **Moje role** w Privileged Identity Management.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**. Aby uzyskać informacje na temat dodawania kafelka Privileged Identity Management do pulpitu nawigacyjnego, zobacz [Rozpoczynanie korzystania z Privileged Identity Management](pim-getting-started.md).

1. Wybierz pozycję **Moje role**, a następnie wybierz pozycję **role usługi Azure AD** , aby wyświetlić listę uprawnionych ról usługi Azure AD.

    ![Strona Moje role pokazujące role, które można uaktywnić](./media/pim-how-to-activate-role/my-roles.png)

1. Na liście **role usługi Azure AD** Znajdź rolę, którą chcesz aktywować.

    ![Role usługi Azure AD — lista wszystkich uprawnionych ról](./media/pim-how-to-activate-role/activate-link.png)

1. Wybierz pozycję **Aktywuj** , aby otworzyć okienko Aktywuj.

    ![Strona aktywacji ról usługi Azure AD zawiera czas trwania i zakres](./media/pim-how-to-activate-role/activate-page.png)

1. Wybierz **wymaganą dodatkową weryfikację**"* * i postępuj zgodnie z instrukcjami, aby zapewnić dodatkową weryfikację zabezpieczeń. Wymagane jest uwierzytelnienie tylko raz na sesję.

    ![Ekran zapewniający weryfikację zabezpieczeń, np. kod PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Po wybraniu uwierzytelniania wieloskładnikowego wybierz pozycję **Aktywuj przed kontynuowaniem**.

    ![Sprawdź moją tożsamość z uwierzytelnianiem MFA przed aktywowaniem roli](./media/pim-how-to-activate-role/activate-role-mfa-banner.png)

1. Jeśli chcesz określić ograniczony zakres, wybierz pozycję **zakres** , aby otworzyć okienko filtru. W okienku Filtr można określić zasoby usługi Azure AD, do których chcesz uzyskać dostęp. Najlepszym rozwiązaniem jest zażądanie dostępu do najmniejszej liczby zasobów, które są potrzebne.

1. W razie potrzeby określ czas rozpoczęcia aktywacji niestandardowej. Rola usługi Azure AD zostanie aktywowana po wybranej godzinie.

1. W polu **Przyczyna** wprowadź przyczynę żądania aktywacji.

1. Wybierz pozycję **Aktywuj**.

    Jeśli [rola wymaga zatwierdzenia](pim-resource-roles-approval-workflow.md) do aktywacji, w prawym górnym rogu przeglądarki zostanie wyświetlone powiadomienie z informacją, że żądanie oczekuje na zatwierdzenie.

    ![Żądanie aktywacji oczekuje na powiadomienie o zatwierdzeniu](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests-for-new-version"></a>Wyświetlanie stanu żądań dla nowej wersji

Można wyświetlić stan oczekujących żądań, które należy aktywować.

1. Otwórz Azure AD Privileged Identity Management.

1. Wybierz pozycję **Moje żądania** , aby wyświetlić listę ról usługi Azure AD i żądań ról zasobów platformy Azure.

    ![Moje żądania — Strona usługi Azure AD przedstawiająca oczekujące żądania](./media/pim-how-to-activate-role/my-requests-page.png)

1. Przewiń w prawo, aby wyświetlić kolumnę **stan żądania** .

## <a name="cancel-a-pending-request-for-new-version"></a>Anulowanie oczekującego żądania dla nowej wersji

Jeśli nie wymagasz aktywacji roli wymagającej zatwierdzenia, możesz w dowolnym momencie anulować oczekujące żądanie.

1. Otwórz Azure AD Privileged Identity Management.

1. Wybierz pozycję **Moje żądania**.

1. Dla roli, którą chcesz anulować, wybierz łącze **Anuluj** .

    Po wybraniu przycisku Anuluj żądanie zostanie anulowane. Aby ponownie aktywować rolę, trzeba będzie przesłać nowe żądanie aktywacji.

   ![Lista moje żądania z wyróżnioną akcją anulowania](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot-for-new-version"></a>Rozwiązywanie problemów dotyczących nowej wersji

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Nie udzielono uprawnień po aktywowaniu roli

Po aktywowaniu roli w Privileged Identity Management aktywacja może nie być natychmiast propagowana do wszystkich portali, które wymagają roli uprzywilejowanej. Czasami nawet po rozpropagowaniu zmiany buforowanie internetowe w portalu może spowodować, że zmiany nie zostaną natychmiast uwzględnione. Jeśli aktywacja jest opóźniona, Oto co należy zrobić.

1. Wyloguj się z witryny Azure Portal, a następnie zaloguj się ponownie.

1. W Privileged Identity Management Sprawdź, czy jesteś członkiem roli.

# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

## <a name="activate-a-role-previous-version"></a>Aktywuj rolę (poprzednia wersja)

Gdy musisz wykonać tę rolę usługi Azure AD, możesz zażądać aktywacji przy użyciu opcji nawigacji **My role** w Privileged Identity Management.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**. Aby uzyskać informacje na temat dodawania kafelka Privileged Identity Management do pulpitu nawigacyjnego, zobacz [Rozpoczynanie korzystania z Privileged Identity Management](pim-getting-started.md).

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz pozycję **Moje role** , aby wyświetlić listę uprawnionych ról usługi Azure AD.

    ![Role usługi Azure AD — moje role pokazujące listę ról kwalifikujących się lub aktywnych](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Znajdź rolę, którą chcesz aktywować.

    ![Role usługi Azure AD — Lista moich uprawnionych ról pokazująca link aktywowania](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Wybierz pozycję **Aktywuj** , aby otworzyć okienko Szczegóły aktywacji roli.

1. Jeśli Twoja rola wymaga uwierzytelniania wieloskładnikowego (MFA), wybierz opcję **Weryfikuj swoją tożsamość przed kontynuowaniem**. Musisz tylko raz uwierzytelnić się na sesję.

    ![Weryfikowanie okienka moje tożsamości za pomocą usługi MFA przed aktywacją roli](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Wybierz pozycję **Weryfikuj moją tożsamość** i postępuj zgodnie z instrukcjami, aby zapewnić dodatkową weryfikację zabezpieczeń.

    ![Dodatkowa strona weryfikacji zabezpieczeń z prośbą o kontakt z Tobą](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Wybierz pozycję **Aktywuj** , aby otworzyć okienko aktywacja.

    ![Okienko aktywacji, aby określić czas rozpoczęcia, czas trwania, bilet i powód](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. W razie potrzeby określ czas rozpoczęcia aktywacji niestandardowej.

1. Określ czas trwania aktywacji.

1. W polu **Przyczyna aktywacji** wprowadź przyczynę żądania aktywacji. Niektóre role wymagają podania numeru biletu problemu.

    ![Okienko ukończono aktywację z niestandardowym czasem rozpoczęcia, czasem trwania, biletem i powodem](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Wybierz pozycję **Aktywuj**.

    Jeśli rola nie wymaga zatwierdzenia, zostanie wyświetlone okienko **stanu aktywacji** z wyświetlonym stanem aktywacja.

    ![Na stronie stanu aktywacji są wyświetlane trzy etapy aktywacji](./media/pim-how-to-activate-role/activation-status.png)

    Po zakończeniu wszystkich etapów wybierz link **Wyloguj** się, aby wylogować się z Azure Portal. Po ponownym zalogowaniu się do portalu możesz teraz korzystać z roli.

    Jeśli [rola wymaga zatwierdzenia](./azure-ad-pim-approval-workflow.md) w celu aktywowania, w prawym górnym rogu przeglądarki zostanie wyświetlone powiadomienie z informacją, że żądanie oczekuje na zatwierdzenie.

## <a name="view-the-status-of-your-requests-previous-version"></a>Wyświetlanie stanu żądań (poprzednia wersja)

Można wyświetlić stan oczekujących żądań, które należy aktywować.

1. Otwórz Azure AD Privileged Identity Management.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz pozycję **Moje żądania** , aby wyświetlić listę Twoich żądań.

    ![Role usługi Azure AD — moje żądania](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role-previous-version"></a>Dezaktywuj rolę (poprzednia wersja)

Po aktywowaniu roli zostanie ona automatycznie zdezaktywowana, gdy zostanie osiągnięty limit czasu (uprawniony czas trwania).

W przypadku wczesnego wykonywania zadań administratora można także dezaktywować rolę ręcznie w Azure AD Privileged Identity Management.

1. Otwórz Azure AD Privileged Identity Management.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz pozycję **Moje role**.

1. Wybierz pozycję **aktywne role** , aby wyświetlić listę aktywnych ról.

1. Znajdź rolę, której używasz, a następnie wybierz pozycję **Dezaktywuj**.

## <a name="cancel-a-pending-request-previous-version"></a>Anulowanie oczekującego żądania (poprzednia wersja)

Jeśli nie wymagasz aktywacji roli wymagającej zatwierdzenia, możesz w dowolnym momencie anulować oczekujące żądanie.

1. Otwórz Azure AD Privileged Identity Management.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz pozycję **Moje żądania**.

1. Dla roli, którą chcesz anulować, wybierz przycisk **Anuluj** .

    Po wybraniu **przycisku Anuluj** żądanie zostanie anulowane. Aby ponownie aktywować rolę, trzeba będzie przesłać nowe żądanie aktywacji.

   ![Lista moje żądania z wyróżnionym przyciskiem Anuluj](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot-previous-version"></a>Rozwiązywanie problemów (poprzednia wersja)

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Nie udzielono uprawnień po aktywowaniu roli

Po aktywowaniu roli w Privileged Identity Management aktywacja może nie być natychmiast propagowana do wszystkich portali, które wymagają roli uprzywilejowanej. Czasami nawet po rozpropagowaniu zmiany buforowanie internetowe w portalu może spowodować, że zmiany nie zostaną natychmiast uwzględnione. Jeśli aktywacja jest opóźniona, Oto co należy zrobić.

1. Wyloguj się z witryny Azure Portal, a następnie zaloguj się ponownie.

    Po aktywowaniu roli usługi Azure AD zobaczysz etapy aktywacji. Po zakończeniu wszystkich etapów zobaczysz link **Wyloguj**. Możesz użyć tego linku, aby się wylogować. Spowoduje to rozproszenie większości przypadków opóźnienia aktywacji.

1. W Privileged Identity Management Sprawdź, czy jesteś członkiem roli.

 ---

## <a name="next-steps"></a>Następne kroki

- [Aktywuj moje role usługi Azure AD w Privileged Identity Management](pim-how-to-activate-role.md)
