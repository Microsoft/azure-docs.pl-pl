---
title: Skonfiguruj konto i zaloguj się przy użyciu konta Microsoft
titleSuffix: Azure AD B2C
description: Podaj konto i zaloguj się do klientów przy użyciu kont Microsoft w swoich aplikacjach za pomocą Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a05c987299a8a4967c81b7eb9cc481f253ac5b09
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584857"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Skonfiguruj konto i zaloguj się za pomocą konto Microsoft przy użyciu Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

::: zone pivot="b2c-user-flow"

* [Utwórz przepływ użytkownika](tutorial-create-user-flows.md), aby umożliwić użytkownikom rejestrowanie się w aplikacji i logowanie do niej.
* Jeśli jeszcze tego nie zrobiono, [dodaj aplikację internetowego interfejsu API do dzierżawy usługi Azure Active Directory B2C](add-web-api-application.md).

::: zone-end

::: zone pivot="b2c-custom-policy"

* Wykonaj kroki opisane w temacie [wprowadzenie do zasad niestandardowych w Active Directory B2C](custom-policy-get-started.md).
* Jeśli jeszcze tego nie zrobiono, [dodaj aplikację internetowego interfejsu API do dzierżawy usługi Azure Active Directory B2C](add-web-api-application.md).

::: zone-end

## <a name="create-a-microsoft-account-application"></a>Tworzenie aplikacji konto Microsoft

