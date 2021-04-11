---
title: Przykłady kodu dla Azure Active Directory v 1.0 | Microsoft Docs
description: Zawiera indeks przykładów kodu dla Azure Active Directory (punkt końcowy v 1.0) zorganizowanych według scenariusza.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: sample
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 4d2b1a60856877c24d61379a688ea630e506a848
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075240"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Przykłady kodu Azure Active Directory (punkt końcowy v 1.0)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Za pomocą Microsoft Azure Active Directory (Azure AD) można dodawać uwierzytelnianie i autoryzację do aplikacji sieci Web i interfejsów API sieci Web.

Ta sekcja zawiera linki do przykładów, których można użyć, aby dowiedzieć się więcej o punkcie końcowym usługi Azure AD v 1.0. Te przykłady pokazują, jak to robi się wraz z fragmentami kodu, których można używać w aplikacjach. Na stronie przykładowy kod znajdziesz szczegółowe tematy do odczytu, które ułatwiają wymagania, instalację i konfigurację. A kod jest opatrzony komentarzem, aby ułatwić zapoznanie się z sekcjami krytycznymi.

> [!NOTE]
> Jeśli interesuje Cię przykłady kodu w wersji 2 usługi Azure AD, zobacz [przykłady kodu w wersji 2.0 według scenariusza](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Aby zrozumieć podstawowy scenariusz dla każdego typu przykładu, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](v1-authentication-scenarios.md).

