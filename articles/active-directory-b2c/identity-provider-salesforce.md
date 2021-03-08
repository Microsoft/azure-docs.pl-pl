---
title: Konfigurowanie rejestracji i logowania przy użyciu konta usługi Salesforce
titleSuffix: Azure AD B2C
description: Zapewnij klientom konta usługi Salesforce i zaloguj się w swoich aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 578a248de3ee6aacc0db7c5116b66031b404c9ae
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448170"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>Skonfiguruj konto usługi Salesforce i zaloguj się przy użyciu usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>Tworzenie aplikacji usługi Salesforce

Aby włączyć Logowanie użytkowników przy użyciu konta usługi Salesforce w Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację w [Menedżerze aplikacji](https://login.salesforce.com/)usługi Salesforce. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień podstawowej połączonej aplikacji](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm)i [Włączanie ustawień uwierzytelniania OAuth dla integracji z interfejsem API](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm)

1. [Zaloguj się do usługi Salesforce](https://login.salesforce.com/).
1. Z menu wybierz pozycję **Setup (Konfiguracja**).
1.  Rozwiń węzeł **aplikacje**, a następnie wybierz pozycję **App Manager**.
1. Wybierz pozycję **Nowa podłączona aplikacja**.
1. W obszarze **podstawowe informacje** wpisz:
    1. **Nazwa połączonej aplikacji** — nazwa połączonej aplikacji jest wyświetlana w Menedżerze aplikacji i na kafelku uruchamiania aplikacji. Nazwa musi być unikatowa w organizacji. 
    1. **Nazwa interfejsu API** 
    1. **Kontaktowy adres e-mail** — kontaktowy adres e-mail w usłudze Salesforce
1. W obszarze **interfejs API (Włączanie ustawień uwierzytelniania OAuth)** wybierz pozycję **Włącz ustawienia uwierzytelniania OAuth** .
    1. W polu **adres URL wywołania zwrotnego** wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Zamień `your-tenant-name` na nazwę dzierżawy. Musisz użyć wszystkich małych liter, wprowadzając nazwę dzierżawy, nawet jeśli dzierżawa jest zdefiniowana z dużymi literami w Azure AD B2C.
    1. W **wybranych zakresach uwierzytelniania OAuth** wybierz pozycję **dostęp do podstawowych informacji (identyfikator, profil, adres e-mail, numer telefonu)** i **Zezwalaj na dostęp do unikatowego identyfikatora (OpenID Connect)**.
    1. Wybierz pozycję **Wymagaj wpisu tajnego dla przepływu serwera sieci Web**.
1. Wybierz pozycję **Konfiguruj token identyfikatora** 
    1. Ustaw **token ważny przez** 5 minut.
    1. Wybierz pozycję **Uwzględnij standardowe oświadczenia**.
1. Kliknij pozycję **Zapisz**.
1. Skopiuj wartości **klucza klienta** i **wpisu tajnego klienta**. Oba te elementy będą potrzebne do skonfigurowania usługi Salesforce jako dostawcy tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.

::: zone pivot="b2c-user-flow"

## <a name="configure-salesforce-as-an-identity-provider"></a>Konfigurowanie usługi Salesforce jako dostawcy tożsamości

1. Upewnij się, że używasz katalogu, który zawiera Azure AD B2C dzierżawcy. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Nowy dostawca połączenia OpenID Connect**.
1. Wprowadź **nazwę**. Na przykład wprowadź w usłudze *Salesforce*.
1. W polu **adres URL metadanych** wprowadź adres URL [dokumentu konfiguracji programu Salesforce OpenID Connect Connect](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm). W przypadku piaskownicy login.salesforce.com jest zastępowany test.salesforce.com. W przypadku społeczności login.salesforce.com zostaje zamieniony na adres URL Wspólnoty, taki jak username.force.com/.well-known/openid-configuration. Adres URL musi być adresem HTTPS.

    ```
    https://login.salesforce.com/.well-known/openid-configuration
    ```

1. W polu **Identyfikator klienta** wprowadź wcześniej zarejestrowany identyfikator aplikacji.
1. W polu **klucz tajny klienta** wprowadź wcześniej zarejestrowany klucz tajny klienta.
1. Dla **zakresu**, wprowadź `openid id profile email` .
1. Pozostaw wartości domyślne dla **typu odpowiedzi** i **trybu odpowiedzi**.
1. Obowiązkowe Dla wskazówki dotyczącej **domeny** wprowadź wartość `contoso.com` . Aby uzyskać więcej informacji, zobacz [Konfigurowanie bezpośredniego logowania przy użyciu Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. W obszarze **Mapowanie oświadczeń dostawcy tożsamości** wybierz następujące oświadczenia:

    - **Identyfikator użytkownika**: *Sub*
    - **Nazwa wyświetlana**: *Nazwa*
    - **Imię:** *given_name*
    - **Nazwisko**: *family_name*
    - **Wiadomość e-mail**: *e-mail*

1. Wybierz pozycję **Zapisz**.

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>Dodawanie dostawcy tożsamości usługi Salesforce do przepływu użytkownika 

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Kliknij przepływ użytkownika, do którego chcesz dodać dostawcę tożsamości usługi Salesforce.
1. W obszarze **dostawcy tożsamości społecznościowej** wybierz pozycję **Salesforce**.
1. Wybierz pozycję **Zapisz**.
1. Aby przetestować zasady, wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *testapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom przepływ użytkownika** .
1. Na stronie rejestracji lub logowania wybierz pozycję **Salesforce** , aby zalogować się przy użyciu konta usługi Salesforce.

Jeśli proces logowania powiedzie się, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.

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
7. Wprowadź **nazwę** klucza zasad. Na przykład `SalesforceSecret`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
8. W **kluczu tajnym** wprowadź wcześniej zarejestrowany klucz tajny klienta.
9. W obszarze **użycie klucza** wybierz opcję `Signature` .
10. Kliknij pozycję **Utwórz**.

## <a name="configure-salesforce-as-an-identity-provider"></a>Konfigurowanie usługi Salesforce jako dostawcy tożsamości

Aby umożliwić użytkownikom logowanie się przy użyciu konta usługi Salesforce, należy zdefiniować konto jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Konto usługi Salesforce można zdefiniować jako dostawcę oświadczeń, dodając je do elementu **ClaimsProviders** w pliku rozszerzenia zasad.

1. Otwórz *TrustFrameworkExtensions.xml*.
2. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
3. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OpenIdConnect">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://login.salesforce.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid id profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your Salesforce application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_SalesforceSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **Metadane** są ustawiane na adres URL [dokumentu konfiguracji programu Salesforce OpenID Connect Connect](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm). W przypadku piaskownicy login.salesforce.com jest zastępowany test.salesforce.com. W przypadku społeczności login.salesforce.com zostaje zamieniony na adres URL Wspólnoty, taki jak username.force.com/.well-known/openid-configuration. Adres URL musi być adresem HTTPS.
5. Ustaw **client_id** na identyfikator aplikacji z rejestracji aplikacji.
6. Zapisz plik.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testowanie zasad niestandardowych

1. Na przykład wybierz zasady jednostek uzależnionych `B2C_1A_signup_signin` .
1. W przypadku **aplikacji** wybierz [wcześniej zarejestrowaną](troubleshoot-custom-policies.md#troubleshoot-the-runtime)aplikację sieci Web. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom teraz** .
1. Na stronie rejestracji lub logowania wybierz pozycję **Salesforce** , aby zalogować się przy użyciu konta usługi Salesforce.

Jeśli proces logowania powiedzie się, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.


::: zone-end

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [przekazać token usługi Salesforce do aplikacji](idp-pass-through-user-flow.md).
