---
title: Microsoft Identity platform — Omówienie — Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej na temat składników platformy tożsamości firmy Microsoft i sposobu, w jaki mogą one pomóc w tworzeniu obsługi zarządzania tożsamościami i dostępem (IAM) w aplikacjach.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 07/09/2020
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: identityplatformtop40, contperf-fy21q2
ms.openlocfilehash: a4ce8242bd3110fee038ac826973e6a134413344
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426845"
---
# <a name="what-is-the-microsoft-identity-platform"></a>Co to jest platforma tożsamości firmy Microsoft?

Platforma tożsamości firmy Microsoft ułatwia tworzenie aplikacji, do których użytkownicy i klienci mogą logować się przy użyciu tożsamości firmy Microsoft lub kont społecznościowych, a także zapewniają autoryzowany dostęp do własnych interfejsów API lub interfejsów API firmy Microsoft, takich jak Microsoft Graph.

Istnieje kilka składników, które tworzą platformę tożsamości firmy Microsoft:

- **Uwierzytelnianie OAuth 2,0 i OpenID Connect łączenie usługi uwierzytelniania zgodnego ze standardem** , dzięki czemu deweloperzy mogą uwierzytelniać kilka typów tożsamości, w tym:
  - Konta służbowe, które są obsługiwane przez usługę Azure AD
  - Osobiste konto Microsoft, takie jak Skype, Xbox i Outlook.com
  - Konta społecznościowe lub lokalne przy użyciu Azure AD B2C
- **Biblioteki Open Source**: biblioteki uwierzytelniania firmy Microsoft (MSAL) i obsługa innych bibliotek zgodnych ze standardami
- **Portal zarządzania aplikacjami**: środowisko rejestracji i konfiguracji w Azure Portal wraz z innymi funkcjami zarządzania platformy Azure.
- **Interfejs API konfiguracji aplikacji i program PowerShell**: programowe Konfigurowanie aplikacji za pomocą interfejsu API Microsoft Graph i programu PowerShell, dzięki czemu można zautomatyzować zadania DevOps.
- **Zawartość dla deweloperów**: Dokumentacja techniczna, w tym Przewodniki Szybki Start, samouczki, przewodniki i przykłady kodu.

W przypadku deweloperów platforma tożsamości firmy Microsoft oferuje integrację nowoczesnych innowacji w zakresie tożsamości i przestrzeni zabezpieczeń, takich jak uwierzytelnianie bezhasła, uwierzytelnianie krok po kroku i dostęp warunkowy. Nie musisz wprowadzać takich funkcji samodzielnie: Aplikacje zintegrowane z platformą tożsamości firmy Microsoft w sposób natywny wykorzystują takie innowacje.

Korzystając z platformy tożsamości firmy Microsoft, możesz napisać kod jeden raz i skontaktować się z dowolnym użytkownikiem. Aplikację można skompilować raz i korzystać z niej na wielu platformach lub utworzyć aplikację, która działa jako klient, a także aplikację zasobów (API).

Aby obejrzeć film wideo z omówieniem platformy i demonstracją środowiska uwierzytelniania, zobacz [co to jest platforma tożsamości firmy Microsoft dla deweloperów?](https://youtu.be/uDU1QTSw7Ps).

## <a name="getting-started"></a>Wprowadzenie

Wybierz [scenariusz aplikacji](authentication-flows-app-scenarios.md) , który chcesz skompilować. Każda z tych ścieżek scenariusza rozpoczyna się od przeglądu i łączy do przewodnika Szybki Start, aby ułatwić rozpoczęcie pracy:

- [Aplikacja jednostronicowa (SPA)](scenario-spa-overview.md)
- [Aplikacja internetowa, która loguje użytkowników](scenario-web-app-sign-user-overview.md)
- [Aplikacja internetowa, która wywołuje internetowe interfejsy API](scenario-web-app-call-api-overview.md)
- [Chroniony internetowy interfejs API](scenario-protected-web-api-overview.md)
- [Internetowy interfejs API wywołujący internetowe interfejsy API](scenario-web-api-call-api-overview.md)
- [Aplikacja klasyczna](scenario-desktop-overview.md)
- [Aplikacja demona](scenario-daemon-overview.md)
- [aplikacja mobilna](scenario-mobile-overview.md)

Podczas pracy z platformą tożsamości firmy Microsoft w celu integracji uwierzytelniania i autoryzacji w aplikacjach można odwołać się do tego obrazu, który przedstawia najbardziej typowe scenariusze aplikacji i ich składniki tożsamości. Wybierz obraz, aby wyświetlić go w pełnym rozmiarze.

[![Mapa Metro przedstawiająca kilka scenariuszy aplikacji na platformie Microsoft Identity platform](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="learn-authentication-concepts"></a>Poznaj pojęcia związane z uwierzytelnianiem

Dowiedz się, jak uwierzytelnianie podstawowe i pojęcia dotyczące usługi Azure AD dotyczą platformy tożsamości firmy Microsoft w tym zalecanym zestawie artykułów:

- [Podstawowe informacje o uwierzytelnianiu](./authentication-vs-authorization.md)
- [Nazwy główne aplikacji i usługi](app-objects-and-service-principals.md)
- [Odbiorców](v2-supported-account-types.md)
- [Uprawnienia i wyrażanie zgody](v2-permissions-and-consent.md)
- [Tokeny identyfikatorów](id-tokens.md)
- [Tokeny dostępu](access-tokens.md)
- [Przepływy uwierzytelniania i scenariusze aplikacji](authentication-flows-app-scenarios.md)

## <a name="more-identity-and-access-management-options"></a>Więcej opcji zarządzania tożsamościami i dostępem

[Azure AD B2C](../../active-directory-b2c/overview.md) — Twórz aplikacje dla klientów, do których użytkownicy mogą logować się przy użyciu kont społecznościowych, takich jak Facebook lub Google, lub przy użyciu adresu e-mail i hasła.

[Usługa Azure AD B2B](../external-identities/what-is-b2b.md) — Zapraszanie użytkowników zewnętrznych do dzierżawy usługi Azure AD jako użytkowników "Gość" oraz przypisywanie uprawnień do autoryzacji podczas korzystania z istniejących poświadczeń do uwierzytelniania.

[Azure Active Directory dla deweloperów (v 1.0)](../azuread-dev/v1-overview.md) — tutaj przedstawiono dla deweloperów z istniejącymi aplikacjami korzystającymi z starszego punktu końcowego v 1.0. **Nie** należy używać programu v 1.0 dla nowych projektów.

## <a name="next-steps"></a>Następne kroki

Jeśli masz konto platformy Azure, masz już dostęp do dzierżawy Azure Active Directory, ale większość deweloperów platformy tożsamości firmy Microsoft potrzebuje własnej dzierżawy usługi Azure AD do użycia podczas opracowywania aplikacji, "dzierżawy dev".

Dowiedz się, jak utworzyć własną dzierżawę do użycia podczas kompilowania aplikacji:

[Szybki Start: Konfigurowanie dzierżawy usługi Azure AD](quickstart-create-new-tenant.md)
