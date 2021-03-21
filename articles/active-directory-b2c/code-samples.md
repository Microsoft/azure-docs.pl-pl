---
title: Przykłady kodu usługi Azure Active Directory B2C | Microsoft Docs
description: Przykłady kodu mobilnych, klasycznych, internetowych i jednostronicowych aplikacji usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b09587d90024a8c376be8b0d93f7ef7b6cc51a1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008488"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Przykłady kodu usługi Azure Active Directory B2C

Poniższe tabele zawierają linki do przykładów dla aplikacji systemu iOS i Android oraz platform .NET i Node.js.

## <a name="mobile-and-desktop-apps"></a>Aplikacje mobilne i klasyczne

| Sample | Opis |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | Przykład dla systemu iOS w języku Swift, który uwierzytelnia użytkowników usługi Azure AD B2C i wywołuje interfejs API przy użyciu protokołu OAuth 2.0 |
| [android-native-msal](https://github.com/Azure-Samples/ms-identity-android-java#b2cmodefragment-class) | Prosta aplikacja systemu Android przedstawiająca sposób użycia biblioteki MSAL do uwierzytelniania użytkowników za pośrednictwem usługi Azure Active Directory B2C oraz do uzyskiwania dostępu do internetowego interfejsu API z tokenami wynikowymi. |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | Przykład pokazujący, jak można użyć biblioteki innej firmy w celu utworzenia aplikacji dla systemu iOS w ramach celu, która uwierzytelnia użytkowników tożsamości firmy Microsoft w naszej usłudze tożsamości Azure AD B2C. |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | Przykład pokazujący, jak można użyć biblioteki innej firmy do tworzenia aplikacji systemu Android, która uwierzytelnia użytkowników tożsamości firmy Microsoft w naszej usłudze tożsamości B2C i wywołuje internetowy interfejs API przy użyciu tokenów dostępu OAuth 2,0. |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | Przykład przedstawiający sposób użycia aplikacji klasycznej systemu Windows na platformie .NET (WPF) do logowania użytkownika za pomocą usługi Azure AD B2C, uzyskiwania tokenu dostępu przy użyciu platformy MSAL.NET i wywoływania interfejsu API. |
| [xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Prosta aplikacja Xamarin Forms przedstawiająca sposób użycia biblioteki MSAL do uwierzytelniania użytkowników za pośrednictwem usługi Azure Active Directory B2C oraz do uzyskiwania dostępu do internetowego interfejsu API z tokenami wynikowymi. |

## <a name="web-apps-and-apis"></a>Aplikacje internetowe i interfejsy API

| Sample | Opis |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | Połączony przykład dotyczący internetowej aplikacji platformy .NET, która wywołuje internetowy interfejs API platformy .NET, a obydwa rozwiązania są zabezpieczane przy użyciu usługi Azure AD B2C. |
| [dotnetcore-webapp-openidconnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C) | Aplikacja sieci Web ASP.NET Core, która używa programu OpenID Connect Connect do logowania użytkowników w programie Azure AD B2C. |
| [dotnetcore-webapp-msal-API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C) | Internetowa aplikacja platformy ASP.NET Core, która może zalogować użytkownika za pomocą usługi Azure AD B2C, uzyskać token dostępu przy użyciu platformy MSAL.NET i wywołać interfejs API. |
| [openidconnect-nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | Aplikacja Node.js, która zapewnia szybki i łatwy sposób konfigurowania aplikacji internetowej w usłudze Express przy użyciu protokołu OpenID Connect. |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | Niewielki internetowy interfejs API środowiska node.js interfejsu API dla usługi Azure AD B2C, który pokazuje, jak chronić internetowy interfejs API i akceptować tokeny dostępu B2C przy użyciu biblioteki passport.js. |
| [MS-Identity-Python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/README_B2C.md) | DoB2C się, jak zintegrować platformę tożsamości firmy Microsoft z aplikacją sieci Web w języku Python.  |

## <a name="single-page-apps"></a>Aplikacje jednostronicowe

| Sample | Opis |
|--------| ----------- |
| [MS-Identity-JavaScript — samouczek](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/2-call-api-b2c) | Aplikacja jednostronicowa wywołująca internetowy interfejs API. Uwierzytelnianie odbywa się z Azure AD B2C przy użyciu reakcji MSAL. Ten przykład używa przepływu kodu autoryzacji z PKCE. |
| [MS-Identity-B2C-JavaScript-Spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) | Aplikacja jednostronicowa wywołująca internetowy interfejs API. Uwierzytelnianie odbywa się z Azure AD B2C przy użyciu MSAL.js. Ten przykład używa przepływu kodu autoryzacji z PKCE. |
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | Aplikacja jednostronicowa wywołująca internetowy interfejs API. Uwierzytelnianie odbywa się z Azure AD B2C przy użyciu MSAL.js. Ten przykład używa przepływu niejawnego.|
| [JavaScript — NodeJS — zarządzanie](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter1) | Wywołanie aplikacji jednostronicowej (SPA) Microsoft Graph do zarządzania użytkownikami w katalogu B2C. Uwierzytelnianie odbywa się z Azure AD B2C przy użyciu MSAL.js. Ten przykład używa przepływu kodu autoryzacji z PKCE.|

## <a name="consoledaemon-apps"></a>Aplikacje konsolowe/demona

| Sample | Opis |
|--------| ----------- |
| [JavaScript — NodeJS — zarządzanie](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter2) | Aplikacja Node.js i program Express Console DAEMON wywołująca Microsoft Graph z własną tożsamością do zarządzania użytkownikami w katalogu B2C. Uwierzytelnianie odbywa się z Azure AD B2C przy użyciu węzła MSAL. Ten przykład używa przepływu kodu autoryzacji.|
| [dotnetcore — B2C — Zarządzanie kontem](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) | Aplikacja konsolowa platformy .NET Core wywołuje Microsoft Graph z własną tożsamością do zarządzania użytkownikami w katalogu B2C. Uwierzytelnianie odbywa się z Azure AD B2C przy użyciu MSAL.NET. Ten przykład używa przepływu kodu autoryzacji.|

## <a name="saml-test-application"></a>Aplikacja testowa SAML

| Sample | Opis |
|--------| ----------- |
| [SAML-Sp-Tester](https://github.com/azure-ad-b2c/saml-sp-tester/tree/master/source-code) | Aplikacja testowa SAML do testowania Azure AD B2C skonfigurowana do działania jako dostawca tożsamości SAML. |

## <a name="api-connectors"></a>Łączniki interfejsu API

W poniższych tabelach przedstawiono linki do przykładów kodu służących do używania interfejsów API sieci Web w przepływach [interfejsu API](api-connectors-overview.md).

### <a name="azure-function-quickstarts"></a>Przewodniki Szybki Start dla usługi Azure Functions

| Sample                                                                                                                          | Opis                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Ten przykład funkcji platformy .NET Core Azure demonstruje, jak ograniczyć logowanie do określonych domen poczty e-mail i sprawdzać poprawność informacji podawanych przez użytkownika. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Ten Node.js przykład funkcji platformy Azure pokazuje, jak ograniczyć logowanie do określonych domen poczty e-mail i sprawdzić poprawność informacji podanych przez użytkownika.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Ten przykład funkcji platformy Azure w języku Python pokazuje, jak ograniczyć logowanie do określonych domen poczty e-mail i sprawdzić poprawność informacji podanych przez użytkownika.    |


### <a name="automated-fraud-protection-services--captcha"></a>Usługi zautomatyzowanej ochrony przed oszustwem & CAPTCHA
| Sample                                                                                                            | Opis                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Arkose Labs i ochrona przed oszustwem](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) | Ten przykład pokazuje, jak chronić logowania użytkowników przy użyciu usługi Arkose Labs i ochrony przed oszustwem. |
| [reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) | Ten przykład pokazuje, jak chronić logowania użytkowników przy użyciu wyzwania reCAPTCHA, aby zapobiec automatycznemu nadużyciom. |


### <a name="identity-verification"></a>Weryfikacja tożsamości

| Sample                                                                                                            | Opis                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | Ten przykład pokazuje, jak zweryfikować tożsamość użytkownika w ramach przepływów tworzenia konta przy użyciu łącznika interfejsu API do integracji z usługą IDology. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | Ten przykład pokazuje, jak zweryfikować tożsamość użytkownika w ramach przepływów tworzenia konta przy użyciu łącznika interfejsu API do integracji z usługą Experian. |


### <a name="other"></a>Inne

| Sample                                                                                                            | Opis                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Kod zaproszenia](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-invitation-code) | Ten przykład pokazuje, jak ograniczyć rejestrację do określonych odbiorców za pomocą kodów zaproszenia.|
| [Przykłady społeczności łącznika interfejsu API](https://github.com/azure-ad-b2c/api-connector-samples) | To repozytorium zawiera przykłady scenariuszy obsługiwanych przez łączniki interfejsu API dla społeczności.|
