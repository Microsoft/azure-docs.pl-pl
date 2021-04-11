---
title: Konfigurowanie rejestracji i logowania przy użyciu konta Amazon
titleSuffix: Azure AD B2C
description: Zapewnij klientom konta usługi Amazon i zaloguj się w swoich aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.custom: project-no-code
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b6c0d9d5430d84006b208c50e78b8d875c95b8ac
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028387"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Skonfiguruj konto usługi Amazon i zaloguj się na nim przy użyciu Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-app-in-the-amazon-developer-console"></a>Tworzenie aplikacji w konsoli dewelopera usługi Amazon

Aby włączyć Logowanie użytkowników przy użyciu konta Amazon w Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację w [usłudze Amazon Developer Services i technologiach](https://developer.amazon.com). Aby uzyskać więcej informacji, zobacz [Rejestrowanie na potrzeby logowania za pomocą usługi Amazon](https://developer.amazon.com/docs/login-with-amazon/register-web.html). Jeśli nie masz jeszcze konta usługi Amazon, możesz zarejestrować się w usłudze [https://www.amazon.com/](https://www.amazon.com/) .

1. Zaloguj się do [konsoli dewelopera usługi Amazon](https://developer.amazon.com/dashboard) przy użyciu poświadczeń konta Amazon.
1. Jeśli jeszcze tego nie zrobiono, wybierz pozycję **zarejestruj się**, postępuj zgodnie z procedurami rejestracji dla deweloperów, a następnie zaakceptuj zasady.
1. Z poziomu pulpitu nawigacyjnego wybierz pozycję **Zaloguj się za pomocą usługi Amazon**.
1. Wybierz pozycję **Utwórz nowy profil zabezpieczeń**.
1. Wprowadź wartość w polu **Nazwa profilu zabezpieczeń**, **opis profilu zabezpieczeń** i **adres URL powiadomienia o ochronie prywatności**, na przykład `https://www.contoso.com/privacy` adres URL informacji o prywatności to strona, którą zarządzasz dla użytkowników. Następnie kliknij przycisk **Zapisz**.
1. W sekcji **Logowanie za pomocą usługi Amazon konfiguracje** Wybierz utworzoną **nazwę profilu zabezpieczeń** , wybierz ikonę **Zarządzaj** , a następnie wybierz pozycję **Ustawienia sieci Web**.
1. W sekcji **Ustawienia sieci Web** Skopiuj wartości **Identyfikator klienta**. Wybierz pozycję **Pokaż klucz tajny** , aby uzyskać klucz tajny klienta, a następnie skopiuj go. Wymagane są obie wartości, aby skonfigurować konto Amazon jako dostawcę tożsamości w dzierżawie. **Klucz tajny klienta** jest ważnym poświadczeniem zabezpieczeń.
1. W sekcji **Ustawienia sieci Web** wybierz pozycję **Edytuj**. 
    1. W obszarze **dozwolone źródła** wprowadź `https://your-tenant-name.b2clogin.com` . Zamień `your-tenant-name` na nazwę dzierżawy. Jeśli używasz [domeny niestandardowej](custom-domain.md), wprowadź `https://your-domain-name` .
    1.  **Dozwolone zwrotne adresy URL** , wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` .  Jeśli używasz [domeny niestandardowej](custom-domain.md), wprowadź `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` .  Zamień na `your-tenant-name` nazwę dzierżawy i `your-domain-name` domenę niestandardową.
1. Wybierz pozycję **Zapisz**.

::: zone pivot="b2c-user-flow"

## <a name="configure-amazon-as-an-identity-provider"></a>Konfigurowanie usługi Amazon jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Amazon**.
1. Wprowadź **nazwę**. Na przykład *Amazon*.
1. W polu **Identyfikator klienta** wprowadź identyfikator klienta aplikacji Amazon, który został utworzony wcześniej.
1. W polu **klucz tajny klienta** Wprowadź zarejestrowany wpis tajny klienta.
1. Wybierz pozycję **Zapisz**.

## <a name="add-amazon-identity-provider-to-a-user-flow"></a>Dodawanie dostawcy tożsamości Amazon do przepływu użytkownika 

W tym momencie dostawca tożsamości Amazon został skonfigurowany, ale nie jest jeszcze dostępny na żadnej stronie logowania. Aby dodać dostawcę tożsamości Amazon do przepływu użytkownika:

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Kliknij przepływ użytkownika, do którego chcesz dodać dostawcę tożsamości usługi Amazon.
1. W obszarze **dostawcy tożsamości społecznościowej** wybierz pozycję **Amazon**.
1. Wybierz pozycję **Zapisz**.
1. Aby przetestować zasady, wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *testapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom przepływ użytkownika** .
1. Na stronie rejestracji lub logowania wybierz pozycję **Amazon** , aby zalogować się przy użyciu konta usługi Amazon.

Jeśli proces logowania powiedzie się, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać klucz tajny klienta, który został wcześniej zarejestrowany w dzierżawie Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.
5. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
6. W obszarze **Opcje** wybierz opcję `Manual` .
7. Wprowadź **nazwę** klucza zasad. Na przykład `AmazonSecret`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
8. W **kluczu tajnym** wprowadź wcześniej zarejestrowany klucz tajny klienta.
9. W obszarze **użycie klucza** wybierz opcję `Signature` .
10. Kliknij pozycję **Utwórz**.

## <a name="configure-amazon-as-an-identity-provider"></a>Konfigurowanie usługi Amazon jako dostawcy tożsamości

Aby umożliwić użytkownikom logowanie się przy użyciu konta Amazon, należy zdefiniować konto jako dostawcę oświadczeń. Azure AD B2C może komunikować się za pomocą punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Konto Amazon można zdefiniować jako dostawcę oświadczeń, dodając je do elementu **ClaimsProviders** w pliku rozszerzenia zasad.


1. Otwórz *TrustFrameworkExtensions.xml*.
2. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
3. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAuth2">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

4. Ustaw **client_id** na identyfikator aplikacji z rejestracji aplikacji.
5. Zapisz plik.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testowanie zasad niestandardowych

1. Na przykład wybierz zasady jednostek uzależnionych `B2C_1A_signup_signin` .
1. W przypadku **aplikacji** wybierz [wcześniej zarejestrowaną](tutorial-register-applications.md)aplikację sieci Web. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom teraz** .
1. Na stronie rejestracji lub logowania wybierz pozycję **Amazon** , aby zalogować się przy użyciu konta usługi Amazon.

Jeśli proces logowania powiedzie się, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.

::: zone-end
