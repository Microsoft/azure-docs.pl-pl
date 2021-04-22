---
title: Logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator — Azure Active Directory
description: Włączanie logowania bez hasła w usłudze Azure AD przy użyciu Microsoft Authenticator aplikacji
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/21/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: d586294f101c271f139867d0046576dc9a32f076
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861791"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app"></a>Włączanie logowania bez hasła przy użyciu Microsoft Authenticator aplikacji 

Aplikacja Microsoft Authenticator może służyć do logowania się do dowolnego konta usługi Azure AD bez użycia hasła. Microsoft Authenticator używa uwierzytelniania opartego na kluczach, aby włączyć poświadczenia użytkownika powiązane z urządzeniem, w którym urządzenie używa numeru PIN lub danych biometrycznych. [Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/hello-identity-verification) używa podobnej technologii.

Tej technologii uwierzytelniania można używać na dowolnej platformie urządzenia, w tym na urządzeniach przenośnych. Tej technologii można również używać z dowolną aplikacją lub witryną internetową zintegrowaną z bibliotekami uwierzytelniania firmy Microsoft.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="Przykład logowania za pomocą przeglądarki z prośbą o zatwierdzenie logowania przez użytkownika.":::

Osoby, które włączyły logowanie za pomocą telefonu Microsoft Authenticator aplikacji zobaczą komunikat z prośbą o naciśnięcie numeru w aplikacji. Nie jest monit o nazwę użytkownika ani hasło. Aby ukończyć proces logowania w aplikacji, użytkownik musi wykonać następujące czynności:

1. Dopasuj liczbę.
2. Wybierz pozycję **Zatwierdź**.
3. Podaj numer PIN lub dane biometryczne.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było używać logowania za pomocą telefonu bez hasła Microsoft Authenticator aplikacji, należy spełnić następujące wymagania wstępne:

- Usługa Azure AD Multi-Factor Authentication z powiadomieniami push dozwolonymi jako metoda weryfikacji.
- Najnowsza wersja programu Microsoft Authenticator na urządzeniach z systemem iOS 8.0 lub nowszą albo Systemem Android 6.0 lub nowszą.
- Urządzenie, na którym zainstalowano Microsoft Authenticator aplikacji, musi być zarejestrowane w ramach dzierżawy usługi Azure AD dla pojedynczego użytkownika. 

> [!NOTE]
> Jeśli włączono logowanie Microsoft Authenticator hasła przy użyciu programu Azure AD PowerShell, zostało ono włączone dla całego katalogu. Jeśli włączysz tę nową metodę, będzie ona przesłoniła zasady programu PowerShell. Zalecamy włączenie tej opcji dla wszystkich użytkowników w dzierżawie za pośrednictwem *nowego* menu Metody uwierzytelniania. W przeciwnym razie użytkownicy nie korzystający z nowych zasad nie będą już mogli logować się bez hasła.

## <a name="enable-passwordless-authentication-methods"></a>Włączanie metod uwierzytelniania bez hasła

Aby używać uwierzytelniania bez hasła w usłudze Azure AD, najpierw włącz połączone środowisko rejestracji, a następnie włącz dla użytkowników metodę mniej hasła.

### <a name="enable-the-combined-registration-experience"></a>Włączanie połączonego procesu rejestracji

Funkcje rejestracji dla metod uwierzytelniania bez hasła korzystają z połączonej funkcji rejestracji. Aby umożliwić użytkownikom ukończenie połączonej rejestracji samodzielnie, wykonaj kroki, aby włączyć [rejestrację połączonych informacji zabezpieczających.](howto-registration-mfa-sspr-combined.md)

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Włączanie metod uwierzytelniania logowania za pomocą telefonu bez hasła

Usługa Azure AD umożliwia wybranie metod uwierzytelniania, które mogą być używane podczas procesu logowania. Następnie użytkownicy rejestrują się, aby uzyskać metody, których chcą używać.

Aby włączyć metodę uwierzytelniania dla logowania za pomocą telefonu bez hasła, wykonaj następujące kroki:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) konta *administratora globalnego.*
1. Wyszukaj i wybierz pozycję *Azure Active Directory*, a następnie przejdź do opcji **Metody**  >  **uwierzytelniania zabezpieczeń**  >  **Zasady.**
1. W **Microsoft Authenticator** wybierz następujące opcje:
   1. **Włącz** — tak lub nie
   1. **Cel** — wszyscy użytkownicy lub Wybierz użytkowników
1. Każdy dodany użytkownik lub grupa jest domyślnie włączona, aby używać Microsoft Authenticator w trybie powiadomień bez hasła i powiadomień wypychanych ("Dowolny"). Aby to zmienić, dla każdego wiersza:
   1. Przejdź **do...**  >  **Skonfiguruj usługę**.
   1. W **przypadku trybu uwierzytelniania** — dowolny, bez hasła lub wypychania
1. Aby zastosować nowe zasady, wybierz pozycję **Zapisz**.

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Rejestracja użytkowników i zarządzanie Microsoft Authenticator

Użytkownicy rejestrują się w celu użycia metody uwierzytelniania bez hasła w usłudze Azure AD, korzystając z następujących kroków:

