---
title: Konfigurowanie tokenów — Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak skonfigurować okresy istnienia tokenu i ustawienia zgodności w programie Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f22a55a65b7dc4fd4f714d880804e6fd65dbbe46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101654378"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurowanie tokenów w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

W tym artykule dowiesz się, jak skonfigurować [okres istnienia i zgodność tokenu](tokens-overview.md) w Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="token-lifetime-behavior"></a>Zachowanie okresu istnienia tokenu

Okres istnienia tokenu można skonfigurować, w tym:

- **Okresy istnienia tokenu dostępu i identyfikatora (w minutach)** — okres istnienia tokenów okaziciela OAuth 2,0 i tokenów identyfikatorów. Wartość domyślna to 60 minut (1 godzina). Minimalna (włącznie) to 5 minut. Wartość maksymalna (włącznie) to 1 440 minut (24 godziny).
- **Okres istnienia tokenu odświeżania (w dniach)** — maksymalny przedział czasu, w którym token odświeżania może zostać użyty do uzyskania nowego tokenu dostępu, jeśli Twoja aplikacja uzyskała `offline_access` zakres. Wartość domyślna to 14 dni. Minimalny (włącznie) to jeden dzień. Maksymalna (włącznie) 90 dni.
- **Okres istnienia okna przewijania tokenu odświeżania** — typ okna przewijania tokenu odświeżania. `Bounded` wskazuje, że token odświeżania można rozszerzyć jak określono w **okresie istnienia (w dniach)**. `No expiry` wskazuje, że okres istnienia okna przewijania tokenu odświeżania nigdy nie wygasa.
- **Długość okresu istnienia (w dniach)** — po upływie tego czasu użytkownik jest zmuszony do ponownego uwierzytelnienia, niezależnie od okresu ważności ostatniego tokenu odświeżania nabytego przez aplikację. Wartość musi być większa lub równa wartości **okresu istnienia tokenu odświeżania** .

Na poniższym diagramie przedstawiono zachowanie okresu istnienia przewijania tokenu odświeżania.

![Okres istnienia tokenu odświeżania](./media/configure-tokens/refresh-token-lifetime.png)

> [!NOTE]
> >Aplikacje jednostronicowe korzystające z przepływu kodu autoryzacji z PKCE zawsze mają okres istnienia tokenu odświeżania równy 24 godziny, podczas gdy aplikacje mobilne, aplikacje klasyczne i aplikacje sieci Web nie występują w tym ograniczeniu. [Dowiedz się więcej o implikacjach dotyczących zabezpieczeń tokenów odświeżania w przeglądarce](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).


## <a name="configure-token-lifetime"></a>Skonfiguruj okres istnienia tokenu

::: zone pivot="b2c-user-flow"

Aby skonfigurować okres istnienia tokenu przepływu użytkownika:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **przepływy użytkownika (zasady)**.
1. Otwórz wcześniej utworzony przepływ użytkownika.
1. Wybierz pozycję **Właściwości**.
1. W obszarze **okres istnienia tokenu** Dostosuj właściwości tak, aby odpowiadały potrzebom aplikacji.
1. Kliknij pozycję **Zapisz**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Aby zmienić ustawienia zgodności z tokenem, należy ustawić metadane profilu technicznego [wystawcy tokenu](jwt-issuer-technical-profile.md) w rozszerzeniu lub plik jednostki uzależnionej zasad, które mają mieć wpływ. Profil techniczny wystawcy token wygląda podobnie do poniższego przykładu:

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
          <!--<Item Key="allow_infinite_rolling_refresh_token">true</Item>-->
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

Poniższe wartości są ustawiane w poprzednim przykładzie:

- okresy istnienia tokenu dostępu **token_lifetime_secs** (w sekundach). Wartość domyślna to 3 600 (1 godzina). Wartość minimalna to 300 (5 minut). Wartość maksymalna to 86 400 (24 godziny). 
- okresy istnienia tokenów identyfikatora **id_token_lifetime_secs** (w sekundach). Wartość domyślna to 3 600 (1 godzina). Wartość minimalna to 300 (5 minut). Wartość maksymalna to 86 400 (24 godziny). 
- **refresh_token_lifetime_secs** Okresy istnienia tokenu odświeżania (w sekundach). Wartość domyślna to 120, 9600 (14 dni). Wartość minimalna to 86 400 (24 godziny). Wartość maksymalna to 7 776 000 (90 dni). 
- **rolling_refresh_token_lifetime_secs** — okres istnienia ruchomego tokenu odświeżania (w sekundach). Wartość domyślna to 7 776 000 (90 dni). Wartość minimalna to 86 400 (24 godziny). Wartość maksymalna to 31 536 000 (365 dni). Jeśli nie chcesz wymusić okresu istnienia ruchomego okna, ustaw wartość `allow_infinite_rolling_refresh_token` na `true` . 
- **allow_infinite_rolling_refresh_token** — okres istnienia ruchomego tokenu odświeżania nigdy nie wygasa. 

::: zone-end


## <a name="token-compatibility-settings"></a>Ustawienia zgodności tokenu

Można skonfigurować zgodność tokenów, w tym:

- **Claim (ISS)** — format wystawcy tokenu dostępu i identyfikatora.
- **Zastrzeżenie (Sub)** — podmiot zabezpieczeń, dla którego token potwierdza informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani ponownie użyć. Może służyć do bezpiecznego sprawdzania autoryzacji, na przykład gdy token jest używany w celu uzyskania dostępu do zasobu. Domyślnie, zgłoszenie podmiotu jest wypełniane IDENTYFIKATORem obiektu użytkownika w katalogu.
- Zgłoszenie **reprezentujące przepływ użytkownika** — to stwierdzenie identyfikuje przepływ użytkownika, który został wykonany. Możliwe wartości: `tfp` (ustawienie domyślne) lub `acr` .

::: zone pivot="b2c-user-flow"

Aby skonfigurować ustawienia zgodności przepływu użytkownika:

1. Wybierz pozycję **przepływy użytkownika (zasady)**.
1. Otwórz wcześniej utworzony przepływ użytkownika.
1. Wybierz pozycję **Właściwości**.
1. W obszarze **Ustawienia zgodności tokenu** Dostosuj właściwości, aby odpowiadały potrzebom aplikacji.
1. Kliknij pozycję **Zapisz**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Aby zmienić ustawienia zgodności z tokenem, należy ustawić metadane profilu technicznego [wystawcy tokenu](jwt-issuer-technical-profile.md) w rozszerzeniu lub plik jednostki uzależnionej zasad, które mają mieć wpływ. Profil techniczny wystawcy token wygląda podobnie do poniższego przykładu:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          ...
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

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

::: zone-end

## <a name="provide-optional-claims-to-your-app"></a>Podaj opcjonalne oświadczenia do aplikacji

Oświadczenia aplikacji to wartości, które są zwracane do aplikacji. Zaktualizuj przepływ użytkownika w taki sposób, aby zawierał odpowiednie oświadczenia.

::: zone pivot="b2c-user-flow"

1. Wybierz pozycję **przepływy użytkownika (zasady)**.
1. Otwórz wcześniej utworzony przepływ użytkownika.
1. Wybierz pozycję **Oświadczenia aplikacji**.
1. Wybierz oświadczenia i atrybuty, które mają być wysyłane z powrotem do aplikacji.
1. Kliknij pozycję **Zapisz**.

::: zone-end

::: zone pivot="b2c-custom-policy"

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

::: zone-end

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [żądania tokenów dostępu](access-tokens.md).