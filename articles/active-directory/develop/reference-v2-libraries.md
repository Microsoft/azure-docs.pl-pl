---
title: Biblioteki uwierzytelniania platformy tożsamości firmy Microsoft
description: Zgodne biblioteki klienckie i biblioteki pośredniczące serwera oraz powiązane biblioteki, źródła i przykładowe linki dla punktu końcowego platformy tożsamości firmy Microsoft.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 072407ae64a8dc6083ffdca23ba9c758b2b35868
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91257815"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Biblioteki uwierzytelniania platformy tożsamości firmy Microsoft

[Punkt końcowy platformy tożsamości firmy Microsoft](../azuread-dev/azure-ad-endpoint-comparison.md) obsługuje standardowe w branży protokoły OAuth 2,0 i openid connect Connect 1,0. Biblioteka Microsoft Authentication Library (MSAL) została zaprojektowana do pracy z punktem końcowym platformy tożsamości firmy Microsoft. Można również użyć bibliotek Open Source, które obsługują uwierzytelnianie OAuth 2,0 i OpenID Connect Connect 1,0.

Zalecamy używanie bibliotek pisanych przez ekspertów domen protokołu, którzy przestrzegają metodologii cyklu projektowania zabezpieczeń (SDL). Takie metodologie obejmują te [, które firma Microsoft stosuje][Microsoft-SDL]. Jeśli kod jest dostępny dla protokołów, należy postępować zgodnie z metodologią, taką jak Microsoft SDL. Zwróć szczególną uwagę na kwestie dotyczące zabezpieczeń w specyfikacjach standardów dla każdego protokołu.

