---
title: Konfigurowanie zmiany hasła przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się, jak umożliwić użytkownikom zmianę hasła przy użyciu zasad niestandardowych w programie Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/17/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a42cb97d123d0943dab02bf1f70fcf306d6bcd96
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97629134"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie zmiany hasła przy użyciu zasad niestandardowych w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W Azure Active Directory B2C (Azure AD B2C) można umożliwić użytkownikom zalogowanym przy użyciu konta lokalnego zmianę hasła bez konieczności potwierdzania autentyczności przez weryfikację poczty e-mail. Przepływ zmiany hasła obejmuje następujące kroki:

1. Zaloguj się przy użyciu konta lokalnego. Jeśli sesja jest nadal aktywna, Azure AD B2C autoryzuje użytkownika i przechodzi do następnego kroku.
1. Użytkownicy muszą zweryfikować **stare hasło**, utworzyć i potwierdzić **nowe hasło**.

![Przepływ zmiany hasła](./media/add-password-change-policy/password-change-flow.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych w Active Directory B2C](custom-policy-get-started.md).
* Jeśli jeszcze tego nie zrobiono, [zarejestruj aplikację sieci Web w Azure Active Directory B2C](tutorial-register-applications.md).

## <a name="add-the-elements"></a>Dodaj elementy

1. Otwórz plik *TrustframeworkExtensions.xml* i Dodaj następujący element **ClaimType** z identyfikatorem `oldPassword` do elementu [ClaimsSchema](claimsschema.md) :

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter your old password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Element [ClaimsProvider](claimsproviders.md) zawiera profil techniczny, który uwierzytelnia użytkownika. Dodaj następujących dostawców oświadczeń do elementu **ClaimsProviders** :

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              </InputClaims>
            <IncludeTechnicalProfile ReferenceId="login-NonInteractive" />
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

3. Element [UserJourney](userjourneys.md) definiuje ścieżkę, którą użytkownik podejmuje podczas korzystania z aplikacji. Dodaj element **UserJourneys** , jeśli nie istnieje z **UserJourney** identyfikowanego jako `PasswordChange` :

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.signuporsignin">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="5" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Zapisz plik zasad *TrustFrameworkExtensions.xml* .
5. Skopiuj plik *ProfileEdit.xml* pobrany z pakietem startowym i nadaj mu nazwę *ProfileEditPasswordChange.xml*.
6. Otwórz nowy plik i zaktualizuj atrybut **PolicyId** z unikatową wartością. Ta wartość jest nazwą zasad. Na przykład *B2C_1A_profile_edit_password_change*.
7. Zmodyfikuj atrybut **ReferenceId** w programie `<DefaultUserJourney>` , aby odpowiadał identyfikatorowi utworzonej przez Ciebie podróży użytkownika. Na przykład *PasswordChange*.
8. Zapisz zmiany.

## <a name="upload-and-test-the-policy"></a>Przekazywanie i testowanie zasad

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **platforma obsługi tożsamości**.
5. Na stronie zasady niestandardowe kliknij pozycję **Przekaż zasady**.
6. Wybierz opcję **Zastąp zasady, jeśli istnieje**, a następnie wyszukaj i wybierz plik *TrustframeworkExtensions.xml* .
7. Kliknij pozycję **Przekaż**.
8. Powtórz kroki od 5 do 7 dla pliku jednostki uzależnionej, takie jak *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Uruchamianie zasad

1. Otwórz zasady, które zostały zmienione. Na przykład *B2C_1A_profile_edit_password_change*.
2. W przypadku **aplikacji** wybierz wcześniej zarejestrowaną aplikację. Aby wyświetlić token, należy wyświetlić **adres URL odpowiedzi** `https://jwt.ms` .
3. Kliknij pozycję **Uruchom teraz**. Zaloguj się przy użyciu wcześniej utworzonego konta. Teraz należy mieć możliwość zmiany hasła.

## <a name="next-steps"></a>Następne kroki

- Znajdź przykładowe zasady w witrynie [GitHub](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).
- Dowiedz się więcej [na temat konfigurowania złożoności haseł w Azure AD B2C](password-complexity.md).
- Skonfiguruj [przepływ resetowania hasła](add-password-reset-policy.md).

::: zone-end
