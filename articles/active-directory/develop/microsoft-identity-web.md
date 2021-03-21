---
title: Omówienie biblioteki uwierzytelniania w sieci Web firmy Microsoft
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o usłudze Microsoft Identity Web, uwierzytelnianiu i bibliotece autoryzacji dla ASP.NET Core aplikacji, które integrują się z Azure Active Directory, Azure AD B2C Microsoft Graph i innymi interfejsami API sieci Web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/09/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8bc9773fa7e1b3db2e249e803f4d2a3cf39edb48
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100102550"
---
# <a name="microsoft-identity-web-authentication-library"></a>Biblioteka uwierzytelniania w sieci Web firmy Microsoft

Microsoft Identity Web to zestaw bibliotek ASP.NET Core, które upraszczają Dodawanie obsługi uwierzytelniania i autoryzacji do aplikacji sieci Web i interfejsów API sieci Web integracji z platformą tożsamości firmy Microsoft. Zapewnia ona wygodną warstwę interfejsu API obejmującą jedną powierzchnię, która łączy się ze sobą ASP.NET Core, jej oprogramowania pośredniczącego uwierzytelniania i [biblioteki uwierzytelniania firmy Microsoft (MSAL) dla platformy .NET](https://github.com/azuread/microsoft-authentication-library-for-dotnet).

## <a name="supported-application-scenarios"></a>Obsługiwane scenariusze aplikacji

Jeśli tworzysz ASP.NET Core aplikacje sieci Web lub interfejsy API sieci Web i chcesz używać Azure Active Directory (Azure AD) lub Azure AD B2C do zarządzania tożsamościami i dostępem (IAM), zalecamy korzystanie z sieci Web Microsoft Identity dla wszystkich następujących scenariuszy:

- [Aplikacja internetowa, która loguje użytkowników](scenario-web-app-sign-user-overview.md)
- [Aplikacja sieci Web, która loguje użytkowników i wywołuje interfejs API sieci Web w ich imieniu](scenario-web-app-call-api-overview.md)
- [Chroniony internetowy interfejs API, do którego dostęp mają tylko uwierzytelnieni użytkownicy](scenario-protected-web-api-overview.md)
- [Chroniony internetowy interfejs API, który wywołuje inny (podrzędny) interfejs API sieci Web w imieniu zalogowanego użytkownika](scenario-web-api-call-api-overview.md)

## <a name="get-the-library"></a>Pobierz bibliotekę

Możesz uzyskać dostęp do usługi Microsoft Identity Web z pakietu [NuGet](#nuget), [szablonów projektów platformy .NET Core](#project-templates)i usługi [GitHub](#github).

#### <a name="nuget"></a>NuGet

Microsoft Identity Web jest dostępna w pakiecie NuGet jako zestaw pakietów, które zapewniają modularną funkcjonalność w zależności od potrzeb aplikacji. Użyj narzędzia interfejsu wiersza polecenia platformy .NET `dotnet add` lub **Menedżera pakietów NuGet** programu Visual Studio, aby zainstalować pakiety odpowiednie dla Twojego projektu:

- [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) — pakiet główny. Wymagane przez wszystkie aplikacje korzystające z sieci Web Microsoft Identity.
- [Microsoft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) — opcjonalne. Dodaje interfejs użytkownika do logowania i wylogowywania użytkowników oraz skojarzony kontroler dla aplikacji sieci Web.
- [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) — opcjonalny. Zapewnia uproszczoną interakcję z interfejsem API Microsoft Graph.
- [Microsoft. Identity. Web. MicrosoftGraphBeta](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraphBeta) — opcjonalny. Zapewnia uproszczoną interakcję z [punktem końcowym](/graph/api/overview?view=graph-rest-beta&preserve-view=true)Microsoft Graph API beta.

#### <a name="project-templates"></a>Szablony projektów

Szablony projektu sieci Web Microsoft Identity są zawarte w programie .NET 5,0 i są dostępne do pobrania dla projektów ASP.NET Core 3,1.

Jeśli używasz ASP.NET Core 3,1, zainstaluj szablony przy użyciu interfejsu wiersza polecenia platformy .NET:

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::1.0.0
```

Na poniższym diagramie przedstawiono ogólny widok obsługiwanych typów aplikacji oraz ich odpowiednie argumenty:

:::image type="content" source="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" lightbox="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" alt-text="Diagram dostępnych szablonów projektów interfejsu wiersza polecenia platformy .NET dla usługi Microsoft Identity Web":::
<br /><sup><b>*</b></sup>`MultiOrg`program nie jest obsługiwany w programie `webapi2` , ale można go włączyć w *appsettings.jsna* podstawie ustawienia dzierżawcy na `common` lub`organizations`
<br /><sup><b>**</b></sup>`--calls-graph`nie jest obsługiwane w przypadku Azure AD B2C

To przykładowe polecenie interfejsu wiersza polecenia platformy .NET, które zostało wykonane z naszego [samouczka dotyczącego serwera Blazor](tutorial-blazor-server.md), generuje nowy projekt serwera Blazor, który zawiera odpowiednie pakiety i początkowy kod (pokazane wartości zastępcze):

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph --client-id "00000000-0000-0000-0000-000000000000" --tenant-id "11111111-1111-1111-1111-111111111111" --output my-blazor-app
```

#### <a name="github"></a>GitHub

Microsoft Identity Web to projekt Open Source hostowany w witrynie GitHub: <a href="https://github.com/AzureAD/microsoft-identity-web" target="_blank">AzureAD/Microsoft-Identity-Web</a>

[Witryna typu wiki repozytorium](https://github.com/AzureAD/microsoft-identity-web/wiki) zawiera dodatkową dokumentację i jeśli potrzebna jest pomoc lub odkryje usterkę, można [zgłosić problem](https://github.com/AzureAD/microsoft-identity-web/issues).

## <a name="features"></a>Funkcje

Program Microsoft Identity Web zawiera kilka funkcji, które nie są dostępne w przypadku używania domyślnych szablonów projektów ASP.NET 3,1.

| Cecha                                                                                  | ASP.NET Core 3,1                                                     | Microsoft Identity Web                                                                                  |
|------------------------------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| [Logowanie użytkowników](scenario-web-app-sign-user-app-configuration.md) w usłudze Web Apps             | <li>Konta służbowe<li>Tożsamości społecznościowe (z Azure AD B2Cem) | <li>Konta służbowe<li>Osobiste konta Microsoft<li>Tożsamości społecznościowe (z Azure AD B2Cem)     |
| [Ochrona internetowych interfejsów API](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) | <li>Konta służbowe<li>Tożsamości społecznościowe (z Azure AD B2Cem) | <li>Konta służbowe<li>Osobiste konta Microsoft<li>Tożsamości społecznościowe (z Azure AD B2Cem)     |
| Walidacja wystawcy w aplikacjach wielodostępnych                                                   | Nie                                                                   | Tak, dla [wszystkich chmur](authentication-national-cloud.md) i [Azure AD B2C](../../active-directory-b2c/index.yml) |
| Aplikacja sieci Web/interfejs API [wywołania programu Microsoft Graph] [scenariusz-API-Call-Graph]                             | Nie                                                                   | Tak                                                                                                     |
| Aplikacja sieci Web/interfejs API [wywołania interfejsu Web API] [scenariusz-API-Call-API]                                       | Nie                                                                   | Tak                                                                                                     |
| Obsługuje poświadczenia certyfikatu                                                         | Nie                                                                   | Tak, w tym Azure Key Vault                                                                          |
| Udzielanie przyrostowej zgody i dostępu warunkowego w usłudze Web Apps                           | Nie                                                                   | Tak, na stronach MVC, Razor i Blazor                                                                    |
| Certyfikaty szyfrowania tokenu w interfejsach API sieci Web                                                | Nie                                                                   | Tak                                                                                                     |
| [Zakres/weryfikacja roli aplikacji] [scenariusz-API — Walidacja] w interfejsach API sieci Web                        | Nie                                                                   | Tak                                                                                                     |
| `WWW-Authenticate` Generowanie nagłówka w interfejsach API sieci Web                                         | Nie                                                                   | Tak                                                                                                     |

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić witrynę sieci Web firmy Microsoft dotyczącą tożsamości, wypróbuj nasz samouczek serwera Blazor:

[Samouczek: Tworzenie aplikacji serwera Blazor, która używa platformy tożsamości firmy Microsoft do uwierzytelniania](tutorial-blazor-server.md)

Witryna sieci Web Microsoft Identity webwiki w witrynie GitHub zawiera obszerną dokumentację referencyjną dla różnych aspektów biblioteki. Na przykład użycie certyfikatu, propozycję zgody i dostęp warunkowy można znaleźć tutaj:

- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates" target="_blank">Używanie certyfikatów z firmą Microsoft. Identity. Web</a> (GitHub)
- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Managing-incremental-consent-and-conditional-access" target="_blank">Prowyrażenie przyrostowe i dostęp warunkowy</a> (GitHub)

<!-- LINKS -->
<!--  [miw-certs]: microsoft-identity-web-certificates.md  -->
<!--  [miw-certs-decrypt]: microsoft-identity-web-certificates.md#decryption-certificates  -->
<!--  [miw-inc-consent-ca-header]: microsoft-identity-web-consent-conditional-access.md#handling-incremental-consent-or-conditional-access-in-web-apis  -->
<!--  [miw-inc-consent-ca]: microsoft-identity-web-consent-conditional-access.md  -->
[scenariusz-API-Call-API]: scenariusz-Web-API-Call-API-Call-API. MD # Option-1-Call-Microsoft-Graph-with-SDK  
[scenariusz-API-Call-Graph]: scenariusz-Web-API-Call-API-Call-API. MD # Option-1-Call-Microsoft-Graph-with-SDK  
[scenariusz-API-Validation]: scenario-protected-web-api-verification-scope-app-roles.md  
