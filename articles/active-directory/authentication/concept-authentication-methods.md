---
title: Metody i funkcje uwierzytelniania — Azure Active Directory
description: Poznaj różne metody i funkcje uwierzytelniania dostępne w Azure Active Directory, aby pomóc w ulepszaniu i zabezpieczaniu zdarzeń związanych z logowaniem
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: dcbfd05df84e32423df425f3bdd231a26e4f3bca
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90527052"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Jakie metody uwierzytelniania i weryfikacji są dostępne w usłudze Azure Active Directory?

W ramach funkcji logowania dla kont w usłudze Azure Active Directory (Azure AD) istnieją różne sposoby, aby użytkownik mógł się uwierzytelnić. Nazwa użytkownika i hasło są najczęstszym sposobem, w jaki użytkownik mógłby historycznie podawać poświadczenia. Dzięki nowoczesnych funkcjom uwierzytelniania i zabezpieczeń w usłudze Azure AD podstawowe hasło należy uzupełnić lub zastąpić bardziej bezpiecznymi metodami uwierzytelniania.

![Tabela mocnych i preferowanych metod uwierzytelniania w usłudze Azure AD](media/concept-authentication-methods/authentication-methods.png)

Metody uwierzytelniania bez hasła, takie jak Windows Hello, FIDO2 klucze zabezpieczeń i aplikacja Microsoft Authenticator zapewniają najbezpieczniejsze zdarzenia logowania.

Usługa Azure Multi-Factor Authentication dodaje dodatkowe zabezpieczenia tylko przy użyciu hasła po zalogowaniu się użytkownika. Użytkownik może zostać poproszony o dodatkowe formy uwierzytelniania, na przykład aby odpowiedzieć na powiadomienie wypychane, wprowadzić kod z oprogramowania lub tokenu sprzętowego lub odpowiedzieć na wiadomość SMS lub połączenie telefoniczne.

Aby uprościć środowisko użytkownika i zarejestrować się na potrzeby usługi MFA i SSPR, zalecamy [włączenie połączonej rejestracji informacji o zabezpieczeniach](howto-registration-mfa-sspr-combined.md). W celu zapewnienia odporności zalecamy, aby użytkownicy musieli zarejestrować wiele metod uwierzytelniania. Gdy jedna metoda nie jest dostępna dla użytkownika podczas logowania lub SSPR, może zdecydować się na uwierzytelnienie przy użyciu innej metody. Aby uzyskać więcej informacji, zobacz [Tworzenie elastycznej strategii zarządzania kontroli dostępu w usłudze Azure AD](concept-resilient-controls.md).

## <a name="authentication-method-strength-and-security"></a>Siła i zabezpieczenia metody uwierzytelniania

W przypadku wdrażania funkcji takich jak Azure Multi-Factor Authentication w organizacji zapoznaj się z dostępnymi metodami uwierzytelniania. Wybierz metody, które spełniają lub przekraczają wymagania w zakresie zabezpieczeń, użyteczności i dostępności. Jeśli to możliwe, użyj metod uwierzytelniania z najwyższym poziomem zabezpieczeń.

W poniższej tabeli przedstawiono zagadnienia dotyczące zabezpieczeń dostępnych metod uwierzytelniania. Dostępność to wskazanie, że użytkownik może korzystać z metody uwierzytelniania, a nie dostępności usługi w usłudze Azure AD:

| Metoda uwierzytelniania       | Zabezpieczenia | Łatwość obsługi | Phisable? | Gniazdo w kanale? | Dostępność |
|-----------------------------|:--------:|:---------:|:---------:|:-----------------:|:------------:|
| Klucz zabezpieczeń FIDO2          | Wysoki     | Wysoki      | Nie        | Nie                | Wysoki         |
| Aplikacja Microsoft Authenticator | Wysoki     | Wysoki      | Yes       | Nie <sup>1</sup>   | Wysoki         |
| Windows Hello dla firm  | Wysoki     | Wysoki      | Nie        | Nie                | Wysoki         |
| Sprzętowe tokeny OATH        | Średnia   | Średnia    | Yes       | Nie                | Wysoki         |
| Tokeny OATH oprogramowania        | Średnia   | Średnia    | Yes       | Nr <sup>2</sup>   | Wysoki         |
| SMS                         | Średniaa   | Wysoki      | Yes       | Yes               | Średniaa       |
| Połączenia głosowe                       | Średnia   | Średnia    | Yes       | Yes               | Średniaa       |
| Hasło                    | Nis.      | Wys.      | Yes       | Yes               | Wysoki         |

<sup>1</sup> w trybie bezhasła, gdy aplikacja jest zarejestrowana na określonym urządzeniu<br />
<sup>2</sup> przy założeniu, że aplikacja wymaga odblokowania urządzenia

