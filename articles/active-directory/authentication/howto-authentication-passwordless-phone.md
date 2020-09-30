---
title: Logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator — Azure Active Directory
description: Włączanie logowania bezhasła do usługi Azure AD przy użyciu aplikacji Microsoft Authenticator (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 09/29/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b4792e73f6326bb9ac67ce3aabe10b8314bb826
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568214"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Włącz logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator (wersja zapoznawcza)

Aplikacja Microsoft Authenticator może służyć do logowania się do dowolnego konta usługi Azure AD bez użycia hasła. Podobnie jak w przypadku technologii [Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator używa uwierzytelniania opartego na kluczach, aby umożliwić poświadczenie użytkownika powiązanego z urządzeniem i używa biometrycznych lub PIN. Tej metody uwierzytelniania można używać na dowolnej platformie urządzeń, w tym dla urządzeń przenośnych, z dowolną aplikacją lub witryną sieci Web, która integruje się z bibliotekami uwierzytelniania firmy Microsoft.

![Przykład logowania do przeglądarki z prośbą do użytkownika o zatwierdzenie logowania](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Zamiast wyświetlać monit o hasło po wprowadzeniu nazwy użytkownika, osoba, która włączyła logowanie za pomocą telefonu z aplikacji Microsoft Authenticator zobaczy komunikat z prośbą o naciśnięcie numeru w swojej aplikacji. Aby ukończyć proces logowania w aplikacji, użytkownik musi dopasować tę liczbę, wybrać pozycję **Zatwierdź**, a następnie podać numer PIN lub biometryczny.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z logowania jednokrotnego przy użyciu aplikacji Microsoft Authenticator, należy spełnić następujące wymagania wstępne:

- Usługa Azure Multi-Factor Authentication z powiadomieniami wypychanymi, które są dozwolone jako metoda weryfikacji.
- Najnowsza wersja Microsoft Authenticator zainstalowana na urządzeniach z systemem iOS 8,0 lub nowszym albo systemem Android 6,0 lub nowszym.

> [!NOTE]
> Jeśli włączono obsługę logowania bezhasłem aplikacji Microsoft Authenticator przy użyciu programu Azure AD PowerShell, został on włączony dla całego katalogu. Jeśli włączysz tę nową metodę, zastępuje ona zasady programu PowerShell. Zalecamy włączenie dla wszystkich użytkowników w dzierżawie za pomocą menu nowe *metody uwierzytelniania* , w przeciwnym razie użytkownicy, którzy nie znajdują się w nowych zasadach, nie będą już mogli logować się bez hasła.

## <a name="enable-passwordless-authentication-methods"></a>Włącz metody uwierzytelniania bezhasła

Aby korzystać z uwierzytelniania bez hasła w usłudze Azure AD, najpierw włącz połączone środowisko rejestracji, a następnie zezwól użytkownikom na użycie metody mniejszej niż hasło.

### <a name="enable-the-combined-registration-experience"></a>Włącz połączone środowisko rejestracji

Funkcje rejestracji dla metod uwierzytelniania bezhaseł polegają na funkcji rejestracji połączonej. Aby umożliwić użytkownikom dokończenie połączonej rejestracji, postępuj zgodnie z instrukcjami, aby [włączyć rejestrację informacji o zabezpieczeniach](howto-registration-mfa-sspr-combined.md).

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Włącz metodę uwierzytelniania logowania jednokrotnego przy użyciu hasła

Usługa Azure AD umożliwia wybranie metod uwierzytelniania, które mogą być używane podczas procesu logowania. Następnie użytkownicy rejestrują się do metod, których chcesz użyć.

Aby włączyć metodę uwierzytelniania dla logowania za pomocą telefonu bezhasłem, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta *administratora globalnego* .
1. Wyszukaj i wybierz *Azure Active Directory*, a następnie przejdź do **Security**  >  **metody uwierzytelniania**zabezpieczeń  >  **zasady metody uwierzytelniania (wersja zapoznawcza)**
1. W obszarze **Logowanie za pomocą telefonu bezhasło**wybierz następujące opcje:
   1. **Włącz** — tak lub nie
   1. **Cel** — wszyscy użytkownicy lub wybrani użytkownicy
1. Aby zastosować nowe zasady, wybierz pozycję **Zapisz**.

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Rejestracja użytkownika i zarządzanie aplikacją Microsoft Authenticator

Korzystając z metody uwierzytelniania bez hasła, która jest dostępna w usłudze Azure AD, użytkownicy muszą teraz zarejestrować się w ramach metody uwierzytelniania bez hasła, wykonując następujące czynności:

1. Przejdź do [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
1. Zaloguj się, a następnie Dodaj aplikację Authenticator, wybierając pozycję **Dodaj metodę > wystawcy aplikację**, a następnie pozycję **Dodaj**.
1. Postępuj zgodnie z instrukcjami, aby zainstalować i skonfigurować aplikację Microsoft Authenticator na urządzeniu.
1. Wybierz pozycję **gotowe** , aby ukończyć konfigurację uwierzytelniania.
1. W aplikacji **Microsoft Authenticator** wybierz pozycję **Włącz logowanie** za pomocą telefonu z menu rozwijanego dla zarejestrowanego konta.
1. Postępuj zgodnie z instrukcjami w aplikacji, aby zakończyć rejestrowanie konta do logowania za pomocą telefonu bezhasłem.

Organizacje mogą kierować użytkowników do [logowania się przy użyciu telefonu, a nie hasła](../user-help/user-help-auth-app-sign-in.md) do dalszej pomocy w konfigurowaniu aplikacji Microsoft Authenticator i włączaniu logowania za pomocą telefonu.

> [!NOTE]
> Użytkownicy, którzy nie są przypisani przez zasady do korzystania z logowania za pomocą telefonu, nie mogą już włączyć jej w ramach aplikacji Microsoft Authenticator.  

## <a name="sign-in-with-passwordless-credential"></a>Zaloguj się przy użyciu poświadczeń bez hasła

Użytkownik może rozpocząć logowanie za pomocą hasła, gdy administrator włączył swoją dzierżawę, a użytkownik zaktualizował Microsoft Authenticator aplikację, aby włączyć logowanie za pomocą telefonu.

Aby zacząć korzystać z logowania za pomocą telefonu, po wpisaniu nazwy użytkownika na stronie logowania i wybraniu **przycisku Dalej**użytkownicy mogą chcieć wybrać **inne sposoby logowania**, a następnie **zatwierdzić żądanie w aplikacji Microsoft Authenticator**. Użytkownicy są wyświetlani z numerem i są monitowani w swojej aplikacji Microsoft Authenticator, aby wybrać odpowiednią liczbę do uwierzytelnienia, a nie przy użyciu hasła. Gdy użytkownicy będą korzystać z logowania za pomocą telefonu bez hasła, otrzymają monit o jego ponowne użycie, dopóki nie użyją innej metody.

![Przykład logowania w przeglądarce przy użyciu aplikacji Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Znane problemy

W bieżącej wersji zapoznawczej istnieją następujące znane problemy.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Nie widzi opcji logowania jednokrotnego bez hasła

Jeśli użytkownik nie ma oczekującej weryfikacji logowania jednokrotnego przy użyciu hasła i próbuje zalogować się ponownie, użytkownik może zobaczyć tylko opcję wprowadzenia hasła. Otwórz Microsoft Authenticator i Odpowiedz na powiadomienia dotyczące powiadomień, aby nadal korzystać z logowania za pomocą telefonu bez hasła.

### <a name="federated-accounts"></a>Konta federacyjne

Gdy użytkownik włączył jakiekolwiek poświadczenie bez hasła, logowania usługi Azure AD przestaną używać login_hint do przyspieszenia użytkownika do federacyjnej lokalizacji logowania. Ta logika zapobiega kierowaniu do AD FS użytkowników w dzierżawie hybrydowej w celu weryfikacji logowania bez konieczności podejmowania przez użytkownika dodatkowego kroku kliknięcia "Użyj hasła zamiast".

### <a name="azure-mfa-server"></a>Serwer usługi Azure MFA

Użytkownicy końcowi korzystający z usług MFA za pomocą lokalnego serwera usługi Azure MFA w organizacji mogą nadal tworzyć i używać jednego poświadczenia logowania jednokrotnego bez hasła. Jeśli użytkownik próbuje uaktualnić wiele instalacji (5 +) Microsoft Authenticator przy użyciu poświadczenia, ta zmiana może spowodować wystąpienie błędu.  

### <a name="device-registration"></a>Rejestracja urządzenia

Jedno z wymagań wstępnych dotyczących tworzenia nowego silnego poświadczenia polega na tym, że urządzenie, na którym zainstalowano aplikację Microsoft Authenticator, musi być również zarejestrowane w ramach dzierżawy usługi Azure AD dla pojedynczego użytkownika. Ze względu na bieżące ograniczenia rejestracji urządzeń urządzenie może być rejestrowane tylko w jednej dzierżawie. Ten limit oznacza, że tylko jedno konto służbowe w aplikacji Microsoft Authenticator może być włączone do logowania za pomocą telefonu.

> [!NOTE]
> Rejestracja urządzeń nie jest taka sama jak w przypadku zarządzania urządzeniami lub "MDM". W katalogu usługi Azure AD kojarzy tylko identyfikator urządzenia i identyfikator użytkownika.  

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o metodach uwierzytelniania i bezhasła usługi Azure AD, zobacz następujące artykuły:

* [Dowiedz się, jak działa uwierzytelnianie bezhasłem](concept-authentication-passwordless.md)
* [Informacje o rejestracji urządzeń](../devices/overview.md#getting-devices-in-azure-ad)
* [Dowiedz się więcej o usłudze Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
