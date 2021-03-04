---
title: Punkt końcowy UserInfo | Microsoft Docs
description: Zdefiniuj punkt końcowy UserInfo w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 55d3f38405d8f03ea7c13077872c2b7f7bc30b72
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120661"
---
# <a name="userinfo-endpoint"></a>Punkt końcowy informacji o użytkowniku

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Punkt końcowy UserInfo jest częścią specyfikacji [OpenID Connect Connect Standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC) i jest przeznaczony do zwracania oświadczeń dotyczących uwierzytelnionego użytkownika. Punkt końcowy UserInfo jest zdefiniowany w zasadach jednostki uzależnionej przy użyciu elementu [punktu końcowego](relyingparty.md#endpoints) .  

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="userinfo-endpoint-overview"></a>Przegląd punktu końcowego UserInfo

UserJourney informacji o użytkowniku określa:

- **Autoryzacja**: punkt końcowy UserInfo jest chroniony za pomocą tokenu okaziciela. Wystawiony token dostępu jest prezentowany w nagłówku autoryzacji w punkcie końcowym UserInfo. Zasady określają profil techniczny, który sprawdza poprawność tokenu przychodzącego i wyodrębnia oświadczenia, takie jak identyfikator objectId użytkownika. Identyfikator objectId użytkownika służy do pobierania oświadczeń do zwrócenia w odpowiedzi na podróż punktu końcowego UserInfo. 
- **Krok aranżacji**: 
  - Krok aranżacji służy do zbierania informacji o użytkowniku. W oparciu o oświadczenia w ramach przychodzącego tokenu dostępu, podróż użytkownika wywołuje [profil techniczny Azure Active Directory](active-directory-technical-profile.md) , aby pobrać dane dotyczące użytkownika, na przykład odczytując użytkownika według identyfikatora obiektu. 
  - **Opcjonalne kroki aranżacji** — możesz dodać więcej kroków aranżacji, takich jak profil techniczny interfejsu API REST, aby uzyskać więcej informacji o użytkowniku. 
  - **Wystawca UserInfo** — określa listę oświadczeń zwracanych przez punkt końcowy UserInfo.

## <a name="create-a-userinfo-endpoint"></a>Tworzenie punktu końcowego UserInfo

### <a name="1-add-the-token-issuer-technical-profile"></a>1. Dodaj profil techniczny wystawcy tokenu

1. Otwórz plik *TrustFrameworkExtensions.xml* .
1. Jeśli jeszcze nie istnieje, Dodaj element ClaimsProvider i jego elementy podrzędne jako pierwszy element w elemencie BuildingBlocks.
1. Dodaj następującego dostawcę oświadczeń:

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Token Issuer</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="UserInfoIssuer">
            <DisplayName>JSON Issuer</DisplayName>
            <Protocol Name="None" />
            <OutputTokenFormat>JSON</OutputTokenFormat>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <!-- The Below claims are what will be returned on the UserInfo Endpoint if in the Claims Bag-->
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId"/>
              <InputClaim ClaimTypeReferenceId="givenName"/>
              <InputClaim ClaimTypeReferenceId="surname"/>
              <InputClaim ClaimTypeReferenceId="displayName"/>
              <InputClaim ClaimTypeReferenceId="signInNames.emailAddress"/>
            </InputClaims>
          </TechnicalProfile>
          <TechnicalProfile Id="UserInfoAuthorization">
            <DisplayName>UserInfo authorization</DisplayName>
            <Protocol Name="None" />
            <InputTokenFormat>JWT</InputTokenFormat>
            <Metadata>
              <!-- Update the Issuer and Audience below -->
              <!-- Audience is optional, Issuer is required-->
              <Item Key="issuer">https://yourtenant.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/</Item>
              <Item Key="audience">[ "22222222-2222-2222-2222-222222222222", "33333333-3333-3333-3333-333333333333" ]</Item>
              <Item Key="client_assertion_type">urn:ietf:params:oauth:client-assertion-type:jwt-bearer</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
              <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" PartnerClaimType="email"/>
              <!-- Optional claims to read from the access token. -->
              <!-- <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
                 <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
                 <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/> -->
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ``` 

1. Sekcja InputClaims w profilu technicznym **UserInfoIssuer** określa atrybuty, które mają zostać zwrócone. Profil techniczny UserInfoIssuer jest wywoływany na końcu podróży użytkownika. 
1. Profil techniczny **UserInfoAuthorization** sprawdza poprawność podpisu, nazwy wystawcy i odbiorców tokenu, a następnie wyodrębnia z tokenu przychodzącego. Zmień następujące metadane, aby odzwierciedlały Twoje środowisko:
    1. **wystawca** — ta wartość musi być taka sama jak w przypadku żądania `iss` tokenu dostępu. Tokeny wystawione przez Azure AD B2C używają wystawcy w formacie `https://yourtenant.b2clogin.com/your-tenant-id/v2.0/` . Dowiedz się więcej o [dostosowywaniu tokenu](configure-tokens.md).
    1. **IdTokenAudience** — musi być taka sama jak w przypadku żądania `aud` tokenu dostępu. W Azure AD B2C to `aud` jest identyfikator aplikacji jednostki uzależnionej. Ta wartość jest kolekcją i obsługuje wiele wartości przy użyciu ogranicznika przecinka.

        W poniższym tokenie dostępu `iss` wartość jest równa `https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/` . `aud`Wartość żądania to `22222222-2222-2222-2222-222222222222` .

        ```json
        {
          "exp": 1605549468,
          "nbf": 1605545868,
          "ver": "1.0",
          "iss": "https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/",
          "sub": "44444444-4444-4444-4444-444444444444",
          "aud": "22222222-2222-2222-2222-222222222222",
          "acr": "b2c_1a_signup_signin",
          "nonce": "defaultNonce",
          "iat": 1605545868,
          "auth_time": 1605545868,
          "name": "John Smith",
          "given_name": "John",
          "family_name": "Smith",
          "tid": "11111111-1111-1111-1111-111111111111"
        }
        ```
    
1.  Element OutputClaims profilu technicznego **UserInfoAuthorization** określa atrybuty, które mają zostać odczytane z tokenu dostępu. **ClaimTypeReferenceId** jest odwołaniem do typu zgłoszenia. Opcjonalna **PartnerClaimType** to nazwa żądania zdefiniowanego w tokenie dostępu.



### <a name="2-add-the-userjourney-element"></a>2. Dodaj element UserJourney 

Element [UserJourney](userjourneys.md) definiuje ścieżkę, którą użytkownik podejmuje podczas korzystania z aplikacji. Dodaj element **UserJourneys** , jeśli nie istnieje z **UserJourney** identyfikowanego jako `UserInfoJourney` :

```xml
<!-- 
<UserJourneys> -->
  <UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
    <Authorization>
      <AuthorizationTechnicalProfiles>
        <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
      </AuthorizationTechnicalProfiles>
    </Authorization>
    <OrchestrationSteps >
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>objectId</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges UserIdentity="false">
          <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="UserInfoIssuer" />
    </OrchestrationSteps>
  </UserJourney>
<!-- 
</UserJourneys> -->
```

### <a name="3-include-the-endpoint-to-the-relying-party-policy"></a>3. Dołącz punkt końcowy do zasad jednostki uzależnionej

Aby dołączyć punkt końcowy UserInfo w aplikacji jednostki uzależnionej, Dodaj element [Endpoint](relyingparty.md#endpoints) do pliku *SocialAndLocalAccounts/SignUpOrSignIn.xml* . 

```xml
<Endpoints>
  <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
</Endpoints>
```

Ukończony element jednostki uzależnionej będzie następujący:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="yourtenant.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <Endpoints>
      <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
    </Endpoints>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="4-upload-the-files"></a>4. Przekaż pliki

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **platforma obsługi tożsamości**.
1. Na stronie **zasady niestandardowe** wybierz opcję **Przekaż zasady niestandardowe**.
1. Wybierz opcję **Zastąp zasady niestandardowe, jeśli już istnieje**, a następnie wyszukaj i wybierz plik *TrustframeworkExtensions.xml* .
1. Kliknij pozycję **Przekaż**.
1. Powtórz kroki od 5 do 7 dla pliku jednostki uzależnionej, takie jak *SignUpOrSignIn.xml*.

## <a name="calling-the-userinfo-endpoint"></a>Wywoływanie punktu końcowego UserInfo

Punkt końcowy UserInfo korzysta ze standardowego interfejsu API OAuth2 okaziciela, wywoływanego przy użyciu tokenu dostępu otrzymanego podczas uzyskiwania tokenu dla aplikacji. Zwraca odpowiedź JSON zawierającą oświadczenia dotyczące użytkownika. Punkt końcowy UserInfo jest hostowany na Azure AD B2C w:

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/openid/v2.0/userinfo
```

/.Well-Known Konfiguracja punktu końcowego (OpenID Connect Connect Discovery Document) wyświetla `userinfo_endpoint` pole. Można programowo wykryć punkt końcowy UserInfo przy użyciu punktu końcowego/.Well-Known Configure w: 

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/v2.0/.well-known/openid-configuration 
```

### <a name="test-the-policy"></a>Testowanie zasad

1. W obszarze **zasady niestandardowe** wybierz zasady, które zostały zintegrowane z punktem końcowym UserInfo przy użyciu programu. Na przykład *B2C_1A_SignUpOrSignIn*.
1. Wybierz pozycję **Uruchom teraz**. 
1. W obszarze **Wybierz aplikację** wybierz wcześniej zarejestrowaną aplikację. Dla **opcji wybierz adres URL odpowiedzi** wybierz opcję `https://jwt.ms` . Aby uzyskać więcej informacji, zobacz [Rejestrowanie aplikacji sieci Web w Azure Active Directory B2C](tutorial-register-applications.md).
1. Utwórz konto lub Zaloguj się przy użyciu adresu e-mail lub konta społecznościowego.
1. Skopiuj id_token w swoim zakodowanym formacie z [https://jwt.ms](https://jwt.ms) witryny sieci Web. Możesz użyć tego do przesłania żądania GET do punktu końcowego UserInfo i pobrania informacji o użytkowniku.
1. Prześlij żądanie GET do punktu końcowego UserInfo i Pobierz informacje o użytkowniku.

```http
GET /yourtenant.onmicrosoft.com/b2c_1a_signup_signin/openid/v2.0/userinfo
Host: b2cninja.b2clogin.com
Authorization: Bearer <your ID token>
```

Pomyślna odpowiedź będzie wyglądać następująco:

```json
{
    "objectId": "44444444-4444-4444-4444-444444444444",
    "givenName": "John",
    "surname": "Smith",
    "displayName": "John Smith",
    "signInNames.emailAddress": "john.s@contoso.com"
}
```

## <a name="next-steps"></a>Następne kroki

- Przykład zasad usługi UserInfo można znaleźć w witrynie [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/user-info-endpoint).

::: zone-end
