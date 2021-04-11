---
title: Skonfiguruj konto i zaloguj się przy użyciu identyfikatora Apple ID
titleSuffix: Azure AD B2C
description: Udostępnianie kont i logowanie klientom z identyfikatorem Apple ID w aplikacjach przy użyciu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f5e4ff93b90c7644c1d1498a14c8b9954041cb34
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028370"
---
# <a name="set-up-sign-up-and-sign-in-with-an-apple-id--using-azure-active-directory-b2c-preview"></a>Skonfiguruj konto i zaloguj się za pomocą identyfikatora Apple ID przy użyciu Azure Active Directory B2C (wersja zapoznawcza)

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-apple-id-application"></a>Tworzenie aplikacji Apple ID

Aby włączyć Logowanie użytkowników przy użyciu identyfikatora Apple ID w Azure Active Directory B2C (Azure AD B2C), musisz utworzyć aplikację w programie [https://developer.apple.com](https://developer.apple.com) . Aby uzyskać więcej informacji, zobacz [Logowanie za pomocą firmy Apple](https://developer.apple.com/sign-in-with-apple/get-started/). Jeśli nie masz jeszcze konta dewelopera firmy Apple, możesz zarejestrować się w [programie Apple Developer](https://developer.apple.com/programs/enroll/).

1. Zaloguj się do [portalu dla deweloperów firmy Apple](https://developer.apple.com/account) przy użyciu poświadczeń konta.
1. Z menu wybierz pozycję **certyfikaty, identyfikatory, & profile**, a następnie wybierz pozycję **(+)**.
1. W celu **zarejestrowania nowego identyfikatora** wybierz pozycję **identyfikatory aplikacji**, a następnie wybierz pozycję **Kontynuuj**.
1. W obszarze **Wybierz typ** wybierz pozycję **aplikacja**, a następnie wybierz pozycję **Kontynuuj**.
1. Aby **zarejestrować identyfikator aplikacji**:
    1. Wprowadź **Opis** 
    1. Wprowadź **Identyfikator pakietu**, taki jak `com.contoso.azure-ad-b2c` . 
    1. W obszarze **możliwości** wybierz pozycję **Zaloguj się przy użyciu firmy Apple** z listy możliwości. 
    1. Zanotuj prefiks identyfikatora aplikacji (identyfikator zespołu) z tego kroku. Będzie on potrzebny później.
    1. Wybierz pozycję **Kontynuuj** , a następnie **zarejestruj**.
1. Z menu wybierz pozycję **certyfikaty, identyfikatory, & profile**, a następnie wybierz pozycję **(+)**.
1. W celu **zarejestrowania nowego identyfikatora** wybierz pozycję **identyfikatory usług**, a następnie wybierz pozycję **Kontynuuj**.
1. Aby **zarejestrować identyfikator usługi**:
    1. Wprowadź **Opis**. Opis jest wyświetlany użytkownikowi na ekranie wyrażania zgody.
    1. Wprowadź **Identyfikator**, taki jak `com.consoto.azure-ad-b2c-service` . Identyfikator jest IDENTYFIKATORem klienta dla przepływu połączenia usługi OpenID Connect.
    1. Wybierz pozycję **Kontynuuj**, a następnie wybierz pozycję **zarejestruj**.
1. Z **identyfikatorów** wybierz utworzony identyfikator.
1. Wybierz pozycję **Zaloguj się przy użyciu firmy Apple**, a następnie wybierz pozycję **Konfiguruj**.
    1. Wybierz **Identyfikator podstawowej aplikacji** , dla której chcesz skonfigurować logowanie za pomocą firmy Apple.
    1. W **domenach i poddomenach** wprowadź `your-tenant-name.b2clogin.com` . Zastąp nazwę dzierżawy nazwą swojej dzierżawy. Jeśli używasz [domeny niestandardowej](custom-domain.md), wprowadź `https://your-domain-name` .
    1. W polu **zwrotne adresy URL** wprowadź `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Jeśli używasz [domeny niestandardowej](custom-domain.md), wprowadź `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Zamień na `your-tenant-name` nazwę dzierżawy i `your-domain-name` domenę niestandardową.
    1. Wybierz pozycję **dalej**, a następnie wybierz pozycję **gotowe**.
    1. Po zamknięciu okna podręcznego wybierz pozycję **Kontynuuj**, a następnie wybierz pozycję **Zapisz**.

## <a name="creating-an-apple-client-secret"></a>Tworzenie klucza tajnego klienta firmy Apple

1. W menu Portal dla deweloperów firmy Apple wybierz pozycję **klucze**, a następnie wybierz pozycję **(+)**.
1. Aby **zarejestrować nowy klucz**:
    1. Wpisz **nazwę klucza**.
    1. Wybierz pozycję **Zaloguj się przy użyciu firmy Apple**, a następnie wybierz pozycję **Konfiguruj**.
    1. W polu **Identyfikator aplikacji głównej** Wybierz utworzoną wcześniej aplikację i wybierz pozycję **Zapisz**.
    1. Wybierz pozycję **Konfiguruj**, a następnie wybierz pozycję **zarejestruj** , aby zakończyć proces rejestracji klucza.
1. Aby **pobrać klucz**, wybierz pozycję **Pobierz** , aby pobrać plik. P8, który zawiera klucz.


::: zone pivot="b2c-user-flow"

## <a name="configure-apple-as-an-identity-provider"></a>Konfigurowanie firmy Apple jako dostawcy tożsamości

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako Administrator globalny dzierżawy Azure AD B2C.
1. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W obszarze **usługi platformy Azure** wybierz pozycję **Azure AD B2C**. Lub użyj pola wyszukiwania, aby znaleźć i wybrać **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Apple (wersja zapoznawcza)**.
1. Wprowadź **nazwę**. Na przykład *Apple*.
1. Wprowadź **Identyfikator dewelopera firmy Apple (identyfikator zespołu)**.
1. Wprowadź **Identyfikator usługi firmy Apple (identyfikator klienta)**.
1. Wprowadź **Identyfikator klucza firmy Apple**.
1. Wybierz i przekaż **dane certyfikatu Apple**.
1. Wybierz pozycję **Zapisz**.


> [!IMPORTANT] 
> - Zalogowanie się przy użyciu firmy Apple wymaga od administratora odnowienia klucza tajnego klienta co 6 miesięcy. 
> - W publicznej wersji zapoznawczej tej funkcji należy ręcznie odnowić klucz tajny klienta firmy Apple w przypadku jego wygaśnięcia. Ostrzeżenie zostanie wyświetlone z góry u dostawców tożsamości firmy Apple Skonfiguruj stronę społeczności dostawcy tożsamości, ale zalecamy ustawienie własnego przypomnienia. 
> - Jeśli musisz odnowić klucz tajny, Otwórz Azure AD B2C w Azure Portal, przejdź do pozycji **dostawcy tożsamości**  >  **Apple** i wybierz pozycję **Odnów klucz tajny**.

## <a name="add-the-apple-identity-provider-to-a-user-flow"></a>Dodawanie dostawcy tożsamości firmy Apple do przepływu użytkownika

Aby umożliwić użytkownikom logowanie się przy użyciu identyfikatora Apple ID, należy dodać dostawcę tożsamości firmy Apple do przepływu użytkownika. Logowanie za pomocą firmy Apple można skonfigurować tylko dla **zalecanej** wersji przepływów użytkowników. Aby dodać dostawcę tożsamości firmy Apple do przepływu użytkownika:

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Wybierz przepływ użytkownika, dla którego chcesz dodać dostawcę tożsamości firmy Apple. 
1. W obszarze **dostawcy tożsamości społecznościowej** wybierz pozycję **Apple (wersja zapoznawcza)**.
1. Wybierz pozycję **Zapisz**.
1. Aby przetestować zasady, wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *testapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom przepływ użytkownika** .
1. Na stronie rejestracji lub logowania wybierz pozycję **Apple** , aby zalogować się przy użyciu identyfikatora Apple ID.

Jeśli proces logowania powiedzie się, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="signing-the-client-secret"></a>Podpisywanie klucza tajnego klienta

Użyj wcześniej pobranego pliku. P8, aby podpisać klucz tajny klienta do tokenu JWT. Istnieje wiele bibliotek, które mogą utworzyć i podpisać token JWT. Użyj [funkcji platformy Azure, która tworzy](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-with-apple/azure-function) dla Ciebie token. 

1. Utwórz [funkcję platformy Azure](../azure-functions/functions-create-function-app-portal.md).
1. W obszarze **deweloper** wybierz pozycję **Code + test**. 
1. Skopiuj zawartość pliku [Run. CSX](https://github.com/azure-ad-b2c/samples/blob/master/policies/sign-in-with-apple/azure-function/run.csx) i wklej go w edytorze.
1. Wybierz pozycję **Zapisz**.
1. Utwórz żądanie HTTP `POST` i podaj następujące informacje:

    - **appleTeamId**: Identyfikator zespołu deweloperów firmy Apple
    - **appleServiceId**: identyfikator usługi firmy Apple (również identyfikator klienta)
    - **p8key**: klucz formatu PEM. Można to uzyskać, otwierając plik. P8 w edytorze tekstu i kopiując wszystko między `-----BEGIN PRIVATE KEY-----` i `-----END PRIVATE KEY-----` bez podziałów wierszy.
 
Poniższy kod JSON jest przykładem wywołania funkcji platformy Azure:

```json
{
    "appleTeamId": "ABC123DEFG",
    "appleKeyId": "URKEYID001",
    "appleServiceId": "com.yourcompany.app1",
    "p8key": "MIGTAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBHkwdwIBAQQg+s07NiAcuGEu8rxsJBG7ttupF6FRe3bXdHxEipuyK82gCgYIKoZIzj0DAQehRANCAAQnR1W/KbbaihTQayXH3tuAXA8Aei7u7Ij5OdRy6clOgBeRBPy1miObKYVx3ki1msjjG2uGqRbrc1LvjLHINWRD"
}
```

Funkcja platformy Azure reaguje z prawidłowym sformatowanym i podpisanym tokenem JWT klienta w odpowiedzi na przykład:

```json
{
    "token": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDI2OTY3NSwiZXhwIjoxNTYwMzU2MDc1LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.Dt9qA9NmJ_mk6tOqbsuTmfBrQLFqc9BnSVKR6A-bf9TcTft2XmhWaVODr7Q9w1PP3QOYShFXAnNql5OdNebB4g"
}
```

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać klucz tajny klienta, który został wcześniej zarejestrowany w dzierżawie Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
2. W obszarze **usługi platformy Azure** wybierz pozycję **Azure AD B2C**. Lub użyj pola wyszukiwania, aby znaleźć i wybrać **Azure AD B2C**.
1. Na stronie **Przegląd** wybierz pozycję **Struktura środowiska tożsamości**.
1. Wybierz pozycję **klucze zasad**, a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje** wybierz pozycję **Ręczne**.
1. Wprowadź **nazwę** klucza zasad. Na przykład "AppleSecret". Prefiks "B2C_1A_" jest automatycznie dodawany do nazwy klucza.
1. W **kluczu tajnym** wprowadź wartość tokenu zwracanego przez funkcję platformy Azure (token JWT).
1. W obszarze **użycie klucza** wybierz pozycję **podpis**.
1. Wybierz przycisk **Utwórz**.

> [!IMPORTANT] 
> - Zalogowanie się przy użyciu firmy Apple wymaga od administratora odnowienia klucza tajnego klienta co 6 miesięcy.
> - Jeśli wygaśnie, należy ręcznie odnowić klucz tajny klienta firmy Apple i zapisać nową wartość w kluczu zasad.
> - Zalecamy ustawienie własnego przypomnienia w ciągu 6 miesięcy, aby wygenerować nowy klucz tajny klienta. 

## <a name="configure-apple-as-an-identity-provider"></a>Konfigurowanie firmy Apple jako dostawcy tożsamości

Aby umożliwić użytkownikom logowanie się przy użyciu identyfikatora Apple ID, należy zdefiniować konto jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik jest uwierzytelniony.

Identyfikator Apple ID można zdefiniować jako dostawcę oświadczeń, dodając go do elementu **ClaimsProviders** w pliku rozszerzenia zasad.

1. Otwórz *TrustFrameworkExtensions.xml*.
2. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
3. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>apple.com</Domain>
      <DisplayName>Apple</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Apple-OIDC">
          <DisplayName>Apple</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">apple</Item>
            <Item Key="authorization_endpoint">https://appleid.apple.com/auth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://appleid.apple.com/auth/token</Item>
            <Item Key="JWKS">https://appleid.apple.com/auth/keys</Item>
            <Item Key="issuer">https://appleid.apple.com</Item>
            <Item Key="scope">name email openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="response_types">code</Item>
            <Item Key="external_user_identity_claim_id">sub</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="ReadBodyClaimsOnIdpRedirect">user.name.firstName user.name.lastName user.email</Item>
            <Item Key="client_id">You Apple ID</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AppleSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="https://appleid.apple.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="user.name.firstName"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="user.name.lastName"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="user.email"/>
          </OutputClaims>
          <OutputClaimsTransformations>
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

4. Ustaw **client_id** identyfikatorem usługi. Na przykład `com.consoto.azure-ad-b2c-service`.
5. Zapisz plik.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AppleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AppleExchange" TechnicalProfileReferenceId="Apple-OIDC" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testowanie zasad niestandardowych

1. Na przykład wybierz zasady jednostek uzależnionych `B2C_1A_signup_signin` .
1. W przypadku **aplikacji** wybierz [wcześniej zarejestrowaną](tutorial-register-applications.md)aplikację sieci Web. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz przycisk **Uruchom teraz** .
1. Na stronie rejestracji lub logowania wybierz pozycję **Apple** , aby zalogować się przy użyciu identyfikatora Apple ID.

Jeśli proces logowania powiedzie się, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.

::: zone-end
