---
title: Skonfiguruj konto i zaloguj się przy użyciu konta usługi Twitter
titleSuffix: Azure AD B2C
description: Zalogować się do klientów za pomocą kont usługi Twitter w swoich aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a998491729a1d3bd472ecc3de9722c142f8dc182
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98953788"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Skonfiguruj konto usługi Twitter i zaloguj się przy użyciu usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]
::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-application"></a>Tworzenie aplikacji

Aby włączyć Logowanie użytkowników przy użyciu konta usługi Twitter w Azure AD B2C, musisz utworzyć aplikację w usłudze Twitter. Jeśli nie masz jeszcze konta usługi Twitter, możesz zarejestrować się w usłudze [https://twitter.com/signup](https://twitter.com/signup) . Należy również [zastosować do konta dewelopera](https://developer.twitter.com/en/apply/user.html). Aby uzyskać więcej informacji, zobacz temat [Apply for Access](https://developer.twitter.com/en/apply-for-access).

1. Zaloguj się do [portalu dla deweloperów](https://developer.twitter.com/portal/projects-and-apps) w usłudze Twitter przy użyciu poświadczeń konta usługi Twitter.
1. W obszarze **Aplikacje autonomiczne** wybierz pozycję **+ Utwórz aplikację**.
1. Wprowadź **nazwę aplikacji**, a następnie wybierz pozycję **ukończone**.
1. Skopiuj wartość **klucz aplikacji** i klucz **tajny klucza interfejsu API**.  Oba te elementy umożliwiają skonfigurowanie usługi Twitter jako dostawcy tożsamości w dzierżawie. 
1. W obszarze **Konfigurowanie aplikacji** wybierz pozycję **Ustawienia aplikacji**.
1. W obszarze **Ustawienia uwierzytelniania** wybierz pozycję **Edytuj** .
    1. Zaznacz pole wyboru **Włącz 3-bokami OAuth** .
    1. Zaznacz pole wyboru **Żądaj adresu e-mail z użytkownikami** .
    1. W polu **adresy URL wywołania zwrotnego** wprowadź wartość `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` . Zastąp `your-tenant` wartość nazwą swojej dzierżawy i `your-user-flow-Id` identyfikatorem przepływu użytkownika. Na przykład `b2c_1A_signup_signin_twitter`. Użyj wszystkich małych liter, wprowadzając nazwę dzierżawy i identyfikator przepływu użytkownika, nawet jeśli są one zdefiniowane przy użyciu wielkich liter w Azure AD B2C.
    1. W polu **adres URL witryny sieci Web** wprowadź `https://your-tenant.b2clogin.com` . Zamień `your-tenant` na nazwę dzierżawy. Na przykład `https://contosob2c.b2clogin.com`.
    1. Wprowadź adres URL dla **warunków użytkowania usługi**, na przykład `http://www.contoso.com/tos` . Adres URL zasad to strona, która jest utrzymywana w celu zapewnienia warunków i postanowień aplikacji.
    1. Wprowadź na przykład adres URL **zasad ochrony prywatności** `http://www.contoso.com/privacy` . Adres URL zasad to strona, którą przechowujesz, aby zapewnić informacje o ochronie prywatności dla aplikacji.
    1. Wybierz pozycję **Zapisz**.

::: zone pivot="b2c-user-flow"

## <a name="configure-twitter-as-an-identity-provider"></a>Konfigurowanie usługi Twitter jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Twitter**.
1. Wprowadź **nazwę**. Na przykład *serwis Twitter*.
1. W polu **Identyfikator klienta** wprowadź *klucz interfejsu API* utworzonej wcześniej aplikacji Twitter.
1. Dla **wpisu tajnego klienta** wprowadź zarejestrowane *hasło klucza interfejsu API* .
1. Wybierz pozycję **Zapisz**.

## <a name="add-twitter-identity-provider-to-a-user-flow"></a>Dodawanie dostawcy tożsamości usługi Twitter do przepływu użytkownika 

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Wybierz przepływ użytkownika, do którego chcesz dodać dostawcę tożsamości w usłudze Twitter.
1. W obszarze **dostawcy tożsamości społecznościowej** wybierz pozycję **Twitter**.
1. Wybierz pozycję **Zapisz**.
1. Aby przetestować zasady, wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *testapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij pozycję **Uruchom przepływ użytkownika**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać klucz tajny, który został wcześniej zarejestrowany w dzierżawie Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog, w którym znajduje się Twoja dzierżawa.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.
5. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
6. W obszarze **Opcje** wybierz opcję `Manual` .
7. Wprowadź **nazwę** klucza zasad. Na przykład `TwitterSecret`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
8. W **kluczu tajnym** wprowadź wcześniej zarejestrowany klucz tajny klienta.
9. W obszarze **użycie klucza** wybierz opcję `Encryption` .
10. Kliknij pozycję **Utwórz**.

## <a name="configure-twitter-as-an-identity-provider"></a>Konfigurowanie usługi Twitter jako dostawcy tożsamości

Aby umożliwić użytkownikom logowanie się przy użyciu konta usługi Twitter, należy zdefiniować konto jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Konto w usłudze Twitter można zdefiniować jako dostawcę oświadczeń, dodając je do elementu **ClaimsProviders** w pliku rozszerzenia zasad.

1. Otwórz *TrustFrameworkExtensions.xml*.
2. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
3. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAuth1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application API key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
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

4. Zastąp wartość **client_id** wartością *tajnego klucza interfejsu API* , który został wcześniej zarejestrowany.
5. Zapisz plik.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAuth1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]


::: zone-end
