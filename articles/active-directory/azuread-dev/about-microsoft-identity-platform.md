---
title: Ewolucja platformy tożsamości firmy Microsoft — Azure
description: Dowiedz się więcej o platformie tożsamości firmy Microsoft, ewolucji usługi tożsamości Azure Active Directory (Azure AD) i platformy deweloperskiej.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 12/09/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv, marsma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ab0057f55ddb5bf76f6fa74b0211b5ea5af24288
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608218"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Ewolucja platformy tożsamości firmy Microsoft

[Platforma tożsamości firmy Microsoft](../develop/index.yml) to ewolucja platformy deweloperów Azure Active Directory (Azure AD). Dzięki temu deweloperzy mogą tworzyć aplikacje, które logują użytkowników, uzyskują tokeny do wywoływania interfejsów API, takich jak Microsoft Graph lub interfejsy API zaprojektowane przez deweloperów. Obejmuje ona usługę uwierzytelniania, biblioteki typu open source, rejestrację aplikacji i konfigurację (za pomocą portalu dla deweloperów i interfejsu API aplikacji), pełną dokumentację dla deweloperów, przykłady przewodnika Szybki Start, przykłady kodu, samouczki, przewodniki i inne elementy deweloperskie. Platforma tożsamości firmy Microsoft obsługuje standardowe protokoły branżowe, takie jak OAuth 2.0 i OpenID Connect.

Wielu programistów pracowało wcześniej z platformą Azure AD v 1.0 do uwierzytelniania kont służbowych (zainicjowanych przez usługę Azure AD) przez żądanie tokenów z punktu końcowego usługi Azure AD v 1.0, przy użyciu biblioteki Azure AD Authentication Library (ADAL), Azure Portal do rejestracji i konfiguracji aplikacji oraz interfejsu API Microsoft Graph do konfiguracji aplikacji programowych.

Za pomocą ujednoliconej platformy tożsamości firmy Microsoft (v 2.0) można napisać kod jednokrotnie i uwierzytelnić tożsamość firmy Microsoft w swojej aplikacji. W przypadku kilku platform zaleca się używanie w pełni obsługiwanej biblioteki uwierzytelniania Microsoft Open Source (MSAL) w odniesieniu do punktów końcowych platformy tożsamości. Usługa MSAL jest prosta do użycia, zapewnia użytkownikom doskonałe funkcje logowania jednokrotnego (SSO, Single Sign-on), pomaga osiągnąć wysoką niezawodność i wydajność oraz jest opracowywana przy użyciu usługi Microsoft Secure Development (SDL). Podczas wywoływania interfejsów API można skonfigurować aplikację tak, aby korzystała z powiększej zgody, która pozwala opóźnić żądanie zgody na bardziej inwazyjne zakresy, dopóki użycie aplikacji zagwarantuje to w czasie wykonywania.  Program MSAL obsługuje również Azure Active Directory B2C, więc klienci używają własnych tożsamości dla konta społecznościowego, przedsiębiorstwa lub lokalnego do uzyskiwania dostępu jednokrotnego do aplikacji i interfejsów API.

Korzystając z platformy tożsamości firmy Microsoft, Rozszerz swój dostęp do tego rodzaju użytkowników:

- Konta służbowe (konta aprowizacji usługi Azure AD)
- Konta osobiste (takie jak Outlook.com lub Hotmail.com)
- Klienci, którzy korzystają z własnych wiadomości e-mail lub tożsamości społecznościowej (takich jak LinkedIn, Facebook, Google) za pośrednictwem MSAL i Azure AD B2C

Za pomocą Azure Portal można zarejestrować i skonfigurować aplikację oraz użyć interfejsu API Microsoft Graph do konfiguracji aplikacji programistycznych.

Zaktualizuj swoją aplikację w swoim własnym tempie. Aplikacje skompilowane przy użyciu bibliotek ADAL są nadal obsługiwane. Obsługiwane są również mieszane portfolio aplikacji, które obejmują aplikacje skompilowane przy użyciu biblioteki ADAL i aplikacje skompilowane przy użyciu bibliotek MSAL. Oznacza to, że aplikacje korzystające z najnowszej biblioteki ADAL i najnowszej MSAL będą dostarczać Logowanie jednokrotne w portfolio, zapewniane przez pamięć podręczną tokenów udostępnionych między tymi bibliotekami. Aplikacje zaktualizowane z biblioteki ADAL do MSAL będą obsługiwać stan logowania użytkownika po uaktualnieniu.

## <a name="microsoft-identity-platform-experience"></a>Środowisko platformy tożsamości firmy Microsoft

Na poniższym diagramie przedstawiono ogólnie środowisko obsługi tożsamości firmy Microsoft, obejmujące środowisko rejestracji aplikacji, zestawy SDK, punkty końcowe i obsługiwane tożsamości.

![Platforma tożsamości firmy Microsoft obecnie](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Środowisko rejestracji aplikacji

Środowisko **[Rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908)** Azure Portal to jedno środowisko portalu do zarządzania wszystkimi aplikacjami, które zostały zintegrowane z platformą tożsamości firmy Microsoft. Jeśli korzystasz z portalu rejestracji aplikacji, zamiast tego zacznij korzystać z Azure Portal rejestracji aplikacji.

Aby przeprowadzić integrację z usługą Azure AD B2C (podczas uwierzytelniania tożsamości społecznościowych lub lokalnych), należy zarejestrować aplikację w dzierżawie Azure AD B2C. To środowisko jest również częścią Azure Portal.

Użyj [interfejsu API aplikacji](/graph/api/resources/application) , aby programowo skonfigurować aplikacje zintegrowane z platformą tożsamości firmy Microsoft na potrzeby uwierzytelniania dowolnej tożsamości firmy Microsoft.

### <a name="msal-libraries"></a>Biblioteki MSAL

Biblioteka MSAL może służyć do kompilowania aplikacji, które uwierzytelniają wszystkie tożsamości firmy Microsoft. Biblioteki MSAL w programie .NET i JavaScript są ogólnie dostępne. Biblioteki MSAL dla systemów iOS i Android są w wersji zapoznawczej i są odpowiednie do użycia w środowisku produkcyjnym. Zapewniamy tę samą obsługę produkcyjną dla bibliotek MSAL w wersji zapoznawczej, tak jak w przypadku systemów MSAL i ADAL, które są ogólnie dostępne.

Za pomocą bibliotek MSAL można także zintegrować swoją aplikację z Azure AD B2C.

### <a name="microsoft-identity-platform-endpoint"></a>Punkt końcowy platformy tożsamości firmy Microsoft

Punkt końcowy Microsoft Identity platform (v 2.0) jest certyfikowany OIDC. Współpracuje z bibliotekami uwierzytelniania firmy Microsoft (MSAL) lub dowolną inną biblioteką zgodną ze standardami. Służy do implementowania zakresów ludzkich, zgodnie ze standardami branżowymi.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej z [dokumentacji platformy tożsamości firmy Microsoft](../develop/index.yml).
