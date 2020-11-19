---
title: Definiowanie profilu technicznego dla wystawcy SAML w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny dla wystawcy token SAML (SAML) w zasadach niestandardowych w programie Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 73c381624b69acb6fe7a6296a3153160812818bf
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886417"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny dla wystawcy tokenów SAML w zasadach niestandardowych Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) emituje kilka typów tokenów zabezpieczających podczas przetwarzania poszczególnych przepływów uwierzytelniania. Profil techniczny wystawcy tokenów SAML emituje token SAML zwracanego z powrotem do aplikacji jednostki uzależnionej (dostawcy usługi). Zazwyczaj ten profil techniczny to ostatni krok aranżacji w podróży użytkownika.

## <a name="protocol"></a>Protokół

Atrybut **name** elementu **Protocol** musi być ustawiony na `SAML2` . Ustaw element **OutputTokenFormat** na wartość `SAML2` .

W poniższym przykładzie przedstawiono profil techniczny dla `Saml2AssertionIssuer` :

```xml
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="SAML2"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
    <Item Key="TokenNotBeforeSkewInSeconds">600</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Oświadczenia danych wejściowych, wyjściowych i utrwalania

Elementy **InputClaims**, **OutputClaims** i **PersistClaims** są puste lub nie istnieją. Obecne są również elementy **InutputClaimsTransformations** i **OutputClaimsTransformations** .

## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| IssuerUri | Nie | Nazwa wystawcy, która pojawia się w odpowiedzi SAML. Wartość powinna być taka sama jak nazwa skonfigurowana w aplikacji jednostki uzależnionej. |
| XmlSignatureAlgorithm | Nie | Metoda, która Azure AD B2C używa do podpisywania potwierdzenia SAML. Możliwe wartości: `Sha256` , `Sha384` , `Sha512` lub `Sha1` . Upewnij się, że algorytm podpisu został skonfigurowany na obu stronach z tą samą wartością. Używaj tylko algorytmu obsługiwanego przez certyfikat. Aby skonfigurować odpowiedź SAML, zobacz [metadane SAML jednostki uzależnionej](relyingparty.md#metadata)|
|TokenNotBeforeSkewInSeconds| Nie| Określa pochylenie jako liczbę całkowitą dla sygnatury czasowej, która oznacza początek okresu ważności. Im wyższy numer to, tym więcej z powrotem w czasie okres ważności rozpoczyna się w odniesieniu do czasu, w którym oświadczenia są wystawiane dla jednostki uzależnionej. Na przykład gdy TokenNotBeforeSkewInSeconds jest ustawiony na 60 sekund, jeśli token jest wystawiony o 13:05:10 czasu UTC, token jest ważny od 13:04:10 czasu UTC. Wartość domyślna to 0. Wartość maksymalna to 3600 (jedna godzina). |

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Element CryptographicKeys zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| MetadataSigning | Tak | Certyfikat x509 (zestaw kluczy RSA) służący do podpisywania metadanych protokołu SAML. Azure AD B2C używa tego klucza do podpisywania metadanych. |
| SamlMessageSigning| Tak| Określ certyfikat x509 (zestaw kluczy RSA), który ma być używany do podpisywania komunikatów SAML. Azure AD B2C używa tego klucza do podpisywania wysyłania odpowiedzi `<samlp:Response>` do jednostki uzależnionej.|

## <a name="session-management"></a>Zarządzanie sesjami

Aby skonfigurować Azure AD B2C sesje SAML między aplikacją jednostki uzależnionej, atrybut `UseTechnicalProfileForSessionManagement` elementu, odwołanie do sesji SSO [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) .

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z poniższym artykułem, aby zapoznać się z profilem technicznym wystawcy SAML:

- [Rejestrowanie aplikacji SAML w Azure AD B2C](connect-with-saml-service-providers.md)

