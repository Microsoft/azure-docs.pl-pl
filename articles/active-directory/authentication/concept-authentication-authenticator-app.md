---
title: Microsoft Authenticator uwierzytelniania aplikacji — Azure Active Directory
description: Dowiedz się więcej na temat Microsoft Authenticator aplikacji Azure Active Directory w celu ulepszania i zabezpieczania zdarzeń logowania
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3175b1292a7e69506b9193d1182e184e257ebda3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530498"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Metody uwierzytelniania w Azure Active Directory — Microsoft Authenticator aplikacji

Aplikacja Microsoft Authenticator zapewnia dodatkowy poziom zabezpieczeń konta służbowego usługi Azure AD lub konta konto Microsoft i jest dostępna dla systemów [Android](https://go.microsoft.com/fwlink/?linkid=866594) [i iOS.](https://go.microsoft.com/fwlink/?linkid=866594) Dzięki aplikacji Microsoft Authenticator użytkownicy mogą uwierzytelniać się w sposób bez hasła podczas logowania lub jako dodatkowa opcja weryfikacji podczas samoobsługowego resetowania hasła (SSPR) lub zdarzeń usługi Azure AD Multi-Factor Authentication.

Użytkownicy mogą otrzymać za pośrednictwem aplikacji mobilnej powiadomienie o zatwierdzeniu lub odmówieniu albo użyć aplikacji Authenticator do wygenerowania kodu weryfikacyjnego OAUTH, który można wprowadzić w interfejsie logowania. Jeśli włączysz zarówno powiadomienie, jak i kod weryfikacyjny, użytkownicy, którzy zarejestrują aplikację Authenticator, będą w stanie zweryfikować swoją tożsamość przy użyciu jednej z metod.

Aby użyć aplikacji Authenticator w wierszu polecenia logowania zamiast kombinacji nazwy użytkownika i [hasła,](howto-authentication-passwordless-phone.md)zobacz Włączanie logowania bez hasła przy użyciu Microsoft Authenticator aplikacji .

> [!NOTE]
> Użytkownicy nie mają możliwości zarejestrowania aplikacji mobilnej po włączeniu funkcji SSPR. Zamiast tego użytkownicy mogą zarejestrować swoją aplikację mobilną pod adresem lub w ramach połączonej rejestracji informacji [https://aka.ms/mfasetup](https://aka.ms/mfasetup) zabezpieczających pod adresem [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

## <a name="passwordless-sign-in"></a>Logowanie bez hasła

Zamiast wyświetlać monit o hasło po wprowadzeniu nazwy użytkownika, użytkownik, który włączył logowanie za pomocą telefonu z aplikacji Microsoft Authenticator, zobaczy komunikat o naciśnięciu numeru w aplikacji. Po wybraniu poprawnej liczby proces logowania zostanie ukończony.

![Przykład logowania za pomocą przeglądarki z prośbą o zatwierdzenie logowania przez użytkownika](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Ta metoda uwierzytelniania zapewnia wysoki poziom zabezpieczeń i nie wymaga podania hasła przez użytkownika podczas logowania. 

Aby rozpocząć logowanie bez hasła, zobacz Włączanie logowania bez hasła przy [użyciu Microsoft Authenticator aplikacji](howto-authentication-passwordless-phone.md).

## <a name="notification-through-mobile-app"></a>Powiadomienie za pośrednictwem aplikacji mobilnej

Aplikacja Authenticator może pomóc zapobiec nieautoryzowanemu dostępowi do kont i zatrzymać fałszywe transakcje, wypychając powiadomienie na smartfon lub tablet. Użytkownicy wyświetlą powiadomienie i jeśli jest ono wiarygodne, wybierz pozycję **Weryfikuj**. W przeciwnym razie mogą wybrać pozycję **Odmów**.

![Zrzut ekranu przedstawiający przykładowy monit przeglądarki internetowej o powiadomienie aplikacji Authenticator o ukończeniu procesu logowania](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Jeśli Twoja organizacja ma personel pracujący lub  podróżując do Chin, metoda powiadomień za pośrednictwem aplikacji mobilnej na urządzeniach z systemem Android nie działa w tym kraju/regionie, ponieważ usługi Google Play (w tym powiadomienia wypychane) są blokowane w regionie. Jednak powiadomienia systemu iOS działają. W przypadku urządzeń z systemem Android dla tych użytkowników powinny zostać udostępnione alternatywne metody uwierzytelniania.

## <a name="verification-code-from-mobile-app"></a>Kod weryfikacyjny z aplikacji mobilnej

Aplikacja Authenticator może służyć jako token oprogramowania do generowania kodu weryfikacyjnego OATH. Po wprowadzeniu nazwy użytkownika i hasła wprowadź kod dostarczony przez aplikację Authenticator w interfejsie logowania. Kod weryfikacyjny stanowi drugą formę uwierzytelniania.

Użytkownicy mogą mieć kombinację maksymalnie pięciu tokenów sprzętowych OATH lub aplikacji wystawcy uwierzytelnień, takich jak Microsoft Authenticator aplikacji, skonfigurowanych do użycia w dowolnym momencie.

> [!WARNING]
> Aby zapewnić najwyższy poziom zabezpieczeń samoobsługowego resetowania hasła, gdy do zresetowania jest wymagana tylko jedna metoda, kod weryfikacyjny jest jedyną opcją dostępną dla użytkowników.
>
> Jeśli są wymagane dwie metody, użytkownicy mogą zresetować się przy użyciu powiadomienia lub kodu weryfikacyjnego oprócz innych włączonych metod.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć logowanie bez hasła, zobacz Włączanie logowania bez hasła przy [użyciu Microsoft Authenticator aplikacji](howto-authentication-passwordless-phone.md).

Dowiedz się więcej na temat konfigurowania metod uwierzytelniania przy [użyciu Microsoft Graph API REST.](/graph/api/resources/authenticationmethods-overview)