Aby uzyskać więcej informacji na temat luk w zabezpieczeniach i wektorów ataków, zobacz [Informacje o wygnieździe kanału i phishingu](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)w czasie rzeczywistym.

> [!TIP]
> Aby zapewnić elastyczność i użyteczność, zalecamy korzystanie z aplikacji Microsoft Authenticator. Ta metoda uwierzytelniania zapewnia najlepsze środowisko użytkownika i wiele trybów, takich jak bezhasło, powiadomienia push usługi MFA i kody OATH.

## <a name="how-each-authentication-method-works"></a>Jak działa każda metoda uwierzytelniania

Niektóre metody uwierzytelniania mogą być używane jako główny czynnik podczas logowania się do aplikacji lub urządzenia, na przykład przy użyciu klucza zabezpieczeń FIDO2 lub hasła. Inne metody uwierzytelniania są dostępne tylko jako składnik pomocniczy w przypadku korzystania z usługi Azure Multi-Factor Authentication lub SSPR.

Poniższa tabela przedstawia, kiedy Metoda uwierzytelniania może być używana podczas zdarzenia logowania:

| Metoda                         | Uwierzytelnianie podstawowe | Uwierzytelnianie pomocnicze  |
|--------------------------------|:----------------------:|:-------------------------:|
| Klucze zabezpieczeń FIDO2 (wersja zapoznawcza)  | Yes                    | Funkcja                       |
| Aplikacja Microsoft Authenticator    | Tak (wersja zapoznawcza)          | Uwierzytelnianie MFA i SSPR              |
| Windows Hello dla firm     | Yes                    | Funkcja                       |
| Tokeny sprzętowe OATH (wersja zapoznawcza) | Nie                     | Funkcja                       |
| Tokeny oprogramowania OATH           | Nie                     | Funkcja                       |
| SMS                            | Tak (wersja zapoznawcza)          | Uwierzytelnianie MFA i SSPR              |
| Połączenie głosowe                     | Nie                     | Uwierzytelnianie MFA i SSPR              |
| Hasło                       | Yes                    |                           |

Wszystkie te metody uwierzytelniania można skonfigurować w Azure Portal i w coraz większym stopniu przy użyciu [interfejsu API REST Microsoft Graph beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

Aby dowiedzieć się więcej o tym, jak działa każda metoda uwierzytelniania, zobacz następujące oddzielne artykuły koncepcyjne:

* [Klucze zabezpieczeń FIDO2 (wersja zapoznawcza)](concept-authentication-passwordless.md#fido2-security-keys)
* [Aplikacja Microsoft Authenticator](concept-authentication-authenticator-app.md)
* [Windows Hello dla firm](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Tokeny oprogramowania OATH](concept-authentication-oath-tokens.md#oath-software-tokens)
* [Tokeny sprzętowe OATH (wersja zapoznawcza)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* Logowanie za pomocą programu SMS [(wersja zapoznawcza)](howto-authentication-sms-signin.md) i [weryfikacja](concept-authentication-phone-options.md#mobile-phone-verification)
* [Weryfikacja połączenia głosowego](concept-authentication-phone-options.md)
* Hasło

> [!NOTE]
> W usłudze Azure AD hasło jest często jedną z podstawowych metod uwierzytelniania. Nie można wyłączyć metody uwierzytelniania hasła. Jeśli używasz hasła jako podstawowego czynnika uwierzytelniania, Zwiększ bezpieczeństwo zdarzeń logowania za pomocą usługi Azure Multi-Factor Authentication.

W niektórych scenariuszach można używać następujących dodatkowych metod weryfikacji:

* [Hasła aplikacji](howto-mfa-app-passwords.md) — używane w przypadku starych aplikacji, które nie obsługują nowoczesnego uwierzytelniania i można je skonfigurować dla Multi-Factor Authentication platformy Azure dla poszczególnych użytkowników.
* [Pytania zabezpieczające](concept-authentication-security-questions.md) — używane tylko w przypadku SSPR
* [Adres e-mail](concept-sspr-howitworks.md#authentication-methods) — służy tylko do SSPR

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, zobacz Samouczek dotyczący samoobsługowego [resetowania hasła (SSPR)][tutorial-sspr] i [platformy Azure Multi-Factor Authentication][tutorial-azure-mfa].

Aby dowiedzieć się więcej o pojęciach SSPR, zobacz Jak działa funkcja samoobsługowego [resetowania hasła w usłudze Azure AD][concept-sspr].

Aby dowiedzieć się więcej na temat pojęć MFA, zobacz [jak działa usługa Azure Multi-Factor Authentication][concept-mfa].

Dowiedz się więcej na temat konfigurowania metod uwierzytelniania przy użyciu [interfejsu API REST Microsoft Graph w wersji beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
