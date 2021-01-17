---
title: Konfigurowanie logowania dla organizacji usługi Azure AD
titleSuffix: Azure AD B2C
description: Skonfiguruj logowanie do konkretnej organizacji Azure Active Directory w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2b640730bac410136ef8fdd4ea8e0261f68a3284
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538145"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Konfigurowanie logowania dla konkretnej organizacji Azure Active Directory w programie Azure Active Directory B2C

W tym artykule opisano sposób włączania logowania dla użytkowników z określonej organizacji usługi Azure AD przy użyciu przepływu użytkownika w Azure AD B2C.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-azure-ad-app"></a>Rejestrowanie aplikacji usługi Azure AD

Aby włączyć Logowanie użytkowników przy użyciu konta usługi Azure AD z określonej organizacji usługi Azure AD, w Azure Active Directory B2C (Azure AD B2C) musisz utworzyć aplikację w [Azure Portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [Rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu zawierającego swoją organizacyjną dzierżawę usługi Azure AD (na przykład contoso.com). Wybierz **Filtr katalog + subskrypcja** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD.
1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **rejestracje aplikacji**.
1. Wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład `Azure AD B2C App`.
1. Zaakceptuj domyślny wybór **kont w tym katalogu organizacyjnym tylko** dla tej aplikacji.
1. Dla **identyfikatora URI przekierowania** Zaakceptuj wartość **sieci Web** i wprowadź następujący adres URL w postaci małych liter, gdzie `your-B2C-tenant-name` jest zastępowana nazwą dzierżawy Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Na przykład `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Wybierz pozycję **Zarejestruj**. Zapisz **Identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.
1. Wybierz pozycję **certyfikaty & wpisy tajne**, a następnie wybierz pozycję **nowy klucz tajny klienta**.
1. Wprowadź **Opis** wpisu tajnego, wybierz pozycję Wygaśnięcie, a następnie wybierz pozycję **Dodaj**. Zapisz **wartość** wpisu tajnego do użycia w późniejszym kroku.

### <a name="configuring-optional-claims"></a>Konfigurowanie oświadczeń opcjonalnych

Jeśli chcesz uzyskać `family_name` `given_name` oświadczenia i usługi Azure AD, możesz skonfigurować opcjonalne oświadczenia dla swojej aplikacji w Azure Portal interfejsie użytkownika lub manifeście aplikacji. Aby uzyskać więcej informacji, zobacz [jak dostarczyć opcjonalne oświadczenia do aplikacji usługi Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W sekcji **Zarządzanie** wybierz pozycję **rejestracje aplikacji**.
1. Wybierz aplikację, dla której chcesz skonfigurować oświadczenia opcjonalne.
1. W sekcji **Zarządzanie** wybierz pozycję **Konfiguracja tokenu**.
1. Wybierz pozycję **Dodaj opcjonalne** pole.
1. W polu **Typ tokenu** wybierz pozycję **Identyfikator**.
1. Wybierz opcjonalne oświadczenia do dodania, `family_name` a następnie `given_name` .
1. Kliknij pozycję **Dodaj**.

::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurowanie usługi Azure AD jako dostawcy tożsamości

1. Upewnij się, że używasz katalogu, który zawiera Azure AD B2C dzierżawcy. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **Nowy dostawca połączenia OpenID Connect**.
1. Wprowadź **nazwę**. Na przykład wprowadź nazwę *contoso Azure AD*.
1. W polu **adres URL metadanych** wprowadź następujący adres URL zastępujący `{tenant}` nazwą domeny dzierżawy usługi Azure AD:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Na przykład `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Na przykład `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. W polu **Identyfikator klienta** wprowadź wcześniej zarejestrowany identyfikator aplikacji.
1. W polu **klucz tajny klienta** wprowadź wcześniej zarejestrowany klucz tajny klienta.
1. Dla **zakresu**, wprowadź `openid profile` .
1. Pozostaw wartości domyślne dla **typu odpowiedzi** i **trybu odpowiedzi**.
1. Obowiązkowe Dla wskazówki dotyczącej **domeny** wprowadź wartość `contoso.com` . Aby uzyskać więcej informacji, zobacz [Konfigurowanie bezpośredniego logowania przy użyciu Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. W obszarze **Mapowanie oświadczeń dostawcy tożsamości** wybierz następujące oświadczenia:

    - **Identyfikator użytkownika**: *OID*
    - **Nazwa wyświetlana**: *Nazwa*
    - **Imię:** *given_name*
    - **Nazwisko**: *family_name*
    - **Adres e-mail**: *preferred_username*

1. Wybierz pozycję **Zapisz**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać klucz aplikacji utworzony w dzierżawie Azure AD B2C.

1. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. Wybierz **Filtr katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje** wybierz opcję `Manual` .
1. Wprowadź **nazwę** klucza zasad. Na przykład `ContosoAppSecret`.  Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza podczas jego tworzenia, więc jego odwołanie w kodzie XML w poniższej sekcji ma *B2C_1A_ContosoAppSecret*.
1. W **kluczu tajnym** wprowadź wcześniej zarejestrowany klucz tajny klienta.
1. W obszarze **użycie klucza** wybierz opcję `Signature` .
1. Wybierz przycisk **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Jeśli chcesz, aby użytkownicy mogli się logować przy użyciu usługi Azure AD, musisz zdefiniować usługę Azure AD jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Usługę Azure AD można zdefiniować jako dostawcę oświadczeń, dodając usługę Azure AD do elementu **ClaimsProvider** w pliku rozszerzenia zasad.

1. Otwórz plik *TrustFrameworkExtensions.xml* .
2. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
3. Dodaj nową **ClaimsProvider** w następujący sposób:
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="OIDC-Contoso">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
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

4. W elemencie **ClaimsProvider** należy zaktualizować wartość dla **domeny** do unikatowej wartości, która może być używana do odróżnienia od innych dostawców tożsamości. Na przykład: `Contoso`. Nie umieszczasz `.com` na końcu tego ustawienia domeny.
5. W elemencie **ClaimsProvider** zaktualizuj wartość parametru **DisplayName** do przyjaznej nazwy dostawcy oświadczeń. Ta wartość nie jest obecnie używana.

### <a name="update-the-technical-profile"></a>Aktualizowanie profilu technicznego

Aby uzyskać token z punktu końcowego usługi Azure AD, należy zdefiniować protokoły, które Azure AD B2C powinny być używane do komunikacji z usługą Azure AD. Jest to wykonywane w elemencie **profilu technicznym** elementu  **ClaimsProvider**.

1. Zaktualizuj identyfikator elementu **profilu technicznym** . Ten identyfikator jest używany do odwoływania się do tego profilu technicznego z innych części zasad, na przykład `OIDC-Contoso` .
1. Zaktualizuj wartość parametru **DisplayName**. Ta wartość będzie wyświetlana na przycisku logowania na ekranie logowania.
1. Zaktualizuj wartość w polu **Opis**.
1. Usługa Azure AD korzysta z protokołu Connect OpenID Connect, więc upewnij się, że jest to wartość dla **protokołu** `OpenIdConnect` .
1. Ustaw wartość **metadanych** na `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration` , gdzie `tenant-name` jest nazwą dzierżawy usługi Azure AD. Na przykład `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. Ustaw **client_id** na identyfikator aplikacji z rejestracji aplikacji.
1. W obszarze **CryptographicKeys** zaktualizuj wartość **identyfikatorze storagereferenceid** do nazwy utworzonego wcześniej klucza zasad. Na przykład `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia w celu weryfikacji

Teraz zasady zostały skonfigurowane tak, aby Azure AD B2C wie, jak komunikować się z katalogiem usługi Azure AD. Spróbuj przekazać plik rozszerzenia zasad tylko, aby upewnić się, że nie ma żadnych problemów do tej pory.

1. Na stronie **zasady niestandardowe** w dzierżawie Azure AD B2C wybierz pozycję **Przekaż zasady**.
1. Włącz **Zastępowanie zasad, jeśli istnieje**, a następnie wyszukaj i wybierz plik *TrustFrameworkExtensions.xml* .
1. Kliknij pozycję **Przekaż**.

## <a name="register-the-claims-provider"></a>Rejestrowanie dostawcy oświadczeń

W tym momencie dostawca tożsamości został skonfigurowany, ale nie jest jeszcze dostępny na żadnej stronie rejestracji/logowania. Aby udostępnić ten element, Utwórz duplikat istniejącej przez użytkownika szablonu, a następnie zmodyfikuj go tak, aby miał także dostawcę tożsamości usługi Azure AD:

1. Otwórz plik *TrustFrameworkBase.xml* z pakietu początkowego.
1. Znajdź i Skopiuj całą zawartość elementu **UserJourney** , który zawiera `Id="SignUpOrSignIn"` .
1. Otwórz *TrustFrameworkExtensions.xml* i Znajdź element **UserJourneys** . Jeśli element nie istnieje, Dodaj go.
1. Wklej całą zawartość elementu **UserJourney** , który został skopiowany jako element podrzędny elementu **UserJourneys** .
1. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `SignUpSignInContoso`.

### <a name="display-the-button"></a>Wyświetl przycisk

Element **ClaimsProviderSelection** jest analogiczny do przycisku dostawcy tożsamości na stronie rejestracji/logowania. Jeśli dodasz element **ClaimsProviderSelection** dla usługi Azure AD, zostanie wyświetlony nowy przycisk, gdy użytkownik zostanie wystawiony na stronie.

1. Znajdź element **OrchestrationStep** , który obejmuje `Order="1"` podróż użytkownika utworzoną w *TrustFrameworkExtensions.xml*.
1. W obszarze **ClaimsProviderSelections** Dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** na odpowiednią wartość, na przykład `ContosoExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk z akcją

Teraz, gdy masz już przycisk, musisz połączyć go z akcją. W tym przypadku akcja w tym przypadku Azure AD B2C do komunikacji z usługą Azure AD w celu uzyskania tokenu. Połącz przycisk z akcją, łącząc profil techniczny dostawcy oświadczeń usługi Azure AD:

1. Znajdź **OrchestrationStep** obejmujący `Order="2"` w podróży użytkownika.
1. Dodaj następujący element **ClaimsExchange** , aby upewnić się, że używasz tej samej wartości dla **identyfikatora** , który został użyty dla **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId** na **Identyfikator** utworzonego wcześniej profilu technicznego. Na przykład `OIDC-Contoso`.

1. Zapisz plik *TrustFrameworkExtensions.xml* i przekaż go ponownie w celu weryfikacji.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>Dodawanie dostawcy tożsamości usługi Azure AD do przepływu użytkownika 

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Kliknij przepływ użytkownika, do którego chcesz dodać dostawcę tożsamości usługi Azure AD.
1. W obszarze **dostawcy tożsamości społecznościowej** wybierz pozycję **contoso Azure AD**.
1. Wybierz pozycję **Zapisz**.
1. Aby przetestować zasady, wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *testapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij pozycję **Uruchom przepływ użytkownika**

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnionej

Zaktualizuj plik jednostki uzależnionej (RP), który inicjuje utworzoną przez Ciebie podróż użytkownika.

1. Utwórz kopię *SignUpOrSignIn.xml* w katalogu roboczym i zmień jej nazwę. Na przykład zmień nazwę na *SignUpSignInContoso.xml*.
1. Otwórz nowy plik i zaktualizuj wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** przy użyciu unikatowej wartości. Na przykład `SignUpSignInContoso`.
1. Zaktualizuj wartość **PublicPolicyUri** za pomocą identyfikatora URI dla zasad. Na przykład `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Zaktualizuj wartość atrybutu **ReferenceId** w **DefaultUserJourney** w taki sposób, aby odpowiadała identyfikatorowi podróży użytkownika, który został utworzony wcześniej. Na przykład *SignUpSignInContoso*.
1. Zapisz zmiany i Przekaż plik.
1. W obszarze **zasady niestandardowe** wybierz nowe zasady z listy.
1. Z listy rozwijanej **Wybierz aplikację** Wybierz utworzoną wcześniej aplikację Azure AD B2C. Na przykład *testapp1*.
1. Skopiuj **punkt końcowy Uruchom teraz** i otwórz go w prywatnym oknie przeglądarki, na przykład w trybie incognito w przeglądarce Google Chrome lub w oknie InPrivate w programie Microsoft Edge. Otwieranie w prywatnym oknie przeglądarki umożliwia przetestowanie pełnej podróży użytkowników, nie używając obecnie buforowanych poświadczeń usługi Azure AD.
1. Wybierz przycisk Zaloguj usługi Azure AD, na przykład pracownik firmy *contoso*, a następnie wprowadź poświadczenia dla użytkownika w dzierżawie organizacyjnej usługi Azure AD. Zostanie wyświetlona prośba o autoryzację aplikacji, a następnie wprowadzenie informacji dla Twojego profilu.

Jeśli proces logowania zakończy się pomyślnie, przeglądarka zostanie przekierowana do `https://jwt.ms` , która wyświetla zawartość tokenu zwróconego przez Azure AD B2C.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z zasadami niestandardowymi czasami mogą być potrzebne dodatkowe informacje podczas rozwiązywania problemów z zasadami podczas jej tworzenia.

Aby ułatwić diagnozowanie problemów, można tymczasowo wprowadzić zasady do trybu dewelopera i zebrać dzienniki za pomocą usługi Azure Application Insights. Dowiedz się, jak w [Azure Active Directory B2C: zbieranie dzienników](troubleshoot-with-application-insights.md).

::: zone-end