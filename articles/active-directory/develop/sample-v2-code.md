---
title: Przykłady kodu dla platformy tożsamości firmy Microsoft
description: Zawiera indeks dostępnych przykładów kodu platformy Microsoft Identity platform (Endpoint), zorganizowanych według scenariusza.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 06/01/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 948eea168b071813cf20e03d3a0744a824fd4c8c
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91773896"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Przykłady kodu platformy tożsamości firmy Microsoft (punkt końcowy v 2.0)

Platformy tożsamości firmy Microsoft można używać do:

- Dodawanie uwierzytelniania i autoryzacji do aplikacji sieci Web i interfejsów API sieci Web.
- Wymagaj tokenu dostępu w celu uzyskania dostępu do chronionego internetowego interfejsu API.

W tym artykule krótko opisano i przedstawiono linki do przykładów dla punktu końcowego platformy tożsamości firmy Microsoft. Te przykłady pokazują, jak to zrobiono, a także zawierają fragmenty kodu, których można używać w aplikacjach. Na stronie przykładowy kod znajdziesz szczegółowe tematy dotyczące pliku Readme, które ułatwiają wymagania, instalację i konfigurację. Komentarze w kodzie pomagają zrozumieć sekcje krytyczne.

> [!NOTE]
> Jeśli interesuje Cię przykłady w wersji 1.0, zobacz [przykłady kodu usługi Azure AD (punkt końcowy w wersji 1.0)](../azuread-dev/sample-v1-code.md).

Aby zrozumieć podstawowy scenariusz dla każdego typu przykładu, zobacz [typy aplikacji dla punktu końcowego platformy tożsamości firmy Microsoft](v2-app-types.md).

