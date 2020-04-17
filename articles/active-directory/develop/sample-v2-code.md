---
title: Przykłady kodu dla platformy tożsamości firmy Microsoft
description: Zawiera indeks dostępnych przykładów kodu platformy tożsamości firmy Microsoft (punkt końcowy w wersji 2.0), uporządkowany według scenariusza.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 15578b6adc19eb3513e0d7e1d3d2c400c9170250
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535982"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Przykłady kodu platformy tożsamości firmy Microsoft (punkt końcowy w wersji 2.0)

Za pomocą platformy tożsamości firmy Microsoft można:

- Dodaj uwierzytelnianie i autoryzację do aplikacji sieci web i internetowych interfejsów API.
- Wymagaj tokenu dostępu, aby uzyskać dostęp do chronionego interfejsu API sieci web.

W tym artykule pokrótce opisano i zawiera łącza do przykładów dla punktu końcowego platformy tożsamości firmy Microsoft. Te przykłady pokazują, jak to zrobić, a także zawierają fragmenty kodu, które można użyć w aplikacjach. Na przykładowej stronie kodu znajdziesz szczegółowe tematy readme, które pomagają w wymaganiach, instalacji i konfiguracji. Komentarze w kodzie pomagają zrozumieć sekcje krytyczne.

> [!NOTE]
> Jeśli interesują Cię przykłady w wersji 1.0, zobacz [przykłady kodu usługi Azure AD (punkt końcowy w wersji 1.0).](../azuread-dev/sample-v1-code.md)

Aby zrozumieć podstawowy scenariusz dla każdego przykładowego typu, zobacz [Typy aplikacji dla punktu końcowego platformy tożsamości firmy Microsoft](v2-app-types.md).

