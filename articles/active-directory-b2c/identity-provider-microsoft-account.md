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
ms.date: 01/27/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1f4aea472988555047a736e6a248fa5690e42a23
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645860"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Skonfiguruj konto i zaloguj się za pomocą konto Microsoft przy użyciu Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-microsoft-account-application"></a>Tworzenie aplikacji konto Microsoft

Aby włączyć logowanie dla użytkowników za pomocą konto Microsoft w Azure Active Directory B2C (Azure AD B2C), należy utworzyć aplikację w [Azure Portal](https://portal.azure.com). Aby uzyskać więcej informacji, zobacz [Rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](../active-directory/develop/quickstart-register-app.md). Jeśli nie masz jeszcze konto Microsoft, możesz uzyskać jeden z nich [https://www.live.com/](https://www.live.com/) .

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **katalog + subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **rejestracje aplikacji**.
1. Wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *MSAapp1*.
1. W obszarze **obsługiwane typy kont** wybierz pozycję **konta w dowolnym katalogu organizacyjnym (dowolny katalog usługi Azure AD — wielodostępny) i osobiste konta Microsoft (np. Skype, Xbox)**.

   Aby uzyskać więcej informacji na temat różnych typów kont, zobacz [Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](../active-directory/develop/quickstart-register-app.md).
1. W obszarze **URI przekierowania (opcjonalnie)** wybierz pozycję **Sieć Web** i wprowadź `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` wartość w polu tekstowym. Zamień `<tenant-name>` na nazwę dzierżawy Azure AD B2C.
1. Wybierz pozycję **zarejestruj**
1. Zapisz **Identyfikator aplikacji (klienta)** widoczny na stronie przeglądu aplikacji. Identyfikator klienta jest potrzebny podczas konfigurowania dostawcy tożsamości w następnej sekcji.
1. Wybierz **certyfikaty & wpisy tajne**
1. Kliknij pozycję **Nowy klucz tajny klienta**.
1. Wprowadź **Opis** wpisu tajnego, na przykład *hasło aplikacji 1*, a następnie kliknij przycisk **Dodaj**.
1. Zapisz hasło aplikacji wyświetlane w kolumnie **wartość** . Klucz tajny klienta jest potrzebny podczas konfigurowania dostawcy tożsamości w następnej sekcji.

::: zone pivot="b2c-user-flow"

## <a name="configure-microsoft-as-an-identity-provider"></a>Skonfiguruj firmę Microsoft jako dostawcę tożsamości

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **dostawcy tożsamości**, a następnie wybierz pozycję **konto Microsoft**.
1. Wprowadź **nazwę**. Na przykład *MSA*.
1. W polu **Identyfikator klienta** wprowadź identyfikator aplikacji (klienta) utworzonej wcześniej aplikacji usługi Azure AD.
1. W polu **klucz tajny klienta** Wprowadź zarejestrowany wpis tajny klienta.
1. Wybierz pozycję **Zapisz**.

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>Dodawanie dostawcy tożsamości firmy Microsoft do przepływu użytkownika 

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Kliknij przepływ użytkownika, do którego chcesz dodać dostawcę tożsamości firmy Microsoft.
1. W obszarze **dostawcy tożsamości społecznościowej** wybierz pozycję **konto Microsoft**.
1. Wybierz pozycję **Zapisz**.
1. Aby przetestować zasady, wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *testapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij pozycję **Uruchom przepływ użytkownika**

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
1. Kliknij pozycję **Utwórz**.

## <a name="configure-microsoft-as-an-identity-provider"></a>Skonfiguruj firmę Microsoft jako dostawcę tożsamości

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
        <TechnicalProfile Id="MSA-MicrosoftAccount-OpenIdConnect">
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

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-MicrosoftAccount-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end
