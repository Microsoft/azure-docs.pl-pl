---
title: Konfigurowanie logowania dla usługi Azure AD z wieloma dzierżawami według zasad niestandardowych
titleSuffix: Azure AD B2C
description: Dodaj wielodostępnego dostawcy tożsamości usługi Azure AD przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 320723744e1366fdc73cd0593fb0ebece03367f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678103"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie logowania dla usługi Azure Active Directory z wieloma dzierżawami przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule pokazano, jak włączyć logowanie dla użytkowników korzystających z punktu końcowego wielu dzierżaw dla usługi Azure Active Directory (Azure AD) przy użyciu [zasad niestandardowych](custom-policy-overview.md) w usłudze Azure AD B2C. Dzięki temu użytkownicy z wielu dzierżaw usługi Azure AD do logowania się przy użyciu usługi Azure AD B2C, bez konieczności konfigurowania dostawcy tożsamości dla każdej dzierżawy. Jednak członkowie gości w żadnej z tych dzierżaw nie **będzie** mógł się zalogować. W tym celu należy [indywidualnie skonfigurować każdego dzierżawcę.](identity-provider-azure-ad-single-tenant-custom.md)

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w [wprowadzenie do niestandardowych zasad w usłudze Azure Active Directory B2C](custom-policy-get-started.md).

## <a name="register-an-application"></a>Rejestrowanie aplikacji

Aby włączyć logowanie dla użytkowników z określonej organizacji usługi Azure AD, należy zarejestrować aplikację w dzierżawie usługi Azure AD.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD (na przykład contoso.com). Wybierz **filtr Katalog + subskrypcja** w górnym menu, a następnie wybierz katalog zawierający dzierżawę.
1. Wybierz **pozycję Wszystkie usługi** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz pozycję **Rejestracje aplikacji.**
1. Wybierz **pozycję Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład `Azure AD B2C App`.
1. Wybierz **pozycję Konta w dowolnym katalogu organizacyjnym** dla tej aplikacji.
1. W przypadku **identyfikatora URI przekierowania**zaakceptuj wartość **sieci Web**i wprowadź `your-B2C-tenant-name` następujący adres URL we wszystkich małych literach, gdzie jest zastępowany nazwą dzierżawy usługi Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Na przykład `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Wybierz pozycję **Zarejestruj**. Zarejestruj **identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.
1. Wybierz **pozycję Certyfikaty & wpisy tajne**, a następnie wybierz pozycję Nowy klucz tajny **klienta**.
1. Wprowadź **opis klucza** tajnego, wybierz wygaśnięcie, a następnie wybierz pozycję **Dodaj**. Zapisz **wartość** klucza tajnego do użycia w późniejszym kroku.

## <a name="configuring-optional-claims"></a>Konfigurowanie oświadczeń opcjonalnych

Jeśli chcesz uzyskać `family_name` i `given_name` oświadczeń z usługi Azure AD, można skonfigurować opcjonalne oświadczenia dla aplikacji w interfejsie użytkownika witryny azure portalu lub manifestu aplikacji. Aby uzyskać więcej informacji, zobacz [Jak podać opcjonalne oświadczenia do aplikacji usługi Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W sekcji **Zarządzanie** wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz aplikację, dla której chcesz skonfigurować opcjonalne oświadczenia na liście.
1. W sekcji **Zarządzanie** wybierz pozycję **Konfiguracja tokenu**.
1. Wybierz **dodaj oświadczenie opcjonalne**.
1. Dla **typu tokenu**wybierz **id**.
1. Wybierz opcjonalne oświadczenia `family_name` do `given_name`dodania i .
1. Kliknij pozycję **Add** (Dodaj).

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać klucz aplikacji utworzony w dzierżawie usługi Azure AD B2C.

1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C. Wybierz **filtr subskrypcja katalogu +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. W obszarze **Zasady**wybierz pozycję **Identity Experience Framework**.
1. Wybierz **pozycję Klawisze zasad,** a następnie wybierz pozycję **Dodaj**.
1. W **Options**przypadku `Manual`opcji wybierz opcję .
1. Wprowadź **nazwę** klucza zasad. Na przykład `AADAppSecret`.  Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza podczas jego tworzenia, więc jego odwołaniem w pliku XML w poniższej sekcji jest *B2C_1A_AADAppSecret*.
1. W **pliku Secret**wprowadź klucz tajny klienta, który został nagrany wcześniej.
1. Dla **użycia klucza**wybierz opcję `Signature`.
1. Wybierz pozycję **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Jeśli chcesz, aby użytkownicy logowali się przy użyciu usługi Azure AD, musisz zdefiniować usługę Azure AD jako dostawcę oświadczeń, z którego usługa Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik uwierzytelnił.

Usługę Azure AD można zdefiniować jako dostawcę oświadczeń, dodając usługę Azure AD do elementu **ClaimsProvider** w pliku rozszerzenia zasad.

1. Otwórz plik *TrustFrameworkExtensions.xml.*
1. Znajdź **ClaimsProviders** element. Jeśli nie istnieje, dodaj go w elemencie głównym.
1. Dodaj nowy **ClaimsProvider** w następujący sposób:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
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

1. W obszarze **ClaimsProvider** element, zaktualizuj wartość **domeny** do unikatowej wartości, która może służyć do odróżnienia go od innych dostawców tożsamości.
1. W obszarze **TechnicalProfile** element, na przykład, zaktualizuj wartość **DisplayName** `Contoso Employee`. Ta wartość jest wyświetlana na przycisku logowania na stronie logowania.
1. Ustaw **client_id** identyfikatorem aplikacji usługi Azure AD z aplikacją wielodostępną zarejestrowaną wcześniej.
1. W obszarze **CryptographicKeys**zaktualizuj wartość **identyfikatora StorageReferenceId** do nazwy klucza zasad utworzonego wcześniej. Na przykład `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Ograniczanie dostępu

