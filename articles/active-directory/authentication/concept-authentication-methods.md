---
title: Metody i funkcje uwierzytelniania — Azure Active Directory
description: Poznaj różne metody i funkcje uwierzytelniania dostępne w Azure Active Directory, aby pomóc w ulepszaniu i zabezpieczaniu zdarzeń związanych z logowaniem
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 0bbaf5fa4f3404b0e4fdb4dc016b703c58910457
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652083"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Jakie metody uwierzytelniania i weryfikacji są dostępne w usłudze Azure Active Directory?

W ramach funkcji logowania dla kont w usłudze Azure Active Directory (Azure AD) istnieją różne sposoby, aby użytkownik mógł się uwierzytelnić. Nazwa użytkownika i hasło są najczęstszym sposobem, w jaki użytkownik mógłby historycznie podawać poświadczenia. Dzięki nowoczesnych funkcjom uwierzytelniania i zabezpieczeń w usłudze Azure AD podstawowe hasło należy uzupełnić lub zastąpić bardziej bezpiecznymi metodami uwierzytelniania.

![Tabela mocnych i preferowanych metod uwierzytelniania w usłudze Azure AD](media/concept-authentication-methods/authentication-methods.png)

Metody uwierzytelniania bez hasła, takie jak Windows Hello, FIDO2 klucze zabezpieczeń i aplikacja Microsoft Authenticator zapewniają najbezpieczniejsze zdarzenia logowania.

Usługa Azure AD Multi-Factor Authentication (MFA) zwiększa bezpieczeństwo tylko za pomocą hasła po zalogowaniu się użytkownika. Użytkownik może zostać poproszony o dodatkowe formy uwierzytelniania, na przykład aby odpowiedzieć na powiadomienie wypychane, wprowadzić kod z oprogramowania lub tokenu sprzętowego lub odpowiedzieć na wiadomość SMS lub połączenie telefoniczne.

Aby uprościć środowisko użytkownika i zarejestrować się na potrzeby usługi MFA i samoobsługowego resetowania hasła (SSPR), zalecamy [włączenie funkcji rejestracji informacji o zabezpieczeniach](howto-registration-mfa-sspr-combined.md). W celu zapewnienia odporności zalecamy, aby użytkownicy musieli zarejestrować wiele metod uwierzytelniania. Gdy jedna metoda nie jest dostępna dla użytkownika podczas logowania lub SSPR, może zdecydować się na uwierzytelnienie przy użyciu innej metody. Aby uzyskać więcej informacji, zobacz [Tworzenie elastycznej strategii zarządzania kontroli dostępu w usłudze Azure AD](concept-resilient-controls.md).

