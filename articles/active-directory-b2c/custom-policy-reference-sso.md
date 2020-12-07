---
title: Zarządzanie sesjami logowania jednokrotnego przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zarządzać sesjami logowania jednokrotnego przy użyciu zasad niestandardowych w programie Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e47eda29b0ad1c47edad08195b2ffd0fe3835af
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750479"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Zarządzanie sesjami logowania jednokrotnego w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Zarządzanie [sesjami logowania](session-overview.md) jednokrotnego używa tej samej semantyki co inny profil techniczny w zasadach niestandardowych. Po wykonaniu kroku aranżacji do odwołania zostanie odpytany profil techniczny skojarzony z tym krokiem `UseTechnicalProfileForSessionManagement` . Jeśli taki istnieje, oznacza to, że dostawca sesji rejestracji jednokrotnej, którego dotyczy odwołanie, jest sprawdzany w celu sprawdzenia, czy użytkownik jest uczestnikiem sesji. W takim przypadku dostawca sesji logowania jednokrotnego jest używany do ponownego zapełnienia sesji. Podobnie po zakończeniu wykonywania kroku aranżacji dostawca jest używany do przechowywania informacji w sesji, jeśli określono dostawcę sesji logowania jednokrotnego.

Azure AD B2C zdefiniował wielu dostawców sesji rejestracji jednokrotnej, których można użyć:

|Dostawca sesji  |Zakres  |
|---------|---------|
|[NoopSSOSessionProvider](#noopssosessionprovider)     |  Brak       |       
|[DefaultSSOSessionProvider](#defaultssosessionprovider)    | Azure AD B2C wewnętrzny Menedżer sesji.      |       
|[ExternalLoginSSOSessionProvider](#externalloginssosessionprovider)     | Między Azure AD B2C i OAuth1, OAuth2 lub OpenID Connect Connect Identity Provider.        |         |
|[OAuthSSOSessionProvider](#oauthssosessionprovider)     | Między usługą OAuth2 lub OpenID Connect Połącz aplikację jednostki uzależnionej i Azure AD B2C.        |        
|[SamlSSOSessionProvider](#samlssosessionprovider)     | Między Azure AD B2C i dostawcą tożsamości SAML. I między dostawcą usług SAML (aplikacja jednostki uzależnionej) i Azure AD B2C.  |        




Klasy zarządzania logowaniem jednokrotnym są określane przy użyciu `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` elementu profilu technicznego.

## <a name="input-claims"></a>Oświadczenia wejściowe

`InputClaims`Element jest pusty lub nie istnieje.

## <a name="persisted-claims"></a>Utrwalone oświadczenia

Oświadczenia, które muszą zostać zwrócone do aplikacji lub używane przez warunki wstępne w kolejnych krokach, powinny być przechowywane w sesji lub uzupełnione przez odczyt z profilu użytkownika w katalogu. Korzystanie z utrwalonych oświadczeń gwarantuje, że w przypadku brakujących oświadczeń przejazdów uwierzytelniania nie powiedzie się. Aby dodać oświadczenia w sesji, użyj `<PersistedClaims>` elementu profilu technicznego. Gdy dostawca jest używany do ponownego wypełniania sesji, utrwalane oświadczenia są dodawane do zbioru oświadczeń.

## <a name="output-claims"></a>Oświadczenia wyjściowe

Służy `<OutputClaims>` do pobierania oświadczeń z sesji.

## <a name="session-providers"></a>Dostawcy sesji

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Jak nazywa się, ten dostawca niczego nie robi. Ten dostawca może służyć do pomijania zachowania logowania jednokrotnego dla określonego profilu technicznego. Poniższy `SM-Noop` profil techniczny jest zawarty w [pakiecie startowym zasad niestandardowych](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Tego dostawcy można używać do przechowywania oświadczeń w sesji. Ten dostawca jest zwykle przywoływany w profilu technicznym używanym do zarządzania kontami lokalnymi i federacyjnymi. Poniższy `SM-AAD` profil techniczny jest zawarty w [pakiecie startowym zasad niestandardowych](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```


Poniższy `SM-MFA` profil techniczny jest zawarty w [pakiecie startowym zasad niestandardowych](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa` . Ten profil techniczny służy do zarządzania sesją uwierzytelniania wieloskładnikowego.

```xml
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Ten dostawca służy do pomijania ekranu "wybieranie dostawcy tożsamości" i wylogowania z federacyjnego dostawcy tożsamości. Zwykle jest to przywoływane w profilu technicznym skonfigurowanym dla dostawcy tożsamości federacyjnych, takiego jak Facebook lub Azure Active Directory. Poniższy `SM-SocialLogin` profil techniczny jest zawarty w [pakiecie startowym zasad niestandardowych](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | Nie | Obecnie nie są używane, można je zignorować. |

### <a name="oauthssosessionprovider"></a>OAuthSSOSessionProvider

Ten dostawca służy do zarządzania sesjami Azure AD B2C między jednostką uzależnioną usługi OAuth2 lub OpenID Connect i Azure AD B2C.

```xml
<TechnicalProfile Id="SM-jwt-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Ten dostawca służy do zarządzania Azure AD B2C sesji SAML między aplikacją jednostki uzależnionej lub dostawcą federacyjnego tożsamości SAML. W przypadku korzystania z dostawcy rejestracji jednokrotnej w celu przechowywania sesji dostawcy tożsamości SAML `RegisterServiceProviders` należy ustawić wartość `false` . Poniższy `SM-Saml-idp` profil techniczny jest używany przez [profil techniczny dostawcy tożsamości SAML](saml-identity-provider-technical-profile.md).

```xml
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

W przypadku korzystania z dostawcy do przechowywania sesji SAML B2C, `RegisterServiceProviders` musi ona mieć ustawioną wartość `true` . Wylogowywanie sesji języka SAML wymaga `SessionIndex` `NameID` zakończenia i.

Następujący `SM-Saml-issuer` profil techniczny jest używany przez [profil techniczny wystawcy SAML](saml-issuer-technical-profile.md)

```xml
<TechnicalProfile Id="SM-Saml-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis|
| --- | --- | --- |
| IncludeSessionIndex | Nie | Obecnie nie są używane, można je zignorować.|
| RegisterServiceProviders | Nie | Wskazuje, że dostawca powinien rejestrować wszystkich dostawców usług SAML, którzy wystawiły potwierdzenie. Możliwe wartości: `true` (ustawienie domyślne) lub `false` .|


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [sesji Azure AD B2C](session-overview.md).
- Dowiedz się, jak [skonfigurować zachowanie sesji w zasadach niestandardowych](session-behavior-custom-policy.md).
