---
title: Dowiedz się więcej o MSAL | Azure
titleSuffix: Microsoft identity platform
description: Biblioteka uwierzytelniania firmy Microsoft (MSAL) umożliwia deweloperom aplikacji nabywanie tokenów w celu wywołania zabezpieczonych interfejsów API sieci web. Te internetowe interfejsy API mogą być microsoft graph, inne interfejsy API firmy Microsoft, interfejsy API sieci web innych firm lub własny interfejs API sieci web. MSAL obsługuje wiele architektur aplikacji i platform.
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
ms.openlocfilehash: 89c5117e59f7856c3bd572bbea297a836b5ad589
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536220"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Omówienie biblioteki uwierzytelniania firmy Microsoft (MSAL)
Biblioteka uwierzytelniania firmy Microsoft (MSAL) umożliwia deweloperom nabywanie [tokenów](developer-glossary.md#security-token) z punktu końcowego platformy tożsamości firmy Microsoft w celu uzyskania dostępu do zabezpieczonych interfejsów API sieci web. Te internetowe interfejsy API mogą być microsoft graph, inne interfejsy API firmy Microsoft, interfejsy API sieci web innych firm lub własny interfejs API sieci web. Usługa MSAL jest dostępna dla platformy .NET, JavaScript, Android i iOS, które obsługują wiele różnych architektur i platform aplikacji.

MSAL daje wiele sposobów, aby uzyskać tokeny, ze spójnym interfejsem API dla wielu platform. Korzystanie z msal zapewnia następujące korzyści:

* Nie ma potrzeby bezpośredniego używania bibliotek OAuth lub kodu względem protokołu w aplikacji.
* Nabywa tokeny w imieniu użytkownika lub w imieniu aplikacji (w stosownych przypadkach do platformy).
* Utrzymuje pamięć podręczną tokenów i odświeża tokeny, gdy są one bliskie wygaśnięcia. Nie musisz obsługiwać wygasania tokenu na własną rękę.
* Pomaga określić, którzy odbiorcy mają się logować (organizacja, kilka organizacji, konta osobiste w szkole i szkoły oraz konta osobiste firmy Microsoft, tożsamości społecznościowe za pomocą usługi Azure AD B2C, użytkownicy w chmurach suwerennych i krajowych).
* Pomaga skonfigurować aplikację z plików konfiguracyjnych.
* Pomaga rozwiązywać problemy z aplikacją, uwidaczniając wyjątki, rejestrowanie i dane telemetryczne.

## <a name="application-types-and-scenarios"></a>Typy aplikacji i scenariusze
Za pomocą usługi MSAL token można uzyskać z wielu typów aplikacji: aplikacji sieci web, interfejsów API sieci Web, aplikacji jednostronicowych (JavaScript), aplikacji mobilnych i natywnych oraz demonów i aplikacji po stronie serwera.

MSAL może być używany w wielu scenariuszach aplikacji, w tym w następujących:

* [Aplikacje jednostronicowe (JavaScript)](scenario-spa-overview.md)
* [Logowanie się do aplikacji sieci Web użytkowników](scenario-web-app-sign-user-overview.md)
* [Logowanie się aplikacji sieci Web do użytkownika i wywoływanie internetowego interfejsu API w imieniu użytkownika](scenario-web-app-call-api-overview.md)
* [Ochrona internetowego interfejsu API, aby dostęp do niego tylko uwierzytelnieni użytkownicy mogli uzyskać dostęp](scenario-protected-web-api-overview.md)
* [Internetowy interfejs API wywołujący inny podrzędny interfejs API sieci Web w imieniu zalogowanego użytkownika](scenario-web-api-call-api-overview.md)
* [Aplikacja komputerowa wywołująca internetowy interfejs API w imieniu zalogowanego użytkownika](scenario-desktop-overview.md)
* [Aplikacja mobilna wywołująca internetowy interfejs API w imieniu użytkownika, który jest zalogowany interaktywnie.](scenario-mobile-overview.md)
* [Aplikacja demona pulpitu/usługi wywołująca internetowy interfejs API w imieniu samym](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Języki i struktury

| Biblioteka | Obsługiwane platformy i struktury|
| --- | --- |
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, Uniwersalna platforma Windows|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| Struktury JavaScript/TypeScript, takie jak AngularJS, Ember.js lub Durandal.js|
| [MSAL dla Androida](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [Biblioteka MSAL dla systemów iOS i macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS i macOS|
| [MSAL Java (wersja zapoznawcza)](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Java|
| [MSAL Python (wersja zapoznawcza)](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Python|

## <a name="differences-between-adal-and-msal"></a>Różnice między ADAL i MSAL

Biblioteka uwierzytelniania usługi Active Directory (ADAL) integruje się z punktem końcowym usługi Azure AD dla deweloperów (w wersji 1.0), gdzie msal integruje się z punktem końcowym platformy tożsamości firmy Microsoft (w wersji 2.0). Punkt końcowy w wersji 1.0 obsługuje konta służbowe, ale nie konta osobiste. Punkt końcowy w wersji 2.0 to ujednolicenie kont osobistych i służbowych firmy Microsoft w jeden system uwierzytelniania. Ponadto za pomocą usługi MSAL można również uzyskać uwierzytelnianie dla usługi Azure AD B2C.

Aby uzyskać bardziej szczegółowe informacje, przeczytaj artykuł o [migracji do MSAL.NET z ADAL.NET](msal-net-migration.md) i migracji do pliku [MSAL.js z pliku ADAL.js](msal-compare-msal-js-and-adal-js.md).
