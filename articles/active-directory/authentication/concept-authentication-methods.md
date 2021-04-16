---
title: Metody i funkcje uwierzytelniania — Azure Active Directory
description: Dowiedz się więcej o różnych metodach uwierzytelniania i funkcjach dostępnych w Azure Active Directory, aby ułatwić ulepszanie i zabezpieczanie zdarzeń logowania
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: b4d69157f4544daad962cca15e53802e7b912399
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530437"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Jakie metody uwierzytelniania i weryfikacji są dostępne w usłudze Azure Active Directory?

W ramach logowania dla kont w usłudze Azure Active Directory (Azure AD) użytkownik może się uwierzytelnić na różne sposoby. Nazwa użytkownika i hasło to najczęściej spotykany sposób, w jaki użytkownik poda poświadczenia w przeszłości. Dzięki nowoczesnym uwierzytelnianiem i zabezpieczeniu w usłudze Azure AD to podstawowe hasło powinno zostać uzupełnione lub zastąpione bezpieczniejszymi metodami uwierzytelniania.

![Tabela mocnych stron i preferowanych metod uwierzytelniania w usłudze Azure AD](media/concept-authentication-methods/authentication-methods.png)

Metody uwierzytelniania bez hasła, takie jak Windows Hello, klucze zabezpieczeń FIDO2 i aplikacja Microsoft Authenticator, zapewniają najbezpieczniejsze zdarzenia logowania.

Usługa Azure AD Multi-Factor Authentication (MFA) dodaje dodatkowe zabezpieczenia tylko przy użyciu hasła podczas logowania użytkownika. Użytkownik może zostać poproszony o dodatkowe formy uwierzytelniania, takie jak odpowiadanie na powiadomienie wypychane, wprowadzanie kodu z tokenu programowego lub sprzętowego albo odpowiadanie na wiadomość SMS lub połączenie telefoniczne.

Aby uprościć środowisko użytkownika i zarejestrować się w celu uwierzytelniania wieloskładnikowego i samoobsługowego resetowania hasła (SSPR), zalecamy włączenie połączonej rejestracji [informacji zabezpieczających.](howto-registration-mfa-sspr-combined.md) W celu zapewnienia odporności zalecamy wymaganie od użytkowników zarejestrowania wielu metod uwierzytelniania. Jeśli jedna z metod nie jest dostępna dla użytkownika podczas logowania lub korzystania z usługi SSPR, może wybrać uwierzytelnianie za pomocą innej metody. Aby uzyskać więcej informacji, zobacz Tworzenie strategii zarządzania kontrolą dostępu odporną [na błędy w usłudze Azure AD.](concept-resilient-controls.md)

