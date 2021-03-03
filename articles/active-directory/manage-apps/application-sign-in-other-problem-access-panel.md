---
title: Rozwiązywanie problemów z logowaniem do aplikacji z usługi Azure AD moje aplikacje
description: Rozwiązywanie problemów z logowaniem do aplikacji z usługi Azure AD moje aplikacje
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: 2a0411e97f78104de1356d482e4e43a42701c073
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687628"
---
# <a name="troubleshoot-problems-signing-in-to-an-application-from-azure-ad-my-apps"></a>Rozwiązywanie problemów z logowaniem do aplikacji z usługi Azure AD moje aplikacje

Moje aplikacje są portalem opartym na sieci Web, który umożliwia użytkownikowi z kontem służbowym w usłudze Azure Active Directory (Azure AD) wyświetlanie i uruchamianie aplikacji opartych na chmurze, do których administrator usługi Azure AD udzielił im dostępu. Dostęp do aplikacji odbywa się przy użyciu przeglądarki sieci Web pod adresem [https://myapps.microsoft.com](https://myapps.microsoft.com) .

Aby dowiedzieć się więcej o korzystaniu z usługi Azure AD jako dostawcy tożsamości dla aplikacji, zobacz artykuł dotyczący [zarządzania aplikacjami w usłudze Azure AD](what-is-application-management.md). Aby szybko uzyskać szybki dostęp, zapoznaj się z [serią szybki start na stronie Zarządzanie aplikacjami](view-applications-portal.md).

Te aplikacje są konfigurowane w imieniu użytkownika w portalu usługi Azure AD. Aplikacja musi być poprawnie skonfigurowana i przypisana do użytkownika lub grupy, do której należy użytkownik, aby wyświetlić aplikację w obszarze Moje aplikacje. 

Typ aplikacji widocznych dla użytkownika można podzielić na następujące kategorie:
-   Microsoft 365 aplikacji
-   Aplikacje firmy Microsoft i innych firm skonfigurowane z logowaniem jednokrotnym opartym na Federacji
-   Aplikacje logowania jednokrotnego oparte na hasłach
-   Aplikacje z istniejącymi rozwiązaniami logowania jednokrotnego

Oto kilka rzeczy, aby sprawdzić, czy aplikacja jest wyświetlana lub nie pojawia się.
- Upewnij się, że aplikacja została dodana do usługi Azure AD i upewnij się, że użytkownik jest przypisany. Aby dowiedzieć się więcej, zobacz [Przewodnik Szybki Start dotyczący zarządzania aplikacjami](add-application-portal.md).
- Jeśli aplikacja została niedawno dodana, należy wylogować się i ponownie z powrotem. 
- Jeśli aplikacja wymaga licencji, takiej jak pakiet Office, upewnij się, że użytkownik ma przypisaną odpowiednią licencję.
- Czas potrzebny na zmiany licencjonowania może się różnić w zależności od rozmiaru i złożoności grupy.

## <a name="general-issues-to-check-first"></a>Ogólne problemy do sprawdzenia w pierwszej kolejności

-   Upewnij się, że przeglądarka sieci Web spełnia wymagania, zobacz [Moje aplikacje obsługiwane przeglądarki](../user-help/my-apps-portal-end-user-access.md).
-   Upewnij się, że przeglądarka użytkownika dodała adres URL aplikacji do jej **zaufanych witryn**.
-   Upewnij się, że aplikacja została prawidłowo **skonfigurowana** .
-   Upewnij się, że konto użytkownika jest **włączone** do logowania.
-   Upewnij się, że konto użytkownika **nie jest zablokowane.**
-   Upewnij się, że **hasło użytkownika nie wygasło lub zostało zapomniane.**
-   Upewnij się, że **Multi-Factor Authentication** nie blokuje dostępu użytkownika.
-   Upewnij się, że **zasady dostępu warunkowego** lub zasady **ochrony tożsamości** nie blokują dostępu użytkownika.
-   Upewnij się, że **informacje kontaktowe uwierzytelniania** użytkownika są aktualne, aby zezwolić na wymuszanie zasad dostępu Multi-Factor Authentication lub warunkowego.
-   Upewnij się, że wyczyścisz również pliki cookie w przeglądarce i spróbujesz ponownie zalogować.

## <a name="problems-with-the-users-account"></a>Problemy związane z kontem użytkownika
Dostęp do aplikacji można zablokować z powodu problemu z kontem użytkownika. Poniżej przedstawiono kilka sposobów rozwiązywania problemów z użytkownikami i ich ustawień konta:
-   [Sprawdź, czy konto użytkownika istnieje w Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)
-   [Sprawdź stan konta użytkownika](#check-a-users-account-status)
-   [Resetowanie hasła użytkownika](#reset-a-users-password)
-   [Włączanie samoobsługowego resetowania hasła](#enable-self-service-password-reset)
-   [Sprawdź stan usługi uwierzytelniania wieloskładnikowego użytkownika](#check-a-users-multi-factor-authentication-status)
-   [Sprawdź informacje kontaktowe uwierzytelniania użytkownika](#check-a-users-authentication-contact-info)
-   [Sprawdź członkostwo w grupach użytkowników](#check-a-users-group-memberships)
-   [Sprawdź, czy użytkownik ma więcej niż 999 przypisań ról aplikacji](#check-if-a-user-has-more-than-999-app-role-assignments)
-   [Sprawdź przypisane licencje użytkownika](#check-a-users-assigned-licenses)
-   [Przypisywanie użytkownikowi licencji](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Sprawdź, czy konto użytkownika istnieje w Azure Active Directory
Aby sprawdzić, czy konto użytkownika jest obecne, wykonaj następujące czynności:
1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**
2.  Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.
3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .
4.  W menu nawigacji wybierz pozycję **Użytkownicy i grupy** .
5.  Wybierz pozycję **Wszyscy użytkownicy**.
6.  **Wyszukaj** interesującego Cię użytkownika i **Wybierz wiersz** do wybrania.
7.  Sprawdź właściwości obiektu użytkownika, aby upewnić się, że będą wyglądały zgodnie z oczekiwaniami, i nie ma żadnych danych.

### <a name="check-a-users-account-status"></a>Sprawdź stan konta użytkownika
Aby sprawdzić stan konta użytkownika, wykonaj następujące kroki:
1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**
2.  Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.
3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .
4.  W menu nawigacji wybierz pozycję **Użytkownicy i grupy** .
5.  Wybierz pozycję **Wszyscy użytkownicy**.
6.  **Wyszukaj** interesującego Cię użytkownika i **Wybierz wiersz** do wybrania.
7.  Wybierz pozycję **profil**.
8.  W obszarze **Ustawienia** upewnij się, że opcja **Zablokuj logowanie** jest ustawiona na wartość **nie**.

### <a name="reset-a-users-password"></a>Resetowanie hasła użytkownika
Aby zresetować hasło użytkownika, wykonaj następujące kroki:
1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**
2.  Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.
3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .
4.  W menu nawigacji wybierz pozycję **Użytkownicy i grupy** .
5.  Wybierz pozycję **Wszyscy użytkownicy**.
6.  **Wyszukaj** interesującego Cię użytkownika i **Wybierz wiersz** do wybrania.
7.  Wybierz przycisk **Resetuj hasło** w górnej części okienka użytkownika.
8.  Wybierz przycisk **Resetuj hasło** w wyświetlonym okienku **Resetowanie hasła** .
9.  Skopiuj **hasło tymczasowe** lub **wprowadź nowe hasło** dla użytkownika.
10. Poinformuj użytkownika o tym nowym haśle, aby móc zmienić to hasło podczas kolejnego logowania do Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Włączanie samoobsługowego resetowania hasła
Aby włączyć funkcję samoobsługowego resetowania hasła, wykonaj następujące kroki wdrażania:
-   [Zezwalaj użytkownikom na Resetowanie swoich haseł Azure Active Directory](../authentication/tutorial-enable-sspr.md)
-   [Zezwól użytkownikom na Resetowanie lub zmienianie Active Directory haseł lokalnych](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>Sprawdź stan usługi uwierzytelniania wieloskładnikowego użytkownika
Aby sprawdzić stan usługi uwierzytelniania wieloskładnikowego użytkownika, wykonaj następujące kroki:
1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**
2. Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.
3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .
4. W menu nawigacji wybierz pozycję **Użytkownicy i grupy** .
5. Wybierz pozycję **Wszyscy użytkownicy**.
6. Wybierz przycisk **Multi-Factor Authentication** w górnej części okienka.
7. Po załadowaniu **portalu administracyjnego Multi-Factor Authentication** upewnij się, że jesteś na karcie **Użytkownicy** .
8. Znajdź użytkownika na liście użytkowników, wyszukując, filtrując lub sortując.
9. Wybierz użytkownika z listy użytkowników i **Włącz**, **Wyłącz** lub **wymuszj** uwierzytelnianie wieloskładnikowe zgodnie z potrzebami.
   >[!NOTE]
   >Jeśli użytkownik jest w stanie **wymuszonym** , można ustawić je jako czasowo **wyłączone** , aby umożliwić im powrót do konta. Po ponownym uruchomieniu programu można zmienić jego stan na ponownie **włączony** , aby wymagać od nich ponownego zarejestrowania informacji kontaktowych podczas kolejnego logowania. Alternatywnie możesz wykonać kroki opisane w [informacji kontaktowych dotyczących uwierzytelniania użytkownika](#check-a-users-authentication-contact-info) , aby sprawdzić lub ustawić dla nich te dane.

### <a name="check-a-users-authentication-contact-info"></a>Sprawdź informacje kontaktowe uwierzytelniania użytkownika
Aby sprawdzić informacje kontaktowe uwierzytelniania użytkownika używane do uwierzytelniania wieloskładnikowego, dostępu warunkowego, ochrony tożsamości i resetowania hasła, wykonaj następujące kroki:
1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**
2.  Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.
3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .
4.  W menu nawigacji wybierz pozycję **Użytkownicy i grupy** .
5.  Wybierz pozycję **Wszyscy użytkownicy**.
6.  **Wyszukaj** interesującego Cię użytkownika i **Wybierz wiersz** do wybrania.
7.  Wybierz pozycję **profil**.
8.  Przewiń w dół do **informacji kontaktowych dotyczących uwierzytelniania**.
9.  **Przejrzyj** dane zarejestrowane dla użytkownika i zaktualizuj je w razie potrzeby.

### <a name="check-a-users-group-memberships"></a>Sprawdź członkostwo w grupach użytkowników
Aby sprawdzić członkostwa w grupach użytkowników, wykonaj następujące kroki:
1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**
2.  Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.
3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .
4.  W menu nawigacji wybierz pozycję **Użytkownicy i grupy** .
5.  Wybierz pozycję **Wszyscy użytkownicy**.
6.  **Wyszukaj** interesującego Cię użytkownika i **Wybierz wiersz** do wybrania.
7.  Wybierz **grupy** , aby zobaczyć grupy, do których należy użytkownik.

### <a name="check-if-a-user-has-more-than-999-app-role-assignments"></a>Sprawdź, czy użytkownik ma więcej niż 999 przypisań ról aplikacji
Jeśli użytkownik ma więcej niż 999 przypisań ról aplikacji, może nie zobaczyć wszystkich swoich aplikacji w moich aplikacjach.

Wynika to z faktu, że moje aplikacje odczytuje do 999 przypisań ról aplikacji, aby określić aplikacje, do których użytkownicy są przypisani. Jeśli użytkownik jest przypisany do ponad 999 aplikacji, nie jest możliwe kontrolowanie, które z tych aplikacji będą wyświetlane w portalu My Apps.

Aby sprawdzić, czy użytkownik ma więcej niż 999 przypisań ról aplikacji, wykonaj następujące kroki:
1. Zainstaluj moduł programu [**Microsoft. Graph**](https://github.com/microsoftgraph/msgraph-sdk-powershell) PowerShell.
2. Uruchom polecenie `Connect-MgGraph -Scopes "User.ReadBasic.All Application.Read.All"`.
3. Uruchom, `(Get-MgUserAppRoleAssignment -UserId "<userId>" -Top 999).Count` Aby określić liczbę przypisań ról aplikacji aktualnie przyznanych przez użytkownika.
4. Jeśli wynikiem jest 999, użytkownik prawdopodobnie ma więcej niż 999 przypisań ról aplikacji.

### <a name="check-a-users-assigned-licenses"></a>Sprawdź przypisane licencje użytkownika
Aby sprawdzić przypisane licencje użytkownika, wykonaj następujące kroki:
1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**
2.  Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.
3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .
4.  W menu nawigacji wybierz pozycję **Użytkownicy i grupy** .
5.  Wybierz pozycję **Wszyscy użytkownicy**.
6.  **Wyszukaj** interesującego Cię użytkownika i **Wybierz wiersz** do wybrania.
7.  Wybierz pozycję **licencje** , aby zobaczyć, które licencje aktualnie przypisane do użytkownika.

### <a name="assign-a-user-a-license"></a>Przypisywanie użytkownikowi licencji 
Aby przypisać licencję do użytkownika, wykonaj następujące kroki:
1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**
2.  Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.
3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .
4.  W menu nawigacji wybierz pozycję **Użytkownicy i grupy** .
5.  Wybierz pozycję **Wszyscy użytkownicy**.
6.  **Wyszukaj** interesującego Cię użytkownika i **Wybierz wiersz** do wybrania.
7.  Wybierz pozycję **licencje** , aby zobaczyć, które licencje aktualnie przypisane do użytkownika.
8.  Wybierz przycisk **Przypisz** .
9.  Wybierz **co najmniej jeden produkt** z listy dostępnych produktów.
10. **Opcjonalnie** wybierz element **Opcje przypisania** , aby szczegółowo przypisać produkty. Wybierz przycisk **OK**.
11. Wybierz przycisk **Przypisz** , aby przypisać te licencje temu użytkownikowi.

## <a name="troubleshooting-deep-links"></a>Rozwiązywanie problemów z linkami bezpośrednimi
Linki głębokie lub adresy URL dostępu użytkowników to linki, których użytkownicy mogą używać w celu uzyskania dostępu do aplikacji logowania jednokrotnego w przeglądarce. Przechodzenie do tego linku powoduje automatyczne zalogowanie użytkowników do aplikacji bez konieczności wcześniejszego przechodzenia do moich aplikacji. Link jest tym samym, przy użyciu którego użytkownicy uzyskują dostęp do tych aplikacji z poziomu uruchamiania aplikacji Microsoft 365.

### <a name="checking-the-deep-link"></a>Sprawdzanie linku głębokiego

Aby sprawdzić, czy masz poprawne łącze głębokie, wykonaj następujące czynności:
1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**
2. Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.
3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .
4. Wybierz pozycję **Aplikacje dla przedsiębiorstw** z menu nawigacji usługi Azure Active Directory po lewej stronie.
5. Wybierz pozycję **Wszystkie aplikacje**, aby wyświetlić listę wszystkich aplikacji.
   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**
6. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**
7. Otwórz **rozszerzenie Azure Active Directory** , wybierając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.
8. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .
9. Wybierz pozycję **Aplikacje dla przedsiębiorstw** z menu nawigacji usługi Azure Active Directory po lewej stronie.
10. Wybierz pozycję **Wszystkie aplikacje**, aby wyświetlić listę wszystkich aplikacji.
    * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**
11. Wybierz aplikację, dla której chcesz sprawdzić link bezpośredni.
12. Znajdź etykietę **adres URL dostępu użytkownika**. Link bezpośredni powinien pasować do tego adresu URL.

## <a name="contact-support"></a>Kontakt z pomocą techniczną
Otwórz bilet pomocy technicznej z następującymi informacjami, jeśli są dostępne:
-   Identyfikator błędu korelacji
-   Nazwa UPN (adres e-mail użytkownika)
-   TenantID
-   Typ przeglądarki
-   Strefa czasowa i czas/przedział czasu w trakcie wystąpienia błędu
-   Ślady programu Fiddler

## <a name="next-steps"></a>Następne kroki
- [Seria szybkiego startu w zarządzaniu aplikacjami](view-applications-portal.md)