> [!NOTE]
> Używanie `https://login.microsoftonline.com/` jako wartości **validTokenIsssuerPrefixes** umożliwia wszystkim użytkownikom usługi Azure AD zalogowanie się do aplikacji.

Należy zaktualizować listę prawidłowych wystawców tokenu i ograniczyć dostęp do określonej listy użytkowników dzierżawy usługi Azure AD, którzy mogą się logować.

Aby uzyskać wartości, spójrz na metadane odnajdywania OpenID Connect dla każdej dzierżawy usługi Azure AD, z których użytkownicy mają się logować. Format adresu URL metadanych `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`jest `your-tenant` podobny do , gdzie jest nazwa dzierżawy usługi Azure AD. Przykład:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Wykonaj następujące kroki dla każdej dzierżawy usługi Azure AD, która powinna być używana do logowania się:

1. Otwórz przeglądarkę i przejdź do adresu URL metadanych OpenID Connect dla dzierżawy. Znajdź obiekt **wystawcy** i zapisz jego wartość. Powinien wyglądać podobnie `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/`do .
1. Skopiuj i wklej wartość do klucza **ValidTokenIssuerPrefixes.** Oddziel wiele wystawców przecinkiem. Przykład z dwoma wystawcami `ClaimsProvider` pojawia się w poprzedniej próbce XML.

### <a name="upload-the-extension-file-for-verification"></a>Prześlij plik rozszerzenia w celu weryfikacji

Do tej pory skonfigurowano zasady, dzięki czemu usługa Azure AD B2C wie, jak komunikować się z katalogami usługi Azure AD. Spróbuj przesłać plik rozszerzenia zasad, aby potwierdzić, że do tej pory nie ma żadnych problemów.

1. Na stronie **Zasady niestandardowe** w dzierżawie usługi Azure AD B2C wybierz pozycję **Przekaż zasady**.
2. Włącz **zastępowanie zasad, jeśli istnieje**, a następnie przejdź do pliku *TrustFrameworkExtensions.xml.*
3. Wybierz **pozycję Przekaż**.

## <a name="register-the-claims-provider"></a>Zarejestruj dostawcę oświadczeń

W tym momencie dostawca tożsamości został skonfigurowany, ale nie jest dostępny w żadnym z ekranów rejestracji/logowania. Aby udostępnić go, należy utworzyć duplikat istniejącej podróży użytkownika szablonu, a następnie zmodyfikować go tak, aby miał również dostawcę tożsamości usługi Azure AD.

1. Otwórz plik *TrustFrameworkBase.xml* z pakietu startowego.
2. Znajdź i skopiuj całą zawartość elementu `Id="SignUpOrSignIn"` **UserJourney,** który zawiera .
3. Otwórz *trustFrameworkExtensions.xml* i znajdź **UserJourneys** element. Jeśli element nie istnieje, dodaj jeden.
4. Wklej całą zawartość **elementu UserJourney,** który został skopiowany jako element podrzędny elementu **UserJourneys.**
5. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `SignUpSignInContoso`.

