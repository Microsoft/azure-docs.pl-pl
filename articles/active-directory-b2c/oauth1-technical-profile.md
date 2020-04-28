---
title: Zdefiniuj profil techniczny OAuth1 w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny OAuth 1,0 w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7f734844859d44e66bddbc2ddd999659e52f9668
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78184081"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny OAuth1 w zasadach niestandardowych Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę dostawcy tożsamości [protokołu OAuth 1,0](https://tools.ietf.org/html/rfc5849) . W tym artykule opisano szczegóły dotyczące profilu technicznego dotyczącego współpracy z dostawcą oświadczeń obsługującym ten standardowy protokół. Profil techniczny OAuth1 umożliwia sfederować z dostawcą tożsamości opartym na OAuth1, takim jak Twitter. Federowanie z dostawcą tożsamości umożliwia użytkownikom logowanie się przy użyciu istniejących tożsamości społecznościowych lub firmowych.

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **name** elementu **Protocol** musi być ustawiony na `OAuth1`. Na przykład protokół dla profilu technicznego **Twitter-OAUTH1** ma wartość `OAuth1`.

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>Oświadczenia wejściowe

Elementy **InputClaims** i **InputClaimsTransformations** są puste lub nie są obecne.

## <a name="output-claims"></a>Oświadczenia wyjściowe

Element **OutputClaims** zawiera listę oświadczeń zwracanych przez dostawcę tożsamości OAuth1. Może być konieczne zamapowanie nazwy żądania zdefiniowanego w zasadach na nazwę zdefiniowaną w dostawcy tożsamości. Można również uwzględnić oświadczenia, które nie są zwracane przez dostawcę tożsamości, dopóki atrybut **DefaultValue** jest ustawiony.

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

W poniższym przykładzie przedstawiono oświadczenia zwrócone przez dostawcę tożsamości usługi Twitter:

- **USER_ID** , które jest mapowane na **issuerUserId** .
- **Screen_name** , które jest mapowane na wartość **DisplayName** .
- Bez **email** mapowania nazwy.

Profil techniczny zwraca również oświadczenia, które nie są zwracane przez dostawcę tożsamości:

- **IdentityProvider** , który zawiera nazwę dostawcy tożsamości.
- **AuthenticationSource** z wartością domyślną `socialIdpAuthentication`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| client_id | Tak | Identyfikator aplikacji dostawcy tożsamości. |
| ProviderName | Nie | Nazwa dostawcy tożsamości. |
| request_token_endpoint | Tak | Adres URL punktu końcowego tokenu żądania zgodnie z dokumentem RFC 5849. |
| authorization_endpoint | Tak | Adres URL punktu końcowego autoryzacji zgodnie z dokumentem RFC 5849. |
| access_token_endpoint | Tak | Adres URL tokenu dla punktu końcowego w formacie RFC 5849. |
| ClaimsEndpoint | Nie | Adres URL punktu końcowego informacji o użytkowniku. |
| ClaimsResponseFormat | Nie | Format odpowiedzi na oświadczenia.|

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Element **CryptographicKeys** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| client_secret | Tak | Klucz tajny klienta aplikacji dostawcy tożsamości.   |

## <a name="redirect-uri"></a>Identyfikator URI przekierowania

Podczas konfigurowania adresu URL przekierowania dostawcy tożsamości wprowadź `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`wartość. Pamiętaj o zamianie **dzierżawy** na nazwę dzierżawy (na przykład contosob2c.onmicrosoft.com) i **policyId** z identyfikatorem zasad (na przykład b2c_1a_policy). Identyfikator URI przekierowania musi zawierać tylko małe litery. Dodaj adres URL przekierowania dla wszystkich zasad korzystających z logowania dostawcy tożsamości.

Jeśli używasz domeny **b2clogin.com** zamiast **login.microsoftonline.com** upewnij się, że używasz b2clogin.com zamiast login.microsoftonline.com.

Przykłady:

- [Dodawanie usługi Twitter jako dostawcy tożsamości OAuth1 przy użyciu zasad niestandardowych](identity-provider-twitter-custom.md)













