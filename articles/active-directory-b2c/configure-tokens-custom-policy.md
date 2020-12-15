---
title: Zarządzanie użyciem rejestracji jednokrotnej i tokenu przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Więcej informacji na temat zarządzania logowaniem jednokrotnym i użyciem tokenu przy użyciu zasad niestandardowych w programie Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6bfabd13c43501f7539eb3756ffbd06802cee823
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503476"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Zarządzanie użyciem rejestracji jednokrotnej i tokenu przy użyciu zasad niestandardowych w Azure Active Directory B2C

Ten artykuł zawiera informacje o sposobach zarządzania konfiguracją tokenu, sesji i logowania jednokrotnego (SSO) przy użyciu [zasad niestandardowych](custom-policy-overview.md) w programie Azure Active Directory B2C (Azure AD B2C).

## <a name="jwt-token-lifetimes-and-claims-configuration"></a>Okresy istnienia tokenów JWT i konfiguracja oświadczeń

Aby zmienić ustawienia dla okresów istnienia tokenu, należy dodać element [ClaimsProviders](claimsproviders.md) w pliku jednostki uzależnionej zasad, które mają mieć wpływ.  Element **ClaimsProviders** jest elementem podrzędnym elementu [TrustFrameworkPolicy](trustframeworkpolicy.md) .

Wstaw element ClaimsProviders między elementem BasePolicy i elementem RelyingParty pliku jednostki uzależnionej.

Wewnątrz programu należy umieścić informacje, które mają wpływ na okresy istnienia tokenu. KOD XML wygląda podobnie do tego przykładu:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

Poniższe wartości są ustawiane w poprzednim przykładzie:

- **Okresy istnienia tokenu dostępu** — wartość okresu istnienia tokenu dostępu jest ustawiana za pomocą **token_lifetime_secs** elementu metadanych. Wartość domyślna to 3600 sekund (60 minut).
- **Okres istnienia tokenu identyfikatora** — wartość okresu istnienia tokenu identyfikatora jest ustawiana za pomocą elementu metadanych **id_token_lifetime_secs** . Wartość domyślna to 3600 sekund (60 minut).
- **Okres istnienia tokenu odświeżania** — wartość okresu istnienia tokenu odświeżania jest ustawiana za pomocą elementu metadanych **refresh_token_lifetime_secs** . Wartość domyślna to 1209600 sekund (14 dni).
- **Okres istnienia przesuwanego okna tokenu odświeżania** — Jeśli chcesz ustawić okres istnienia ruchomego okna do tokenu odświeżania, ustaw wartość **rolling_refresh_token_lifetime_secs** elementu metadanych. Wartość domyślna to 7776000 (90 dni). Jeśli nie chcesz wymusić okresu istnienia ruchomego okna, Zastąp element pozycją `<Item Key="allow_infinite_rolling_refresh_token">True</Item>` .
- **Claimname (ISS)** — w ramach żądania wystawcy (ISS) jest ustawiany element metadanych **IssuanceClaimPattern** . Odpowiednie wartości to `AuthorityAndTenantGuid` i `AuthorityWithTfp` .
- **Ustawianie zgłoszenia reprezentującego identyfikator zasad** — opcje ustawiania tej wartości są `TFP` (zasady struktury zaufania) i `ACR` (odwołanie kontekstu uwierzytelniania). `TFP` jest to zalecana wartość. Ustaw **AuthenticationContextReferenceClaimPattern** z wartością `None` .

    W elemencie **ClaimsSchema** Dodaj następujący element:

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    W elemencie **OutputClaims** Dodaj następujący element:

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    W przypadku ACR Usuń element **AuthenticationContextReferenceClaimPattern** .

- **Zastrzeżenie podmiotu (Sub)** — ta opcja domyślnie ma wartość objectid, jeśli chcesz zmienić to ustawienie na `Not Supported` , Zastąp ten wiersz:

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    z tym wierszem:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

> [!NOTE]
> Aplikacje jednostronicowe korzystające z przepływu kodu autoryzacji z PKCE zawsze mają okres istnienia tokenu odświeżania równy 24 godziny. [Dowiedz się więcej o implikacjach dotyczących zabezpieczeń tokenów odświeżania w przeglądarce](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="provide-optional-claims-to-your-app"></a>Podaj opcjonalne oświadczenia do aplikacji

Oświadczenia dotyczące danych wyjściowych [profilu technicznego zasad jednostki uzależnionej](relyingparty.md#technicalprofile) to wartości, które są zwracane do aplikacji. Dodanie oświadczeń wyjściowych spowoduje wydanie oświadczeń do tokenu po pomyślnym przejściu użytkownika i zostanie wysłane do aplikacji. Zmodyfikuj element profil techniczny w sekcji jednostki uzależnionej, aby dodać żądane oświadczenia jako oświadczenie wyjściowe.

1. Otwórz niestandardowy plik zasad. Na przykład SignUpOrSignin.xml.
1. Znajdź element OutputClaims. Dodaj oświadczenie outputclaim, które mają być zawarte w tokenie. 
1. Ustaw atrybuty zgłoszenia wynikowego. 

Poniższy przykład dodaje to zgłoszenie `accountBalance` . AccountBalance jest wysyłana do aplikacji jako saldo. 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Element oświadczenie outputclaim zawiera następujące atrybuty:

  - **ClaimTypeReferenceId** — identyfikator typu "Claim" jest już zdefiniowany w sekcji [ClaimsSchema](claimsschema.md) w pliku zasad lub nadrzędnym pliku zasad.
  - **PartnerClaimType** — pozwala zmienić nazwę żądania w tokenie. 
  - **DefaultValue** — wartość domyślna. Możesz również ustawić wartość domyślną dla [mechanizmu rozwiązywania konfliktów](claim-resolver-overview.md), takie jak identyfikator dzierżawy.
  - **AlwaysUseDefaultValue** — Wymuś użycie wartości domyślnej.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [sesji Azure AD B2C](session-overview.md).
- Dowiedz się, jak [skonfigurować zachowanie sesji w zasadach niestandardowych](session-behavior-custom-policy.md).
- Odwołanie: [JwtIssuer](jwt-issuer-technical-profile.md).
