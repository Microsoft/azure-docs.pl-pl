---
title: Konfigurowanie logowania za pomocą konta usługi Azure AD przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Skonfiguruj logowanie za pomocą konta usługi Azure Active Directory w usłudze Azure Active Directory B2C przy użyciu zasad niestandardowych.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: be3a7a3ce4ce3a06398436058ea5d4d935ef5a5c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678089"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie logowania za pomocą konta usługi Azure Active Directory przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule pokazano, jak włączyć logowanie użytkowników z organizacji usługi Azure Active Directory (Azure AD) przy użyciu [zasad niestandardowych](custom-policy-overview.md) w usłudze Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w [wprowadzenie do niestandardowych zasad w usłudze Azure Active Directory B2C](custom-policy-get-started.md).


[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Należy przechowywać klucz aplikacji utworzony w dzierżawie usługi Azure AD B2C.

1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C. Wybierz **filtr subskrypcja katalogu +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. W obszarze **Zasady**wybierz pozycję **Identity Experience Framework**.
1. Wybierz **pozycję Klawisze zasad,** a następnie wybierz pozycję **Dodaj**.
1. W **Options**przypadku `Manual`opcji wybierz opcję .
1. Wprowadź **nazwę** klucza zasad. Na przykład `ContosoAppSecret`.  Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza podczas jego tworzenia, więc jego odwołaniem w pliku XML w poniższej sekcji jest *B2C_1A_ContosoAppSecret*.
1. W **pliku Secret**wprowadź klucz tajny klienta, który został nagrany wcześniej.
1. Dla **użycia klucza**wybierz opcję `Signature`.
1. Wybierz pozycję **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Jeśli chcesz, aby użytkownicy logowali się przy użyciu usługi Azure AD, musisz zdefiniować usługę Azure AD jako dostawcę oświadczeń, z którego usługa Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez usługę Azure AD B2C, aby sprawdzić, czy określony użytkownik uwierzytelnił.

Usługę Azure AD można zdefiniować jako dostawcę oświadczeń, dodając usługę Azure AD do elementu **ClaimsProvider** w pliku rozszerzenia zasad.

1. Otwórz plik *TrustFrameworkExtensions.xml.*
2. Znajdź **ClaimsProviders** element. Jeśli nie istnieje, dodaj go w elemencie głównym.
3. Dodaj nowy **ClaimsProvider** w następujący sposób:
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

4. W obszarze **ClaimsProvider** element, zaktualizuj wartość **domeny** do unikatowej wartości, która może służyć do odróżnienia go od innych dostawców tożsamości. Na przykład: `Contoso`. Nie umieszczaj na `.com` końcu tego ustawienia domeny.
5. W obszarze **ClaimsProvider** element, zaktualizuj wartość **DisplayName** do przyjaznej nazwy dla dostawcy oświadczeń. Ta wartość nie jest obecnie używana.

### <a name="update-the-technical-profile"></a>Aktualizowanie profilu technicznego

Aby uzyskać token z punktu końcowego usługi Azure AD, należy zdefiniować protokoły, które usługi Azure AD B2C powinny używać do komunikowania się z usługą Azure AD. Odbywa się to wewnątrz **Elementu TechnicalProfile** **ClaimsProvider**.

1. Zaktualizuj identyfikator elementu **TechnicalProfile.** Ten identyfikator jest używany do odwoływania się do tego profilu `OIDC-Contoso`technicznego z innych części polityki, na przykład .
1. Zaktualizuj wartość **displayname**. Ta wartość zostanie wyświetlona na przycisku logowania na ekranie logowania.
1. Zaktualizuj wartość **opisu**.
1. Usługa Azure AD używa protokołu OpenID Connect, więc **Protocol** upewnij `OpenIdConnect`się, że wartość protokołu to .
1. Ustaw wartość **METADANYCH** `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration`na `tenant-name` , gdzie jest twoja nazwa dzierżawy usługi Azure AD. Na przykład: `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. Ustaw **client_id** identyfikatorem aplikacji z rejestracji aplikacji.
1. W obszarze **CryptographicKeys**zaktualizuj wartość **identyfikatora StorageReferenceId** do nazwy klucza zasad utworzonego wcześniej. Na przykład `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Prześlij plik rozszerzenia w celu weryfikacji

Do tej pory skonfigurowano zasady, dzięki czemu usługa Azure AD B2C wie, jak komunikować się z katalogiem usługi Azure AD. Spróbuj przesłać plik rozszerzenia zasad, aby potwierdzić, że do tej pory nie ma żadnych problemów.

1. Na stronie **Zasady niestandardowe** w dzierżawie usługi Azure AD B2C wybierz pozycję **Przekaż zasady**.
1. Włącz **zastępowanie zasad, jeśli istnieje**, a następnie przejdź do pliku *TrustFrameworkExtensions.xml.*
1. Kliknij pozycję **Przekaż**.

## <a name="register-the-claims-provider"></a>Zarejestruj dostawcę oświadczeń

W tym momencie dostawca tożsamości został skonfigurowany, ale nie jest jeszcze dostępny w żadnej ze stron rejestracji/logowania. Aby udostępnić go, należy utworzyć duplikat istniejącej podróży użytkownika szablonu, a następnie zmodyfikować go tak, aby miał również dostawcę tożsamości usługi Azure AD:

1. Otwórz plik *TrustFrameworkBase.xml* z pakietu startowego.
1. Znajdź i skopiuj całą zawartość elementu `Id="SignUpOrSignIn"` **UserJourney,** który zawiera .
1. Otwórz *trustFrameworkExtensions.xml* i znajdź **UserJourneys** element. Jeśli element nie istnieje, dodaj jeden.
1. Wklej całą zawartość **elementu UserJourney,** który został skopiowany jako element podrzędny elementu **UserJourneys.**
1. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `SignUpSignInContoso`.

### <a name="display-the-button"></a>Wyświetl przycisk

**Element ClaimsProviderSelection** jest analogiczny do przycisku dostawcy tożsamości na stronie rejestracji/logowania. Jeśli dodasz **ClaimsProviderSelection** element dla usługi Azure AD, nowy przycisk pojawia się, gdy użytkownik ląduje na stronie.

1. Znajdź element **OrchestrationStep,** który zawiera `Order="1"` w podróży użytkownika utworzonej w pliku *TrustFrameworkExtensions.xml*.
1. W obszarze **ClaimsProviderSelections**dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** na odpowiednią wartość, `ContosoExchange`na przykład:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Łączenie przycisku z akcją

Teraz, gdy masz przycisk w miejscu, musisz połączyć go z działaniem. Akcja, w tym przypadku jest dla usługi Azure AD B2C do komunikowania się z usługą Azure AD, aby otrzymać token. Połącz przycisk z akcją, łącząc profil techniczny dostawcy oświadczeń usługi Azure AD:

1. Znajdź **OrchestrationStep,** `Order="2"` który zawiera w podróży użytkownika.
1. Dodaj następujący element **ClaimsExchange** upewniając się, że używasz tej samej wartości dla **identyfikatora,** która została użyta dla **obiektu TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId** do **identyfikatora** profilu technicznego utworzonego wcześniej. Na przykład `OIDC-Contoso`.

1. Zapisz plik *TrustFrameworkExtensions.xml* i przekaż go ponownie w celu weryfikacji.

## <a name="create-an-azure-ad-b2c-application"></a>Tworzenie aplikacji usługi Azure AD B2C

Komunikacja z usługą Azure AD B2C odbywa się za pośrednictwem aplikacji, która rejestrujesz się w dzierżawie B2C. W tej sekcji wymieniono opcjonalne kroki, które można wykonać, aby utworzyć aplikację testową, jeśli jeszcze tego nie zrobiono.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnianej

Zaktualizuj plik jednostki uzależniającej (RP), który inicjuje proces użytkownika, który został utworzony.

1. Zrób kopię *pliku SignUpOrSignIn.xml* w katalogu roboczym i zmień jego nazwę. Na przykład zmień jego nazwę na *SignUpSignInContoso.xml*.
1. Otwórz nowy plik i zaktualizuj wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** z unikatową wartością. Na przykład `SignUpSignInContoso`.
1. Zaktualizuj wartość **PublicPolicyUri** za pomocą identyfikatora URI dla zasad. Na przykład `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Zaktualizuj wartość atrybutu **ReferenceId** w **DefaultUserJourney,** aby dopasować identyfikator podróży użytkownika, który został utworzony wcześniej. Na przykład *SignUpSignInContoso*.
1. Zapisz zmiany i przekaż plik.
1. W **obszarze Zasady niestandardowe**wybierz nową zasadę na liście.
1. Z listy rozwijanej **Wybierz aplikację** wybierz aplikację usługi Azure AD B2C utworzoną wcześniej. Na przykład *testapp1*.
1. Skopiuj **punkt końcowy Uruchom teraz** i otwórz go w prywatnym oknie przeglądarki, na przykład w trybie incognito w Google Chrome lub w oknie InPrivate w przeglądarce Microsoft Edge. Otwarcie w oknie przeglądarki prywatnej umożliwia przetestowanie pełnej podróży użytkownika, nie używając żadnych aktualnie buforowanych poświadczeń usługi Azure AD.
1. Wybierz przycisk logowania usługi Azure AD, na przykład *Pracownik contoso*, a następnie wprowadź poświadczenia dla użytkownika w dzierżawie organizacyjnej usługi Azure AD. Zostaniesz poproszony o autoryzację aplikacji, a następnie wprowadź informacje dotyczące profilu.

Jeśli proces logowania zakończy się pomyślnie, przeglądarka zostanie przekierowana do `https://jwt.ms`, który wyświetla zawartość tokenu zwróconego przez usługę Azure AD B2C.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z zasadami niestandardowymi czasami mogą być potrzebne dodatkowe informacje podczas rozwiązywania problemów z zasadami podczas jej opracowywania.

Aby ułatwić diagnozowanie problemów, można tymczasowo umieścić zasady w "trybie dewelopera" i zbierać dzienniki za pomocą usługi Azure Application Insights. Dowiedz się, jak w [usłudze Azure Active Directory B2C: Zbieranie dzienników](troubleshoot-with-application-insights.md).
