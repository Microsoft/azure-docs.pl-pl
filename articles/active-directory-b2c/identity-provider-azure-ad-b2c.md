---
title: Konfigurowanie rejestracji i logowania przy użyciu konta Azure AD B2C z poziomu innej dzierżawy Azure AD B2C
titleSuffix: Azure AD B2C
description: Podaj konto i zaloguj się do klientów z kontami Azure AD B2C z innej dzierżawy w aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c9a20305f05b285b29d4a5eaf75116c862f3a6d4
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448493"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>Konfigurowanie rejestracji i logowania przy użyciu konta Azure AD B2C z poziomu innej dzierżawy Azure AD B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>Omówienie

W tym artykule opisano sposób konfigurowania Federacji z inną dzierżawą Azure AD B2C. Gdy aplikacje są chronione za pomocą Azure AD B2C, umożliwi to użytkownikom z innych B2C's usługi Azure AD logowanie się przy użyciu istniejących kont. Na poniższym diagramie użytkownicy mogą zalogować się do aplikacji chronionej przez Azure AD B2C firmy *contoso* przy użyciu konta zarządzanego przez dzierżawę Azure AD B2C firmy *Fabrikam* 

![Azure AD B2C Federacji z inną dzierżawą Azure AD B2C](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji Azure AD B2C

Aby włączyć Logowanie użytkowników przy użyciu konta z innej dzierżawy Azure AD B2C (na przykład Fabrikam), w Azure AD B2C (na przykład contoso):

1. Tworzenie [przepływu użytkownika](tutorial-create-user-flows.md)lub [zasad niestandardowych](custom-policy-get-started.md).
1. Następnie Utwórz aplikację w Azure AD B2C, zgodnie z opisem w tej sekcji. 

Aby utworzyć aplikację.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera inną dzierżawę Azure AD B2C (na przykład Fabrikam.com).
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *ContosoApp*.
1. W obszarze **obsługiwane typy kont** wybierz pozycję **konta w dowolnym dostawcy tożsamości lub katalogu organizacji (do uwierzytelniania użytkowników za pomocą przepływów użytkowników)**.
1. W obszarze **Identyfikator URI przekierowania** wybierz pozycję **Sieć Web**, a następnie wprowadź następujący adres URL w postaci małych liter, gdzie `your-B2C-tenant-name` jest zastępowana nazwą dzierżawy Azure AD B2C (na przykład contoso).

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Na przykład `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. W obszarze uprawnienia zaznacz pole wyboru **Udziel zgody na uprawnienia administratora do OpenID Connect i offline_access** .
1. Wybierz pozycję **Zarejestruj**.
1. Na stronie **Azure AD B2C rejestracje aplikacji** Wybierz utworzoną aplikację, na przykład *ContosoApp*.
1. Zapisz **Identyfikator aplikacji (klienta)** widoczny na stronie przeglądu aplikacji. Jest to potrzebne podczas konfigurowania dostawcy tożsamości w następnej sekcji.
1. W menu po lewej stronie w obszarze **Zarządzaj** wybierz pozycję **Certyfikaty & wpisy tajne**.
1. Wybierz pozycję **Nowy wpis tajny klienta**.
1. Wprowadź opis wpisu tajnego klienta w polu **Opis** . Na przykład *clientsecret1*.
1. W obszarze **wygaśnięcie** wybierz czas trwania, dla którego wpis tajny jest prawidłowy, a następnie wybierz pozycję **Dodaj**.
1. Zapisz **wartość** klucza tajnego. Jest to potrzebne podczas konfigurowania dostawcy tożsamości w następnej sekcji.


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Konfigurowanie Azure AD B2C jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C, dla którego chcesz skonfigurować Federacji (na przykład contoso). W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawcę Azure AD B2C (na przykład contoso).
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Nowy dostawca połączenia OpenID Connect**.
1. Wprowadź **nazwę**. Na przykład wprowadź *Fabrikam*.
1. W polu **adres URL metadanych** wprowadź następujący adres URL zastępujący `{tenant}` nazwą domeny dzierżawy Azure AD B2C (na przykład Fabrikam). Zastąp wartość `{policy}` nazwą zasad skonfigurowaną w innej dzierżawie:

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    Na przykład `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.

1. W polu **Identyfikator klienta** wprowadź wcześniej zarejestrowany identyfikator aplikacji.
1. W polu **klucz tajny klienta** wprowadź wcześniej zarejestrowany klucz tajny klienta.
1. Dla **zakresu**, wprowadź `openid` .
1. Pozostaw wartości domyślne dla **typu odpowiedzi** i **trybu odpowiedzi**.
1. Obowiązkowe Dla wskazówki dotyczącej **domeny** wprowadź nazwę domeny, która ma być używana do [logowania bezpośredniego](direct-signin.md#redirect-sign-in-to-a-social-provider). Na przykład *fabrikam.com*.
1. W obszarze **Mapowanie oświadczeń dostawcy tożsamości** wybierz następujące oświadczenia:

    - **Identyfikator użytkownika**: *Sub*
    - **Nazwa wyświetlana**: *Nazwa*
    - **Imię:** *given_name*
    - **Nazwisko**: *family_name*
    - **Wiadomość e-mail**: *e-mail*

1. Wybierz pozycję **Zapisz**.

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>Dodawanie Azure AD B2C dostawcy tożsamości do przepływu użytkownika 

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Kliknij przepływ użytkownika, do którego chcesz dodać dostawcę tożsamości Azure AD B2C.
1. W obszarze **dostawcy tożsamości społecznościowej** wybierz pozycję **Fabrikam**.
1. Wybierz pozycję **Zapisz**.
1. Aby przetestować zasady, wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *testapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom przepływ użytkownika** .
1. Na stronie rejestracji lub logowania wybierz pozycję **Fabrikam** , aby zalogować się przy użyciu innej dzierżawy Azure AD B2C.

Jeśli proces logowania powiedzie się, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać klucz aplikacji utworzony wcześniej w dzierżawie Azure AD B2C.

1. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C, dla którego chcesz skonfigurować Federacji (na przykład contoso). W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawcę Azure AD B2C (na przykład contoso).
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje** wybierz opcję `Manual` .
1. Wprowadź **nazwę** klucza zasad. Na przykład `FabrikamAppSecret`.  Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza podczas jego tworzenia, więc jego odwołanie w kodzie XML w poniższej sekcji ma *B2C_1A_FabrikamAppSecret*.
1. W **kluczu tajnym** wprowadź wcześniej zarejestrowany klucz tajny klienta.
1. W obszarze **użycie klucza** wybierz opcję `Signature` .
1. Wybierz przycisk **Utwórz**.

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Konfigurowanie Azure AD B2C jako dostawcy tożsamości

Aby umożliwić użytkownikom logowanie się przy użyciu konta z innej dzierżawy usługi Azure AD B2C (Fabrikam), należy zdefiniować inne Azure AD B2C jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Można zdefiniować Azure AD B2C jako dostawcę oświadczeń przez dodanie Azure AD B2C do elementu **ClaimsProvider** w pliku rozszerzenia zasad.

1. Otwórz plik *TrustFrameworkExtensions.xml* .
1. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
1. Dodaj nową **ClaimsProvider** w następujący sposób:
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AzureADB2CFabrikam-OpenIdConnect">
        <DisplayName>Fabrikam</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <Metadata>
          <!-- Update the Client ID below to the Application ID -->
          <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
          <!-- Update the metadata URL with the other Azure AD B2C tenant name and policy name -->
          <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="response_types">code</Item>
          <Item Key="scope">openid</Item>
          <Item Key="response_mode">form_post</Item>
          <Item Key="HttpBinding">POST</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_FabrikamAppSecret"/>
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          <OutputClaim ClaimTypeReferenceId="otherMails" PartnerClaimType="emails"/>    
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
      </TechnicalProfile>
     </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Zaktualizuj następujące elementy XML przy użyciu odpowiedniej wartości:

    |Element XML  |Wartość  |
    |---------|---------|
    |ClaimsProvider\Domain  | Nazwa domeny używana do [bezpośredniego logowania](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider). Wprowadź nazwę domeny, która ma być używana podczas logowania bezpośredniego. Na przykład *fabrikam.com*. |
    |TechnicalProfile\DisplayName|Ta wartość będzie wyświetlana na przycisku logowania na ekranie logowania. Na przykład *firma Fabrikam*. |
    |Metadata \ client_id|Identyfikator aplikacji dostawcy tożsamości. Zaktualizuj identyfikator klienta przy użyciu identyfikatora aplikacji utworzonego wcześniej w innej dzierżawie Azure AD B2C.|
    |Metadata\METADATA|Adres URL wskazujący dokument konfiguracji dostawcy tożsamości OpenID Connect Connect, który jest również znany jako OpenID Connect dobrze znana konfiguracja. Wprowadź następujący adres URL zastępujący `{tenant}` nazwą domeny innej Azure AD B2C dzierżawy (Fabrikam). Zastąp wartość `{tenant}` nazwą zasad skonfigurowaną w innej dzierżawie i `{policy]` nazwą zasad: `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration` . Na przykład `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.|
    |CryptographicKeys| Zaktualizuj wartość **identyfikatorze storagereferenceid** do nazwy utworzonego wcześniej klucza zasad. Na przykład `B2C_1A_FabrikamAppSecret`.| 
    

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADB2CFabrikamExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADB2CFabrikamExchange" TechnicalProfileReferenceId="AzureADB2CFabrikam-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]


## <a name="test-your-custom-policy"></a>Testowanie zasad niestandardowych

1. Na przykład wybierz zasady jednostek uzależnionych `B2C_1A_signup_signin` .
1. W przypadku **aplikacji** wybierz [wcześniej zarejestrowaną](troubleshoot-custom-policies.md#troubleshoot-the-runtime)aplikację sieci Web. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom teraz** .
1. Na stronie rejestracji lub logowania wybierz pozycję **Fabrikam** , aby zalogować się przy użyciu innej dzierżawy Azure AD B2C.

Jeśli proces logowania powiedzie się, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.

::: zone-end

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak przekazać inny token Azure AD B2C do aplikacji](idp-pass-through-user-flow.md).
