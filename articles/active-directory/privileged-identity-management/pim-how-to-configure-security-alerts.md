---
title: Alerty zabezpieczeń dla ról usługi Azure AD w usłudze PIM — Azure AD | Microsoft Docs
description: Skonfiguruj alerty zabezpieczeń dla ról usługi Azure AD Privileged Identity Management w Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26b519ce11747ab3374d9bd286800a6c93129019
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565235"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Konfigurowanie alertów zabezpieczeń dla ról usługi Azure AD w Privileged Identity Management

Privileged Identity Management (PIM) generuje alerty w przypadku podejrzenia lub niebezpiecznego działania w organizacji Azure Active Directory (Azure AD). Po wyzwoleniu alertu zostanie on wyświetlony na pulpicie nawigacyjnym Privileged Identity Management. Wybierz Alert, aby wyświetlić raport zawierający listę użytkowników lub ról, które wyzwoliły alert.

## <a name="determine-your-version-of-pim"></a>Określanie używanej wersji programu PIM

Od listopada 2019 część Privileged Identity Management ról usługi Azure AD jest aktualizowana do nowej wersji, która jest zgodna z środowiskami dla ról zasobów platformy Azure. Spowoduje to utworzenie dodatkowych funkcji, a także [zmian w istniejącym interfejsie API](azure-ad-roles-features.md#api-changes). Gdy nowa wersja jest wdrażana, procedury, które należy wykonać w tym artykule, zależą od aktualnie zainstalowanej wersji programu Privileged Identity Management. Wykonaj kroki opisane w tej sekcji, aby określić, która wersja Privileged Identity Management masz. Po uzyskaniu informacji o wersji Privileged Identity Management możesz wybrać procedury w tym artykule, które pasują do tej wersji.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który znajduje się w roli [administrator ról uprzywilejowanych](../roles/permissions-reference.md#privileged-role-administrator) .
1. Otwórz **Azure AD Privileged Identity Management**. Jeśli masz baner w górnej części strony przegląd, postępuj zgodnie z instrukcjami wyświetlanymi na karcie **Nowa wersja** tego artykułu. W przeciwnym razie postępuj zgodnie z instrukcjami podanymi w **poprzedniej wersji** karty.

  [![Wybierz Privileged Identity Management usługi Azure AD >.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Wykonaj kroki opisane w tym artykule, aby zbadać alerty zabezpieczeń dla ról usługi Azure AD.

# <a name="new-version"></a>[Nowa wersja](#tab/new)

![Zrzut ekranu przedstawiający stronę "alerty" z listą alertów i ich ważnością.](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>Alerty zabezpieczeń

W tej sekcji znajduje się lista wszystkich alertów zabezpieczeń dla ról usługi Azure AD, a także sposób naprawy i zapobiegania. Ważność ma następujące znaczenie:

- **Wysoka**: wymaga natychmiastowej akcji z powodu naruszenia zasad.
- **Średni**: nie wymaga natychmiastowego działania, ale sygnalizuje potencjalne naruszenie zasad.
- **Niska**: nie wymaga natychmiastowego działania, ale sugeruje bardziej preferowaną zmianę zasad.

### <a name="administrators-arent-using-their-privileged-roles"></a>Administratorzy nie korzystają z ról uprzywilejowanych

Ważność: **niski**

| | Opis |
| --- | --- |
| **Dlaczego otrzymuję ten alert?** | Użytkownicy, którym przypisano role uprzywilejowane, których nie potrzebują, zwiększają prawdopodobieństwo ataku. Łatwiejsze jest również, aby osoby atakujące pozostały niezauważalny w przypadku kont, które nie są aktywnie używane. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i Usuń ich z uprzywilejowanych ról, których nie potrzebują. |
| **Zapobieganie** | Przypisywanie ról uprzywilejowanych tylko użytkownikom, którzy mają uzasadnienie biznesowe. </br>Zaplanuj regularne [przeglądy dostępu](pim-how-to-start-security-review.md) , aby upewnić się, że użytkownicy nadal potrzebują dostępu. |
| **Akcja łagodzenia w portalu** | Usuwa konto z roli uprzywilejowanej. |
| **Wyzwalacz** | Wyzwalane, jeśli użytkownik przejdzie przez określoną liczbę dni bez aktywowania roli. |
| **Liczba dni** | To ustawienie określa maksymalną liczbę dni od 0 do 100, którą użytkownik może wybrać bez aktywowania roli.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Role nie wymagają uwierzytelniania wieloskładnikowego na potrzeby aktywacji

Ważność: **niski**

| | Opis |
| --- | --- |
| **Dlaczego otrzymuję ten alert?** | Bez uwierzytelniania wieloskładnikowego użytkownicy ze złamanymi zabezpieczeniami mogą aktywować uprzywilejowane role. |
| **Jak naprawić?** | Przejrzyj listę ról i [Wymagaj uwierzytelniania wieloskładnikowego](pim-how-to-change-default-settings.md) w przypadku każdej roli. |
| **Zapobieganie** | [Wymagaj uwierzytelniania wieloskładnikowego](pim-how-to-change-default-settings.md) dla każdej roli.  |
| **Akcja łagodzenia w portalu** | Powoduje, że uwierzytelnianie wieloskładnikowe jest wymagane do aktywacji roli uprzywilejowanej. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>Organizacja nie ma Azure AD — wersja Premium P2

Ważność: **niski**

| | Opis |
| --- | --- |
| **Dlaczego otrzymuję ten alert?** | Bieżąca organizacja usługi Azure AD nie ma Azure AD — wersja Premium P2. |
| **Jak naprawić?** | Przejrzyj informacje o [wersjach usługi Azure AD](../fundamentals/active-directory-whatis.md). Uaktualnij do Azure AD — wersja Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potencjalne przestarzałe konta w roli uprzywilejowanej

Ważność: **średnia**

| | Opis |
| --- | --- |
| **Dlaczego otrzymuję ten alert?** | Konta w roli uprzywilejowanej nie zmieniły swojego hasła w ciągu ostatnich 90 dni. Te konta mogą być kontami usług lub udostępnionymi, które nie są utrzymywane i są narażone na ataki. |
| **Jak naprawić?** | Przejrzyj konta na liście. Jeśli nie potrzebują już dostępu, usuń je z ról uprzywilejowanych. |
| **Zapobieganie** | Upewnij się, że konta, które są udostępnione, obracają silne hasła w przypadku zmiany użytkowników, którzy znają hasło. </br>Regularnie Przeglądaj konta z rolami uprzywilejowanymi przy użyciu [przeglądów dostępu](pim-how-to-start-security-review.md) i usuwaj przypisania ról, które nie są już potrzebne. |
| **Akcja łagodzenia w portalu** | Usuwa konto z roli uprzywilejowanej. |
| **Najlepsze praktyki** | Konta dostępu współdzielonego, usługi i trybu awaryjnego, które uwierzytelniają się przy użyciu hasła i są przypisane do uprzywilejowanych ról administracyjnych, takich jak Administrator globalny lub administrator zabezpieczeń, powinny być obracane w następujących przypadkach:<ul><li>Po wystąpieniu zdarzenia zabezpieczeń związanego z nieprawidłowym lub naruszeniem praw dostępu administracyjnego</li><li>Po zmianie uprawnień użytkownika, tak aby nie były one już administratorami (na przykład po odejściu pracownika przez administratora lub opuszczenia organizacji)</li><li>W regularnych odstępach czasu (na przykład co kwartał lub co rok) nawet w przypadku braku znanego naruszenia lub zmiany działu IT</li></ul>Ponieważ wielu osób ma dostęp do tych kont, należy je obrócić, aby upewnić się, że osoby, które opuściły swoje role, nie będą miały dostępu do kont. [Dowiedz się więcej o zabezpieczaniu kont](../roles/security-planning.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Role są przypisywane poza Privileged Identity Management

Ważność: **wysoka**

| | Opis |
| --- | --- |
| **Dlaczego otrzymuję ten alert?** | Przypisania ról uprzywilejowanych wprowadzone poza Privileged Identity Management nie są prawidłowo monitorowane i mogą wskazywać na aktywny atak. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i Usuń ich z uprzywilejowanych ról przypisanych poza Privileged Identity Management. |
| **Zapobieganie** | Sprawdź, gdzie użytkownicy są przypisani do ról uprzywilejowanych spoza Privileged Identity Management i Zabroń w przyszłości. |
| **Akcja łagodzenia w portalu** | Usuwa użytkownika z roli uprzywilejowanej. |

### <a name="there-are-too-many-global-administrators"></a>Zbyt wielu administratorów globalnych

Ważność: **niski**

| | Opis |
| --- | --- |
| **Dlaczego otrzymuję ten alert?** | Administrator globalny jest najwyższą rolą uprzywilejowaną. W przypadku naruszenia zabezpieczeń administratora globalnego osoba atakująca uzyskuje dostęp do wszystkich swoich uprawnień, co stanowi zagrożenie dla całego systemu. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i Usuń wszystkie, które nie potrzebują absolutnie roli administratora globalnego. </br>Zamiast tego Przypisz do tych użytkowników niższych ról uprzywilejowanych. |
| **Zapobieganie** | Przypisywanie użytkownikom najniższych ról, których potrzebują. |
| **Akcja łagodzenia w portalu** | Usuwa konto z roli uprzywilejowanej. |
| **Wyzwalacz** | Wyzwalane, jeśli spełnione są dwa różne kryteria, i można skonfigurować obie. Najpierw należy uzyskać określony próg przypisań roli administratora globalnego. Sekunda, pewna część całkowitych przypisań ról musi być administratorami globalnymi. Jeśli spełniasz tylko jeden z tych pomiarów, alert nie zostanie wyświetlony. |
| **Minimalna liczba administratorów globalnych** | To ustawienie określa liczbę przypisań roli administratora globalnego z przedziału od 2 do 100, które należy wziąć pod uwagę za mało dla organizacji usługi Azure AD. |
| **Procent administratorów globalnych** | To ustawienie określa minimalny odsetek administratorów, którzy są administratorami globalnymi z przedziału od 0% do 100%, poniżej którego organizacja usługi Azure AD nie ma być DIP. |

### <a name="roles-are-being-activated-too-frequently"></a>Role są uaktywniane zbyt często

Ważność: **niski**

| | Opis |
| --- | --- |
| **Dlaczego otrzymuję ten alert?** | Wielokrotne aktywacje z tą samą uprzywilejowaną rolą przez tego samego użytkownika jest znakiem ataku. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i upewnij się, że [czas trwania aktywacji](pim-how-to-change-default-settings.md) dla roli uprzywilejowanej jest ustawiony wystarczająco długo, aby wykonywał zadania. |
| **Zapobieganie** | Upewnij się, że [czas trwania aktywacji](pim-how-to-change-default-settings.md) dla ról uprzywilejowanych jest ustawiony na wystarczająco długi, aby użytkownicy mogli wykonywać swoje zadania.</br>[Wymagaj uwierzytelniania wieloskładnikowego](pim-how-to-change-default-settings.md) dla ról uprzywilejowanych, które mają konta współużytkowane przez wielu administratorów. |
| **Akcja łagodzenia w portalu** | Nie dotyczy |
| **Wyzwalacz** | Wyzwalane, jeśli użytkownik aktywuje tę samą uprzywilejowaną rolę wiele razy w określonym przedziale czasu. Można skonfigurować zarówno okres, jak i liczbę aktywacji. |
| **Przedział czasu odnawiania aktywacji** | To ustawienie określa liczbę dni, godzin, minut i sekund okresu, który ma być używany do śledzenia podejrzanych odnowień. |
| **Liczba odnowień aktywacji** | To ustawienie określa liczbę aktywacji z przedziału od 2 do 100, przy której chcesz otrzymywać powiadomienia w wybranym okresie. To ustawienie można zmienić, przesuwając suwak lub wpisując liczbę w polu tekstowym. |

## <a name="customize-security-alert-settings"></a>Dostosuj ustawienia alertów zabezpieczeń

Na stronie **alerty** wybierz pozycję **Ustawienia**.

![Strona alerty z wyróżnionymi ustawieniami](media/pim-how-to-configure-security-alerts/alert-settings.png)

Dostosuj ustawienia różnych alertów, aby współpracowały ze środowiskiem i celami zabezpieczeń.

![Ustawianie strony alertu do włączania i konfigurowania ustawień](media/pim-how-to-configure-security-alerts/security-alert-settings.png)

# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

![Role usługi Azure AD — alert dotyczący wyświetlania i ważności](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alert-details"></a>Szczegóły alertu zabezpieczeń

W tej sekcji znajduje się lista wszystkich alertów zabezpieczeń dla ról usługi Azure AD, a także sposób naprawy i zapobiegania. Ważność ma następujące znaczenie:

- **Wysoka**: wymaga natychmiastowej akcji z powodu naruszenia zasad.
- **Średni**: nie wymaga natychmiastowego działania, ale sygnalizuje potencjalne naruszenie zasad.
- **Niska**: nie wymaga natychmiastowego działania, ale sugeruje bardziej preferowaną zmianę zasad.

### <a name="administrators-arent-using-their-privileged-roles"></a>Administratorzy nie korzystają z ról uprzywilejowanych

Ważność: **niski**

| | Opis |
| --- | --- |
| **Dlaczego otrzymuję ten alert?** | Użytkownicy, którym przypisano role uprzywilejowane, których nie potrzebują, zwiększają prawdopodobieństwo ataku. Łatwiejsze jest również, aby osoby atakujące pozostały niezauważalny w przypadku kont, które nie są aktywnie używane. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i Usuń ich z uprzywilejowanych ról, których nie potrzebują. |
| **Zapobieganie** | Przypisywanie ról uprzywilejowanych tylko użytkownikom, którzy mają uzasadnienie biznesowe. </br>Zaplanuj regularne [przeglądy dostępu](pim-how-to-start-security-review.md) , aby upewnić się, że użytkownicy nadal potrzebują dostępu. |
| **Akcja łagodzenia w portalu** | Usuwa konto z roli uprzywilejowanej. |
| **Wyzwalacz** | Wyzwalane, jeśli użytkownik przejdzie przez określoną liczbę dni bez aktywowania roli. |
| **Liczba dni** | To ustawienie określa maksymalną liczbę dni od 0 do 100, którą użytkownik może wybrać bez aktywowania roli.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Role nie wymagają uwierzytelniania wieloskładnikowego na potrzeby aktywacji

Ważność: **niski**

| | Opis |
| --- | --- |
| **Dlaczego otrzymuję ten alert?** | Bez uwierzytelniania wieloskładnikowego użytkownicy ze złamanymi zabezpieczeniami mogą aktywować uprzywilejowane role. |
| **Jak naprawić?** | Przejrzyj listę ról i [Wymagaj uwierzytelniania wieloskładnikowego](pim-how-to-change-default-settings.md) w przypadku każdej roli. |
| **Zapobieganie** | [Wymagaj uwierzytelniania wieloskładnikowego](pim-how-to-change-default-settings.md) dla każdej roli.  |
| **Akcja łagodzenia w portalu** | Powoduje, że uwierzytelnianie wieloskładnikowe jest wymagane do aktywacji roli uprzywilejowanej. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>Organizacja nie ma Azure AD — wersja Premium P2

Ważność: **niski**

| | Opis |
| --- | --- |
| **Dlaczego otrzymuję ten alert?** | Bieżąca organizacja usługi Azure AD nie ma Azure AD — wersja Premium P2. |
| **Jak naprawić?** | Przejrzyj informacje o [wersjach usługi Azure AD](../fundamentals/active-directory-whatis.md). Uaktualnij do Azure AD — wersja Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potencjalne przestarzałe konta w roli uprzywilejowanej

Ważność: **średnia**

| | Opis |
| --- | --- |
| **Dlaczego otrzymuję ten alert?** | Konta w roli uprzywilejowanej nie zmieniły swojego hasła w ciągu ostatnich 90 dni. Te konta mogą być kontami usług lub udostępnionymi, które nie są utrzymywane i są narażone na ataki. |
| **Jak naprawić?** | Przejrzyj konta na liście. Jeśli nie potrzebują już dostępu, usuń je z ról uprzywilejowanych. |
| **Zapobieganie** | Upewnij się, że konta, które są udostępnione, obracają silne hasła w przypadku zmiany użytkowników, którzy znają hasło. </br>Regularnie Przeglądaj konta z rolami uprzywilejowanymi przy użyciu [przeglądów dostępu](pim-how-to-start-security-review.md) i usuwaj przypisania ról, które nie są już potrzebne. |
| **Akcja łagodzenia w portalu** | Usuwa konto z roli uprzywilejowanej. |
| **Najlepsze praktyki** | Konta dostępu współdzielonego, usługi i trybu awaryjnego, które uwierzytelniają się przy użyciu hasła i są przypisane do uprzywilejowanych ról administracyjnych, takich jak Administrator globalny lub administrator zabezpieczeń, powinny być obracane w następujących przypadkach:<ul><li>Po wystąpieniu zdarzenia zabezpieczeń związanego z nieprawidłowym lub naruszeniem praw dostępu administracyjnego</li><li>Po zmianie uprawnień użytkownika, tak aby nie były one już administratorami (na przykład po odejściu pracownika przez administratora lub opuszczenia organizacji)</li><li>W regularnych odstępach czasu (na przykład co kwartał lub co rok) nawet w przypadku braku znanego naruszenia lub zmiany działu IT</li></ul>Ponieważ wielu osób ma dostęp do tych kont, należy je obrócić, aby upewnić się, że osoby, które opuściły swoje role, nie będą miały dostępu do kont. [Dowiedz się więcej](../roles/security-planning.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Role są przypisywane poza Privileged Identity Management

Ważność: **wysoka**

| | Opis |
| --- | --- |
| **Dlaczego otrzymuję ten alert?** | Przypisania ról uprzywilejowanych wprowadzone poza Privileged Identity Management nie są prawidłowo monitorowane i mogą wskazywać na aktywny atak. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i Usuń ich z uprzywilejowanych ról przypisanych poza Privileged Identity Management. |
| **Zapobieganie** | Sprawdź, gdzie użytkownicy są przypisani do ról uprzywilejowanych spoza Privileged Identity Management i Zabroń w przyszłości. |
| **Akcja łagodzenia w portalu** | Usuwa użytkownika z roli uprzywilejowanej. |

### <a name="there-are-too-many-global-administrators"></a>Zbyt wielu administratorów globalnych

Ważność: **niski**

| | Opis |
| --- | --- |
| **Dlaczego otrzymuję ten alert?** | Administrator globalny jest najwyższą rolą uprzywilejowaną. W przypadku naruszenia zabezpieczeń administratora globalnego osoba atakująca uzyskuje dostęp do wszystkich swoich uprawnień, co stanowi zagrożenie dla całego systemu. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i Usuń wszystkie, które nie potrzebują absolutnie roli administratora globalnego. </br>Zamiast tego Przypisz do tych użytkowników niższych ról uprzywilejowanych. |
| **Zapobieganie** | Przypisywanie użytkownikom najniższych ról, których potrzebują. |
| **Akcja łagodzenia w portalu** | Usuwa konto z roli uprzywilejowanej. |
| **Wyzwalacz** | Wyzwalane, jeśli spełnione są dwa różne kryteria, i można skonfigurować obie. Najpierw należy uzyskać określony próg dla administratorów globalnych. Sekunda, pewna część całkowitych przypisań ról musi być administratorami globalnymi. Jeśli spełniasz tylko jeden z tych pomiarów, alert nie zostanie wyświetlony. |
| **Minimalna liczba administratorów globalnych** | To ustawienie określa liczbę administratorów globalnych z przedziału od 2 do 100, które należy wziąć pod uwagę za mało dla organizacji usługi Azure AD. |
| **Procent administratorów globalnych** | To ustawienie określa minimalny odsetek administratorów, którzy są administratorami globalnymi z przedziału od 0% do 100%, poniżej którego organizacja usługi Azure AD nie ma być DIP. |

### <a name="roles-are-being-activated-too-frequently"></a>Role są uaktywniane zbyt często

Ważność: **niski**

| | Opis |
| --- | --- |
| **Dlaczego otrzymuję ten alert?** | Wielokrotne aktywacje z tą samą uprzywilejowaną rolą przez tego samego użytkownika jest znakiem ataku. |
| **Jak naprawić?** | Przejrzyj użytkowników na liście i upewnij się, że [czas trwania aktywacji](pim-how-to-change-default-settings.md) dla roli uprzywilejowanej jest ustawiony wystarczająco długo, aby wykonywał zadania. |
| **Zapobieganie** | Upewnij się, że [czas trwania aktywacji](pim-how-to-change-default-settings.md) dla ról uprzywilejowanych jest ustawiony na wystarczająco długi, aby użytkownicy mogli wykonywać swoje zadania.</br>[Wymagaj uwierzytelniania wieloskładnikowego](pim-how-to-change-default-settings.md) dla ról uprzywilejowanych, które mają konta współużytkowane przez wielu administratorów. |
| **Akcja łagodzenia w portalu** | Nie dotyczy |
| **Wyzwalacz** | Wyzwalane, jeśli użytkownik aktywuje tę samą uprzywilejowaną rolę wiele razy w określonym przedziale czasu. Można skonfigurować zarówno okres, jak i liczbę aktywacji. |
| **Przedział czasu odnawiania aktywacji** | To ustawienie określa liczbę dni, godzin, minut i sekund okresu, który ma być używany do śledzenia podejrzanych odnowień. |
| **Liczba odnowień aktywacji** | To ustawienie określa liczbę aktywacji z przedziału od 2 do 100, przy której chcesz otrzymywać powiadomienia w wybranym okresie. To ustawienie można zmienić, przesuwając suwak lub wpisując liczbę w polu tekstowym. |

## <a name="configure-security-alert-settings"></a>Konfigurowanie ustawień alertów zabezpieczeń

Niektóre alerty zabezpieczeń w Privileged Identity Management mogą być dostosowane do potrzeb organizacji i celów związanych z bezpieczeństwem. Wykonaj następujące kroki, aby otworzyć ustawienia alertu zabezpieczeń:

1. Otwórz **Privileged Identity Management** w usłudze Azure AD.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz opcję **Ustawienia** , a następnie pozycję **alerty**.

    ![Role usługi Azure AD — ustawienia z alertami wybrane](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Wybierz nazwę alertu, aby skonfigurować ustawienie dla tego alertu.

    ![Dla wybranego alertu okienko ustawień alertów zabezpieczeń](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

---

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie ustawień roli usługi Azure AD w Privileged Identity Management](pim-how-to-change-default-settings.md)