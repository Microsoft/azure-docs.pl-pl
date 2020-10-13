---
title: Informacje o usłudze Azure Multi-Factor Authentication i okres istnienia sesji
description: Dowiedz się więcej na temat zalecanej konfiguracji w celu ponownego uwierzytelnienia przy użyciu usługi Azure Multi-Factor Authentication i sposobu stosowania okresu istnienia sesji.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a759d4e686f43133716cc1c40237db74a015210
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964948"
---
# <a name="optimize-reauthentication-prompts-and-understand-session-lifetime-for-azure-multi-factor-authentication"></a>Optymalizowanie wierszy ponownego uwierzytelniania i zrozumienie okresu istnienia sesji dla usługi Azure Multi-Factor Authentication

Azure Active Directory (Azure AD) ma wiele ustawień, które określają, jak często użytkownicy muszą ponownie przeprowadzić uwierzytelnienie. To ponowne uwierzytelnienie może być przy użyciu pierwszego czynnika, takiego jak hasło, FIDO lub bezhasła Microsoft Authenticator lub w celu przeprowadzenia uwierzytelniania wieloskładnikowego (MFA). Te ustawienia ponownego uwierzytelniania można skonfigurować zgodnie z potrzebami w Twoim środowisku i dla użytkownika.

Domyślna konfiguracja usługi Azure AD dla częstotliwości logowania użytkownika to stopniowe okno o 90 dni. Zaproszenie użytkowników o poświadczenia często wygląda na to, że jest to rozsądna czynność, ale może ona zostać zastosowana. Jeśli użytkownicy są przeszkoleni, aby wprowadzić swoje poświadczenia bez zastanawiania się, mogą przypadkowo dostarczyć je do złośliwego monitu o poświadczenia.

Może to spowodować, że dźwięk nie poprosił użytkownika o ponowne zalogowanie się, mimo że zasady IT odwołują sesję. Niektóre przykłady obejmują zmianę hasła, niezgodne urządzenie lub operację wyłączania konta. Możesz również jawnie [odwołać sesje użytkowników przy użyciu programu PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken).

Ten artykuł zawiera szczegółowe informacje o zalecanych konfiguracjach oraz o sposobie działania różnych ustawień i współpracy ze sobą.

## <a name="recommended-settings"></a>Zalecane ustawienia

Aby zapewnić użytkownikom odpowiednie saldo zabezpieczeń i łatwość używania, z prośbą o zalogowanie się zgodnie z właściwą częstotliwością zalecamy następujące konfiguracje:

* Jeśli masz Azure AD — wersja Premium:
    * Włącz logowanie jednokrotne (SSO) w aplikacjach przy użyciu [zarządzanych urządzeń](../devices/overview.md) lub [bezproblemowego logowania jednokrotnego](../hybrid/how-to-connect-sso.md).
    * Jeśli wymagane jest ponowne uwierzytelnienie, należy użyć [zasad częstotliwości logowania](../conditional-access/howto-conditional-access-session-lifetime.md)dostępu warunkowego.
    * W przypadku użytkowników logujących się z urządzeń niezarządzanych lub scenariuszy dotyczących urządzeń przenośnych Użyj dostępu warunkowego w celu włączenia trwałych sesji przeglądarki i zasad częstotliwości logowania.
* Jeśli masz licencje na aplikacje Microsoft 365 lub bezpłatną warstwę usługi Azure AD:
    * Włącz logowanie jednokrotne (SSO) w aplikacjach przy użyciu [zarządzanych urządzeń](../devices/overview.md) lub [bezproblemowego logowania jednokrotnego](../hybrid/how-to-connect-sso.md).
    * Pozostaw włączoną opcję *pozostawania zalogowanego* i Poinformuj użytkowników, aby zaakceptowali ją.
* W przypadku scenariuszy z urządzeniami przenośnymi upewnij się, że użytkownicy korzystają z aplikacji Microsoft Authenticator. Ta aplikacja jest używana jako Broker do innych aplikacji federacyjnych usługi Azure AD i zmniejsza na urządzeniu komunikaty dotyczące uwierzytelniania.

