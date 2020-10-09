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
ms.openlocfilehash: 63a79df8e540e88d0f1e3f4c5b8d339ff03c877f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91854294"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Przykłady kodu usługi Azure Active Directory B2C

Poniższe tabele zawierają linki do przykładów dla aplikacji systemu iOS i Android oraz platform .NET i Node.js.

## <a name="mobile-and-desktop-apps"></a>Aplikacje mobilne i klasyczne

| Sample | Opis |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | Przykład dla systemu iOS w języku Swift, który uwierzytelnia użytkowników usługi Azure AD B2C i wywołuje interfejs API przy użyciu protokołu OAuth 2.0 |
| [android-native-msal](https://github.com/Azure-Samples/ms-identity-android-java#b2cmodefragment-class) | Prosta aplikacja systemu Android przedstawiająca sposób użycia biblioteki MSAL do uwierzytelniania użytkowników za pośrednictwem usługi Azure Active Directory B2C oraz do uzyskiwania dostępu do internetowego interfejsu API z tokenami wynikowymi. |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | Przykład przedstawiający sposób użycia biblioteki innej firmy do kompilowania aplikacji systemu iOS w języku Objective-C, która uwierzytelnia użytkowników tożsamości firmy Microsoft w naszej usłudze tożsamości Azure AD B2C. |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | Przykład przedstawiający sposób użycia biblioteki innej firmy do kompilowania aplikacji systemu Android, która uwierzytelnia użytkowników tożsamości firmy Microsoft w naszej usłudze tożsamości Azure AD B2C i wywołuje internetowy interfejs API za pomocą tokenów dostępu standardu OAuth 2.0. |
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
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | Aplikacja jednostronicowa (SPA) wywołująca internetowy interfejs API. Uwierzytelnianie odbywa się z Azure AD B2C przy użyciu MSAL.js. |

## <a name="saml-test-application"></a>Aplikacja testowa SAML

| Sample | Opis |
|--------| ----------- |
| [SAML-Sp-Tester](https://github.com/azure-ad-b2c/saml-sp-tester/tree/master/source-code) | Aplikacja testowa SAML do testowania Azure AD B2C skonfigurowana do działania jako dostawca tożsamości SAML. |

## <a name="api-connectors"></a>Łączniki interfejsu API

W poniższych tabelach przedstawiono linki do przykładów kodu służących do używania interfejsów API sieci Web w przepływach [interfejsu API](api-connectors-overview.md).

## <a name="api-connector-azure-function-quickstarts"></a>Łącznik interfejsu API — Przewodniki Szybki Start

| Sample                                                                                                                          | Opis                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Ten przykład funkcji platformy .NET Core Azure demonstruje, jak ograniczyć logowanie do określonych domen poczty e-mail i sprawdzać poprawność informacji podawanych przez użytkownika. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Ten Node.js przykład funkcji platformy Azure pokazuje, jak ograniczyć logowanie do określonych domen poczty e-mail i sprawdzić poprawność informacji podanych przez użytkownika.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Ten przykład funkcji platformy Azure w języku Python pokazuje, jak ograniczyć logowanie do określonych domen poczty e-mail i sprawdzić poprawność informacji podanych przez użytkownika.    |