Aby użyć konto Microsoft jako [dostawcy tożsamości](openid-connect.md) w Azure Active Directory B2C (Azure AD B2C), należy utworzyć aplikację w dzierżawie usługi Azure AD. Dzierżawa usługi Azure AD nie jest taka sama jak dzierżawa Azure AD B2C. Jeśli nie masz jeszcze konto Microsoft, możesz uzyskać jeden z nich [https://www.live.com/](https://www.live.com/) .

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **katalog + subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **rejestracje aplikacji**.
1. Wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *MSAapp1*.
1. W obszarze **obsługiwane typy kont** wybierz pozycję **konta w dowolnym katalogu organizacyjnym (dowolny katalog usługi Azure AD — wielodostępny) i osobiste konta Microsoft (np. Skype, Xbox)**.

   Aby uzyskać więcej informacji na temat różnych typów kont, zobacz [Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](../active-directory/develop/quickstart-register-app.md).
1. W obszarze **URI przekierowania (opcjonalnie)** wybierz pozycję **Sieć Web** i wprowadź `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` wartość w polu tekstowym. Zamień `<tenant-name>` na nazwę dzierżawy Azure AD B2C.
1. Wybierz pozycję **zarejestruj**
1. Zapisz **Identyfikator aplikacji (klienta)** widoczny na stronie przeglądu aplikacji. Jest to potrzebne podczas konfigurowania dostawcy tożsamości w następnej sekcji.
1. Wybierz **certyfikaty & wpisy tajne**
1. Kliknij pozycję **Nowy klucz tajny klienta**.
1. Wprowadź **Opis** wpisu tajnego, na przykład *hasło aplikacji 1*, a następnie kliknij przycisk **Dodaj**.
1. Zapisz hasło aplikacji wyświetlane w kolumnie **wartość** . Jest to potrzebne podczas konfigurowania dostawcy tożsamości w następnej sekcji.

::: zone pivot="b2c-user-flow"

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Konfigurowanie konto Microsoft jako dostawcy tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **konto Microsoft**.
1. Wprowadź **nazwę**. Na przykład *MSA*.
1. W polu **Identyfikator klienta** wprowadź identyfikator aplikacji (klienta) utworzonej wcześniej aplikacji usługi Azure AD.
1. W polu **klucz tajny klienta** Wprowadź zarejestrowany wpis tajny klienta.
1. Wybierz pozycję **Zapisz**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="configuring-optional-claims"></a>Konfigurowanie oświadczeń opcjonalnych

Jeśli chcesz uzyskać `family_name` `given_name` oświadczenia i usługi Azure AD, możesz skonfigurować opcjonalne oświadczenia dla swojej aplikacji w Azure Portal interfejsie użytkownika lub manifeście aplikacji. Aby uzyskać więcej informacji, zobacz [jak dostarczyć opcjonalne oświadczenia do aplikacji usługi Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W sekcji **Zarządzanie** wybierz pozycję **rejestracje aplikacji**.
1. Wybierz aplikację, dla której chcesz skonfigurować oświadczenia opcjonalne.
1. W sekcji **Zarządzanie** wybierz pozycję **Konfiguracja tokenu (wersja zapoznawcza)**.
1. Wybierz pozycję **Dodaj opcjonalne** pole.
1. Wybierz typ tokenu, który chcesz skonfigurować.
1. Wybierz opcjonalne oświadczenia do dodania.
1. Kliknij pozycję **Dodaj**.

## <a name="create-a-policy-key"></a>Tworzenie klucza zasad

Teraz, po utworzeniu aplikacji w dzierżawie usługi Azure AD, musisz przechowywać klucz tajny klienta tej aplikacji w dzierżawie Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog, w którym znajduje się Twoja dzierżawa.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.
1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje** wybierz opcję `Manual` .
1. Wprowadź **nazwę** klucza zasad. Na przykład `MSASecret`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
1. W **kluczu tajnym** wprowadź klucz tajny klienta zapisany w poprzedniej sekcji.
1. W obszarze **użycie klucza** wybierz opcję `Signature` .
1. Kliknij przycisk **Utwórz**.

## <a name="add-a-claims-provider"></a>Dodawanie dostawcy oświadczeń

Aby umożliwić użytkownikom logowanie się przy użyciu konto Microsoft, należy zdefiniować konto jako dostawcę oświadczeń, z którym Azure AD B2C może komunikować się za pośrednictwem punktu końcowego. Punkt końcowy zawiera zestaw oświadczeń, które są używane przez Azure AD B2C do sprawdzenia, czy określony użytkownik został uwierzytelniony.

Usługę Azure AD można zdefiniować jako dostawcę oświadczeń przez dodanie elementu **ClaimsProvider** w pliku rozszerzenia zasad.

1. Otwórz plik zasad *TrustFrameworkExtensions.xml* .
1. Znajdź element **ClaimsProviders** . Jeśli nie istnieje, Dodaj ją do elementu głównego.
1. Dodaj nową **ClaimsProvider** w następujący sposób:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

1. Zastąp wartość **client_id** wartością *identyfikatora klienta* aplikacji usługi Azure AD, który został zarejestrowany wcześniej.
1. Zapisz plik.

Twoje zasady zostały skonfigurowane tak, aby Azure AD B2C wie, jak komunikować się z aplikacją konto Microsoft w usłudze Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Przekaż plik rozszerzenia w celu weryfikacji

Przed kontynuowaniem Przekaż zmodyfikowane zasady, aby upewnić się, że nie ma żadnych problemów do tej pory.

1. Przejdź do dzierżawy Azure AD B2C w Azure Portal i wybierz pozycję **platforma obsługi tożsamości**.
1. Na stronie **zasady niestandardowe** wybierz opcję **Przekaż zasady niestandardowe**.
1. Włącz **Zastępowanie zasad, jeśli istnieje**, a następnie wyszukaj i wybierz plik *TrustFrameworkExtensions.xml* .
1. Kliknij pozycję **Przekaż**.

Jeśli w portalu nie są wyświetlane żadne błędy, przejdź do następnej sekcji.

## <a name="register-the-claims-provider"></a>Rejestrowanie dostawcy oświadczeń

W tym momencie skonfigurowano dostawcę tożsamości, ale jeszcze nie jest on dostępny na żadnym z ekranów rejestracji lub logowania. Aby udostępnić ten element, Utwórz duplikat istniejącej przez użytkownika szablonu, a następnie zmodyfikuj go tak, aby miał również konto Microsoft dostawcę tożsamości.

1. Otwórz plik *TrustFrameworkBase.xml* z pakietu początkowego.
1. Znajdź i Skopiuj całą zawartość elementu **UserJourney** , który zawiera `Id="SignUpOrSignIn"` .
1. Otwórz *TrustFrameworkExtensions.xml* i Znajdź element **UserJourneys** . Jeśli element nie istnieje, Dodaj go.
1. Wklej całą zawartość elementu **UserJourney** , który został skopiowany jako element podrzędny elementu **UserJourneys** .
1. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `SignUpSignInMSA`.

### <a name="display-the-button"></a>Wyświetl przycisk

Element **ClaimsProviderSelection** jest analogiczny do przycisku dostawcy tożsamości na ekranie rejestracji lub logowania. Jeśli dodasz element **ClaimsProviderSelection** dla konto Microsoft, zostanie wyświetlony nowy przycisk, gdy użytkownik zostanie umieszczony na stronie.

1. W pliku *TrustFrameworkExtensions.xml* Znajdź element **OrchestrationStep** , który zawiera `Order="1"` w podróży użytkownika.
1. W obszarze **ClaimsProviderSelects** Dodaj następujący element. Ustaw wartość **TargetClaimsExchangeId** na odpowiednią wartość, na przykład `MicrosoftAccountExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Połącz przycisk z akcją

Teraz, gdy masz już przycisk, musisz połączyć go z akcją. W tym przypadku akcja w tym przypadku Azure AD B2C do komunikowania się z konto Microsoft w celu uzyskania tokenu.

1. Znajdź **OrchestrationStep** obejmujący `Order="2"` w podróży użytkownika.
1. Dodaj następujący element **ClaimsExchange** , aby upewnić się, że używasz tej samej wartości dla identyfikatora, który został użyty dla **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Zaktualizuj wartość **TechnicalProfileReferenceId** , aby pasowała do `Id` wartości w elemencie **profilu technicznym** dostawcy oświadczeń, który został dodany wcześniej. Na przykład `MSA-OIDC`.

1. Zapisz plik *TrustFrameworkExtensions.xml* i przekaż go ponownie w celu weryfikacji.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>Dodawanie dostawcy tożsamości firmy Microsoft do przepływu użytkownika 

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Kliknij przepływ użytkownika, który chcesz do dostawcy tożsamości firmy Microsoft.
1. W obszarze **dostawcy tożsamości społecznościowej** wybierz pozycję **konto Microsoft**.
1. Wybierz pozycję **Zapisz**.
1. Aby przetestować zasady, wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *testapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij pozycję **Uruchom przepływ użytkownika**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Aktualizowanie i testowanie pliku jednostki uzależnionej

Zaktualizuj plik jednostki uzależnionej (RP), który inicjuje utworzoną przez Ciebie podróż użytkownika.

1. Utwórz kopię *SignUpOrSignIn.xml* w katalogu roboczym i zmień jej nazwę. Na przykład zmień nazwę na *SignUpSignInMSA.xml*.
1. Otwórz nowy plik i zaktualizuj wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** przy użyciu unikatowej wartości. Na przykład `SignUpSignInMSA`.
1. Zaktualizuj wartość **PublicPolicyUri** za pomocą identyfikatora URI dla zasad. Na przykład`http://contoso.com/B2C_1A_signup_signin_msa`
1. Zaktualizuj wartość atrybutu **ReferenceId** w **DefaultUserJourney** w taki sposób, aby odpowiadała identyfikatorowi przejazdu użytkownika utworzonego wcześniej (SignUpSignInMSA).
1. Zapisz zmiany, Przekaż plik, a następnie wybierz nowe zasady z listy.
1. Upewnij się, że aplikacja Azure AD B2C utworzona w poprzedniej sekcji (lub przez zakończenie wymagań wstępnych, na przykład *webapp1* lub *testapp1*) została wybrana w polu **Wybierz aplikację** , a następnie przetestuj ją, klikając polecenie **Uruchom teraz**.
1. Wybierz przycisk **konto Microsoft** i zaloguj się.

::: zone-end