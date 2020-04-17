---
title: Logowanie bez hasła za pomocą aplikacji Microsoft Authenticator — Usługa Azure Active Directory
description: Włącz logowanie bez hasła do usługi Azure AD przy użyciu aplikacji Microsoft Authenticator (wersja zapoznawcza)
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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450976"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Włącz logowanie bez hasła za pomocą aplikacji Microsoft Authenticator (wersja zapoznawcza)

Aplikacja Microsoft Authenticator może służyć do logowania się do dowolnego konta usługi Azure AD bez użycia hasła. Podobnie jak w przypadku technologii [Windows Hello dla firm,](/windows/security/identity-protection/hello-for-business/hello-identity-verification)program Microsoft Authenticator używa uwierzytelniania opartego na kluczach, aby włączyć poświadczenia użytkownika powiązane z urządzeniem i używane są biometryczne lub PIN. Tej metody uwierzytelniania można używać na dowolnej platformie urządzenia, w tym na urządzeniach mobilnych, oraz w dowolnej aplikacji lub witrynie sieci Web zintegrowanej z bibliotekami uwierzytelniania firmy Microsoft. 

![Przykład logowania do przeglądarki z prośbą o zatwierdzenie logowania przez użytkownika](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Zamiast monitu o podanie hasła po wprowadzeniu nazwy użytkownika, osoba, która włączyła logowanie telefonu z aplikacji Microsoft Authenticator, zobaczy komunikat z prośbą o dotknięcie numeru w aplikacji. W aplikacji użytkownik musi dopasować numer, wybierz pozycję Zatwierdź, a następnie podaj swój numer PIN lub dane biometryczne, a następnie zostanie zakończone uwierzytelnienie.

> [!NOTE]
> Ta funkcja jest w aplikacji Microsoft Authenticator od marca 2017 r., więc istnieje możliwość, że gdy zasady są włączone dla katalogu, użytkownicy mogą natychmiast napotkać ten przepływ i wyświetlić komunikat o błędzie, jeśli nie zostały włączone przez zasady. Należy pamiętać i przygotować użytkowników do tej zmiany.

## <a name="prerequisites"></a>Wymagania wstępne

- Uwierzytelnianie wieloskładnikowe platformy Azure z powiadomieniami wypychanych dozwolonymi jako metodą weryfikacji 
- Najnowsza wersja programu Microsoft Authenticator zainstalowana na urządzeniach z systemem iOS 8.0 lub wyższym lub Android 6.0 lub wyższym.

> [!NOTE]
> Jeśli włączono poprzednią aplikację Microsoft Authenticator bez hasła w wersji zapoznawczej przy użyciu programu Azure AD PowerShell, została włączona dla całego katalogu. Jeśli włączysz przy użyciu tej nowej metody, będzie supercede zasad programu PowerShell. Zaleca się włączenie dla wszystkich użytkowników w dzierżawie za pośrednictwem nowych metod uwierzytelniania, w przeciwnym razie użytkownicy nie w nowych zasadach nie będzie już można zalogować się bez hasła. 

## <a name="enable-passwordless-authentication-methods"></a>Włączanie metod uwierzytelniania bez hasła

### <a name="enable-the-combined-registration-experience"></a>Włącz połączone środowisko rejestracji

Funkcje rejestracji metod uwierzytelniania bez hasła opierają się na połączonej funkcji rejestracji. Postępuj zgodnie z instrukcjami zawartymi w artykule [Włączanie połączonej rejestracji informacji o zabezpieczeniach](howto-registration-mfa-sspr-combined.md), aby włączyć rejestrację połączoną.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Włączanie metod uwierzytelniania logowania do telefonu bez hasła

1. Logowanie się do [witryny Azure portal](https://portal.azure.com)
1. Wyszukaj i wybierz pozycję *Azure Active Directory*. Wybieranie > zasad metody**uwierzytelniania** **zabezpieczeń** > **(Wersja zapoznawcza)**
1. W obszarze **Logowanie się do telefonu bez hasła**wybierz następujące opcje:
   1. **Włącz** — Tak lub Nie
   1. **Cel** — wszyscy użytkownicy lub wybierz użytkowników
1. **Zapisz,** aby ustawić nowe zasady

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Rejestracja użytkownika i zarządzanie aplikacją Microsoft Authenticator

1. Przejdź do[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)
1. Zaloguj się, jeśli jeszcze nie
1. Dodawanie aplikacji uwierzytelniającej przez **kliknięcie**przycisku Dodaj metodę , wybranie **opcji Authenticator**i kliknięcie **przycisku Dodaj**
1. Postępuj zgodnie z instrukcjami, aby zainstalować i skonfigurować aplikację Microsoft Authenticator na urządzeniu
1. Kliknij **przycisk Gotowe,** aby zakończyć przepływ konfiguracji aplikacji uwierzytelnianie uwierzytelnienia. 
1. W **menu**rozwijanym Konto wybierz pozycję **Włącz logowanie telefonu**
1. Postępuj zgodnie z instrukcjami w aplikacji, aby zakończyć rejestrację bez hasła logowania się na telefon. 

Organizacje mogą skierować swoich użytkowników do artykułu [Zaloguj się za pomocą telefonu, a nie hasła,](../user-help/microsoft-authenticator-app-phone-signin-faq.md) aby uzyskać dalszą pomoc podczas konfigurowania w aplikacji Microsoft Authenticator i włączania logowania telefonu. Aby zastosować te ustawienia, może być konieczne wylogowanie się i zalogowanie się z powrotem do dzierżawy. 

## <a name="sign-in-with-passwordless-credential"></a>Logowanie się przy użyciu poświadczeń bez hasła

W przypadku publicznej wersji zapoznawczej nie ma możliwości wymuszenia na użytkownikach tworzenia lub używania tego nowego poświadczenia. Użytkownik napotka logowanie bez hasła tylko wtedy, gdy administrator włączy **dzierżawę, a** użytkownik zaktualizował swoją aplikację Microsoft Authenticator, aby włączyć logowanie się do telefonu.

Po wpisaniu nazwy użytkownika w sieci Web i **wybraniu opcji Dalej**użytkownicy otrzymują numer i są monitowani w aplikacji Microsoft Authenticator o wybranie odpowiedniego numeru do uwierzytelnienia zamiast używania hasła. 

![Przykład logowania do przeglądarki przy użyciu aplikacji Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Znane problemy

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>Użytkownik nie jest włączony przez zasady, ale nadal ma bez hasła metodę logowania telefonu w programie Microsoft Authenticator

Jest możliwe, że użytkownik w pewnym momencie utworzył bez hasła poświadczenia logowania telefonu w bieżącej aplikacji Microsoft Authenticator lub na wcześniejszym urządzeniu. Gdy administrator włączy zasady metody uwierzytelniania dla logowania się do telefonu bez hasła, każdy użytkownik z zarejestrowanymi poświadczeniami zacznie doświadczać nowego monitu logowania, niezależnie od tego, czy włączono im korzystanie z zasad, czy nie. Jeśli użytkownik nie może używać poświadczeń według zasad, zobaczy błąd po zakończeniu przepływu uwierzytelniania. 

Administrator może włączyć użytkownika do korzystania z logowania telefonu bez hasła lub użytkownik musi usunąć metodę. Jeśli użytkownik nie ma już zarejestrowanego urządzenia, może przejść do [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) niego i usunąć je. Jeśli nadal używają funkcji Authenticator dla usługi MFA, mogą wybrać **opcję Wyłącz logowanie telefonu** z poziomu programu Microsoft Authenticator.  

### <a name="ad-fs-integration"></a>Integracja usług AD FS

Gdy użytkownik włączył poświadczenia bez hasła microsoft authenticator, uwierzytelnianie dla tego użytkownika zawsze domyślnie wysyła powiadomienie do zatwierdzenia. Ta logika uniemożliwia użytkownikom w dzierżawie hybrydowej kierowanie do usługi ADFS w celu weryfikacji logowania bez konieczności robienia przez użytkownika dodatkowego kroku w celu kliknięcia przycisku "Użyj hasła". Ten proces będzie również pomijać wszelkie lokalne zasady dostępu warunkowego i przepływy uwierzytelniania przekazywanego. 

Jeśli użytkownik ma weryfikację logowania bez hasła bez hasła i próbuje zalogować się ponownie, użytkownik może zostać przewieziony do usługi ADFS, aby wprowadzić hasło.  

### <a name="azure-mfa-server"></a>Serwer usługi Azure MFA

Użytkownicy końcowi, którzy są włączeni dla usługi MFA za pośrednictwem lokalnego serwera usługi Azure MFA w organizacji, mogą nadal tworzyć i używać pojedynczych poświadczeń logowania telefonu bez hasła. Jeśli użytkownik podejmie próbę uaktualnienia wielu instalacji (5+) programu Microsoft Authenticator przy użyciu poświadczeń, ta zmiana może spowodować błąd.  

### <a name="device-registration"></a>Rejestracja urządzenia

Jednym z wymagań wstępnych do utworzenia tego nowego silnego poświadczenia jest to, że urządzenie, na którym jest zainstalowana aplikacja Microsoft Authenticator, musi być również zarejestrowane w dzierżawie usługi Azure AD dla pojedynczego użytkownika. Ze względu na bieżące ograniczenia rejestracji urządzenia urządzenie może być zarejestrowane tylko w jednej dzierżawie. Ten limit oznacza, że tylko jedno konto służbowe lub szkolne w aplikacji Microsoft Authenticator można włączyć do logowania telefonu.

### <a name="intune-mobile-application-management"></a>Zarządzanie aplikacjami mobilnymi w usłudze Intune 

Użytkownicy końcowi, którzy podlegają zasadom, które wymagają zarządzania aplikacjami mobilnymi (MAM), nie mogą zarejestrować poświadczeń bez hasła w aplikacji Microsoft Authenticator. 

> [!NOTE]
> Rejestracja urządzenia nie jest taka sama jak zarządzanie urządzeniami lub "MDM". W katalogu usługi Azure AD jest skojarzony tylko identyfikator urządzenia i identyfikator użytkownika.  

## <a name="next-steps"></a>Następne kroki

[Co to jest logowanie bez hasła?](concept-authentication-passwordless.md)

[Dowiedz się więcej o rejestracji urządzenia](../devices/overview.md#getting-devices-in-azure-ad)

[Dowiedz się więcej o uwierzytelnianiu wieloskładnikowym platformy Azure](../authentication/howto-mfa-getstarted.md)