Można również przyczynić się do przykładów w usłudze GitHub. Aby dowiedzieć się, jak to zrobić, zobacz [Przykłady i dokumentacja usługi Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplikacje jednostronicowe

Te przykłady pokazują, jak napisać jednostronicową aplikację zabezpieczoną za pomocą platformy tożsamości firmy Microsoft. Próbki te używają jednego ze smaków MSAL.js.

| Platforma | Opis | Link |
| -------- | --------------------- | -------- |
| ![Ten obraz eksponuje](media/sample-v2-code/logo_js.png) logo [JavaScript JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Wywołuje program Microsoft Graph |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Ten obraz eksponuje](media/sample-v2-code/logo_js.png) logo [JavaScript JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Połączenia B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Ten obraz eksponuje](media/sample-v2-code/logo_js.png) logo [JavaScript JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Wywołuje własny internetowy interfejs API |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Ten obraz eksponuje](media/sample-v2-code/logo_angular.png) logo Angular [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Wywołuje program Microsoft Graph  | [active-directory-javascript-singlepageapp-kątowe](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Ten obraz eksponuje](media/sample-v2-code/logo_angular.png) logo Angular [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | Połączenia B2C |[active-directory-b2c-javascript-kątowe-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |

## <a name="web-applications"></a>Aplikacje internetowe

Poniższe przykłady ilustrują aplikacje sieci web, które logują się do użytkowników. Niektóre przykłady pokazują również aplikację wywołującą program Microsoft Graph lub własny internetowy interfejs API z tożsamością użytkownika.

| Platforma | Tylko logowanie użytkowników | Loguje się do użytkowników i wywołuje program Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Na tym zdjęciu widać logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | [ASP.NET Core WebApp loguje użytkowników samouczek](https://aka.ms/aspnetcore-webapp-sign-in) | Ten sam przykład w ASP.NET Core aplikacji sieci web wywołuje fazę [Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) |
| ![Ten obraz ekspozycjowy przedstawia logo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET Szybki start](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Ten obraz eksploruje logo Java](media/sample-v2-code/logo_java.png)  |                   | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Ten obraz eksploruje logo Pythona](media/sample-v2-code/logo_python.png)  |                   | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Ten obraz eksploruje logo Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Publiczne aplikacje klienckie dla komputerów i urządzeń przenośnych

W poniższych przykładach przedstawiono publiczne aplikacje klienckie (aplikacje klasyczne lub mobilne), które uzyskują dostęp do interfejsu API programu Microsoft Graph lub własny internetowy interfejs API w imieniu użytkownika. Wszystkie te aplikacje klienckie używają biblioteki uwierzytelniania firmy Microsoft (MSAL).

| Aplikacja kliencka | Platforma | Przepływ/dotacja | Wywołuje program Microsoft Graph | Wywołuje internetowy interfejs API ASP.NET Core 2.0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Pulpit (WPF)      | ![Na tym obrazie jest wyświetlane logo .NET/C#](media/sample-v2-code/logo_NET.png) | [Interaktywne](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Pulpit (konsola)   | ![Ten obraz przedstawia logo .NET/C# (Pulpit)](media/sample-v2-code/logo_NET.png) | [Zintegrowane uwierzytelnianie systemu Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Pulpit (konsola)   | ![Ten obraz eksploruje logo Java](media/sample-v2-code/logo_java.png) | [Zintegrowane uwierzytelnianie systemu Windows](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Pulpit (konsola)   | ![Ten obraz przedstawia logo .NET/C# (Pulpit)](media/sample-v2-code/logo_NETcore.png) | [Nazwa użytkownika/hasło](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Pulpit (konsola) z WAM  | ![Ten obraz przedstawia logo .NET/C# (Pulpit)](media/sample-v2-code/logo_NETcore.png) | [interaktywne z WAM](msal-authentication-flows.md#interactive) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Pulpit (konsola)   | ![Ten obraz eksploruje logo Java](media/sample-v2-code/logo_java.png) | [Nazwa użytkownika/hasło](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Pulpit (konsola)   | ![Ten obraz eksploruje logo Pythona](media/sample-v2-code/logo_python.png) | [Nazwa użytkownika/hasło](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Urządzenia mobilne (Android, iOS, UWP)   | ![Ten obraz przedstawia logo .NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [Interaktywne](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Urządzenia mobilne (iOS)       | ![Na tym obrazie przedstawiono system iOS/Objective-C lub Swift](media/sample-v2-code/logo_iOS.png) | [Interaktywne](msal-authentication-flows.md#interactive) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Pulpit (macOS)       | macOS | [Interaktywne](msal-authentication-flows.md#interactive) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Urządzenia mobilne (Android-Java)   | ![Ten obraz eksploruje logo Androida](media/sample-v2-code/logo_Android.png) | [Interaktywne](msal-authentication-flows.md#interactive) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Telefon komórkowy (Android-Kotlin)   | ![Ten obraz eksploruje logo Androida](media/sample-v2-code/logo_Android.png) | [Interaktywne](msal-authentication-flows.md#interactive) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Aplikacje demonów

W poniższych przykładach przedstawiono aplikację, która uzyskuje dostęp do interfejsu API programu Microsoft Graph z własną tożsamością (bez użytkownika).

| Aplikacja kliencka | Platforma | Przepływ/dotacja | Wywołuje program Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Konsola | ![Na tym obrazie jest wyświetlane logo .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Poświadczenia klienta](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Aplikacja internetowa | ![Ten obraz ekspozycjowy przedstawia logo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Poświadczenia klienta](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Konsola | ![Ten obraz eksploruje logo Java](media/sample-v2-code/logo_java.png) | [Poświadczenia klienta](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Konsola | ![Ten obraz eksploruje logo Pythona](media/sample-v2-code/logo_python.png) | [Poświadczenia klienta](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Zastosowania bezgłowy

W poniższym przykładzie przedstawiono publiczną aplikację kliencką działającą na urządzeniu bez przeglądarki sieci web. Aplikacja może być narzędziem wiersza polecenia, aplikacją działającą na linuksie lub komputerze Mac lub aplikacją IoT. W przykładzie znajduje się aplikacja uzyskująca dostęp do interfejsu API programu Microsoft Graph w imieniu użytkownika, który loguje się interaktywnie na innym urządzeniu (takim jak telefon komórkowy). Ta aplikacja kliencka używa biblioteki uwierzytelniania firmy Microsoft (MSAL).

| Aplikacja kliencka | Platforma | Przepływ/dotacja | Wywołuje program Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Pulpit (konsola)   | ![Ten obraz przedstawia logo .NET/C# (Pulpit)](media/sample-v2-code/logo_NETcore.png) | [Przepływ kodu urządzenia](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Pulpit (konsola)   | ![Ten obraz eksploruje logo Java](media/sample-v2-code/logo_java.png) | [Przepływ kodu urządzenia](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Pulpit (konsola)   | ![Ten obraz eksploruje logo Pythona](media/sample-v2-code/logo_python.png) | [Przepływ kodu urządzenia](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Interfejsy API sieci Web

Poniższe przykłady pokazują, jak chronić internetowy interfejs API za pomocą punktu końcowego platformy tożsamości firmy Microsoft i jak wywołać interfejs API podrzędnego z internetowego interfejsu API.

| Platforma | Przykład |
| -------- | ------------------- |
| ![Na tym zdjęciu widać logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | ASP.NET Core web API (usługa) [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Ten obraz ekspozycjowy przedstawia logo ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Web API (usługa) [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Ten obraz eksploruje logo Java](media/sample-v2-code/logo_java.png) | Internetowy interfejs API (usługa) [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![Na tym obrazie jest wyświetlane logo Node.js](media/sample-v2-code/logo_nodejs.png) | Internetowy interfejs API (usługa) [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![Na tym obrazie jest wyświetlane logo Node.js](media/sample-v2-code/logo_nodejs.png) | B2C Web API (usługa) [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Usługa Azure działa jako internetowe interfejsy API

Poniższe przykłady pokazują, jak chronić funkcję platformy Azure przy użyciu protokołu HttpTrigger i uwidacznianie internetowego interfejsu API za pomocą punktu końcowego platformy tożsamości firmy Microsoft oraz jak wywołać interfejs API podrzędnego z internetowego interfejsu API.

| Platforma | Przykład |
| -------- | ------------------- |
| ![Na tym zdjęciu widać logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | ASP.NET Core web API (usługa) Funkcja platformy Azure [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Na tym obrazie jest wyświetlane logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Web API (usługa) [nodejs i passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Ten obraz eksploruje logo Pythona](media/sample-v2-code/logo_python.png)</p>Python | Internetowy interfejs API (usługa) [języka Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Na tym obrazie jest wyświetlane logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Web API (usługa) [NodeJS i passport-azure-ad za pomocą w imieniu](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Inne przykłady programu Microsoft Graph

Aby dowiedzieć się więcej o [przykładach](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) i samouczkach przedstawiających różne wzorce użycia interfejsu API programu Microsoft Graph, w tym uwierzytelnianie za pomocą usługi Azure AD, zobacz [przykłady & samouczki microsoft Graph Community](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Zobacz też

- [Przewodnik dla deweloperów usługi Azure Active Directory (w wersji 1.0)](../azuread-dev/v1-overview.md)
- [Koncepcyjne i referencyjne interfejsu API programu Microsoft Graph](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)
