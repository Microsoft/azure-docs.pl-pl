---
title: Zarządzanie użyciem rejestracji jednokrotnej i tokenu przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Więcej informacji na temat zarządzania logowaniem jednokrotnym i użyciem tokenu przy użyciu zasad niestandardowych w programie Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0b460d5a3c5535b74e349fa46c6a2ad55fc3a8d8
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82966573"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Zarządzanie użyciem rejestracji jednokrotnej i tokenu przy użyciu zasad niestandardowych w Azure Active Directory B2C

Ten artykuł zawiera informacje o sposobach zarządzania konfiguracją tokenu, sesji i logowania jednokrotnego (SSO) przy użyciu [zasad niestandardowych](custom-policy-overview.md) w programie Azure Active Directory B2C (Azure AD B2C).

## <a name="jtw-token-lifetimes-and-claims-configuration"></a>Okresy istnienia tokenów JTW i konfiguracja oświadczeń

Aby zmienić ustawienia dla okresów istnienia tokenu, należy dodać element [ClaimsProviders](claimsproviders.md) w pliku jednostki uzależnionej zasad, które mają mieć wpływ.  Element **ClaimsProviders** jest elementem podrzędnym elementu [TrustFrameworkPolicy](trustframeworkpolicy.md) .

Wstaw element ClaimsProviders między elementem BasePolicy i elementem RelyingParty pliku jednostki uzależnionej.

Wewnątrz programu należy umieścić informacje, które mają wpływ na okresy istnienia tokenu. KOD XML wygląda podobnie do tego przykładu:

```XML
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
- **Okres istnienia przesuwanego okna tokenu odświeżania** — Jeśli chcesz ustawić okres istnienia ruchomego okna do tokenu odświeżania, ustaw wartość **rolling_refresh_token_lifetime_secs** elementu metadanych. Wartość domyślna to 7776000 (90 dni). Jeśli nie chcesz wymusić okresu istnienia ruchomego okna, Zastąp `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`element pozycją.
- **Claimname (ISS)** — w ramach żądania wystawcy (ISS) jest ustawiany element metadanych **IssuanceClaimPattern** . Odpowiednie wartości to `AuthorityAndTenantGuid` i `AuthorityWithTfp`.
- **Ustawianie zgłoszenia reprezentującego identyfikator zasad** — opcje ustawiania tej wartości są `TFP` (zasady struktury zaufania) i `ACR` (odwołanie kontekstu uwierzytelniania). `TFP`jest to zalecana wartość. Ustaw **AuthenticationContextReferenceClaimPattern** z wartością `None`.

    W elemencie **ClaimsSchema** Dodaj następujący element:

    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    W elemencie **OutputClaims** Dodaj następujący element:

    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    W przypadku ACR Usuń element **AuthenticationContextReferenceClaimPattern** .

- **Zastrzeżenie podmiotu (Sub)** — ta opcja domyślnie ma wartość objectid, jeśli chcesz zmienić to ustawienie na `Not Supported`, Zastąp ten wiersz:

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    z tym wierszem:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [sesji Azure AD B2C](session-overview.md).
- Dowiedz się, jak [skonfigurować zachowanie sesji w zasadach niestandardowych](session-behavior-custom-policy.md).
- Odwołanie: [JwtIssuer](jwt-issuer-technical-profile.md).