1. Przejdź do [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
1. Zaloguj się, a następnie dodaj aplikację Authenticator, wybierając pozycję Dodaj metodę **> aplikacji Authenticator,** a następnie pozycję **Dodaj**.
1. Postępuj zgodnie z instrukcjami, aby zainstalować i Microsoft Authenticator aplikację na urządzeniu.
1. Wybierz **pozycję Gotowe,** aby ukończyć konfigurację aplikacji Authenticator.
1. W **Microsoft Authenticator** wybierz pozycję **Włącz logowanie** za pomocą telefonu z menu rozwijanego dla zarejestrowanego konta.
1. Postępuj zgodnie z instrukcjami w aplikacji, aby zakończyć rejestrowanie konta do logowania za pomocą telefonu bez hasła.

Organizacja może skierować użytkowników do logowania się przy użyciu swoich telefonów bez użycia hasła. Aby uzyskać dalszą pomoc przy konfigurowaniu aplikacji Microsoft Authenticator i włączaniu logowania za pomocą telefonu, zobacz Logowanie się do kont przy użyciu [Microsoft Authenticator aplikacji .](../user-help/user-help-auth-app-sign-in.md)

> [!NOTE]
> Użytkownicy, którym zasady nie mogą używać logowania za pomocą telefonu, nie będą już mogli go włączyć w Microsoft Authenticator aplikacji.

## <a name="sign-in-with-passwordless-credential"></a>Logowanie się przy użyciu poświadczeń bez hasła

Użytkownik może rozpocząć korzystanie z logowania bez hasła po zakończeniu wszystkich następujących akcji:

- Administrator włączył dzierżawę użytkownika.
- Użytkownik zaktualizował swoją aplikację Microsoft Authenticator, aby umożliwić logowanie za pomocą telefonu.

Gdy użytkownik po raz pierwszy rozpoczyna proces logowania za pomocą telefonu, wykonuje następujące kroki:

1. Wprowadza jej imię i nazwisko na stronie logowania.
2. Wybiera pozycję **Dalej.**
3. W razie potrzeby wybierz pozycję **Inne sposoby logowania.**
4. Wybiera pozycję **Zatwierdź żądanie w mojej Microsoft Authenticator aplikacji.**

Następnie użytkownikowi zostanie przedstawiona liczba. Aplikacja monituje użytkownika o uwierzytelnienie, wybierając odpowiedni numer, a nie wprowadzając hasło.

Gdy użytkownik będzie korzystać z logowania za pomocą telefonu bez hasła, aplikacja będzie nadal kierować użytkownika przez tę metodę. Jednak użytkownik zobaczy opcję wybrania innej metody.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="Przykład logowania za pomocą przeglądarki przy użyciu Microsoft Authenticator aplikacji.":::

## <a name="known-issues"></a>Znane problemy

Istnieją następujące znane problemy.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Opcja logowania za pomocą telefonu bez hasła nie jest dostępna

W jednym scenariuszu użytkownik może mieć bez odpowiedzi weryfikację logowania za pomocą telefonu bez hasła, która jest oczekująca. Użytkownik może jednak spróbować zalogować się ponownie. W takim przypadku użytkownik może zobaczyć tylko opcję wprowadzenia hasła.

Aby rozwiązać ten scenariusz, można wykonać następujące kroki:

1. Otwórz aplikację Microsoft Authenticator.
2. Odpowiadanie na wszelkie monity o powiadomienia.

Następnie użytkownik może nadal korzystać z logowania za pomocą telefonu bez hasła.

### <a name="federated-accounts"></a>Konta federowane

Gdy użytkownik włączy jakiekolwiek poświadczenia bez hasła, proces logowania usługi Azure AD przestanie używać wskazówki \_ logowania. W związku z tym proces nie przyspiesza już działania użytkownika w kierunku federacyjną lokalizację logowania.

Ta logika zwykle uniemożliwia kierowanie użytkownika w dzierżawie hybrydowej do usług Federatorów Active Directory (AD FS) w celu weryfikacji logowania. Użytkownik zachowuje jednak opcję kliknięcia pozycji **Użyj hasła zamiast .**

### <a name="azure-mfa-server"></a>Serwer usługi Azure MFA

Użytkownika końcowego można włączyć w celu uwierzytelniania wieloskładnikowego (MFA) za pośrednictwem lokalnego serwera Usługi Azure MFA. Użytkownik może nadal tworzyć i wykorzystywać pojedyncze poświadczenia logowania za pomocą telefonu bez hasła.

Jeśli użytkownik spróbuje uaktualnić wiele instalacji (5 lub więcej) aplikacji Microsoft Authenticator przy użyciu poświadczeń logowania za pomocą telefonu bez hasła, ta zmiana może spowodować błąd.

### <a name="device-registration"></a>Rejestracja urządzenia

Przed utworzeniem tego nowego silnego poświadczenia należy spełnić wymagania wstępne. Jednym z wymagań wstępnych jest to, że urządzenie, na którym zainstalowano Microsoft Authenticator, musi być zarejestrowane w ramach dzierżawy usługi Azure AD dla pojedynczego użytkownika.

Obecnie urządzenie można zarejestrować tylko w jednej dzierżawie. Ten limit oznacza, że tylko jedno konto służbowe w aplikacji Microsoft Authenticator można włączyć na logowanie za pomocą telefonu.

> [!NOTE]
> Rejestracja urządzenia nie jest taka sama jak zarządzanie urządzeniami lub zarządzanie urządzeniami przenośnymi (MDM). Rejestracja urządzenia kojarzy ze sobą tylko identyfikator urządzenia i identyfikator użytkownika w katalogu usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o metodach uwierzytelniania i bez hasła w usłudze Azure AD, zobacz następujące artykuły:

- [Dowiedz się, jak działa uwierzytelnianie bez hasła](concept-authentication-passwordless.md)
- [Dowiedz się więcej o rejestracji urządzenia](../devices/overview.md#getting-devices-in-azure-ad)
- [Dowiedz się więcej o usłudze Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
