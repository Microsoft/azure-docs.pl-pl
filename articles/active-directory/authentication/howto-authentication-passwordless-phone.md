---
title: Logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator — Azure Active Directory
description: Włączanie logowania bezhasła do usługi Azure AD przy użyciu aplikacji Microsoft Authenticator (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3412938cfc2ad3fbec293fd33f64e114e14e6f7e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81450976"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Włącz logowanie bez hasła przy użyciu aplikacji Microsoft Authenticator (wersja zapoznawcza)

Aplikacja Microsoft Authenticator może służyć do logowania się do dowolnego konta usługi Azure AD bez użycia hasła. Podobnie jak w przypadku technologii [Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator używa uwierzytelniania opartego na kluczach, aby umożliwić poświadczenie użytkownika powiązanego z urządzeniem i używa biometrycznych lub PIN. Tej metody uwierzytelniania można używać na dowolnej platformie urządzeń, w tym dla urządzeń przenośnych, z dowolną aplikacją lub witryną sieci Web, która integruje się z bibliotekami uwierzytelniania firmy Microsoft. 

![Przykład logowania do przeglądarki z prośbą do użytkownika o zatwierdzenie logowania](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Zamiast wyświetlać monit o hasło po wprowadzeniu nazwy użytkownika, osoba, która włączyła logowanie za pomocą telefonu z aplikacji Microsoft Authenticator zobaczy komunikat informujący o naciśnięciu numeru w swojej aplikacji. W aplikacji użytkownik musi dopasować liczbę, wybrać pozycję Zatwierdź, a następnie podać numer PIN lub biometryczny, a następnie zostanie wykonane uwierzytelnianie.

> [!NOTE]
> Ta funkcja znajduje się w aplikacji Microsoft Authenticator od marca 2017, dlatego istnieje możliwość, że gdy zasady są włączone dla katalogu, użytkownicy mogą natychmiast napotkać ten przepływ, a komunikat o błędzie, jeśli nie został włączony przez zasady. Weź pod uwagę i przygotuj użytkowników do tej zmiany.

## <a name="prerequisites"></a>Wymagania wstępne

- Multi-Factor Authentication platformy Azure, z powiadomieniami wypychanymi, które są dozwolone jako metoda weryfikacji 
- Najnowsza wersja Microsoft Authenticator zainstalowana na urządzeniach z systemem iOS 8,0 lub nowszym albo systemem Android 6,0 lub nowszym.

> [!NOTE]
> W przypadku włączenia wcześniejszej wersji zapoznawczej logowania bezhasło aplikacji Microsoft Authenticator przy użyciu programu Azure AD PowerShell, została ona włączona dla całego katalogu. Jeśli włączysz tę nową metodę, spowoduje to zastąpenie zasad programu PowerShell. Zalecamy włączenie dla wszystkich użytkowników w dzierżawie za pomocą nowych metod uwierzytelniania. w przeciwnym razie użytkownicy, którzy nie są w nowych zasadach, nie będą już mogli logować się passwordlessly. 

## <a name="enable-passwordless-authentication-methods"></a>Włącz metody uwierzytelniania bezhasła

### <a name="enable-the-combined-registration-experience"></a>Włącz połączone środowisko rejestracji

Funkcje rejestracji dla metod uwierzytelniania bezhaseł polegają na funkcji rejestracji połączonej. Wykonaj kroki opisane w artykule [Włączanie rejestracji informacji o zabezpieczeniach połączonych](howto-registration-mfa-sspr-combined.md), aby włączyć rejestrację łączną.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Włącz metodę uwierzytelniania logowania jednokrotnego przy użyciu hasła

1. Zaloguj się do [Azure Portal](https://portal.azure.com)
1. Wyszukaj i wybierz pozycję *Azure Active Directory*. Wybieranie **Security** >  > **metod uwierzytelniania**zabezpieczeń**zasady metod uwierzytelniania (wersja zapoznawcza)**
1. W obszarze **Logowanie za pomocą telefonu bezhasło**wybierz następujące opcje
   1. **Włącz** — tak lub nie
   1. **Cel** — wszyscy użytkownicy lub wybrani użytkownicy
1. **Zapisz** , aby ustawić nowe zasady

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Rejestracja użytkownika i zarządzanie aplikacją Microsoft Authenticator

1. Przejdź do[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. Zaloguj się, jeśli jeszcze nie
1. Dodaj aplikację Authenticator, klikając pozycję **Dodaj metodę**, wybierając pozycję **aplikacja uwierzytelniania**i klikając przycisk **Dodaj** .
1. Postępuj zgodnie z instrukcjami, aby zainstalować i skonfigurować aplikację Microsoft Authenticator na urządzeniu
1. Kliknij pozycję **gotowe** , aby zakończyć przepływ konfiguracji aplikacji MFA uwierzytelniania. 
1. W **Microsoft Authenticator**wybierz pozycję **Włącz logowanie** za pomocą telefonu z menu rozwijanego konto
1. Postępuj zgodnie z instrukcjami w aplikacji, aby zakończyć rejestrację w celu logowania za pomocą telefonu bezhasłem. 

Organizacje mogą wskazywać, że użytkownicy będą [logować się za pomocą telefonu, a nie hasła](../user-help/microsoft-authenticator-app-phone-signin-faq.md) do dalszej pomocy w konfigurowaniu w aplikacji Microsoft Authenticator i włączeniu logowania do telefonu. Aby można było zastosować te ustawienia, może być konieczne wylogowanie się i zalogowanie się do dzierżawy. 

## <a name="sign-in-with-passwordless-credential"></a>Zaloguj się przy użyciu poświadczeń bez hasła

W publicznej wersji zapoznawczej nie ma możliwości wymuszania użytkownikom tworzenia lub używania tego nowego poświadczenia. Użytkownik będzie napotykał logowanie bezhasło, gdy administrator włączył swoją dzierżawę **, a** użytkownik zaktualizował Microsoft Authenticator aplikację, aby włączyć logowanie za pomocą telefonu.

Po wpisaniu nazwy użytkownika w sieci Web i wybraniu **pozycji dalej**użytkownicy będą wyświetlani z numerem i są Microsoft Authenticator monitowani o wybranie odpowiedniej liczby do uwierzytelnienia, a nie przy użyciu hasła. 

![Przykład logowania w przeglądarce przy użyciu aplikacji Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Znane problemy

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>Użytkownik nie jest włączony przez zasady, ale nadal ma metodę logowania za pomocą telefonu bez hasła w Microsoft Authenticator

Istnieje możliwość, że użytkownik ma w pewnym momencie utworzyć poświadczenia logowania za pomocą telefonu bez hasła w bieżącej aplikacji Microsoft Authenticator lub na wcześniejszym urządzeniu. Gdy administrator włączy zasady metody uwierzytelniania dla logowania za pomocą telefonu bez hasła, każdy użytkownik z zarejestrowanym poświadczeniem rozpocznie korzystanie z nowego monitu logowania, niezależnie od tego, czy włączono do używania zasad, czy nie. Jeśli użytkownik nie zezwolił na korzystanie z poświadczeń za pomocą zasad, zobaczy błąd po zakończeniu przepływu uwierzytelniania. 

Administrator może wybrać opcję, aby umożliwić użytkownikowi korzystanie z logowania jednokrotnego przy użyciu hasła lub użytkownik musi usunąć metodę. Jeśli użytkownik nie ma już zarejestrowanego urządzenia, może przejść do [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) obszaru i usunąć go. Jeśli nadal korzystają z uwierzytelniania wieloskładnikowego, można wybrać opcję **Wyłącz logowanie** za pomocą telefonu z poziomu Microsoft Authenticator.  

### <a name="ad-fs-integration"></a>Integracja AD FS

Jeśli użytkownik włączył Microsoft Authenticator poświadczenie bezhasło, uwierzytelnianie dla tego użytkownika zawsze będzie domyślnie wysyłać powiadomienie o zatwierdzeniu. Ta logika uniemożliwia kierowanie do usług AD FS użytkowników w dzierżawie hybrydowej w celu weryfikacji logowania bez podejmowania dodatkowych kroków przez użytkownika w celu kliknięcia przycisku "Użyj hasła zamiast". Ten proces spowoduje również ominięcie wszelkich lokalnych zasad dostępu warunkowego i przepływów uwierzytelniania przekazywanego. 

Jeśli użytkownik nie ma oczekującej weryfikacji logowania jednokrotnego przy użyciu hasła i próbuje zalogować się ponownie, może zostać przełączony do usług AD FS w celu wprowadzenia hasła.  

### <a name="azure-mfa-server"></a>Serwer usługi Azure MFA

Użytkownicy końcowi korzystający z usług MFA za pomocą lokalnego serwera usługi Azure MFA w organizacji mogą nadal tworzyć i używać jednego poświadczenia logowania jednokrotnego bez hasła. Jeśli użytkownik próbuje uaktualnić wiele instalacji (5 +) Microsoft Authenticator przy użyciu poświadczenia, ta zmiana może spowodować wystąpienie błędu.  

### <a name="device-registration"></a>Rejestracja urządzenia

Jedno z wymagań wstępnych dotyczących tworzenia nowego silnego poświadczenia polega na tym, że urządzenie, na którym zainstalowano aplikację Microsoft Authenticator, musi być również zarejestrowane w ramach dzierżawy usługi Azure AD dla pojedynczego użytkownika. Ze względu na bieżące ograniczenia rejestracji urządzeń urządzenie może być rejestrowane tylko w jednej dzierżawie. Ten limit oznacza, że tylko jedno konto służbowe w aplikacji Microsoft Authenticator może być włączone do logowania za pomocą telefonu.

### <a name="intune-mobile-application-management"></a>Zarządzanie aplikacjami mobilnymi w usłudze Intune 

Użytkownicy końcowi, którzy podlegają zasadom, które wymagają zarządzania aplikacjami mobilnymi (MAM), nie mogą rejestrować poświadczeń bezhaseł w aplikacji Microsoft Authenticator. 

> [!NOTE]
> Rejestracja urządzeń nie jest taka sama jak w przypadku zarządzania urządzeniami lub "MDM". W katalogu usługi Azure AD kojarzy tylko identyfikator urządzenia i identyfikator użytkownika.  

## <a name="next-steps"></a>Następne kroki

[Co to jest logowanie bez hasła?](concept-authentication-passwordless.md)

[Informacje o rejestracji urządzeń](../devices/overview.md#getting-devices-in-azure-ad)

[Dowiedz się więcej o usłudze Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