Możesz również uczestniczyć w naszych przykładach w witrynie GitHub. Aby dowiedzieć się, jak to zrobić, zobacz [Microsoft Azure Active Directory przykłady i dokumentacja](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplikacje jednostronicowe

Ten przykład pokazuje, jak napisać aplikację jednostronicową chronioną za pomocą usługi Azure AD.

| Platforma | Wywołuje własny interfejs API | Wywołuje inny internetowy interfejs API |
|--|--|--|
| ![Ten obraz pokazuje logo JavaScript](media/sample-v2-code/logo-js.png) | [JavaScript — singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
| ![Ten obraz pokazuje logo kątowe JS](media/sample-v2-code/logo-angular.png) | [AngularJS — singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [AngularJS-singlepageapp — CORS](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi) |

## <a name="web-applications"></a>Aplikacje internetowe

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Aplikacje sieci Web służące do logowania użytkowników, wywoływania Microsoft Graph lub interfejsu API sieci Web z tożsamością użytkownika

W poniższych przykładach pokazano, jak aplikacje sieci Web użytkowników podpisujących. Niektóre z tych aplikacji również wywołują Microsoft Graph lub własnego interfejsu API sieci Web, w nazwie zalogowanego użytkownika.

| Platforma | Tylko oznaki użytkowników | Microsoft Graph wywołań | Wywołuje inny internetowy interfejs API ASP.NET lub ASP.NET Core 2,0 |
|--|--|--|--|
| ![Ten obraz zawiera logo ASP.NET Core](media/sample-v2-code/logo-netcore.png)</p>ASP.NET Core 2,0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-WebAPI-wielodostępne-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(Graf usługi AAD) | [dotnet-webapp-WebAPI-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore) |
| ![Ten obraz pokazuje logo platformy ASP.NET Framework](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-WebAPI-OAuth2-tożsamość użytkownika](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-wielodzierżawca-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (Graf usługi AAD) |
| ![Ten obraz pokazuje logo języka Python](media/sample-v2-code/logo-python.png) |  | [Python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |
| ![Ten obraz przedstawia dziennik Java](media/sample-v2-code/logo-java.png) |  | [Java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect) |
| ![Ten obraz pokazuje logo języka PHP](media/sample-v2-code/logo-php.png) |  | [php-graphapi-Web](https://github.com/Azure-Samples/active-directory-php-graphapi-web) |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Aplikacje sieci Web ukazujące kontrolę dostępu opartą na rolach (autoryzacja)

W poniższych przykładach pokazano, jak zaimplementować funkcję kontroli dostępu opartej na rolach (RBAC). RBAC służy do ograniczania uprawnień niektórych funkcji w aplikacji sieci Web do określonych użytkowników. Użytkownicy są autoryzowani w zależności od tego, czy należą do **grupy usługi Azure AD** , czy mają daną **rolę** aplikacji.

| Platforma | Sample | Opis |
|--|--|--|
| ![Ten obraz pokazuje logo platformy ASP.NET Framework](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Aplikacja sieci Web platformy .NET 4,5 MVC korzystająca z **ról** usługi Azure AD na potrzeby autoryzacji |

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Publiczne aplikacje dla komputerów stacjonarnych i mobilnych wywołujące Microsoft Graph lub internetowy interfejs API

W poniższych przykładach przedstawiono publiczne aplikacje klienckie (aplikacje klasyczne/mobilne), które uzyskują dostęp do Microsoft Graph lub internetowego interfejsu API w imieniu użytkownika. W zależności od urządzeń i platform aplikacje mogą logować użytkowników na różne sposoby (przepływy/dotacje):

- Interaktywnie
- Dyskretnie (z zintegrowanym uwierzytelnianiem systemu Windows w systemie Windows lub nazwą użytkownika/hasłem)
- Delegowanie interakcyjnego logowania na inne urządzenie (przepływ kodu urządzenia używany na urządzeniach, które nie udostępniają formantów sieci Web)

Aplikacja kliencka | Platforma | Przepływ/Grant | Microsoft Graph wywołań | Wywołuje interfejs API sieci Web ASP.NET lub ASP.NET Core 2. x
------------------ | -------- | ---------- | -------------------- | -------------------------
Pulpit (WPF)           | ![Ten obraz pokazuje logo .NET/C#](media/sample-v2-code/logo-net.png)  | Interaktywne | Część elementu [dotnet-Native — natywny](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-Native-Desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-natywny aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-WebAPI-Manual-JWT-Validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobile (platformy UWP)            | .![Ten obraz przedstawia/UWP .NET/C#](media/sample-v2-code/logo-windows.png)   | Interaktywne | [dotnet-Native-platformy UWP-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Ten przykład używa [Menedżera wam](/windows/uwp/security/web-account-manager), a nie [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-Windows-Store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (platformy UWP Application przy użyciu ADAL.NET do wywoływania interfejsu API sieci Web z pojedynczym dzierżawcą) </p> [dotnet-WebAPI-wielodostępne-Windows-Store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (platformy UWP Application przy użyciu ADAL.NET do wywoływania wielodostępnego interfejsu API sieci Web)|
Urządzenia przenośne (Android, iOS, platformy UWP)   | ![Ten obraz przedstawia .NET/C# (Xamarin)](media/sample-v2-code/logo-xamarin.png) | Interaktywne | [dotnet-obiekt macierzysty](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobile (Android)           | ![Ten obraz pokazuje logo systemu Android](media/sample-v2-code/logo-android.png) | Interaktywne |   [systemów](https://github.com/Azure-Samples/active-directory-android) |
Urządzenia przenośne (iOS)           | ![Ten obraz przedstawia system iOS/cel C lub SWIFT](media/sample-v2-code/logo-ios.png) | Interaktywne |   [nativeClient — iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Pulpit (konsola)          | ![Ten obraz pokazuje logo .NET/C#](media/sample-v2-code/logo-net.png) | Nazwa użytkownika/hasło </p>  Zintegrowane uwierzytelnianie systemu Windows | | [dotnet-bez trybu bezobsługowego](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Pulpit (konsola)          | ![Ten obraz pokazuje logo języka Java](media/sample-v2-code/logo-java.png) | Nazwa użytkownika/hasło | | [Java — tryb bezobsługowy](https://github.com/Azure-Samples/active-directory-java-native-headless)
Pulpit (konsola)           | ![Ten obraz pokazuje logo .NET Core/C#](media/sample-v2-code/logo-netcore.png) | Przepływ kodu urządzenia | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Aplikacje demona (Uzyskiwanie dostępu do interfejsów API sieci Web przy użyciu tożsamości aplikacji)

W poniższych przykładach pokazano aplikacje klasyczne lub sieci Web, które uzyskują dostęp do Microsoft Graph lub interfejsu API sieci Web bez użytkownika (z tożsamością aplikacji).

Aplikacja kliencka | Platforma | Przepływ/Grant | Wywołuje interfejs API sieci Web ASP.NET lub ASP.NET Core 2,0
------------------ | -------- | ---------- | -------------------- 
Aplikacja demona (konsola)          | ![Ten obraz zawiera logo .NET Framework](media/sample-v2-code/logo-netframework.png) | Poświadczenia klienta z kluczem tajnym aplikacji lub certyfikatem | [Demon dotnet](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-demon-certyfikat-Credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Aplikacja demona (konsola)         | ![Ten obraz pokazuje logo platformy .NET Core](media/sample-v2-code/logo-netcore.png) | Poświadczenia klienta z certyfikatem| [dotnetcore-demon-certyfikat-Credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Aplikacja sieci Web ASP.NET  | ![Ten obraz zawiera logo .NET Framework](media/sample-v2-code/logo-netframework.png) | Poświadczenia klienta | [dotnet-webapp-WebAPI-OAuth2-APPIDENTITY](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Interfejsy API sieci Web

### <a name="web-api-protected-by-azure-active-directory"></a>Internetowy interfejs API chroniony przez Azure Active Directory

Poniższy przykład pokazuje, jak chronić interfejs API sieci Web node.js za pomocą usługi Azure AD.

W poprzednich sekcjach tego artykułu można również znaleźć inne przykłady ilustrujące aplikację kliencką **wywołującą** **interfejs API sieci Web** ASP.NET lub ASP.NET Core. Te przykłady nie zostały opisane ponownie w tej sekcji, ale znajdują się one w ostatniej kolumnie tabel powyżej lub poniżej

| Platforma | Przykład |
|--------|-------------------|
| ![Ten obraz zawiera logo Node.js](media/sample-v2-code/logo-nodejs.png)  | [Node-WebApi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Interfejs API sieci Web wywołujący Microsoft Graph lub inny internetowy interfejs API

W poniższych przykładach przedstawiono internetowy interfejs API, który wywołuje inny internetowy interfejs API. Drugi przykład pokazuje, jak obsługiwać dostęp warunkowy.

| Platforma |  Microsoft Graph wywołań | Wywołuje inny internetowy interfejs API ASP.NET lub ASP.NET Core 2,0 |
| -------- |  --------------------- | ------------------------- |
| ![Ten obraz pokazuje logo platformy ASP.NET Framework](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-WebAPI-OnBehalfOf](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-WebAPI-OnBehalfOf-CA](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-WebAPI-OnBehalfOf](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-WebAPI-OnBehalfOf-CA](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Inne przykłady Microsoft Graph

Aby zapoznać się z przykładami i samouczkami, które demonstrują różne wzorce użycia dla interfejsu API Microsoft Graph, w tym uwierzytelniania za pomocą usługi Azure AD, zobacz [Microsoft Graph przykłady społeczności & samouczków](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Zobacz też

- [Przewodnik dewelopera Azure Active Directory](v1-overview.md)
- [Azure Active Directory biblioteki uwierzytelniania](active-directory-authentication-libraries.md)
- [Informacje koncepcyjne i informacje o interfejsie API Microsoft Graph](/graph/use-the-api)