Oto [film wideo](https://www.youtube.com/watch?v=LB2yj4HSptc&feature=youtu.be) , który został utworzony, aby pomóc Ci wybrać najlepszą metodę uwierzytelniania w celu zapewnienia bezpieczeństwa organizacji.

## <a name="authentication-method-strength-and-security"></a>Siła i zabezpieczenia metody uwierzytelniania

Podczas wdrażania funkcji, takich jak usługa Azure AD Multi-Factor Authentication w organizacji, zapoznaj się z dostępnymi metodami uwierzytelniania. Wybierz metody, które spełniają lub przekraczają wymagania w zakresie zabezpieczeń, użyteczności i dostępności. Jeśli to możliwe, użyj metod uwierzytelniania z najwyższym poziomem zabezpieczeń.

W poniższej tabeli przedstawiono zagadnienia dotyczące zabezpieczeń dostępnych metod uwierzytelniania. Dostępność to wskazanie, że użytkownik może korzystać z metody uwierzytelniania, a nie dostępności usługi w usłudze Azure AD:

| Metoda uwierzytelniania          | Zabezpieczenia | Łatwość obsługi | Dostępność |
|--------------------------------|:--------:|:---------:|:------------:|
| Windows Hello for Business     | Wys.     | Wys.      | Wys.         |
| Aplikacja Microsoft Authenticator    | Wys.     | Wys.      | Wys.         |
| Klucz zabezpieczeń FIDO2             | Wys.     | Wys.      | Wys.         |
| Tokeny sprzętowe OATH           | Średnia   | Średnia    | Wys.         |
| Tokeny oprogramowania OATH           | Średnia   | Średnia    | Wys.         |
| SMS                            | Śred.   | Wys.      | Śred.       |
| Połączenia głosowe                          | Średnia   | Średnia    | Średnia       |
| Hasło                       | Nis.      | Wys.      | Wys.         |

Aby uzyskać najnowsze informacje na temat zabezpieczeń, zapoznaj się z naszymi wpisami w blogu:

- [Czas na przełączenie na transporty telefoniczne na potrzeby uwierzytelniania](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)
- [Luki w zabezpieczeniach i wektory ataków](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)

> [!TIP]
> Aby zapewnić elastyczność i użyteczność, zalecamy korzystanie z aplikacji Microsoft Authenticator. Ta metoda uwierzytelniania zapewnia najlepsze środowisko użytkownika i wiele trybów, takich jak bezhasło, powiadomienia push usługi MFA i kody OATH.

## <a name="how-each-authentication-method-works"></a>Jak działa każda metoda uwierzytelniania

Niektóre metody uwierzytelniania mogą być używane jako główny czynnik podczas logowania się do aplikacji lub urządzenia, na przykład przy użyciu klucza zabezpieczeń FIDO2 lub hasła. Inne metody uwierzytelniania są dostępne tylko jako składnik pomocniczy w przypadku korzystania z usługi Azure AD Multi-Factor Authentication lub SSPR.

Poniższa tabela przedstawia, kiedy Metoda uwierzytelniania może być używana podczas zdarzenia logowania:

| Metoda                         | Uwierzytelnianie podstawowe | Uwierzytelnianie pomocnicze  |
|--------------------------------|:----------------------:|:-------------------------:|
| Windows Hello for Business     | Tak                    | Uwierzytelnianie wieloskładnikowe                       |
| Aplikacja Microsoft Authenticator    | Tak                    | Uwierzytelnianie MFA i SSPR              |
| Klucz zabezpieczeń FIDO2             | Tak                    | Uwierzytelnianie wieloskładnikowe                       |
| Tokeny sprzętowe OATH           | Nie                     | Uwierzytelnianie wieloskładnikowe                       |
| Tokeny oprogramowania OATH           | Nie                     | Uwierzytelnianie wieloskładnikowe                       |
| SMS                            | Tak                    | Uwierzytelnianie MFA i SSPR              |
| Połączenie głosowe                     | Nie                     | Uwierzytelnianie MFA i SSPR              |
| Hasło                       | Tak                    |                           |

Wszystkie te metody uwierzytelniania można skonfigurować w Azure Portal i w coraz większym stopniu przy użyciu [interfejsu API REST Microsoft Graph beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

Aby dowiedzieć się więcej o tym, jak działa każda metoda uwierzytelniania, zobacz następujące oddzielne artykuły koncepcyjne:

* [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Aplikacja Microsoft Authenticator](concept-authentication-authenticator-app.md)
* [Klucz zabezpieczeń FIDO2](concept-authentication-passwordless.md#fido2-security-keys)
* [Tokeny sprzętowe OATH](concept-authentication-oath-tokens.md#oath-hardware-tokens)
* [Tokeny oprogramowania OATH](concept-authentication-oath-tokens.md#oath-software-tokens)
* Logowanie i [weryfikacja](concept-authentication-phone-options.md#mobile-phone-verification) [programu SMS](howto-authentication-sms-signin.md)
* [Weryfikacja połączenia głosowego](concept-authentication-phone-options.md)
* Hasło

> [!NOTE]
> W usłudze Azure AD hasło jest często jedną z podstawowych metod uwierzytelniania. Nie można wyłączyć metody uwierzytelniania hasła. W przypadku użycia hasła jako podstawowego wskaźnika uwierzytelniania należy zwiększyć bezpieczeństwo zdarzeń logowania za pomocą usługi Azure AD Multi-Factor Authentication.

W niektórych scenariuszach można używać następujących dodatkowych metod weryfikacji:

* [Hasła aplikacji](howto-mfa-app-passwords.md) — używane w przypadku starych aplikacji, które nie obsługują nowoczesnego uwierzytelniania i można je skonfigurować dla poszczególnych użytkowników usługi Azure AD Multi-Factor Authentication.
* [Pytania zabezpieczające](concept-authentication-security-questions.md) — używane tylko w przypadku SSPR
* [Adres e-mail](concept-sspr-howitworks.md#authentication-methods) — służy tylko do SSPR

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, zobacz Samouczek dotyczący samoobsługowego [resetowania hasła (SSPR)][tutorial-sspr] i [usługi Azure AD Multi-Factor Authentication][tutorial-azure-mfa].

Aby dowiedzieć się więcej o pojęciach SSPR, zobacz Jak działa funkcja samoobsługowego [resetowania hasła w usłudze Azure AD][concept-sspr].

Aby dowiedzieć się więcej na temat pojęć MFA, zobacz [jak działa usługa Azure AD Multi-Factor Authentication][concept-mfa].

Dowiedz się więcej na temat konfigurowania metod uwierzytelniania przy użyciu [interfejsu API REST Microsoft Graph w wersji beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

Aby sprawdzić, jakie metody uwierzytelniania są używane, zobacz [Analiza metody uwierzytelniania w usłudze Azure AD Multi-Factor Authentication przy użyciu programu PowerShell](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
