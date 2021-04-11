---
title: Konfigurowanie logowania za pomocą dostawcy protokołu SAML usługi Salesforce przy użyciu usługi SAML
titleSuffix: Azure AD B2C
description: Skonfiguruj zalogowanie się za pomocą dostawcy SAML usługi Salesforce przy użyciu protokołu SAML w Azure Active Directory B2C.
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
ms.openlocfilehash: cf2f0cdf8b7c540e569067d68374eef55d3479fe
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028200"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-saml-protocol-in-azure-active-directory-b2c"></a>Konfigurowanie logowania za pomocą dostawcy SAML usługi Salesforce przy użyciu protokołu SAML w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"
[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule opisano sposób włączania logowania dla użytkowników z organizacji usługi Salesforce przy użyciu [zasad niestandardowych](custom-policy-overview.md) w programie Azure Active Directory B2C (Azure AD B2C). Aby włączyć logowanie, Dodaj [dostawcę tożsamości SAML](identity-provider-generic-saml.md) do zasad niestandardowych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]
- Jeśli jeszcze tego nie zrobiono, zarejestruj się w celu uzyskania [bezpłatnego konta dewelopera](https://developer.salesforce.com/signup). W tym artykule jest wykorzystywane [środowisko usługi Salesforce](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Skonfiguruj moją domenę](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) dla organizacji usługi Salesforce.

## <a name="set-up-salesforce-as-an-identity-provider"></a>Konfigurowanie usługi Salesforce jako dostawcy tożsamości

1. [Zaloguj się do usługi Salesforce](https://login.salesforce.com/).
2. W menu po lewej stronie w obszarze **Ustawienia** rozwiń węzeł **tożsamość**, a następnie wybierz pozycję **dostawca tożsamości**.
3. Wybierz pozycję **Włącz dostawcę tożsamości**.
4. W obszarze **Wybierz certyfikat** wybierz certyfikat, który ma być używany przez usługi Salesforce do komunikowania się z Azure AD B2C. Możesz użyć domyślnego certyfikatu.
5. Kliknij pozycję **Zapisz**.

### <a name="create-a-connected-app-in-salesforce"></a>Tworzenie aplikacji połączonej w usłudze Salesforce

1. Na stronie **dostawca tożsamości** wybierz opcję **dostawcy usług są teraz tworzone za pośrednictwem połączonych aplikacji. Kliknij tutaj.**
2. W obszarze **podstawowe informacje** wprowadź wymagane wartości dla połączonej aplikacji.
3. W obszarze **Ustawienia aplikacji sieci Web** zaznacz pole wyboru **Włącz protokół SAML** .
4. W polu **Identyfikator jednostki** wprowadź następujący adres URL. Upewnij się, że zastąpisz wartość dla `your-tenant` nazwy Azure AD B2C dzierżawy.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

      W przypadku korzystania z [domeny niestandardowej](custom-domain.md)Użyj następującego formatu:

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. W polu **adres URL usługi ACS** wprowadź następujący adres URL. Upewnij się, że zastąpisz wartość dla `your-tenant` nazwy Azure AD B2C dzierżawy.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

      W przypadku korzystania z [domeny niestandardowej](custom-domain.md)Użyj następującego formatu:

      ```
      https://your-domain-name/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```

7. Przewiń w dół listy, a następnie kliknij przycisk **Zapisz**.

### <a name="get-the-metadata-url"></a>Pobieranie adresu URL metadanych

1. Na stronie Przegląd połączonej aplikacji kliknij przycisk **Zarządzaj**.
2. Skopiuj wartość **punktu końcowego odnajdywania metadanych**, a następnie zapisz go. Zostanie ona użyta w dalszej części tego artykułu.

### <a name="set-up-salesforce-users-to-federate"></a>Konfigurowanie użytkowników usługi Salesforce do sfederować

1. Na stronie **Zarządzanie** połączonej aplikacji kliknij pozycję **Zarządzaj profilami**.
2. Wybierz Profile (lub grupy użytkowników), które chcesz sfederować z Azure AD B2C. Jako administrator systemu zaznacz pole wyboru **administrator systemu** , aby można było sfederować przy użyciu konta usługi Salesforce.

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać certyfikat utworzony w dzierżawie Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.
5. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
6. W obszarze **Opcje** wybierz opcję `Upload` .
7. Wprowadź wartość **Nazwa** dla zasad. Na przykład SAMLSigningCert. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
8. Przejdź do i wybierz utworzony certyfikat B2CSigningCert. pfx.
9. Wprowadź **hasło** dla certyfikatu.
3. Kliknij pozycję **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Jeśli chcesz, aby użytkownicy mogli się logować przy użyciu konta usługi Salesforce, musisz zdefiniować konto jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Konto usługi Salesforce można zdefiniować jako dostawcę oświadczeń, dodając je do elementu **ClaimsProviders** w pliku rozszerzenia zasad. Aby uzyskać więcej informacji, zobacz [Definiowanie dostawcy tożsamości SAML](identity-provider-generic-saml.md).

1. Otwórz *TrustFrameworkExtensions.xml*.
1. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
1. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-SAML2">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Zaktualizuj wartość **PartnerEntity** za pomocą wcześniej SKOPIOWANEGO adresu URL metadanych usługi Salesforce.
1. Zaktualizuj wartość obu wystąpień elementu **identyfikatorze storagereferenceid** na nazwę klucza certyfikatu podpisywania. Na przykład B2C_1A_SAMLSigningCert.
1. Znajdź `<ClaimsProviders>` sekcję i Dodaj następujący fragment kodu XML. Jeśli zasady zawierają już `SM-Saml-idp` profil techniczny, przejdź do następnego kroku. Aby uzyskać więcej informacji, zobacz temat [Zarządzanie sesjami logowania](custom-policy-reference-sso.md)jednokrotnego.

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Zapisz plik.

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
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testowanie zasad niestandardowych

1. Na przykład wybierz zasady jednostek uzależnionych `B2C_1A_signup_signin` .
1. W przypadku **aplikacji** wybierz [wcześniej zarejestrowaną](tutorial-register-applications.md)aplikację sieci Web. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom teraz** .
1. Na stronie rejestracji lub logowania wybierz pozycję **Salesforce** , aby zalogować się przy użyciu konta usługi Salesforce.

Jeśli proces logowania powiedzie się, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.

::: zone-end