Oto wideo, [które](https://www.youtube.com/watch?v=LB2yj4HSptc&feature=youtu.be) pomoże Ci wybrać najlepszą metodę uwierzytelniania, aby zapewnić bezpieczeństwo organizacji.

## <a name="authentication-method-strength-and-security"></a>Siły i bezpieczeństwo metody uwierzytelniania

Podczas wdrażania w organizacji funkcji, takich jak Usługa Azure AD Multi-Factor Authentication, zapoznaj się z dostępnymi metodami uwierzytelniania. Wybierz metody, które spełniają lub przekraczają wymagania dotyczące zabezpieczeń, użyteczności i dostępności. Tam, gdzie to możliwe, należy używać metod uwierzytelniania z najwyższym poziomem zabezpieczeń.

W poniższej tabeli przedstawiono zagadnienia dotyczące zabezpieczeń dostępnych metod uwierzytelniania. Dostępność oznacza, że użytkownik może korzystać z metody uwierzytelniania, a nie dostępności usługi w usłudze Azure AD:

| Metoda uwierzytelniania          | Zabezpieczenia | Łatwość obsługi | Dostępność |
|--------------------------------|:--------:|:---------:|:------------:|
| Windows Hello for Business     | Wys.     | Wys.      | Wys.         |
| Aplikacja Microsoft Authenticator    | Wys.     | Wys.      | Wys.         |
| Klucz zabezpieczeń FIDO2             | Wys.     | Wys.      | Wys.         |
| Tokeny sprzętowe OATH (wersja zapoznawcza) | Średnia   | Średnia    | Wys.         |
| Tokeny oprogramowania OATH           | Średnia   | Średnia    | Wys.         |
| SMS                            | Śred.   | Wys.      | Śred.       |
| Połączenia głosowe                          | Średnia   | Średnia    | Średnia       |
| Hasło                       | Nis.      | Wys.      | Wys.         |

Aby uzyskać najnowsze informacje na temat zabezpieczeń, zapoznaj się z naszymi wpisami w blogu:

- [Na czas uwierzytelniania można zawieszać się na transportach telefonicznych](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)
- [Luki w zabezpieczeniach uwierzytelniania i wektory ataków](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)

> [!TIP]
> Aby zapewnić elastyczność i użyteczność, zalecamy użycie Microsoft Authenticator aplikacji. Ta metoda uwierzytelniania zapewnia najlepsze środowisko użytkownika i wiele trybów, takich jak bez hasła, powiadomienia wypychane usługi MFA i kody OATH.

## <a name="how-each-authentication-method-works"></a>Jak działa każda metoda uwierzytelniania

Niektóre metody uwierzytelniania mogą służyć jako podstawowy czynnik podczas logowania do aplikacji lub urządzenia, takie jak użycie klucza zabezpieczeń FIDO2 lub hasła. Inne metody uwierzytelniania są dostępne tylko jako czynnik pomocniczy w przypadku korzystania z usługi Azure AD Multi-Factor Authentication lub funkcji SSPR.

W poniższej tabeli przedstawiono, kiedy można użyć metody uwierzytelniania podczas zdarzenia logowania:

| Metoda                         | Uwierzytelnianie podstawowe | Uwierzytelnianie pomocnicze  |
|--------------------------------|:----------------------:|:-------------------------:|
| Windows Hello for Business     | Yes                    | Uwierzytelnianie wieloskładnikowe                       |
| Aplikacja Microsoft Authenticator    | Yes                    | Uwierzytelniania wieloskładnikowego i usługi SSPR              |
| Klucz zabezpieczeń FIDO2             | Yes                    | Uwierzytelnianie wieloskładnikowe                       |
| Tokeny sprzętowe OATH (wersja zapoznawcza) | Nie                     | Uwierzytelnianie wieloskładnikowe                       |
| Tokeny oprogramowania OATH           | Nie                     | Uwierzytelnianie wieloskładnikowe                       |
| SMS                            | Yes                    | Uwierzytelniania wieloskładnikowego i usługi SSPR              |
| Połączenie głosowe                     | Nie                     | Uwierzytelniania wieloskładnikowego i usługi SSPR              |
| Hasło                       | Yes                    |                           |

Wszystkie te metody uwierzytelniania można skonfigurować w Azure Portal i coraz częściej korzystać z interfejsu [API REST Microsoft Graph](/graph/api/resources/authenticationmethods-overview).

Aby dowiedzieć się więcej o poszczególnych metodach uwierzytelniania, zobacz następujące oddzielne artykuły koncepcyjne:

* [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Aplikacja Microsoft Authenticator](concept-authentication-authenticator-app.md)
* [Klucz zabezpieczeń FIDO2](concept-authentication-passwordless.md#fido2-security-keys)
* [Tokeny sprzętowe OATH (wersja zapoznawcza)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [Tokeny oprogramowania OATH](concept-authentication-oath-tokens.md#oath-software-tokens)
* [Logowanie i weryfikacja za pomocą wiadomości SMS](howto-authentication-sms-signin.md) [](concept-authentication-phone-options.md#mobile-phone-verification)
* [Weryfikacja połączeń głosowych](concept-authentication-phone-options.md)
* Hasło

> [!NOTE]
> W usłudze Azure AD hasło jest często jedną z podstawowych metod uwierzytelniania. Nie można wyłączyć metody uwierzytelniania za pomocą hasła. Jeśli używasz hasła jako podstawowego czynnika uwierzytelniania, zwiększ bezpieczeństwo zdarzeń logowania przy użyciu usługi Azure AD Multi-Factor Authentication.

W niektórych scenariuszach można użyć następujących dodatkowych metod weryfikacji:

* [Hasła aplikacji — używane](howto-mfa-app-passwords.md) w przypadku starych aplikacji, które nie obsługują nowoczesnego uwierzytelniania i można je skonfigurować dla uwierzytelniania wieloskładnikowego usługi Azure AD dla 1 użytkownika.
* [Pytania zabezpieczające](concept-authentication-security-questions.md) — używane tylko w przypadku SSPR
* [Adres e-mail](concept-sspr-howitworks.md#authentication-methods) — używany tylko w przypadku usługi SSPR

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę, zobacz samouczek dotyczący samoobsługowego resetowania hasła [(SSPR)][tutorial-sspr] i [uwierzytelniania wieloskładnikowego usługi Azure AD.][tutorial-azure-mfa]

Aby dowiedzieć się więcej na temat pojęć związanych z samoobsługowym resetowaniem hasła, zobacz How Azure AD self-service password reset works (Jak działa [samoobsługowe resetowanie hasła usługi Azure AD).][concept-sspr]

Aby dowiedzieć się więcej na temat pojęć związanych z uwierzytelnianiem wieloskładnikowym, zobacz Jak działa usługa [Azure AD Multi-Factor Authentication.][concept-mfa]

Dowiedz się więcej na temat konfigurowania metod uwierzytelniania przy użyciu [Microsoft Graph API REST.](/graph/api/resources/authenticationmethods-overview)

Aby sprawdzić, jakie metody uwierzytelniania są stosowane, zobacz Analiza metod uwierzytelniania [usługi Azure AD Multi-Factor Authentication przy użyciu programu PowerShell.](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
