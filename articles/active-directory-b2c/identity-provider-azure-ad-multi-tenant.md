---
title: Konfigurowanie logowania dla wielu dzierżawców usługi Azure AD według zasad niestandardowych
titleSuffix: Azure AD B2C
description: Dodawanie dostawcy tożsamości usługi Azure AD z wieloma dzierżawami przy użyciu zasad niestandardowych w Azure Active Directory B2C.
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
ms.openlocfilehash: 5089a289e617aa8c2ec153320763647fc8afac9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489040"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie logowania do Azure Active Directory z wieloma dzierżawcami przy użyciu zasad niestandardowych w programie Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

W tym artykule opisano sposób włączania logowania dla użytkowników przy użyciu wielodostępnego punktu końcowego dla Azure Active Directory (Azure AD). Zezwalanie użytkownikom z wielu dzierżawców usługi Azure AD na logowanie przy użyciu Azure AD B2C, bez konieczności konfigurowania dostawcy tożsamości dla każdej dzierżawy. Jednak członkowie gościa w żadnej z tych dzierżawców **nie będą** mogli się zalogować. W tym celu należy [indywidualnie skonfigurować każdą dzierżawę](identity-provider-azure-ad-single-tenant.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-application"></a>Rejestrowanie aplikacji

Aby włączyć Logowanie użytkowników przy użyciu konta usługi Azure AD w Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację w [Azure Portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [Rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](../active-directory/develop/quickstart-register-app.md).


1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu zawierającego swoją organizacyjną dzierżawę usługi Azure AD (na przykład contoso.com). Wybierz **Filtr katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog, który zawiera dzierżawę.
1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **rejestracje aplikacji**.
1. Wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład `Azure AD B2C App`.
1. Wybierz **konta w dowolnym katalogu organizacji (dowolny katalog usługi Azure AD — wielodostępny)** dla tej aplikacji.
1. Dla **identyfikatora URI przekierowania** Zaakceptuj wartość **sieci Web** i wprowadź następujący adres URL w postaci małych liter, gdzie `your-B2C-tenant-name` jest zastępowana nazwą dzierżawy Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Na przykład `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

    Jeśli używasz [domeny niestandardowej](custom-domain.md), wprowadź `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Zamień `your-domain-name` na domenę niestandardową i `your-tenant-name` nazwę dzierżawy.

1. Wybierz pozycję **Zarejestruj**. Zapisz **Identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.
1. Wybierz pozycję **certyfikaty & wpisy tajne**, a następnie wybierz pozycję **nowy klucz tajny klienta**.
1. Wprowadź **Opis** wpisu tajnego, wybierz pozycję Wygaśnięcie, a następnie wybierz pozycję **Dodaj**. Zapisz **wartość** wpisu tajnego do użycia w późniejszym kroku.

## <a name="configuring-optional-claims"></a>Konfigurowanie oświadczeń opcjonalnych

Jeśli chcesz uzyskać `family_name` `given_name` oświadczenia i usługi Azure AD, możesz skonfigurować opcjonalne oświadczenia dla swojej aplikacji w Azure Portal interfejsie użytkownika lub manifeście aplikacji. Aby uzyskać więcej informacji, zobacz [jak dostarczyć opcjonalne oświadczenia do aplikacji usługi Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W sekcji **Zarządzanie** wybierz pozycję **rejestracje aplikacji**.
1. Wybierz aplikację, dla której chcesz skonfigurować oświadczenia opcjonalne.
1. W sekcji **Zarządzanie** wybierz pozycję **Konfiguracja tokenu**.
1. Wybierz pozycję **Dodaj opcjonalne** pole.
1. W polu **Typ tokenu** wybierz pozycję **Identyfikator**.
1. Wybierz opcjonalne oświadczenia do dodania, `family_name` i `given_name` .
1. Kliknij pozycję **Dodaj**.

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać klucz aplikacji utworzony w dzierżawie Azure AD B2C.

1. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. Wybierz **Filtr katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje** wybierz opcję `Manual` .
1. Wprowadź **nazwę** klucza zasad. Na przykład `AADAppSecret`.  Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza podczas jego tworzenia, więc jego odwołanie w kodzie XML w poniższej sekcji ma *B2C_1A_AADAppSecret*.
1. W **kluczu tajnym** wprowadź wcześniej zarejestrowany klucz tajny klienta.
1. W obszarze **użycie klucza** wybierz opcję `Signature` .
1. Wybierz przycisk **Utwórz**.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurowanie usługi Azure AD jako dostawcy tożsamości

Aby umożliwić użytkownikom logowanie się przy użyciu konta usługi Azure AD, musisz zdefiniować usługę Azure AD jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Usługę Azure AD można zdefiniować jako dostawcę oświadczeń, dodając usługę Azure AD do elementu **ClaimsProvider** w pliku rozszerzenia zasad.

1. Otwórz plik *TrustFrameworkExtensions.xml* .
1. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
1. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADCommon-OpenIdConnect">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. W elemencie **ClaimsProvider** należy zaktualizować wartość dla **domeny** do unikatowej wartości, która może być używana do odróżnienia od innych dostawców tożsamości.
1. W elemencie **profilu technicznym** zaktualizuj wartość parametru **DisplayName**, na przykład `Contoso Employee` . Ta wartość jest wyświetlana na przycisku logowania na stronie logowania.
1. Ustaw **client_id** na identyfikator aplikacji aplikacji wielodostępnej dla wielodostępnej usługi Azure AD, która została zarejestrowana wcześniej.
1. W obszarze **CryptographicKeys** zaktualizuj wartość **identyfikatorze storagereferenceid** do nazwy utworzonego wcześniej klucza zasad. Na przykład `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Ograniczanie dostępu

Użycie `https://login.microsoftonline.com/` jako wartości parametru **ValidTokenIssuerPrefixes** umożliwia wszystkim użytkownikom usługi Azure AD logowanie się do aplikacji. Zaktualizuj listę prawidłowych wystawców tokenów i Ogranicz dostęp do określonej listy użytkowników dzierżawy usługi Azure AD, którzy mogą się zalogować.

Aby uzyskać wartości, przyjrzyj się metadanych odnajdywania OpenID Connect Connect dla każdej dzierżawy usługi Azure AD, z której chcesz się zalogować. Format adresu URL metadanych jest podobny do `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration` , gdzie `your-tenant` jest nazwą dzierżawy usługi Azure AD. Na przykład:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Wykonaj następujące kroki dla każdej dzierżawy usługi Azure AD, która ma zostać użyta do zalogowania:

1. Otwórz przeglądarkę i przejdź do adresu URL metadanych OpenID Connect Connect dla dzierżawy. Znajdź obiekt **Issuer** i Zapisz jego wartość. Powinien wyglądać podobnie do `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/` .
1. Skopiuj i wklej wartość do klucza **ValidTokenIssuerPrefixes** . Rozdziel wiele wystawców średnikami. Przykład z dwoma wystawcami pojawia się w poprzednim `ClaimsProvider` przykładzie kodu XML.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADCommonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADCommonExchange" TechnicalProfileReferenceId="AADCommon-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testowanie zasad niestandardowych

1. Na przykład wybierz zasady jednostek uzależnionych `B2C_1A_signup_signin` .
1. W przypadku **aplikacji** wybierz [wcześniej zarejestrowaną](troubleshoot-custom-policies.md#troubleshoot-the-runtime)aplikację sieci Web. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom teraz** .
1. Na stronie rejestracji lub logowania wybierz pozycję **Common AAD** , aby zalogować się przy użyciu konta usługi Azure AD.

Aby przetestować możliwość logowania z wieloma dzierżawcami, wykonaj ostatnie dwa kroki przy użyciu poświadczeń dla użytkownika, który istnieje w innej dzierżawie usługi Azure AD. Skopiuj **punkt końcowy Uruchom teraz** i otwórz go w prywatnym oknie przeglądarki, na przykład w trybie incognito w przeglądarce Google Chrome lub w oknie InPrivate w programie Microsoft Edge. Otwieranie w prywatnym oknie przeglądarki umożliwia przetestowanie pełnej podróży użytkowników, nie używając obecnie buforowanych poświadczeń usługi Azure AD.

Jeśli proces logowania powiedzie się, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z zasadami niestandardowymi czasami mogą być potrzebne dodatkowe informacje podczas rozwiązywania problemów z zasadami podczas jej tworzenia.

Aby ułatwić diagnozowanie problemów, można tymczasowo wprowadzić zasady do trybu dewelopera i zebrać dzienniki za pomocą usługi Azure Application Insights. Dowiedz się, jak w [Azure Active Directory B2C: zbieranie dzienników](troubleshoot-with-application-insights.md).

::: zone-end