Nasze badania pokazują, że te ustawienia są odpowiednie dla większości dzierżawców. Niektóre kombinacje tych ustawień, takie jak *Pamiętaj MFA* i *pozostają*bez zmian, mogą spowodować, że użytkownicy będą monitowani o zbyt częste uwierzytelnianie. Regularne monity o ponowne uwierzytelnienie są błędne w przypadku produktywności użytkowników i mogą sprawiać, że są one bardziej podatne na ataki.

## <a name="azure-ad-session-lifetime-configuration-settings"></a>Ustawienia konfiguracji okresu istnienia sesji usługi Azure AD

Aby zoptymalizować częstotliwość wyświetlania wierszy uwierzytelniania dla użytkowników, można skonfigurować opcje okresu istnienia sesji usługi Azure AD. Zapoznaj się z potrzebami biznesowymi i użytkownikami oraz Skonfiguruj ustawienia, które zapewniają najlepsze saldo dla danego środowiska.

### <a name="evaluate-session-lifetime-policies"></a>Oceń zasady okresu istnienia sesji

Bez żadnych ustawień okresu istnienia sesji w sesji przeglądarki nie ma trwałych plików cookie. Za każdym razem, gdy użytkownik zamknie i otworzy przeglądarkę, otrzymuje monit o ponowne uwierzytelnienie. W przypadku klientów pakietu Office domyślny okres jest przedziałem czasu 90 dni. Jeśli użytkownik zresetował swoje hasło przy użyciu tej domyślnej konfiguracji pakietu Office lub nie działa przez ponad 90 dni, użytkownik musi ponownie uwierzytelnić się ze wszystkimi wymaganymi czynnikami (pierwszym i drugim czynnikiem).

Użytkownik może zobaczyć wiele wyświetleń usługi MFA na urządzeniu, które nie ma tożsamości w usłudze Azure AD. Jeśli każda aplikacja ma swój własny token odświeżenia OAuth, który nie jest udostępniany innym aplikacjom klienckim, zostanie wyświetlony komunikat z wieloma wynikami. W tym scenariuszu MFA monituje wiele razy, ponieważ każda aplikacja żąda zweryfikowania tokenu odświeżania OAuth przy użyciu usługi MFA.

W usłudze Azure AD najbardziej restrykcyjne zasady dotyczące okresu istnienia sesji decydują o konieczności ponownego uwierzytelnienia użytkownika. Poniżej przedstawiono przykładowy scenariusz:

* Włączasz opcję *zalogować*, która używa trwałego pliku cookie przeglądarki i
* Możesz również włączyć opcję *Zapamiętaj uwierzytelnianie wieloskładnikowe przez 14 dni*

W tym przykładowym scenariuszu użytkownik musi ponownie uwierzytelnić co 14 dni. To zachowanie jest zgodne z najbardziej restrykcyjnymi zasadami, nawet jeśli nie można *zalogować* się do użytkownika w przeglądarce.

### <a name="managed-devices"></a>Urządzenia zarządzane

