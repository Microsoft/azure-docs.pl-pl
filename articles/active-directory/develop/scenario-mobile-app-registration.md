---
title: Rejestrowanie aplikacji mobilnych, które wywołują interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację mobilną wywołującą interfejsy API sieci Web (Rejestracja aplikacji)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 35c1ffb370a158acc91e2378119055337e28580d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443096"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Rejestrowanie aplikacji mobilnych, które wywołują interfejsy API sieci Web

Ten artykuł zawiera instrukcje ułatwiające zarejestrowanie tworzonej aplikacji mobilnej.

## <a name="supported-account-types"></a>Obsługiwane typy kont

Typy kont, które są obsługiwane przez aplikacje mobilne, zależą od środowiska, które chcesz włączyć, oraz przepływów, których chcesz użyć.

### <a name="audience-for-interactive-token-acquisition"></a>Odbiorcy na potrzeby pozyskiwania tokenów interaktywnych

Większość aplikacji mobilnych korzysta z uwierzytelniania interakcyjnego. Jeśli aplikacja korzysta z tej formy uwierzytelniania, użytkownicy mogą logować się z dowolnego [typu konta](quickstart-register-app.md).

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Odbiorcy zintegrowanego uwierzytelniania systemu Windows, nazwy użytkownika i hasła oraz B2C

Jeśli masz aplikację platforma uniwersalna systemu Windows (platformy UWP), możesz zalogować się przy użyciu zintegrowanego uwierzytelniania systemu Windows. Aby można było korzystać ze zintegrowanego uwierzytelniania systemu Windows lub uwierzytelniania przy użyciu hasła użytkownika, aplikacja musi zalogować użytkowników we własnej dzierżawie dewelopera firmy LOB. W scenariuszu niezależny dostawca oprogramowania aplikacja może logować użytkowników w Azure Active Directory organizacji. Te przepływy uwierzytelniania nie są obsługiwane w przypadku kont osobistych firmy Microsoft.

Użytkowników można także zalogować, korzystając z tożsamości społecznościowych, które przekażą B2C Urząd i zasady. Aby użyć tej metody, można użyć tylko uwierzytelniania interakcyjnego i hasła użytkownika. Nazwa użytkownika — uwierzytelnianie hasła jest obecnie obsługiwane tylko w rozszerzeniach Xamarin. iOS, Xamarin. Android i platformy UWP.

Aby uzyskać więcej informacji, zobacz [scenariusze i obsługiwane przepływy uwierzytelniania](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) oraz [scenariusze i obsługiwane platformy i języki](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages).

## <a name="platform-configuration-and-redirect-uris"></a>Konfiguracja platformy i identyfikatory URI przekierowania

### <a name="interactive-authentication"></a>Uwierzytelnianie interakcyjne

Podczas kompilowania aplikacji mobilnej, która korzysta z uwierzytelniania interaktywnego, najważniejszym krokiem rejestracji jest identyfikator URI przekierowania. Uwierzytelnianie interaktywne można ustawić za pomocą [konfiguracji platformy w bloku **uwierzytelnianie**](https://aka.ms/MobileAppReg).

To środowisko umożliwi aplikacji uzyskanie rejestracji jednokrotnej (SSO) za pomocą Microsoft Authenticator (i Intune — Portal firmy w systemie Android). Obsługuje również zasady zarządzania urządzeniami.

Portal rejestracji aplikacji zawiera środowisko w wersji zapoznawczej, które ułatwia Obliczanie identyfikatora URI odpowiedzi obsługiwanej przez brokera dla aplikacji dla systemów iOS i Android:

1. W portalu rejestracji aplikacji wybierz pozycję **uwierzytelnianie**  >  **Wypróbuj nowe środowisko**.

   ![Blok uwierzytelniania, w którym można wybrać nowe środowisko](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Wybierz pozycję **Dodaj platformę**.

   ![Dodaj platformę](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Jeśli lista platform jest obsługiwana, wybierz pozycję **iOS**.

   ![Wybieranie aplikacji mobilnej](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Wprowadź identyfikator pakietu, a następnie wybierz pozycję **zarejestruj**.

   ![Wprowadź identyfikator pakietu](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Po wykonaniu tych kroków identyfikator URI przekierowania zostanie obliczony dla Ciebie, jak na poniższej ilustracji.

![Otrzymany identyfikator URI przekierowania](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Jeśli wolisz ręcznie skonfigurować identyfikator URI przekierowania, możesz to zrobić za pomocą manifestu aplikacji. Oto zalecany format manifestu:

- System **iOS** :`msauth.<BUNDLE_ID>://auth`
  - Na przykład wprowadź `msauth.com.yourcompany.appName://auth`
- System **Android** :`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Można wygenerować skrót sygnatury systemu Android przy użyciu klucza wydania lub klucza debugowania za pomocą polecenia narzędzia.

### <a name="username-password-authentication"></a>Nazwa użytkownika — uwierzytelnianie hasła

Jeśli Twoja aplikacja używa tylko uwierzytelniania przy użyciu hasła użytkownika, nie musisz rejestrować identyfikatora URI przekierowania dla aplikacji. Ten przepływ wykonuje rundy do punktu końcowego Microsoft Identity platform w wersji 2,0. Aplikacja nie zostanie wywołana ponownie na żadnym konkretnym identyfikatorze URI.

Należy jednak zidentyfikować aplikację jako publiczną aplikację kliencką. Aby to zrobić, Zacznij od sekcji **uwierzytelnianie** w aplikacji. W podsekcji **Ustawienia zaawansowane** w obszarze **domyślny typ klienta** dla pytania **Traktuj aplikację jako klienta publicznego** wybierz pozycję **tak**.

## <a name="api-permissions"></a>Uprawnienia aplikacji

Interfejsy API wywołania aplikacji mobilnych w imieniu zalogowanego użytkownika. Aplikacja musi zażądać delegowania uprawnień. Te uprawnienia są również nazywane zakresami. W zależności od wybranego środowiska można zażądać delegowania uprawnień statycznie przez Azure Portal. Można też zażądać ich dynamicznie w czasie wykonywania.

Statycznie rejestrowanie uprawnień pozwala administratorom łatwo zatwierdzać aplikację. Zalecana jest rejestracja statyczna.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu — [Konfiguracja kodu aplikacji](scenario-mobile-app-configuration.md).
