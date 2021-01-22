---
title: Skonfiguruj konto i zaloguj się przy użyciu konta LinkedIn
titleSuffix: Azure AD B2C
description: Zalogować się do klientów i zaloguj się na kontach usługi LinkedIn w swoich aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a378a95a28b1299502ef15cadca60f3807f99429
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98674403"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Konfigurowanie rejestracji i logowania za pomocą konta LinkedIn przy użyciu Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-linkedin-application"></a>Tworzenie aplikacji LinkedIn

Aby włączyć Logowanie użytkowników przy użyciu konta LinkedIn w Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację w [witrynie sieci Web deweloperów serwisu LinkedIn](https://www.developer.linkedin.com/). Aby uzyskać więcej informacji, zobacz [przepływ kodu autoryzacji](/linkedin/shared/authentication/authorization-code-flow). Jeśli nie masz jeszcze konta LinkedIn, możesz zarejestrować się w usłudze [https://www.linkedin.com/](https://www.linkedin.com/) .

1. Zaloguj się do [witryny sieci Web deweloperów serwisu LinkedIn](https://www.developer.linkedin.com/) przy użyciu poświadczeń konta usługi LinkedIn.
1. Wybierz pozycję **Moje aplikacje**, a następnie kliknij pozycję **Utwórz aplikację**.
1. Wprowadź **nazwę aplikacji**, **stronę serwisu LinkedIn**, **adres URL zasad ochrony prywatności** i **logo aplikacji**.
1. Zaakceptuj **warunki użytkowania interfejsu API** usługi LinkedIn i kliknij pozycję **Utwórz aplikację**.
1. Wybierz kartę **uwierzytelnianie** . W obszarze **klucze uwierzytelniania** Skopiuj wartości **Identyfikator klienta** i **klucz tajny klienta**. Musisz mieć oba te elementy, aby skonfigurować konto LinkedIn jako dostawcę tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
1. Wybierz pozycję Edytuj ołówek obok pozycji **autoryzowane adresy URL przekierowań dla aplikacji**, a następnie wybierz pozycję **Dodaj adres URL przekierowania**. Wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` wartość, zastępując `your-tenant-name` ją nazwą dzierżawy. Musisz użyć wszystkich małych liter, wprowadzając nazwę dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z dużymi literami w Azure AD B2C. Wybierz pozycję **Aktualizuj**.
2. Domyślnie aplikacja LinkedIn nie jest zatwierdzona dla zakresów związanych z logowaniem. Aby zażądać przeglądu, wybierz kartę **produkty** , a następnie wybierz pozycję **Zaloguj się przy użyciu konta LinkedIn**. Po zakończeniu przeglądu wymagane zakresy zostaną dodane do aplikacji.
   > [!NOTE]
   > Zakresy, które są obecnie dozwolone dla aplikacji, można wyświetlić na karcie **uwierzytelnianie** w sekcji **zakresy OAuth 2,0** .

::: zone pivot="b2c-user-flow"

## <a name="configure-linkedin-as-an-identity-provider"></a>Konfigurowanie serwisu LinkedIn jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **LinkedIn**.
1. Wprowadź **nazwę**. Na przykład *serwis LinkedIn*.
1. W polu **Identyfikator klienta** wprowadź identyfikator klienta aplikacji LinkedIn, który został utworzony wcześniej.
1. W polu **klucz tajny klienta** Wprowadź zarejestrowany wpis tajny klienta.
1. Wybierz pozycję **Zapisz**.

## <a name="add-linkedin-identity-provider-to-a-user-flow"></a>Dodawanie dostawcy tożsamości LinkedIn do przepływu użytkownika 

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Kliknij przepływ użytkownika, do którego chcesz dodać dostawcę tożsamości LinkedIn.
1. W obszarze **dostawcy tożsamości społecznościowej** wybierz pozycję **LinkedIn**.
1. Wybierz pozycję **Zapisz**.
1. Aby przetestować zasady, wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *testapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij pozycję **Uruchom przepływ użytkownika**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać klucz tajny klienta, który został wcześniej zarejestrowany w dzierżawie Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog, w którym znajduje się Twoja dzierżawa.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.
5. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
6. W obszarze **Opcje** wybierz opcję `Manual` .
7. Wprowadź **nazwę** klucza zasad. Na przykład `LinkedInSecret`. Prefiks *B2C_1A_* jest automatycznie dodawany do nazwy klucza.
8. W **kluczu tajnym** wprowadź wcześniej zarejestrowany klucz tajny klienta.
9. W obszarze **użycie klucza** wybierz opcję `Signature` .
10. Kliknij pozycję **Utwórz**.

## <a name="configure-linkedin-as-an-identity-provider"></a>Konfigurowanie serwisu LinkedIn jako dostawcy tożsamości

Aby umożliwić użytkownikom logowanie się przy użyciu konta LinkedIn, należy zdefiniować konto jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Zdefiniuj konto serwisu LinkedIn jako dostawcę oświadczeń, dodając je do elementu **ClaimsProviders** w pliku rozszerzenia zasad.

1. Otwórz plik * SocialAndLocalAccounts/**TrustFrameworkExtensions.xml** _ w edytorze. Ten plik znajduje się w [pakiecie startowym zasad niestandardowych][starter-pack] pobranym jako część jednego z wymagań wstępnych.
1. Znajdź element _ *ClaimsProviders**. Jeśli nie istnieje, Dodaj ją do elementu głównego.
1. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAuth2">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Zastąp wartość **CLIENT_ID** identyfikatorem klienta aplikacji LinkedIn, który został wcześniej zarejestrowany.
1. Zapisz plik.

### <a name="add-the-claims-transformations"></a>Dodaj przekształcenia oświadczeń

Profil techniczny serwisu LinkedIn wymaga, aby **ExtractGivenNameFromLinkedInResponse** i **ExtractSurNameFromLinkedInResponse** przekształceń oświadczeń zostały dodane do listy ClaimsTransformations. Jeśli nie masz elementu **ClaimsTransformations** zdefiniowanego w pliku, Dodaj nadrzędne elementy XML, jak pokazano poniżej. Przekształcenia oświadczeń wymagają również nowego typu oświadczenia zdefiniowanego nazwa **nullStringClaim**.

Dodaj element **BuildingBlocks** w górnej części pliku *TrustFrameworkExtensions.xml* . Zapoznaj się z przykładem *TrustFrameworkBase.xml* .

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
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
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-create-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="migration-from-v10-to-v20"></a>Migracja z wersji 1.0 do programu v 2.0

Serwis LinkedIn ostatnio [zaktualizował interfejsy API z wersji 1.0 do wersji 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Aby przeprowadzić migrację istniejącej konfiguracji do nowej konfiguracji, Skorzystaj z informacji podanych w poniższych sekcjach, aby zaktualizować elementy w profilu technicznym.

### <a name="replace-items-in-the-metadata"></a>Zastąp elementy w metadanych

W istniejącym elemencie **metadanych** **profilu technicznym** zaktualizuj następujące elementy **elementu** :

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Do:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Dodaj elementy do metadanych

W **metadanych** **profilu technicznym** Dodaj następujące elementy **Item** :

```xml
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Aktualizowanie OutputClaims

W istniejącej **OutputClaims** **profilu technicznym**, zaktualizuj następujące elementy **oświadczenie outputclaim** z:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Do:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Dodaj nowe elementy OutputClaimsTransformation

W **OutputClaimsTransformations** **profilu technicznym** Dodaj następujące elementy **OutputClaimsTransformation** :

```xml
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Zdefiniuj nowe przekształcenia oświadczeń i typ oświadczenia

W ostatnim kroku dodano nowe przekształcenia oświadczeń, które należy zdefiniować. Aby zdefiniować przekształcenia oświadczeń, należy dodać je do listy **ClaimsTransformations**. Jeśli nie masz elementu **ClaimsTransformations** zdefiniowanego w pliku, Dodaj nadrzędne elementy XML, jak pokazano poniżej. Przekształcenia oświadczeń wymagają również nowego typu oświadczenia zdefiniowanego nazwa **nullStringClaim**.

Element **BuildingBlocks** powinien zostać dodany w górnej części pliku. Zobacz *TrustframeworkBase.xml* na przykład.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>Uzyskaj adres e-mail

W ramach migracji serwisu LinkedIn z wersji 1.0 do wersji 2.0 do uzyskania adresu e-mail wymagany jest dodatkowe wywołanie innego interfejsu API. Jeśli musisz uzyskać adres e-mail podczas rejestracji, wykonaj następujące czynności:

1. Wykonaj powyższe kroki, aby zezwolić Azure AD B2C sfederować z usługą LinkedIn na logowanie użytkownika. W ramach Federacji Azure AD B2C otrzymuje token dostępu dla usługi LinkedIn.
1. Zapisz token dostępu LinkedIn w ramach żądania. [Zapoznaj się z instrukcjami znajdującymi się tutaj](idp-pass-through-user-flow.md).
1. Dodaj następującego dostawcę oświadczeń, który wysyła żądanie do `/emailAddress` interfejsu API usługi LinkedIn. Aby można było autoryzować to żądanie, wymagany jest token dostępu do serwisu LinkedIn.

    ```xml
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Dodaj następujący krok aranżacji do podróży użytkownika, aby Dostawca oświadczeń API był wyzwalany, gdy użytkownik zaloguje się przy użyciu usługi LinkedIn. Upewnij się, że `Order` numer jest odpowiednio aktualizowany. Dodaj ten krok bezpośrednio po kroku aranżacji, który wyzwala profil techniczny serwisu LinkedIn.

    ```xml
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

Uzyskiwanie adresu e-mail z serwisu LinkedIn podczas rejestracji jest opcjonalne. Jeśli zdecydujesz się nie uzyskiwać poczty e-mail z serwisu LinkedIn, ale będzie ona potrzebna podczas rejestracji, użytkownik musi ręcznie wprowadzić adres e-mail i sprawdzić jego poprawność.

Aby zapoznać się z pełnymi przykładami zasad, które korzystają z dostawcy tożsamości LinkedIn, zobacz temat [niestandardowy pakiet zasad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="migration-from-v10-to-v20"></a>Migracja z wersji 1.0 do programu v 2.0

Serwis LinkedIn ostatnio [zaktualizował interfejsy API z wersji 1.0 do wersji 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). W ramach migracji Azure AD B2C jest w stanie uzyskać pełną nazwę użytkownika serwisu LinkedIn podczas rejestracji. Jeśli adres e-mail jest jednym z atrybutów, które są zbierane podczas rejestracji, użytkownik musi ręcznie wprowadzić adres e-mail i sprawdzić jego poprawność.

::: zone-end