Urządzenia przyłączone do usługi Azure AD przy użyciu funkcji Azure AD Join lub hybrydowego sprzężenia usługi Azure AD odbierają [podstawowe tokeny odświeżania (PRT)](../devices/concept-primary-refresh-token.md) w celu korzystania z logowania jednokrotnego (SSO) w aplikacjach. Ta PRT umożliwia użytkownikowi Logowanie jednokrotne na urządzeniu i pozwala pracownikom IT upewnić się, że są spełnione standardy dotyczące zabezpieczeń i zgodności. Jeśli użytkownik musi być monitowany o częściowe zalogowanie się na dołączonym urządzeniu dla niektórych aplikacji lub scenariuszy, można to osiągnąć przy użyciu [częstotliwości logowania dostępu warunkowego](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="show-option-to-remain-signed-in"></a>Pokaż opcję, która ma pozostać zalogowana

Gdy użytkownik wybierze opcję **tak** w opcji *pozostania w logowaniu?* podczas logowania, w przeglądarce jest ustawiany trwały plik cookie. Ten trwały plik cookie zapamiętuje zarówno pierwszy, jak i drugi, i ma zastosowanie tylko do żądań uwierzytelnienia w przeglądarce.

![Zrzut ekranu przedstawiający przykładowy wiersz, który pozostanie zalogowany](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/stay-signed-in-prompt.png)

Jeśli masz licencję na Azure AD — wersja Premium 1, zalecamy korzystanie z zasad dostępu warunkowego dla *sesji przeglądarki trwałej*. Te zasady zastępują ustawienia *pozostania po zalogowaniu?* zapewniają Ulepszone środowisko użytkownika. Jeśli nie masz licencji na Azure AD — wersja Premium 1, zalecamy włączenie ustawienia pozostania dla użytkowników.

Aby uzyskać więcej informacji na temat konfigurowania opcji, aby zezwolić użytkownikom na logowanie, zobacz [Dostosowywanie strony logowania do usługi Azure AD](../fundamentals/customize-branding.md#customize-your-azure-ad-sign-in-page).

### <a name="remember-multi-factor-authentication"></a>Pamiętaj Multi-Factor Authentication  

To ustawienie umożliwia skonfigurowanie wartości z zakresu od 1-365 dni i ustawienie trwałego pliku cookie w przeglądarce, gdy użytkownik wybierze opcję **nie Monituj ponownie dla X dni** podczas logowania.

![Zrzut ekranu przedstawiający przykład monitu o zatwierdzenie żądania logowania](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/approve-sign-in-request.png)

Chociaż to ustawienie zmniejsza liczbę uwierzytelnień w usłudze Web Apps, zwiększa liczbę uwierzytelnień dla nowoczesnych klientów uwierzytelniania, takich jak klienci pakietu Office. Ci klienci zwykle monitują się tylko po zresetowaniu hasła lub nieaktywności przez 90 dni. Ustawienie tej wartości na wartość mniejszą niż 90 dni spowoduje jednak skrócenie domyślnych wierszy usługi MFA dla klientów pakietu Office i wydłużenie częstotliwości uwierzytelniania. W przypadku korzystania z programu w połączeniu z zasadami **pozostawania w** systemie lub dostęp warunkowy może zwiększyć liczbę żądań uwierzytelniania.

Jeśli używasz *zapamiętania MFA* i masz Azure AD — wersja Premium 1 licencje, rozważ migrację tych ustawień do częstotliwości logowania dostępu warunkowego. W przeciwnym razie Rozważ użycie opcji nie *wylogowuj mnie?*

Aby uzyskać więcej informacji, zobacz temat [Zapamiętaj Multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

### <a name="authentication-session-management-with-conditional-access"></a>Zarządzanie sesjami uwierzytelniania przy użyciu dostępu warunkowego

**Częstotliwość logowania** pozwala administratorowi na wybranie częstotliwości logowania, która dotyczy zarówno pierwszego, jak i drugiego czynnika zarówno w kliencie, jak i w przeglądarce. Zalecamy korzystanie z tych ustawień wraz z urządzeniami zarządzanymi w sytuacjach, gdy istnieje potrzeba ograniczenia sesji uwierzytelniania, na przykład w przypadku krytycznych aplikacji firmowych.

**Trwała sesja przeglądarki** umożliwia użytkownikom zalogowanie się po zamknięciu i ponownym otwarciu okna przeglądarki. Podobnie jak w przypadku ustawienia *zarejestrowano* , ustawia trwały plik cookie w przeglądarce. Jednak ze względu na to, że jest ona konfigurowana przez administratora, nie wymaga, aby użytkownik wybierze opcję **tak** w opcji *pozostania w zalogowaniu?* zapewnia lepszy komfort pracy użytkowników. Jeśli używasz opcji *pozostawania z zalogowaniem?* zaleca się włączenie w zamian zasad **sesji z trwałymi przeglądarkami** .

Więcej informacji. Zobacz [Konfigurowanie zarządzania sesjami uwierzytelniania przy użyciu dostępu warunkowego](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="configurable-token-lifetimes"></a>Konfigurowalne okresy istnienia tokenów

To ustawienie umożliwia konfigurację okresu istnienia dla tokenu wystawionego przez Azure Active Directory. Te zasady są zastępowane przez *Zarządzanie sesjami uwierzytelniania z dostępem warunkowym*. Jeśli obecnie używasz *konfigurowalnych okresów istnienia tokenu* , zalecamy rozpoczęcie migracji do zasad dostępu warunkowego.

## <a name="review-your-tenant-configuration"></a>Przejrzyj konfigurację dzierżawy  

Teraz, gdy rozumiesz, jak działają różne ustawienia, a zalecaną konfiguracją, można sprawdzić konfigurację dzierżawców i wprowadzić zmiany odpowiednio:

Aby skonfigurować lub przejrzeć opcję *pozostała Rejestracja* , wykonaj następujące czynności:

1. W portalu usługi Azure AD Wyszukaj i wybierz pozycję *Azure Active Directory*.
1. Wybierz opcję **znakowanie firmowe**, a następnie dla każdego ustawienia regionalnego wybierz pozycję **Pokaż opcję, aby pozostać zalogowany**.
1. Wybierz opcję *tak*, a następnie wybierz pozycję **Zapisz**.

Aby zapamiętać ustawienia usługi uwierzytelniania wieloskładnikowego, wykonaj następujące czynności:

1. W portalu usługi Azure AD Wyszukaj i wybierz pozycję *Azure Active Directory*.
1. Wybierz pozycję **zabezpieczenia**, a następnie polecenie **MFA**.
1. W obszarze **Konfiguracja**wybierz pozycję **dodatkowe ustawienia usługi MFA oparte na chmurze**.
1. Na stronie *Ustawienia usługi uwierzytelniania wieloskładnikowego* przewiń listę, aby **zapamiętać ustawienia uwierzytelniania wieloskładnikowego**. Wyłącz ustawienie, usuwając zaznaczenie pola wyboru.

Aby skonfigurować zasady dostępu warunkowego w celu uzyskania częstotliwości logowania i trwałej sesji przeglądarki, wykonaj następujące czynności:

1. W portalu usługi Azure AD Wyszukaj i wybierz pozycję *Azure Active Directory*.
1. Wybierz pozycję **zabezpieczenia**, a następnie **dostęp warunkowy**.
1. Skonfiguruj zasady przy użyciu zalecanych opcji zarządzania sesją opisanych w tym artykule.

Aby przejrzeć okresy istnienia tokenów, [Użyj programu Azure AD PowerShell, aby wykonać zapytanie dotyczące dowolnych zasad usługi Azure AD](../develop/configure-token-lifetimes.md#prerequisites). Wyłącz wszystkie zasady, które są stosowane.

Jeśli w dzierżawie jest włączona więcej niż jedno ustawienie, Zalecamy zaktualizowanie ustawień na podstawie dostępnych licencji. Jeśli na przykład masz licencje usługi Azure AD Premium, należy używać tylko zasad dostępu warunkowego z *częstotliwością logowania* i *trwałą sesją przeglądarki*. Jeśli masz Microsoft 365 aplikacje lub bezpłatne licencje usługi Azure AD, musisz użyć konfiguracji *"zalogować się"?*

Jeśli zostały włączone konfigurowalne okresy istnienia tokenu, ta możliwość zostanie usunięta wkrótce. Zaplanuj migrację do zasad dostępu warunkowego.

Poniższa tabela zawiera podsumowanie zaleceń w oparciu o licencje:

|              | Aplikacje Azure AD — wersja Bezpłatna i Microsoft 365 | Usługa Azure AD — wersja Premium |
|------------------------------|-----------------------------------|------------------|
| **Logowanie jednokrotne**                      | [Usługa Azure AD Join](../devices/concept-azure-ad-join.md) lub [hybrydowe łączenie z usługą Azure AD](../devices/concept-azure-ad-join-hybrid.md)lub [bezproblemowe logowanie jednokrotne](../hybrid/how-to-connect-sso.md) w przypadku urządzeń niezarządzanych. | Dołączenie do usługi Azure AD<br />Dołączenie hybrydowe do usługi Azure AD |
| **Ustawienia ponownego uwierzytelniania** | Pozostanie zalogowany                  | Korzystanie z zasad dostępu warunkowego w celu uzyskania częstotliwości logowania i trwałej sesji przeglądarki |

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę, wykonaj kroki samouczka, aby [zabezpieczyć zdarzenia logowania użytkowników za pomocą usługi azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md) lub [użyć wykrywania ryzyka dla logowania użytkowników w celu wyzwolenia usługi Azure Multi-Factor Authentication](tutorial-risk-based-sspr-mfa.md).