> [!NOTE]
> Szukasz biblioteki uwierzytelniania Azure Active Directory (ADAL)? Zapoznaj się z [przewodnikiem biblioteki ADAL](../azuread-dev/active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Typy bibliotek

Punkt końcowy platformy tożsamości firmy Microsoft współpracuje z dwoma typami bibliotek:

* **Biblioteki klienckie**: natywni klienci i serwery używają bibliotek klienckich, aby uzyskiwać tokeny dostępu do wywoływania zasobu, takiego jak Microsoft Graph.
* **Biblioteki oprogramowania pośredniczącego serwera**: aplikacje sieci Web używają bibliotek oprogramowania serwerowego do logowania użytkownika. Interfejsy API sieci Web używają bibliotek oprogramowania pośredniczącego serwera do weryfikowania tokenów wysyłanych przez natywnych klientów lub przez inne serwery.

## <a name="library-support"></a>Obsługa biblioteki

Biblioteki są dostępne w dwóch kategoriach pomocy technicznej:

* **Obsługiwane przez firmę Microsoft**: Firma Microsoft udostępnia poprawki dla tych bibliotek i w związku z tym ma zakończono staranne podwyższenie do tych bibliotek.
* **Zgodne**: Firma Microsoft przetestowała te biblioteki w podstawowych scenariuszach i potwierdziła, że współpracują z punktem końcowym platformy tożsamości firmy Microsoft. Firma Microsoft nie udostępnia poprawek dla tych bibliotek i nie przeprowadzono przeglądu tych bibliotek. Problemy i żądania funkcji powinny być kierowane do projektu open-source biblioteki.

Aby zapoznać się z listą bibliotek, które współpracują z punktem końcowym platformy tożsamości firmy Microsoft, zobacz następujące sekcje.

## <a name="microsoft-supported-client-libraries"></a>Biblioteki klienckie obsługiwane przez firmę Microsoft

Użyj bibliotek uwierzytelniania klienta, aby uzyskać token do wywoływania chronionego internetowego interfejsu API.

| Platforma | Biblioteka | Pobierz | Kod źródłowy | Przykład | Tematy pomocy | Dokument koncepcyjny | Harmonogram działania |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [Aplikacja jednostronicowa](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Odwołanie](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/) | [Dokumentacja koncepcji](msal-overview.md)| [Harmonogram działania](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
![Angular](media/sample-v2-code/logo_angular.png) | MSAL kątowy | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | [SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) | [Odwołanie](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-angular/) | [Dokumentacja koncepcji](msal-overview.md) | [Harmonogram działania](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![Platforma UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplikacja klasyczna](/windows/apps/desktop/) | [MSAL.NET](/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Dokumentacja koncepcji](msal-overview.md) | [Harmonogram działania](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Samples](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [Witryna Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Harmonogram działania](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java | [Maven](https://search.maven.org/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Samples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [Odwołanie](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Witryna Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [Harmonogram działania](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS i macOS | MSAL iOS i macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [aplikacja systemu iOS](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc), [aplikacja macOS](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [Odwołanie](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [Dokumentacja koncepcji](msal-overview.md) | |
|![System Android/Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [Repozytorium centralne](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplikacja systemu Android](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Dokumentacja koncepcji](msal-overview.md) |[Harmonogram działania](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Biblioteki pośredniczące serwera obsługiwane przez firmę Microsoft

Korzystaj z bibliotek oprogramowania pośredniczącego, aby chronić aplikacje sieci Web i interfejsy API sieci Web. Aplikacje sieci Web lub interfejsy API sieci Web, które są zapisywane z ASP.NET lub ASP.NET Core używają bibliotek oprogramowania pośredniczącego.

| Platforma | Biblioteka | Pobierz | Kod źródłowy | Przykład | Dokumentacja
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | Zabezpieczenia ASP.NET |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[Aplikacja MVC](quickstart-v2-aspnet-webapp.md) |[Wykaz interfejsów API platformy ASP.NET](/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| Rozszerzenia IdentityModel dla platformy .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [Aplikacja MVC](quickstart-v2-aspnet-webapp.md) |[Odwołanie](/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Paszport usługi Azure AD |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Aplikacja internetowa](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Biblioteki obsługiwane przez firmę Microsoft według systemu operacyjnego/języka

W przypadku obsługiwanych systemów operacyjnych i języków, mapowanie jest następujące:

| Platforma    | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET Core, MSAL.Net (.NET PD, rdzeń, platformy UWP)| ASP.NET Core, MSAL.Net (.NET Core) | ASP.NET Core, MSAL.Net (macOS)       | MSAL.Net (Xamarin. iOS) | MSAL.Net (Xamarin. Android)|
| Swift <br> Objective-C |            |            | [Biblioteka MSAL dla systemów iOS i macOS](msal-overview.md) | [Biblioteka MSAL dla systemów iOS i macOS](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j | msal4j | msal4j | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL Python | MSAL Python | MSAL Python |
| ![Node.Js](media/sample-v2-code/logo_nodejs.png) Node.JS | Paszport. Node | Paszport. Node | Paszport. Node |

Zobacz również [scenariusze według obsługiwanych platform i języków](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>Zgodne biblioteki klienckie

| Platforma | Nazwa biblioteki | Przetestowana wersja | Kod źródłowy | Przykład |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | 1.13.5 wersja |[Hello.js](https://github.com/MrSwitch/hello.js) |[HASŁA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
|![Vue](media/sample-v2-code/logo_vue.png)|[VUE MSAL](https://github.com/mvertopoulos/vue-msal) | 3.0.3 wersja |[VUE — msal](https://github.com/mvertopoulos/vue-msal) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Kod w języku Java](https://github.com/scribejava/scribejava) | [3.2.0 wersja](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Biblioteka GLUU OpenID Connect Connect](https://github.com/GluuFederation/oxAuth) | [3.0.2 wersja](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Biblioteka GLUU OpenID Connect Connect](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Żądania — OAuthlib](https://github.com/requests/requests-oauthlib) | [1.2.0 wersja](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Żądania — OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [OpenID Connect — klient](https://github.com/panva/node-openid-client) | [2.4.5 wersja](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [OpenID Connect — klient](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [PHP League OAuth2 — klient](https://github.com/thephpleague/oauth2-client) | [Wersja 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [OAuth2 — klient](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth-OAuth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS, macOS, & Android  | [Reagowanie na natywną autoryzację aplikacji](https://github.com/FormidableLabs/react-native-app-auth) | [4.2.0 wersja](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Reagowanie na natywną autoryzację aplikacji](https://github.com/FormidableLabs/react-native-app-auth) | |

W przypadku dowolnej biblioteki zgodnej ze standardami można użyć punktu końcowego platformy tożsamości firmy Microsoft. Ważne jest, aby wiedzieć, gdzie warto uzyskać pomoc techniczną:

* W przypadku problemów i nowych żądań funkcji w kodzie biblioteki należy skontaktować się z właścicielem biblioteki.
* W przypadku problemów i nowych żądań funkcji w implementacji protokołu po stronie usługi należy skontaktować się z firmą Microsoft.
* Prześlij [żądanie funkcji](https://feedback.azure.com/forums/169401-azure-active-directory) w celu uzyskania dodatkowych funkcji, które mają być widoczne w protokole.
* [Utwórz żądanie pomocy technicznej](../../azure-portal/supportability/how-to-create-azure-support-request.md) , jeśli znajdziesz problem polegający na tym, że punkt końcowy platformy tożsamości firmy Microsoft nie jest zgodny z uwierzytelnianiem OAuth 2,0 lub openid connect Connect 1,0.

## <a name="related-content"></a>Zawartość pokrewna

Aby uzyskać więcej informacji na temat punktu końcowego platformy tożsamości firmy Microsoft, zobacz [Omówienie platformy tożsamości firmy Microsoft][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: ./tutorial-v2-windows-desktop.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: ./tutorial-v2-asp-webapp.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
