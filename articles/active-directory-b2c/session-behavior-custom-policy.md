---
title: Konfigurowanie zachowania sesji przy użyciu zasad niestandardowych — Azure Active Directory B2C | Microsoft Docs
description: Skonfiguruj zachowanie sesji przy użyciu zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a2f20a4521efe2806c4bc66e4612b99caf84382a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85385267"
---
# <a name="configure-session-behavior-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie zachowania sesji przy użyciu zasad niestandardowych w Azure Active Directory B2C

Usługa logowania jednokrotnego [(SSO)](session-overview.md) w programie Azure Active Directory B2C (Azure AD B2C) umożliwia administratorowi sterowanie interakcją z użytkownikiem po jego uwierzytelnieniu. Administrator może na przykład określić, czy jest wyświetlany wybór dostawców tożsamości, czy też należy wprowadzić ponownie szczegóły konta. W tym artykule opisano sposób konfigurowania ustawień logowania jednokrotnego dla Azure AD B2C.

## <a name="session-behavior-properties"></a>Właściwości zachowania sesji

Aby zarządzać sesjami aplikacji sieci Web, można użyć następujących właściwości:

- **Okres istnienia sesji aplikacji sieci Web (w minutach)** — okres istnienia pliku cookie sesji usługi Azure AD B2C's w przeglądarce użytkownika po pomyślnym uwierzytelnieniu.
    - Wartość domyślna = 86400 sekund (1440 minut).
    - Minimum (włącznie) = 900 sekund (15 minut).
    - Maksimum (włącznie) = 86400 sekund (1440 minut).
- **Limit czasu sesji aplikacji sieci Web** — [Typ wygaśnięcia sesji](session-overview.md#session-expiry-type), wartość *krocząca*lub *bezwzględna*. 
- **Konfiguracja logowania** jednokrotnego — [zakres sesji](session-overview.md#session-scope) zachowań logowania jednokrotnego (SSO) w wielu aplikacjach i przepływach użytkowników w dzierżawie Azure AD B2C. 

## <a name="configure-the-properties"></a>Konfigurowanie właściwości

Aby zmienić zachowanie sesji i konfiguracje rejestracji jednokrotnej, należy dodać element **UserJourneyBehaviors** wewnątrz elementu [RelyingParty](relyingparty.md) .  Element **UserJourneyBehaviors** musi występować bezpośrednio po **DefaultUserJourney**. Element **UserJourneyBehavors** powinien wyglądać podobnie do tego przykładu:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="single-sign-out"></a>Wylogowanie jednokrotne

### <a name="configure-the-applications"></a>Konfigurowanie aplikacji

Po przekierowaniu użytkownika do punktu końcowego wylogowania Azure AD B2C (dla protokołów OAuth2 i SAML) Azure AD B2C czyści sesję użytkownika z przeglądarki.  Aby zezwolić na [rejestrację jednokrotną](session-overview.md#single-sign-out), ustaw `LogoutUrl` aplikację z Azure Portal:

1. Przejdź do [Azure Portal](https://portal.azure.com).
1. Wybierz katalog Azure AD B2C, klikając swoje konto w prawym górnym rogu strony.
1. W menu po lewej stronie wybierz **Azure AD B2C**, wybierz pozycję **rejestracje aplikacji**, a następnie wybierz aplikację.
1. Wybierz pozycję **Ustawienia**, wybierz pozycję **Właściwości**, a następnie znajdź pole tekstowe **adres URL wylogowywania** . 

### <a name="configure-the-token-issuer"></a>Konfigurowanie wystawcy tokenu 

Aby zapewnić obsługę logowania jednokrotnego, profile techniczne wystawców tokenów dla tokenu JWT i SAML muszą określać:

- Nazwa protokołu, taka jak `<Protocol Name="OpenIdConnect" />`
- Odwołanie do profilu technicznego sesji, na przykład `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

Poniższy przykład ilustruje wystawców tokenów JWT i SAML z logowaniem jednokrotnym:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [sesji Azure AD B2C](session-overview.md).
