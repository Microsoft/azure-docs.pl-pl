---
title: Metoda uwierzytelniania aplikacji Microsoft Authenticator — Azure Active Directory
description: Dowiedz się więcej o korzystaniu z aplikacji Microsoft Authenticator w usłudze Azure Active Directory w celu ulepszania i zabezpieczania zdarzeń związanych z logowaniem
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7309d9d32eba5cae350bbe8a1df325dfb2ab0c8
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744367"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Metody uwierzytelniania w aplikacji Azure Active Directory Microsoft Authenticator

Aplikacja Microsoft Authenticator zapewnia dodatkowy poziom zabezpieczeń konta służbowego lub szkolnego usługi Azure AD lub konto Microsoft i jest dostępny dla systemów [Android](https://go.microsoft.com/fwlink/?linkid=866594) i [iOS](https://go.microsoft.com/fwlink/?linkid=866594). Za pomocą aplikacji Microsoft Authenticator użytkownicy mogą uwierzytelniać się w sposób niezależny od hasła podczas logowania lub jako dodatkowa opcja weryfikacji podczas samoobsługowego resetowania hasła (SSPR) lub zdarzeń Multi-Factor Authentication usługi Azure AD.

Użytkownicy mogą otrzymać powiadomienie za pomocą aplikacji mobilnej do zatwierdzenia lub odmowy lub użyć aplikacji Authenticator do wygenerowania kodu weryfikacyjnego OATH, który można wprowadzić w interfejsie logowania. Jeśli włączysz powiadomienie i kod weryfikacyjny, użytkownicy rejestrujący aplikację uwierzytelniania mogą użyć dowolnej metody do zweryfikowania swojej tożsamości.

Aby użyć aplikacji uwierzytelniania przy użyciu monitu logowania zamiast kombinacji nazwy użytkownika i hasła, zobacz [Włączanie logowania bez hasła przy użyciu aplikacji Microsoft Authenticator (wersja zapoznawcza)](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Użytkownicy nie mają możliwości zarejestrowania aplikacji mobilnej, gdy włączą SSPR. Zamiast tego użytkownicy mogą rejestrować swoją aplikację mobilną w programie [https://aka.ms/mfasetup](https://aka.ms/mfasetup) lub w ramach rejestracji informacji o zabezpieczeniach w usłudze [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

## <a name="passwordless-sign-in"></a>Logowanie bezhasło

Zamiast wyświetlać monit o hasło po wprowadzeniu nazwy użytkownika, użytkownik, który włączył logowanie za pomocą telefonu z aplikacji Microsoft Authenticator zobaczy komunikat, aby wybrać liczbę w swojej aplikacji. Po wybraniu poprawnego numeru proces logowania zostanie zakończony.

![Przykład logowania do przeglądarki z prośbą do użytkownika o zatwierdzenie logowania](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Ta metoda uwierzytelniania zapewnia wysoki poziom zabezpieczeń i eliminuje konieczność podania hasła podczas logowania. Logowanie bezhasła przy użyciu aplikacji Microsoft Authenticator jest obecnie dostępne w wersji zapoznawczej.

Aby rozpocząć logowanie bez hasła, zobacz [Włączanie logowania bez hasła przy użyciu aplikacji Microsoft Authenticator](howto-authentication-passwordless-phone.md).

## <a name="notification-through-mobile-app"></a>Powiadomienie za poorednictwem aplikacji mobilnej

Aplikacja Authenticator może pomóc zapobiec nieautoryzowanemu dostępowi do kont i zatrzymywać fałszywe transakcje przez wypychanie powiadomienia do telefonu lub tabletu. Użytkownicy wyświetlają powiadomienie i jeśli są wiarygodne, wybierz pozycję **Weryfikuj**. W przeciwnym razie można wybrać opcję **Odmów**.

![Zrzut ekranu przedstawiający przykładowy monit przeglądarki sieci Web dla powiadomienia aplikacji uwierzytelniania w celu ukończenia procesu logowania](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Jeśli Twoja organizacja ma pracę pracowników lub podróżujących z Chin, *powiadomienie za pomocą metody aplikacji mobilnej* na urządzeniach z systemem Android nie działa w tym kraju/regionie, ponieważ usługi Google Play (w tym powiadomienia wypychane) są blokowane w regionie. Jednak powiadomienia dla systemu iOS działają. W przypadku urządzeń z systemem Android należy udostępnić alternatywną metodę uwierzytelniania dla tych użytkowników.

## <a name="verification-code-from-mobile-app"></a>Kod weryfikacyjny z aplikacji mobilnej

Aplikacja Authenticator może służyć jako token oprogramowania do wygenerowania kodu weryfikacyjnego OATH. Po wprowadzeniu nazwy użytkownika i hasła wprowadzasz kod dostarczony przez aplikację Authenticator do interfejsu logowania. Kod weryfikacyjny stanowi drugą formę uwierzytelniania.

Użytkownicy mogą mieć kombinację maksymalnie pięciu tokenów sprzętowych OATH lub aplikacji uwierzytelniania, takich jak Microsoft Authenticator aplikacji skonfigurowanych do użycia w dowolnym momencie.

> [!WARNING]
> Aby zapewnić najwyższy poziom zabezpieczeń dla samoobsługowego resetowania hasła, gdy do resetowania jest wymagana tylko jedna metoda, kod weryfikacyjny jest jedyną opcją dostępną dla użytkowników.
>
> Gdy wymagane są dwie metody, użytkownicy mogą resetować przy użyciu powiadomienia lub kodu weryfikacyjnego oprócz innych włączonych metod.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć logowanie bez hasła, zobacz [Włączanie logowania bez hasła przy użyciu aplikacji Microsoft Authenticator](howto-authentication-passwordless-phone.md).

Dowiedz się więcej na temat konfigurowania metod uwierzytelniania przy użyciu [interfejsu API REST Microsoft Graph w wersji beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).
