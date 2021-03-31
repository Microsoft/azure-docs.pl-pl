---
title: Azure Active Directory biblioteki uwierzytelniania | Microsoft Docs
description: Biblioteka Azure AD Authentication Library (ADAL) umożliwia deweloperom aplikacji klienckich łatwe uwierzytelnianie użytkowników w chmurze lub Active Directory lokalnych (AD), a następnie uzyskanie tokenów dostępu w celu zabezpieczania wywołań interfejsu API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: reference
ms.workload: identity
ms.date: 12/01/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 7b6ffd885e1be2dd59cea87cacd6e5fd5e0f8f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861174"
---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory Authentication Libraries (Biblioteki Azure Active Directory Authentication Libraries)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Biblioteka Azure Active Directory Authentication Library (ADAL) v 1.0 umożliwia deweloperom aplikacji uwierzytelnianie użytkowników w chmurze lub lokalnych Active Directory (AD) oraz uzyskiwanie tokenów zabezpieczających wywołania interfejsów API. Biblioteka ADAL ułatwia deweloperom korzystanie z takich funkcji, jak:

- Konfigurowalna pamięć podręczna tokenów, która przechowuje tokeny dostępu i odświeża tokeny
- Automatyczne odświeżanie tokenów po wygaśnięciu tokenu dostępu i dostępności tokenu odświeżania
- Obsługa wywołań metod asynchronicznych

> [!NOTE]
> Szukasz bibliotek usługi Azure AD v 2.0 (MSAL)? Zaewidencjonuj [Przewodnik po bibliotece MSAL](../develop/reference-v2-libraries.md).
>
>

## <a name="microsoft-supported-client-libraries"></a>Biblioteki klienckie obsługiwane przez firmę Microsoft

