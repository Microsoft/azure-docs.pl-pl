---
title: Konfigurowanie logowania za pomocą konta usługi LinkedIn przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Skonfiguruj logowanie się przy użyciu konta LinkedIn w Azure Active Directory B2C przy użyciu zasad niestandardowych.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 084a8e247ab8bb3e6e6c75cf8cfe8e204384f549
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345154"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie logowania za pomocą konta LinkedIn przy użyciu zasad niestandardowych w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule opisano sposób włączania logowania dla użytkowników z konta LinkedIn przy użyciu [zasad niestandardowych](custom-policy-overview.md) w programie Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych w Azure Active Directory B2C](custom-policy-get-started.md).
- Konto LinkedIn — Jeśli jeszcze go nie masz, [Utwórz konto](https://www.linkedin.com/start/join).
- Strona serwisu LinkedIn — musisz mieć [stronę serwisu LinkedIn](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page) , aby skojarzyć ją z aplikacją LinkedIn utworzoną w następnej sekcji.

## <a name="create-an-application"></a>Tworzenie aplikacji

Aby korzystać z serwisu LinkedIn jako dostawcy tożsamości w Azure AD B2C, musisz utworzyć aplikację LinkedIn.

### <a name="create-app"></a>Tworzenie aplikacji

1. Zaloguj się do witryny sieci Web [zarządzania aplikacjami LinkedIn](https://www.linkedin.com/secure/developer?newapp=) przy użyciu poświadczeń konta usługi LinkedIn.
1. Wybierz pozycję **Utwórz aplikację**.
1. Wprowadź **nazwę aplikacji**.
1. Wprowadź nazwę **firmy** odpowiadającą nazwie strony serwisu LinkedIn. Utwórz stronę serwisu LinkedIn, jeśli jeszcze jej nie masz.
1. Obowiązkowe Wprowadź **adres URL zasad ochrony prywatności**. Musi być prawidłowym adresem URL, ale nie musi być osiągalnym punktem końcowym.
1. Wprowadź **służbowy adres e-mail**.
1. Przekaż obraz **logo aplikacji** . Obraz logo musi być kwadratem, a jego wymiary muszą mieć co najmniej 100x100 pikseli.
1. Pozostaw ustawienia domyślne w sekcji **produkty** .
1. Przejrzyj informacje przedstawione w **postanowieniach prawnych**. Jeśli akceptujesz warunki, zaznacz pole wyboru.
1. Wybierz pozycję **Utwórz aplikację**.

### <a name="configure-auth"></a>Konfigurowanie uwierzytelniania

1. Wybierz kartę **uwierzytelnianie** .
1. Zapisz **Identyfikator klienta**.
1. Odsłoń i Zapisz **klucz tajny klienta**.
1. W obszarze **Ustawienia protokołu OAuth 2,0** Dodaj następujący **adres URL przekierowania**. Zamień `your-tenant` na nazwę dzierżawy. Użyj **wszystkich małych liter** dla nazwy dzierżawy, nawet jeśli jest ona zdefiniowana z dużymi literami w Azure AD B2C.

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

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

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Jeśli chcesz, aby użytkownicy mogli się logować przy użyciu konta LinkedIn, musisz zdefiniować konto jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Zdefiniuj konto serwisu LinkedIn jako dostawcę oświadczeń, dodając je do elementu **ClaimsProviders** w pliku rozszerzenia zasad.

1. Otwórz plik * SocialAndLocalAccounts/**TrustFrameworkExtensions.xml** _ w edytorze. Ten plik znajduje się w [pakiecie startowym zasad niestandardowych][starter-pack] pobranym jako część jednego z wymagań wstępnych.
1. Znajdź element _ *ClaimsProviders**. Jeśli nie istnieje, Dodaj ją do elementu głównego.
1. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
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

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia w celu weryfikacji

Masz już skonfigurowane zasady, które Azure AD B2C wiedzą, jak komunikować się z kontem w serwisie LinkedIn. Spróbuj przekazać plik rozszerzenia zasad, aby upewnić się, że nie ma żadnych problemów do tej pory.

1. Na stronie **zasady niestandardowe** w dzierżawie Azure AD B2C wybierz pozycję **Przekaż zasady**.
2. Włącz **Zastępowanie zasad, jeśli istnieje**, a następnie wyszukaj i wybierz plik *TrustFrameworkExtensions.xml* .
3. Kliknij pozycję **Przekaż**.

## <a name="register-the-claims-provider"></a>Rejestrowanie dostawcy oświadczeń

W tym momencie dostawca tożsamości został skonfigurowany, ale nie jest dostępny na żadnym z ekranów rejestracji lub logowania. Aby można było go udostępnić, należy utworzyć duplikat istniejącej podróży użytkownika szablonu, a następnie zmodyfikować go tak, aby miał także dostawcę tożsamości LinkedIn.

1. Otwórz plik *TrustFrameworkBase.xml* w pakiecie startowym.
2. Znajdź i Skopiuj całą zawartość elementu **UserJourney** , który zawiera `Id="SignUpOrSignIn"` .
3. Otwórz *TrustFrameworkExtensions.xml* i Znajdź element **UserJourneys** . Jeśli element nie istnieje, Dodaj go.
4. Wklej całą zawartość elementu **UserJourney** , który został skopiowany jako element podrzędny elementu **UserJourneys** .
5. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>Wyświetl przycisk

Element **ClaimsProviderSelection** jest analogiczny do przycisku dostawcy tożsamości na ekranie rejestracji lub logowania. Jeśli dodasz element **ClaimsProviderSelection** dla konta LinkedIn, nowy przycisk będzie wyświetlany, gdy użytkownik zostanie wystawiony na stronie.

1. Znajdź element **OrchestrationStep** , który obejmuje `Order="1"` w podróży użytkownika.
2. W obszarze **ClaimsProviderSelections** Dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** na odpowiednią wartość, na przykład `LinkedInExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk z akcją

Teraz, gdy masz już przycisk, musisz połączyć go z akcją. W tym przypadku akcja w tym przypadku Azure AD B2C do komunikowania się z kontem LinkedIn w celu uzyskania tokenu.

1. Znajdź **OrchestrationStep** obejmujący `Order="2"` w podróży użytkownika.
2. Dodaj następujący element **ClaimsExchange** , aby upewnić się, że używasz tej samej wartości dla identyfikatora, który został użyty dla **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId** na identyfikator utworzonego wcześniej profilu technicznego. Na przykład `LinkedIn-OAUTH`.

3. Zapisz plik *TrustFrameworkExtensions.xml* i przekaż go ponownie w celu weryfikacji.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji Azure AD B2C

Komunikacja z Azure AD B2C odbywa się za pomocą aplikacji zarejestrowanej w dzierżawie B2C. W tej sekcji przedstawiono kroki opcjonalne, które można wykonać, aby utworzyć aplikację testową, jeśli nie została jeszcze wykonana.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnionej

Zaktualizuj plik jednostki uzależnionej (RP), który inicjuje utworzoną przez Ciebie podróż użytkownika.

1. Utwórz kopię *SignUpOrSignIn.xml* w katalogu roboczym i zmień jej nazwę. Na przykład zmień nazwę na *SignUpSignInLinkedIn.xml*.
2. Otwórz nowy plik i zaktualizuj wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** przy użyciu unikatowej wartości. Na przykład `SignUpSignInLinkedIn`.
3. Zaktualizuj wartość **PublicPolicyUri** za pomocą identyfikatora URI dla zasad. Na przykład`http://contoso.com/B2C_1A_signup_signin_linkedin`
4. Zaktualizuj wartość atrybutu **ReferenceId** w **DefaultUserJourney** w taki sposób, aby odpowiadała identyfikatorowi nowej podróży użytkownika, która została utworzona (SignUpSignLinkedIn).
5. Zapisz zmiany, Przekaż plik, a następnie wybierz nowe zasady z listy.
6. Upewnij się, że utworzona aplikacja Azure AD B2C została wybrana w polu **Wybierz aplikację** , a następnie przetestuj ją, klikając polecenie **Uruchom teraz**.

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
2. Zapisz token dostępu LinkedIn w ramach żądania. [Zapoznaj się z instrukcjami znajdującymi się tutaj](idp-pass-through-custom.md).
3. Dodaj następującego dostawcę oświadczeń, który wysyła żądanie do `/emailAddress` interfejsu API usługi LinkedIn. Aby można było autoryzować to żądanie, wymagany jest token dostępu do serwisu LinkedIn.

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

4. Dodaj następujący krok aranżacji do podróży użytkownika, aby Dostawca oświadczeń API był wyzwalany, gdy użytkownik zaloguje się przy użyciu usługi LinkedIn. Upewnij się, że `Order` numer jest odpowiednio aktualizowany. Dodaj ten krok bezpośrednio po kroku aranżacji, który wyzwala profil techniczny serwisu LinkedIn.

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