Możesz również współtworzyć przykłady w witrynie GitHub. Aby dowiedzieć się, jak to zrobić, zobacz [Microsoft Azure Active Directory przykłady i dokumentacja](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplikacje jednostronicowe

Te przykłady przedstawiają sposób pisania aplikacji jednostronicowej zabezpieczonej za pomocą platformy tożsamości firmy Microsoft. W tych przykładach użyto jednego z następujących rodzajów MSAL.js.

| Platforma | Opis | Łącze |
| -------- | --------------------- | -------- |
| ![Ten obraz pokazuje logo JavaScript ](media/sample-v2-code/logo_js.png) [javascript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Wywołania SPA Microsoft Graph |[JavaScript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Ten obraz pokazuje logo JavaScript ](media/sample-v2-code/logo_js.png) [javascript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | Wywołania SPA Microsoft Graph przy użyciu przepływu kodu uwierzytelniania w/PKCE |[JavaScript — v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![Ten obraz pokazuje logo JavaScript ](media/sample-v2-code/logo_js.png) [javascript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Wywołania SPA B2C |[B2C-JavaScript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Ten obraz pokazuje logo kątowe ](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL-kątowy)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Wywołania SPA Microsoft Graph  | [Active-Directory-JavaScript-singlepageapp — kątowy](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Ten obraz pokazuje kątowe logo kątowe ](media/sample-v2-code/logo_angular.png) [(MSAL-kątowy)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Wywołania SPA niestandardowego interfejsu API sieci Web | [MS-Identity-JavaScript-aspnetcore-WebApi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![Ten obraz pokazuje kątowe logo kątowe ](media/sample-v2-code/logo_angular.png) [(MSAL-kątowy)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | Wywołania SPA B2C |[Active-Directory-B2C-JavaScript — Spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![Ten obraz przedstawia reaguje na logo reakcji ](media/sample-v2-code/logo_react.png) [(MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| SPA wywołuje niestandardowy internetowy interfejs API, który z kolei wywołuje wywołania Microsoft Graph  | [MS-Identity-JavaScript-reagować-Spa-dotnetcore-WebAPI-OBO](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![Ten obraz pokazuje kątowe logo kątowe ](media/sample-v2-code/logo_angular.png) [(MSAL-kątowy)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA wywołuje niestandardowy internetowy interfejs API z rolami aplikacji i grupami zabezpieczeń |[MS-Identity-JavaScript-dotnetcore-WebAPI-role-Groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |

## <a name="web-applications"></a>Aplikacje internetowe

W poniższych przykładach przedstawiono aplikacje sieci Web, które logują użytkowników. Niektóre przykłady przedstawiają również Microsoft Graph wywoływania aplikacji lub własnego interfejsu API sieci Web przy użyciu tożsamości użytkownika.

| Platforma | Tylko oznaki użytkowników | Logowanie użytkowników i wywołań Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Ten obraz zawiera logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [Samouczek dotyczący ASP.NET Core logowania użytkowników](https://aka.ms/aspnetcore-webapp-sign-in) | Ten sam przykład w [Microsoft Graph fazie ASP.NET Core aplikacji sieci Web](https://aka.ms/aspnetcore-webapp-call-msgraph) |
| ![Ten obraz pokazuje logo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET — szybki start](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-z ograniczeniami-Scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[MSGraph — szkolenia — aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Ten obraz pokazuje logo języka Java](media/sample-v2-code/logo_java.png)  |                   | [MS-Identity-Java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Ten obraz pokazuje logo języka Python](media/sample-v2-code/logo_python.png)  | [MS-Identity-Python-webapp-Authentication](https://github.com/Azure-Samples/ms-identity-python-flask-webapp-authentication) | [MS-Identity-Python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Ten obraz pokazuje logo języka Ruby](media/sample-v2-code/logo_ruby.png) |                   | [MSGraph — szkolenia — rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Publiczne aplikacje dla komputerów stacjonarnych i mobilnych

W poniższych przykładach przedstawiono publiczne aplikacje klienckie (aplikacje klasyczne lub mobilne), które uzyskują dostęp do interfejsu API Microsoft Graph lub własnego internetowego interfejsu API w imieniu użytkownika. Poza *pulpitem (konsolą) z przykładem Menedżera wam* wszystkie te aplikacje klienckie używają biblioteki uwierzytelniania firmy Microsoft (MSAL).

| Aplikacja kliencka | Platforma | Przepływ/Grant | Microsoft Graph wywołań | Wywołuje ASP.NET Core Web API |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Pulpit (WPF)      | ![Ten obraz pokazuje logo .NET/C#](media/sample-v2-code/logo_NET.png) | [Kod autoryzacji](msal-authentication-flows.md#authorization-code)| [dotnet-Desktop-MSGraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Pulpit (konsola)   | ![Ten obraz pokazuje logo .NET/C# (Desktop)](media/sample-v2-code/logo_NET.png) | [Zintegrowane uwierzytelnianie systemu Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-IWA-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Pulpit (konsola)   | ![Ten obraz pokazuje logo języka Java](media/sample-v2-code/logo_java.png) | [Zintegrowane uwierzytelnianie systemu Windows](msal-authentication-flows.md#integrated-windows-authentication) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Pulpit (konsola)   | ![Ten obraz pokazuje logo .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Nazwa użytkownika/hasło](msal-authentication-flows.md#usernamepassword) |[dotnetcore — wersja 2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Pulpit (konsola) z Menedżera WAM  | ![Ten obraz pokazuje logo .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Interaktywny z [menedżerem kont sieci Web](/windows/uwp/security/web-account-manager) (wam) |[dotnet-Native-platformy UWP-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Pulpit (konsola)   | ![Ten obraz pokazuje logo języka Java](media/sample-v2-code/logo_java.png) | [Nazwa użytkownika/hasło](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Pulpit (konsola)   | ![Ten obraz pokazuje logo języka Python](media/sample-v2-code/logo_python.png) | [Nazwa użytkownika/hasło](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Python-Desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Urządzenia przenośne (Android, iOS, platformy UWP)   | ![Ten obraz pokazuje logo .NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [Kod autoryzacji](msal-authentication-flows.md#authorization-code) |[Xamarin-Native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Urządzenia przenośne (iOS)       | ![Ten obraz przedstawia system iOS/cel-C lub SWIFT](media/sample-v2-code/logo_iOS.png) | [Kod autoryzacji](msal-authentication-flows.md#authorization-code) |[iOS-SWIFT-objc-Native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [iOS-Native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Desktop (macOS)       | macOS | [Kod autoryzacji](msal-authentication-flows.md#authorization-code) |[macOS-SWIFT-objc-Native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobile (Android-Java)   | ![Ten obraz pokazuje logo systemu Android](media/sample-v2-code/logo_Android.png) | [Kod autoryzacji](msal-authentication-flows.md#authorization-code) |  [system Android — Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobile (Android-Kotlin)   | ![Ten obraz pokazuje logo systemu Android](media/sample-v2-code/logo_Android.png) | [Kod autoryzacji](msal-authentication-flows.md#authorization-code) |  [Android — Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Aplikacje demona

W poniższych przykładach przedstawiono aplikację, która uzyskuje dostęp do interfejsu API Microsoft Graph przy użyciu własnej tożsamości (bez użytkownika).

| Aplikacja kliencka | Platforma | Przepływ/Grant | Microsoft Graph wywołań |
| ------------------ | -------- | ---------- | -------------------- |
| Konsola | ![Ten obraz pokazuje logo platformy .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Poświadczenia klienta](msal-authentication-flows.md#client-credentials) | [dotnetcore-demon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Aplikacja internetowa | ![Ten obraz pokazuje logo ASP.NET](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Poświadczenia klienta](msal-authentication-flows.md#client-credentials) | [dotnet-demon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Konsola | ![Ten obraz pokazuje logo języka Java](media/sample-v2-code/logo_java.png) | [Poświadczenia klienta](msal-authentication-flows.md#client-credentials) | [MS-Identity-Java — demon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Konsola | ![Ten obraz pokazuje logo języka Python](media/sample-v2-code/logo_python.png) | [Poświadczenia klienta](msal-authentication-flows.md#client-credentials) | [MS-Identity-Python-demona](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Aplikacje bezobsługowe

Poniższy przykład pokazuje publiczną aplikację kliencką działającą na urządzeniu bez przeglądarki sieci Web. Aplikacja może być narzędziem wiersza polecenia, aplikacją działającą na komputerach z systemem Linux lub Mac lub aplikacją IoT. Przykładowa funkcja umożliwia aplikacji dostęp do interfejsu API Microsoft Graph, w imieniu użytkownika, który loguje się interaktywnie na innym urządzeniu (na przykład na telefonie komórkowym). Ta aplikacja kliencka używa biblioteki uwierzytelniania firmy Microsoft (MSAL).

| Aplikacja kliencka | Platforma | Przepływ/Grant | Microsoft Graph wywołań |
| ------------------ | -------- |  ----------| ---------- |
| Pulpit (konsola)   | ![Ten obraz pokazuje logo .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Przepływ kodu urządzenia](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Pulpit (konsola)   | ![Ten obraz pokazuje logo języka Java](media/sample-v2-code/logo_java.png) | [Przepływ kodu urządzenia](msal-authentication-flows.md#device-code) |[MS-Identity-Java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Pulpit (konsola)   | ![Ten obraz pokazuje logo języka Python](media/sample-v2-code/logo_python.png) | [Przepływ kodu urządzenia](msal-authentication-flows.md#device-code) |[MS-Identity-Python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>Wielodostępne aplikacje SaaS

W poniższych przykładach pokazano, jak skonfigurować aplikację do akceptowania logowań z dowolnej dzierżawy usługi Azure Active Directory (Azure AD). Skonfigurowanie aplikacji w taki sposób, aby była *wielodostępna* , oznacza, że możesz zaoferować aplikację **oprogramowanie jako usługa** (SaaS) w wielu organizacjach, umożliwiając użytkownikom zalogowanie się do aplikacji po dodaniu ich do konta.

| Platforma | Opis | Łącze |
| -------- | --------------------- | -------- |
| ![Ten obraz pokazuje logo JavaScript ](media/sample-v2-code/logo_js.png) [javascript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | Wywołania SPA z wieloma dzierżawcami interfejs API programu Graph |[MS-Identity-JavaScript-skośne-Spa-ASPNET-WebAPI-wielodostępny](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter1) |
| ![Ten obraz pokazuje kątowe logo kątowe ](media/sample-v2-code/logo_angular.png) [(MSAL-kątowy)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | Wiele dzierżawców wywołuje niestandardowy internetowy interfejs API z wieloma dzierżawcami |[MS-Identity-JavaScript-skośne-Spa-ASPNET-WebAPI-wielodostępny](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![Ten obraz przedstawia ASP.NET Core logo ](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core wywołań aplikacji sieci Web MVC interfejs API programu Graph |[Active-Directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![Ten obraz przedstawia ASP.NET Core logo ](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core aplikacji sieci Web MVC ASP.NET Core interfejs API sieci Web |[Active-Directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>Interfejsy API sieci Web

W poniższych przykładach pokazano, jak chronić internetowy interfejs API za pomocą punktu końcowego platformy tożsamości firmy Microsoft oraz jak wywołać podrzędny interfejs API z internetowego interfejsu API.

| Platforma | Przykład |
| -------- | ------------------- |
| ![Ten obraz zawiera logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core Web API (usługa) elementu [dotnet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Ten obraz pokazuje logo ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Internetowy interfejs API (usługa) [MS-Identity-ASPNET-WebAPI-OnBehalfOf](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Ten obraz pokazuje logo języka Java](media/sample-v2-code/logo_java.png) | Interfejs API sieci Web (usługa) [MS-Identity-Java-WebAPI](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![Ten obraz zawiera logo Node.js](media/sample-v2-code/logo_nodejs.png) | Internetowy interfejs API (usługa) [Active-Directory-JavaScript-NodeJS-WebAPI-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![Ten obraz zawiera logo Node.js](media/sample-v2-code/logo_nodejs.png) | B2C Web API (usługa) elementu [Active-Directory-B2C-JavaScript-NodeJS-WebAPI](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure Functions jako interfejsy API sieci Web

W poniższych przykładach pokazano, jak chronić funkcję platformy Azure przy użyciu programu HttpTrigger i uwidaczniać internetowy interfejs API za pomocą punktu końcowego platformy tożsamości firmy Microsoft oraz jak wywołać podrzędny interfejs API z internetowego interfejsu API.

| Platforma | Przykład |
| -------- | ------------------- |
| ![Ten obraz zawiera logo ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core Web API (usługa) Azure Function of [dotnet-Native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Ten obraz zawiera logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Web API (Service) of [NodeJS i Passport-Azure-AD](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Ten obraz pokazuje logo języka Python](media/sample-v2-code/logo_python.png)</p>Python | Internetowy interfejs API (usługa) języka [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Ten obraz zawiera logo Node.js](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Internetowy interfejs API (usługa) [NodeJS i paszport-Azure-AD przy użyciu w imieniu](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Inne przykłady Microsoft Graph

Aby dowiedzieć się więcej na temat [przykładów](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) i samouczków demonstrujących różne wzorce użycia dla interfejsu API Microsoft Graph, w tym uwierzytelniania za pomocą usługi Azure AD, zobacz [Microsoft Graph przykłady społeczności & samouczków](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Zobacz też

- [Przewodnik dewelopera Azure Active Directory (v 1.0)](../azuread-dev/v1-overview.md)
- [Informacje koncepcyjne i informacje o interfejsie API Microsoft Graph](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta)