### <a name="display-the-button"></a>Wyświetl przycisk

**Element ClaimsProviderSelection** jest analogiczny do przycisku dostawcy tożsamości na ekranie rejestracji/logowania. Jeśli dodasz **ClaimsProviderSelection** element dla usługi Azure AD, nowy przycisk pojawia się, gdy użytkownik ląduje na stronie.

1. Znajdź element **OrchestrationStep,** który zawiera `Order="1"` w podróży użytkownika utworzonej w pliku *TrustFrameworkExtensions.xml*.
1. W obszarze **ClaimsProviderSelects**dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** na odpowiednią wartość, `AzureADExchange`na przykład:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Łączenie przycisku z akcją

Teraz, gdy masz przycisk w miejscu, musisz połączyć go z działaniem. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z usługą Azure AD, aby otrzymać token. Połącz przycisk z akcją, łącząc profil techniczny dostawcy oświadczeń usługi Azure AD.

1. Znajdź **OrchestrationStep,** `Order="2"` który zawiera w podróży użytkownika.
2. Dodaj następujący element **ClaimsExchange** upewniając się, że używasz tej samej wartości dla **identyfikatora,** która została użyta dla **obiektu TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId** do **identyfikatora** profilu technicznego utworzonego wcześniej. Na przykład `Common-AAD`.

3. Zapisz plik *TrustFrameworkExtensions.xml* i przekaż go ponownie w celu weryfikacji.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji usługi Azure AD B2C

Komunikacja z usługą Azure AD B2C odbywa się za pośrednictwem aplikacji, która rejestrujesz się w dzierżawie B2C. W tej sekcji wymieniono opcjonalne kroki, które można wykonać, aby utworzyć aplikację testową, jeśli jeszcze tego nie zrobiono.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnianej

Zaktualizuj plik jednostki uzależniającej (RP), który inicjuje proces użytkownika, który został utworzony:

1. Zrób kopię *pliku SignUpOrSignIn.xml* w katalogu roboczym i zmień jego nazwę. Na przykład zmień jego nazwę na *SignUpSignContoso.xml*.
1. Otwórz nowy plik i zaktualizuj wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** z unikatową wartością. Na przykład `SignUpSignInContoso`.
1. Zaktualizuj wartość **PublicPolicyUri** za pomocą identyfikatora URI dla zasad. Na przykład `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Zaktualizuj wartość atrybutu **ReferenceId** w **DefaultUserJourney,** aby dopasować identyfikator podróży użytkownika, który został utworzony wcześniej. Na przykład *SignUpSignInContoso*.
1. Zapisz zmiany i przekaż plik.
1. W **obszarze Zasady niestandardowe**wybierz nową zasadę na liście.
1. Z listy rozwijanej **Wybierz aplikację** wybierz aplikację usługi Azure AD B2C utworzoną wcześniej. Na przykład *testapp1*.
1. Skopiuj **punkt końcowy Uruchom teraz** i otwórz go w prywatnym oknie przeglądarki, na przykład w trybie incognito w Google Chrome lub w oknie InPrivate w przeglądarce Microsoft Edge. Otwarcie w oknie przeglądarki prywatnej umożliwia przetestowanie pełnej podróży użytkownika, nie używając żadnych aktualnie buforowanych poświadczeń usługi Azure AD.
1. Wybierz przycisk logowania usługi Azure AD, na przykład *Pracownik Contoso*, a następnie wprowadź poświadczenia dla użytkownika w jednym z dzierżaw organizacyjnych usługi Azure AD. Zostaniesz poproszony o autoryzację aplikacji, a następnie wprowadź informacje dotyczące profilu.

Jeśli proces logowania zakończy się pomyślnie, przeglądarka zostanie przekierowana do `https://jwt.ms`, który wyświetla zawartość tokenu zwróconego przez usługę Azure AD B2C.

Aby przetestować możliwość logowania z wieloma dzierżawami, wykonaj dwa ostatnie kroki przy użyciu poświadczeń dla użytkownika, który istnieje innej dzierżawy usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z zasadami niestandardowymi czasami mogą być potrzebne dodatkowe informacje podczas rozwiązywania problemów z zasadami podczas jej opracowywania.

Aby ułatwić diagnozowanie problemów, można tymczasowo umieścić zasady w "trybie dewelopera" i zbierać dzienniki za pomocą usługi Azure Application Insights. Dowiedz się, jak w [usłudze Azure Active Directory B2C: Zbieranie dzienników](troubleshoot-with-application-insights.md).
