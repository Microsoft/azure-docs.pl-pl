---
title: Skonfiguruj konto i zaloguj się przy użyciu konta usługi GitHub
titleSuffix: Azure AD B2C
description: Zapewnij klientom konta usługi GitHub i zaloguj się w swoich aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e2c82858b9f4b9846ff5371da347be942eed0a4f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488860"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Skonfiguruj konto i zaloguj się przy użyciu konta usługi GitHub za pomocą Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-github-oauth-application"></a>Tworzenie aplikacji OAuth usługi GitHub

Aby włączyć logowanie się przy użyciu konta usługi GitHub w Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację w portalu dla [deweloperów serwisu GitHub](https://github.com/settings/developers) . Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji OAuth](https://docs.github.com/en/free-pro-team@latest/developers/apps/creating-an-oauth-app). Jeśli nie masz jeszcze konta usługi GitHub, możesz zarejestrować się w usłudze [https://www.github.com/](https://www.github.com/) .

1. Zaloguj się do [deweloperów serwisu GitHub](https://github.com/settings/developers) przy użyciu swoich poświadczeń usługi GitHub.
1. Wybierz pozycję **aplikacje OAuth** , a następnie wybierz pozycję **Nowa aplikacja OAuth**.
1. Wprowadź **nazwę aplikacji** i **adres URL strony głównej**.
1. W polu **adres URL wywołania zwrotnego autoryzacji** wprowadź wartość `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Jeśli używasz [domeny niestandardowej](custom-domain.md), wprowadź `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Zamień `your-domain-name` na domenę niestandardową i `your-tenant-name` nazwę dzierżawy. Użyj wszystkich małych liter, wprowadzając nazwę dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z dużymi literami w Azure AD B2C.
1. Kliknij pozycję **zarejestruj aplikację**.
1. Skopiuj wartości **Identyfikator klienta** i **klucz tajny klienta**. Musisz dodać dostawcę tożsamości do dzierżawy.

::: zone pivot="b2c-user-flow"

## <a name="configure-github-as-an-identity-provider"></a>Konfigurowanie usługi GitHub jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **GitHub (wersja zapoznawcza)**.
1. Wprowadź **nazwę**. Na przykład serwis *GitHub*.
1. W polu **Identyfikator klienta** wprowadź identyfikator klienta utworzonej wcześniej aplikacji usługi GitHub.
1. W polu **klucz tajny klienta** Wprowadź zarejestrowany wpis tajny klienta.
1. Wybierz pozycję **Zapisz**.

## <a name="add-github-identity-provider-to-a-user-flow"></a>Dodawanie dostawcy tożsamości usługi GitHub do przepływu użytkownika 

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Kliknij przepływ użytkownika, do którego chcesz dodać dostawcę tożsamości usługi GitHub.
1. W obszarze **dostawcy tożsamości społecznościowej** wybierz pozycję **GitHub**.
1. Wybierz pozycję **Zapisz**.
1. Aby przetestować zasady, wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *testapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom przepływ użytkownika** .
1. Na stronie rejestracji lub logowania wybierz pozycję **GitHub** , aby zalogować się przy użyciu konta usługi GitHub.

Jeśli proces logowania powiedzie się, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać klucz tajny klienta, który został wcześniej zarejestrowany w dzierżawie Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog, w którym znajduje się Twoja dzierżawa.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.
1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje** wybierz opcję `Manual` .
1. Wprowadź **nazwę** klucza zasad. Na przykład `GitHubSecret`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
1. W **kluczu tajnym** wprowadź wcześniej zarejestrowany klucz tajny klienta.
1. W obszarze **użycie klucza** wybierz opcję `Signature` .
1. Kliknij pozycję **Utwórz**.

## <a name="configure-github-as-an-identity-provider"></a>Konfigurowanie usługi GitHub jako dostawcy tożsamości

Aby umożliwić użytkownikom logowanie się przy użyciu konta usługi GitHub, należy zdefiniować konto jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Konto usługi GitHub można zdefiniować jako dostawcę oświadczeń przez dodanie go do elementu **ClaimsProviders** w pliku rozszerzenia zasad.

1. Otwórz *TrustFrameworkExtensions.xml*.
1. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
1. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>github.com</Domain>
      <DisplayName>GitHub</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="GitHub-OAuth2">
          <DisplayName>GitHub</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">github.com</Item>
            <Item Key="authorization_endpoint">https://github.com/login/oauth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://github.com/login/oauth/access_token</Item>
            <Item Key="ClaimsEndpoint">https://api.github.com/user</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="scope">read:user user:email</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="UserAgentForClaimsExchange">CPIM-Basic/{tenant}/{policy}</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your GitHub application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GitHubSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="numericUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="github.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateIssuerUserId" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Ustaw **client_id** na identyfikator aplikacji z rejestracji aplikacji.
1. Zapisz plik.

### <a name="add-the-claims-transformations"></a>Dodaj przekształcenia oświadczeń

Profil techniczny usługi GitHub wymaga dodania do listy ClaimsTransformations przekształceń roszczeń **CreateIssuerUserId** . Jeśli nie masz elementu **ClaimsTransformations** zdefiniowanego w pliku, Dodaj nadrzędne elementy XML, jak pokazano poniżej. Przekształcenia oświadczeń wymagają również nowego typu oświadczenia zdefiniowanego nazwa **numericUserId**.

1. Wyszukaj element [BuildingBlocks](buildingblocks.md) . Jeśli element nie istnieje, Dodaj go.
1. Znajdź element [ClaimsSchema](claimsschema.md) . Jeśli element nie istnieje, Dodaj go.
1. Dodaj numericUserId do elementu **ClaimsSchema** .
1. Znajdź element [ClaimsTransformations](claimstransformations.md) . Jeśli element nie istnieje, Dodaj go.
1. Dodaj przekształcenia oświadczeń CreateIssuerUserId do elementu **ClaimsTransformations** .

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="numericUserId">
      <DisplayName>Numeric user Identifier</DisplayName>
      <DataType>long</DataType>
    </ClaimType>
  </ClaimsSchema>
  <ClaimsTransformations>
    <ClaimsTransformation Id="CreateIssuerUserId" TransformationMethod="ConvertNumberToStringClaim">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="GitHubExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="GitHubExchange" TechnicalProfileReferenceId="GitHub-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testowanie zasad niestandardowych

1. Na przykład wybierz zasady jednostek uzależnionych `B2C_1A_signup_signin` .
1. W przypadku **aplikacji** wybierz [wcześniej zarejestrowaną](troubleshoot-custom-policies.md#troubleshoot-the-runtime)aplikację sieci Web. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom teraz** .
1. Na stronie rejestracji lub logowania wybierz pozycję **GitHub** , aby zalogować się przy użyciu konta usługi GitHub.

Jeśli proces logowania powiedzie się, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.

::: zone-end
