---
title: Logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator — Azure Active Directory
description: Włączanie logowania bezhasła do usługi Azure AD przy użyciu aplikacji Microsoft Authenticator (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/11/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35eff46a0470d429c8ec6f364ffa836501c65f47
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743602"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Włącz logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator (wersja zapoznawcza)

Aplikacja Microsoft Authenticator może służyć do logowania się do dowolnego konta usługi Azure AD bez użycia hasła. Microsoft Authenticator używa uwierzytelniania opartego na kluczach, aby włączyć poświadczenia użytkownika powiązane z urządzeniem, gdzie urządzenie używa numeru PIN lub biometrycznego. Funkcja [Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/hello-identity-verification) używa podobnej technologii.

Tej technologii uwierzytelniania można używać na dowolnej platformie urządzeń, w tym dla urządzeń przenośnych. Ta technologia może być również używana z dowolną aplikacją lub witryną sieci Web, która integruje się z bibliotekami uwierzytelniania firmy Microsoft.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="Przykład logowania do przeglądarki z zapytaniem użytkownika o zatwierdzenie logowania.":::

Osoby, które włączyli logowanie za pomocą telefonu z aplikacji Microsoft Authenticator, zobaczą komunikat z prośbą o naciśnięcie numeru w swojej aplikacji. Nie zostanie wyświetlony monit o podanie nazwy użytkownika ani hasła. Aby ukończyć proces logowania w aplikacji, użytkownik musi następnie wykonać następujące czynności:

1. Dopasowanie do liczby.
2. Wybierz pozycję **Zatwierdź**.
3. Podaj numer PIN lub biometryczne.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z logowania jednokrotnego przy użyciu aplikacji Microsoft Authenticator, należy spełnić następujące wymagania wstępne:

- Usługa Azure AD Multi-Factor Authentication z użyciem powiadomień wypychanych jako metody weryfikacji.
- Najnowsza wersja Microsoft Authenticator zainstalowana na urządzeniach z systemem iOS 8,0 lub nowszym albo systemem Android 6,0 lub nowszym.

> [!NOTE]
> Jeśli Microsoft Authenticator włączono obsługę logowania bezhasłem w wersji zapoznawczej przy użyciu programu Azure AD PowerShell, został on włączony dla całego katalogu. Jeśli włączysz tę nową metodę, zastępuje ona zasady programu PowerShell. Zalecamy włączenie dla wszystkich użytkowników w dzierżawie za pomocą menu nowe *metody uwierzytelniania* , w przeciwnym razie użytkownicy, którzy nie znajdują się w nowych zasadach, nie będą już mogli logować się bez hasła.

## <a name="enable-passwordless-authentication-methods"></a>Włącz metody uwierzytelniania bezhasła

Aby korzystać z uwierzytelniania bez hasła w usłudze Azure AD, najpierw włącz połączone środowisko rejestracji, a następnie zezwól użytkownikom na użycie metody mniejszej niż hasło.

### <a name="enable-the-combined-registration-experience"></a>Włącz połączone środowisko rejestracji

Funkcje rejestracji dla metod uwierzytelniania bezhaseł polegają na funkcji rejestracji połączonej. Aby umożliwić użytkownikom dokończenie połączonej rejestracji, postępuj zgodnie z instrukcjami, aby [włączyć rejestrację informacji o zabezpieczeniach](howto-registration-mfa-sspr-combined.md).

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Włącz metodę uwierzytelniania logowania jednokrotnego przy użyciu hasła

Usługa Azure AD umożliwia wybranie metod uwierzytelniania, które mogą być używane podczas procesu logowania. Następnie użytkownicy rejestrują się do metod, których chcesz użyć.

Aby włączyć metodę uwierzytelniania dla logowania za pomocą telefonu bezhasłem, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta *administratora globalnego* .
1. Wyszukaj i wybierz *Azure Active Directory*, a następnie przejdź do **Security**  >  **metody uwierzytelniania** zabezpieczeń  >  **zasady metody uwierzytelniania (wersja zapoznawcza)**
1. W obszarze **Logowanie za pomocą telefonu bezhasło** wybierz następujące opcje:
   1. **Włącz** — tak lub nie
   1. **Cel** — wszyscy użytkownicy lub wybrani użytkownicy
1. Aby zastosować nowe zasady, wybierz pozycję **Zapisz**.

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Rejestracja użytkownika i zarządzanie Microsoft Authenticator

Użytkownicy rejestrują się w celu uzyskania metody uwierzytelniania bezhaseł w usłudze Azure AD, wykonując następujące czynności:

1. Przejdź do [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
1. Zaloguj się, a następnie Dodaj aplikację Authenticator, wybierając pozycję **Dodaj metodę > wystawcy aplikację**, a następnie pozycję **Dodaj**.
1. Postępuj zgodnie z instrukcjami, aby zainstalować i skonfigurować aplikację Microsoft Authenticator na urządzeniu.
1. Wybierz pozycję **gotowe** , aby ukończyć konfigurację uwierzytelniania.
1. W **Microsoft Authenticator** wybierz pozycję **Włącz logowanie** za pomocą telefonu z menu rozwijanego dla zarejestrowanego konta.
1. Postępuj zgodnie z instrukcjami w aplikacji, aby zakończyć rejestrowanie konta do logowania za pomocą telefonu bezhasłem.

Organizacja może kierować swoich użytkowników do logowania się przy użyciu telefonów bez użycia hasła. Aby uzyskać dalszą pomoc w konfigurowaniu aplikacji Microsoft Authenticator i włączaniu logowania za pomocą telefonu, zobacz [Logowanie do kont przy użyciu aplikacji Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md).

> [!NOTE]
> Użytkownicy, którzy nie są przypisani przez zasady do korzystania z logowania za pomocą telefonu, nie mogą już włączyć jej w ramach aplikacji Microsoft Authenticator.

## <a name="sign-in-with-passwordless-credential"></a>Zaloguj się przy użyciu poświadczeń bez hasła

Użytkownik może zacząć korzystać z logowania bezhasłem po zakończeniu wszystkich następujących akcji:

- Administrator włączył dzierżawcę użytkownika.
- Użytkownik zaktualizował aplikację Microsoft Authenticator, aby umożliwić logowanie za pomocą telefonu.

Użytkownik po raz pierwszy uruchamia proces logowania za pomocą telefonu, wykonując następujące czynności:

1. Wprowadza swoją nazwę na stronie logowania.
2. Wybierz przycisk **dalej**.
3. W razie potrzeby wybiera **inne sposoby logowania**.
4. Wybierz opcję **Zatwierdź żądanie w aplikacji my Microsoft Authenticator**.

Użytkownik jest następnie prezentowany z liczbą. Aplikacja poprosi użytkownika o uwierzytelnienie, wybierając odpowiednią liczbę, a nie przez wprowadzenie hasła.

Gdy użytkownik skorzysta z logowania jednokrotnego przy użyciu hasła, aplikacja będzie w dalszym ciągu przeprowadzić użytkownika przez tę metodę. Jednak użytkownik zobaczy opcję, aby wybrać inną metodę.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="Przykład logowania w przeglądarce przy użyciu aplikacji Microsoft Authenticator.":::

## <a name="known-issues"></a>Znane problemy

W bieżącej wersji zapoznawczej istnieją następujące znane problemy.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Nie widzi opcji logowania jednokrotnego bez hasła

W jednym scenariuszu użytkownik może mieć odpowiedzianą weryfikację logowania jednokrotnego przy użyciu hasła, która jest w stanie oczekiwania. Użytkownik może jeszcze próbować zalogować się ponownie. W takim przypadku użytkownik może zobaczyć tylko opcję wprowadzenia hasła.

Aby rozwiązać ten scenariusz, można użyć następujących kroków:

1. Otwórz aplikację Microsoft Authenticator.
2. Odpowiadanie na wszelkie komunikaty powiadomień.

Następnie użytkownik może nadal korzystać z logowania za pomocą telefonu bezhasłem.

### <a name="federated-accounts"></a>Konta federacyjne

Gdy użytkownik włączył każde poświadczenie bez hasła, proces logowania usługi Azure AD zostanie zatrzymany przy użyciu \_ wskazówki logowania. W związku z tym proces ten nie przyspiesza już użytkownika w kierunku lokalizacji logowania federacyjnego.

Ta logika zazwyczaj zapobiega kierowaniu użytkowników w dzierżawie hybrydowej do Active Directory usług federacyjnych (AD FS) na potrzeby weryfikacji logowania. Jednak użytkownik zachowuje opcję **użycia hasła zamiast tego**.

### <a name="azure-mfa-server"></a>Serwer usługi Azure MFA

Użytkownik końcowy może włączyć funkcję uwierzytelniania wieloskładnikowego (MFA) za pomocą lokalnego serwera usługi Azure MFA. Użytkownik może nadal tworzyć i korzystać z jednego poświadczenia logowania jednokrotnego przy użyciu hasła.

Jeśli użytkownik podejmie próbę uaktualnienia wielu instalacji (5 +) aplikacji Microsoft Authenticator przy użyciu poświadczeń logowania jednokrotnego dla telefonu bez hasła, ta zmiana może spowodować wystąpienie błędu.

### <a name="device-registration"></a>Rejestracja urządzenia

Aby można było utworzyć nowe silne poświadczenia, istnieją wymagania wstępne. Jednym z wymagań wstępnych jest to, że urządzenie, na którym zainstalowano aplikację Microsoft Authenticator, musi być zarejestrowane w ramach dzierżawy usługi Azure AD dla pojedynczego użytkownika.

Obecnie urządzenie można zarejestrować tylko w jednej dzierżawie. Ten limit oznacza, że tylko jedno konto służbowe w aplikacji Microsoft Authenticator może być włączone do logowania za pomocą telefonu.

> [!NOTE]
> Rejestracja urządzeń nie jest taka sama jak zarządzanie urządzeniami ani zarządzanie urządzeniami przenośnymi (MDM). Rejestracja urządzenia powoduje Skojarzenie identyfikatora urządzenia z identyfikatorem użytkownika w katalogu usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o metodach uwierzytelniania i bezhasła usługi Azure AD, zobacz następujące artykuły:

- [Dowiedz się, jak działa uwierzytelnianie bezhasłem](concept-authentication-passwordless.md)
- [Informacje o rejestracji urządzeń](../devices/overview.md#getting-devices-in-azure-ad)
- [Dowiedz się więcej o usłudze Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