| Platforma | Biblioteka | Pobierz | Kod źródłowy | Przykład | Odwołanie
| --- | --- | --- | --- | --- | --- |
| Klient .NET, Sklep Windows, platformy UWP, Xamarin iOS i Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Aplikacja klasyczna](../develop/quickstart-v2-windows-desktop.md) |[Odwołanie](/dotnet/api/microsoft.identitymodel.clients.activedirectory) |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Aplikacja jednostronicowa](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[Aplikacja systemu iOS](../develop/quickstart-v2-ios.md) | [Odwołanie](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Maven](https://search.maven.org/search?q=g:com.microsoft.aad+AND+a:adal&core=gav) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplikacja systemu Android](../develop/quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Aplikacja internetowa środowiska Node.js](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Odwołanie](/javascript/api/overview/azure/activedirectory) |
| Java |ADAL4J |[Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Aplikacje internetowe w języku Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Odwołanie](https://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Aplikacja sieci Web języka Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Odwołanie](https://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Biblioteki serwerów obsługiwane przez firmę Microsoft

| Platforma | Biblioteka | Pobierz | Kod źródłowy | Przykład | Dokumentacja
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN dla AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.ActiveDirectory) |[Aplikacja MVC](../develop/quickstart-v2-aspnet-webapp.md) | |
| .NET |OWIN dla OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.OpenIdConnect) |[Aplikacja sieci Web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.WsFederation) |[Aplikacja sieci Web MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Rozszerzenia protokołu tożsamości dla programu .NET 4,5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Procedura obsługi JWT dla programu .NET 4,5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Paszport usługi Azure AD |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Interfejs API sieci Web](../develop/authentication-flows-app-scenarios.md)| |

## <a name="scenarios"></a>Scenariusze

Poniżej przedstawiono trzy typowe scenariusze używania biblioteki ADAL w kliencie, który uzyskuje dostęp do zasobu zdalnego:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Uwierzytelnianie użytkowników natywnej aplikacji klienckiej działającej na urządzeniu

W tym scenariuszu deweloper ma klienta mobilnego lub aplikację komputerową, która musi uzyskać dostęp do zasobu zdalnego, takiego jak internetowy interfejs API. Internetowy interfejs API nie zezwala na wywołania anonimowe i musi być wywołany w kontekście uwierzytelnionego użytkownika. Internetowy interfejs API jest wstępnie skonfigurowany do zaufania tokeny dostępu wystawione przez określoną dzierżawę usługi Azure AD. Usługa Azure AD została wstępnie skonfigurowana w celu wystawiania tokenów dostępu dla tego zasobu. Aby wywołać internetowy interfejs API z poziomu klienta, programista korzysta z biblioteki ADAL, aby ułatwić uwierzytelnianie w usłudze Azure AD. Najbezpieczniejszym sposobem korzystania z biblioteki ADAL jest renderowanie interfejsu użytkownika w celu zbierania poświadczeń użytkownika (renderowane jako okno przeglądarki).

Biblioteka ADAL ułatwia uwierzytelnianie użytkownika, uzyskiwanie tokenu dostępu i odświeżanie tokenu z usługi Azure AD, a następnie wywoływanie interfejsu API sieci Web przy użyciu tokenu dostępu.

Przykładowy kod, który demonstruje ten scenariusz przy użyciu uwierzytelniania w usłudze Azure AD, znajduje [się w temacie Natywna aplikacja WPF Client do interfejsu API sieci Web](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Uwierzytelnianie poufnej aplikacji klienckiej działającej na serwerze sieci Web

W tym scenariuszu deweloper ma działającą aplikację na serwerze, który musi uzyskać dostęp do zasobu zdalnego, takiego jak internetowy interfejs API. Internetowy interfejs API nie zezwala na wywołania anonimowe, dlatego musi być wywoływana z autoryzowanej usługi. Internetowy interfejs API jest wstępnie skonfigurowany do zaufania tokeny dostępu wystawione przez określoną dzierżawę usługi Azure AD. Usługa Azure AD została wstępnie skonfigurowana w celu wystawiania tokenów dostępu dla tego zasobu z poświadczeniami klienta (identyfikator klienta i klucz tajny). Biblioteka ADAL ułatwia uwierzytelnianie usługi za pomocą usługi Azure AD zwracającej token dostępu, który może służyć do wywoływania internetowego interfejsu API. Biblioteka ADAL obsługuje również zarządzanie okresem istnienia tokenu dostępu przez buforowanie i odnawianie go w razie potrzeby. Przykładowy kod, który demonstruje ten scenariusz, znajduje [się w temacie Aplikacja konsolowa demona do internetowego interfejsu API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Uwierzytelnianie poufnej aplikacji klienckiej działającej na serwerze w imieniu użytkownika

W tym scenariuszu deweloper ma aplikację sieci Web działającą na serwerze, który musi uzyskać dostęp do zasobu zdalnego, takiego jak internetowy interfejs API. Internetowy interfejs API nie zezwala na wywołania anonimowe, dlatego musi być wywoływana z autoryzowanej usługi w imieniu uwierzytelnionego użytkownika. Internetowy interfejs API jest wstępnie skonfigurowany do zaufania tokenów dostępu wystawionych przez określoną dzierżawę usługi Azure AD, a usługa Azure AD jest wstępnie skonfigurowana w celu wystawiania tokenów dostępu dla tego zasobu z poświadczeniami klienta. Po uwierzytelnieniu użytkownika w aplikacji sieci Web aplikacja może uzyskać kod autoryzacji dla użytkownika z usługi Azure AD. Aplikacja sieci Web może następnie użyć biblioteki ADAL w celu uzyskania tokenu dostępu i tokenu odświeżania w imieniu użytkownika przy użyciu kodu autoryzacji i poświadczeń klienta skojarzonych z aplikacją z usługi Azure AD. Gdy aplikacja sieci Web jest w posiadaniu tokenu dostępu, może wywołać internetowy interfejs API do momentu wygaśnięcia tokenu. Po wygaśnięciu tokenu aplikacja sieci Web może użyć biblioteki ADAL do uzyskania nowego tokenu dostępu przy użyciu tokenu odświeżania, który został wcześniej odebrany. Przykładowy kod, który demonstruje ten scenariusz, znajduje się w temacie [Native Client to Web API to Web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Zobacz też

- [Przewodnik dewelopera Azure Active Directory](v1-overview.md)
- [Scenariusze uwierzytelniania dla usługi Azure Active Directory](v1-authentication-scenarios.md)
- [Przykłady kodu Azure Active Directory](sample-v1-code.md)
