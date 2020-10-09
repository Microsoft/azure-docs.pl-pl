---
title: Więcej informacji na temat MSAL | Azure
titleSuffix: Microsoft identity platform
description: Biblioteka Microsoft Authentication Library (MSAL) umożliwia deweloperom aplikacji uzyskanie tokenów w celu wywołania zabezpieczonych interfejsów API sieci Web. Te interfejsy API sieci Web mogą być Microsoft Graph, inne interfejsy API firmy Microsoft, interfejsy API sieci Web innych firm lub własny internetowy interfejs API. MSAL obsługuje wiele architektur aplikacji i platform.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0c4da177644a1cdb648c00e8309c18031a905d7f
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825950"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Omówienie biblioteki uwierzytelniania firmy Microsoft (MSAL)
Biblioteka Microsoft Authentication Library (MSAL) umożliwia deweloperom uzyskanie [tokenów](developer-glossary.md#security-token) z punktu końcowego platformy tożsamości firmy Microsoft w celu uzyskania dostępu do zabezpieczonych interfejsów API sieci Web. Te interfejsy API sieci Web mogą być Microsoft Graph, inne interfejsy API firmy Microsoft, interfejsy API sieci Web innych firm lub własny internetowy interfejs API. MSAL jest dostępny dla platform .NET, JavaScript, Java, Python, Android i iOS, które obsługują wiele różnych architektur i platform aplikacji.

MSAL zapewnia wiele sposobów uzyskiwania tokenów z spójnym interfejsem API dla wielu platform. Korzystanie z usługi MSAL zapewnia następujące korzyści:

* Nie trzeba bezpośrednio używać bibliotek lub kodu OAuth w aplikacji.
* Uzyskuje tokeny w imieniu użytkownika lub w imieniu aplikacji (jeśli dotyczy platformy).
* Zachowuje pamięć podręczną tokenów i odświeża tokeny, gdy zbliżają się do wygaśnięcia. Nie musisz samodzielnie obsłużyć wygaśnięcia tokenu.
* Pomaga określić, którzy użytkownicy mają logować się do aplikacji (organizacja, kilka organizacje, służbowe i szkolne konta Microsoft, tożsamości społecznościowe z Azure AD B2Cami, użytkownikami w ramach suwerennych i krajowych chmur).
* Pomaga skonfigurować aplikację na podstawie plików konfiguracji.
* Pomaga rozwiązywać problemy z aplikacją przez ujawnienie wyjątków z możliwością działania, rejestrowanie i telemetrię.

## <a name="application-types-and-scenarios"></a>Typy aplikacji i scenariusze
Korzystając z MSAL, można uzyskać token z wielu typów aplikacji: aplikacje sieci Web, interfejsy API sieci Web, aplikacje jednostronicowe (JavaScript), aplikacje mobilne i natywne oraz demony i aplikacje po stronie serwera.

MSAL można używać w wielu scenariuszach aplikacji, w tym następujących:

* [Aplikacje jednostronicowe (JavaScript)](scenario-spa-overview.md)
* [Logowanie aplikacji sieci Web użytkowników](scenario-web-app-sign-user-overview.md)
* [Podpisywanie aplikacji sieci Web przez użytkownika i wywoływanie internetowego interfejsu API w imieniu użytkownika](scenario-web-app-call-api-overview.md)
* [Ochrona internetowego interfejsu API, tak aby tylko uwierzytelnieni użytkownicy mieli do niego dostęp](scenario-protected-web-api-overview.md)
* [Interfejs API sieci Web wywołujący inny podrzędny interfejs API sieci Web w imieniu zalogowanego użytkownika](scenario-web-api-call-api-overview.md)
* [Aplikacja klasyczna wywołująca internetowy interfejs API w imieniu zalogowanego użytkownika](scenario-desktop-overview.md)
* [Aplikacja mobilna wywołuje internetowy interfejs API w imieniu użytkownika, który jest zalogowany interaktywnie](scenario-mobile-overview.md).
* [Aplikacja demona pulpitu/usługi wywołująca internetowy interfejs API w imieniu siebie](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Języki i struktury

| Biblioteka | Obsługiwane platformy i struktury|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, platforma uniwersalna systemu Windows|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| Struktury języka JavaScript/TypeScript, takie jak AngularJS, Ember.js lub Durandal.js|
| [MSAL dla systemu Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [Biblioteka MSAL dla systemów iOS i macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS i macOS|
| [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Windows, macOS, Linux|
| [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Windows, macOS, Linux|

## <a name="differences-between-adal-and-msal"></a>Różnice między bibliotekami ADAL i MSAL

Active Directory Authentication Library (ADAL) integruje się z punktem końcowym usługi Azure AD dla deweloperów (v 1.0), gdzie MSAL integruje się z punktem końcowym Microsoft Identity platform (v 2.0). Punkt końcowy v 1.0 obsługuje konta służbowe, ale nie konta osobiste. Punkt końcowy v 2.0 to nieujednolicenie kont osobistych i kont służbowych firmy Microsoft w ramach jednego systemu uwierzytelniania. Ponadto w przypadku usługi MSAL można również uzyskać uwierzytelnianie dla Azure AD B2C.

Aby uzyskać bardziej szczegółowe informacje, Przeczytaj o [migracji do MSAL.NET z ADAL.NET](msal-net-migration.md) i [migracji do MSAL.js z ADAL.js](msal-compare-msal-js-and-adal-js.md).
