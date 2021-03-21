---
title: Konfigurowanie przepływu poświadczeń hasła właściciela zasobu
titleSuffix: Azure AD B2C
description: Dowiedz się, jak skonfigurować przepływ poświadczeń hasła właściciela zasobu (ROPC) w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3a7c93bb0e0dcc51e35bc27fa0799d8410e66df6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581885"
---
# <a name="set-up-a-resource-owner-password-credentials-flow-in-azure-active-directory-b2c"></a>Konfigurowanie przepływu poświadczeń hasła właściciela zasobu w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

W Azure Active Directory B2C (Azure AD B2C) przepływ poświadczeń hasła właściciela zasobu (ROPC) jest standardowym przepływem uwierzytelniania OAuth. W tym przepływie aplikacja, nazywana również jednostką uzależnioną, wymienia prawidłowe poświadczenia dla tokenów. Poświadczenia zawierają identyfikator użytkownika i hasło. Zwracane tokeny są tokenem identyfikatora, tokenem dostępu i tokenem odświeżania.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]


## <a name="ropc-flow-notes"></a>Uwagi dotyczące przepływu ROPC

W Azure Active Directory B2C (Azure AD B2C) obsługiwane są następujące opcje:

- **Klient natywny**: interakcja użytkownika podczas uwierzytelniania występuje, gdy kod jest uruchamiany na urządzeniu po stronie użytkownika. Urządzenie może być aplikacją mobilną, która działa w natywnym systemie operacyjnym, takim jak Android i iOS.
- **Publiczny przepływ klienta**: tylko poświadczenia użytkownika zebrane przez aplikację są wysyłane w wywołaniu interfejsu API. Poświadczenia aplikacji nie są wysyłane.
- **Dodawanie nowych oświadczeń**: zawartość tokenu identyfikatora można zmienić, aby dodać nowe oświadczenia.

Następujące przepływy nie są obsługiwane:

- **Serwer-serwer**: system ochrony tożsamości musi mieć niezawodny adres IP zebrany od wywołującego (natywnego klienta) w ramach interakcji. W wywołaniu interfejsu API po stronie serwera jest używany tylko adres IP serwera. Jeśli zostanie przekroczony dynamiczny próg nieudanych uwierzytelnień, system ochrony tożsamości może zidentyfikować powtórzony adres IP jako osobę atakującą.
- **Poufny przepływ klienta**: identyfikator klienta aplikacji został zweryfikowany, ale wpis tajny aplikacji nie jest zweryfikowany.

W przypadku korzystania z przepływu ROPC należy wziąć pod uwagę następujące kwestie:

- ROPC nie działa, gdy istnieje jakiekolwiek przerwanie przepływu uwierzytelniania, które wymaga interakcji z użytkownikiem. Jeśli na przykład hasło wygasło lub wymaga zmiany, wymagane jest [uwierzytelnianie wieloskładnikowe](multi-factor-authentication.md) lub jeśli trzeba będzie zbierać więcej informacji podczas logowania (na przykład użytkownik wyraża zgodę).
- ROPC obsługuje tylko konta lokalne. Użytkownicy nie mogą zalogować się za pomocą [federacyjnych dostawców tożsamości](add-identity-provider.md) , takich jak Microsoft, Google +, Twitter, AD FS lub Facebook.
- [Zarządzanie sesjami](session-behavior.md), w tym nie [WYLOGOWUJ mnie (KMSI)](session-behavior.md#enable-keep-me-signed-in-kmsi), nie ma zastosowania.


## <a name="register-an-application"></a>Rejestrowanie aplikacji

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

::: zone pivot="b2c-user-flow"

##  <a name="create-a-resource-owner-user-flow"></a>Tworzenie przepływu użytkownika właściciela zasobu

1. Zaloguj się w witrynie Azure Portal jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Aby przełączyć się do dzierżawy Azure AD B2C, wybierz katalog B2C w prawym górnym rogu portalu.
3. Wybierz pozycję **przepływy użytkownika** i wybierz pozycję **Nowy przepływ użytkownika**.
4. Wybierz pozycję **Zaloguj się przy użyciu poświadczeń hasła właściciela zasobu (ROPC)**.
5. W obszarze **wersja**, upewnij się, że jest zaznaczona opcja **Podgląd** , a następnie wybierz pozycję **Utwórz**.
7. Podaj nazwę przepływu użytkownika, na przykład *ROPC_Auth*.
8. W obszarze **oświadczenia aplikacji** kliknij przycisk **Pokaż więcej**.
9. Wybierz oświadczenia aplikacji, które są potrzebne dla aplikacji, takie jak nazwa wyświetlana, adres E-mail i dostawca tożsamości.
10. Wybierz przycisk **OK**, a następnie wybierz pozycję **Utwórz**.

::: zone-end

::: zone pivot="b2c-custom-policy"

##  <a name="create-a-resource-owner-policy"></a>Tworzenie zasad właściciela zasobu

1. Otwórz plik *TrustFrameworkExtensions.xml* .
2. Jeśli jeszcze nie istnieje, Dodaj element **ClaimsSchema** i jego elementy podrzędne jako pierwszy element w elemencie **BuildingBlocks** :

    ```xml
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. Po **ClaimsSchema** Dodaj element **ClaimsTransformations** i jego elementy podrzędne do elementu **BuildingBlocks** :

    ```xml
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>

      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. Znajdź element **ClaimsProvider** , który ma wartość **DisplayName** `Local Account SignIn` i Dodaj następujący profil techniczny:

    ```xml
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
        <Item Key="grant_type">password</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppId" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppId" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    Zastąp wartość **DefaultValue** dla **Client_id** identyfikatorem aplikacji aplikacji ProxyIdentityExperienceFramework utworzonej w samouczku dotyczącym wymagań wstępnych. Następnie Zastąp wartość **DefaultValue** dla **Resource_id** identyfikatorem aplikacji aplikacji IdentityExperienceFramework, która została również utworzona w samouczku wymagań wstępnych.

5. Dodaj następujące elementy **ClaimsProvider** z ich profilami technicznymi do elementu **ClaimsProviders** :

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

6. Dodaj element **UserJourneys** i jego elementy podrzędne do elementu **TrustFrameworkPolicy** :

    ```xml
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. Na stronie **zasady niestandardowe** w dzierżawie Azure AD B2C wybierz pozycję **Przekaż zasady**.
8. Włącz **Zastępowanie zasad, jeśli istnieje**, a następnie wyszukaj i wybierz plik *TrustFrameworkExtensions.xml* .
9. Kliknij pozycję **Przekaż**.

## <a name="create-a-relying-party-file"></a>Utwórz plik jednostki uzależnionej

Następnie zaktualizuj plik jednostki uzależnionej inicjujący utworzoną przez Ciebie podróż użytkownika:

1. Utwórz kopię pliku *SignUpOrSignin.xml* w katalogu roboczym i zmień jego nazwę na *ROPC_Auth.xml*.
2. Otwórz nowy plik i zmień wartość atrybutu **PolicyId** dla **TrustFrameworkPolicy** na unikatową wartość. Identyfikator zasad to nazwa zasad. Na przykład **B2C_1A_ROPC_Auth**.
3. Zmień wartość atrybutu **ReferenceId** w **DefaultUserJourney** na `ResourceOwnerPasswordCredentials` .
4. Zmień element **OutputClaims** tak, aby zawierał tylko następujące oświadczenia:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. Na stronie **zasady niestandardowe** w dzierżawie Azure AD B2C wybierz pozycję **Przekaż zasady**.
6. Włącz **Zastępowanie zasad, jeśli istnieje**, a następnie wyszukaj i wybierz plik *ROPC_Auth.xml* .
7. Kliknij pozycję **Przekaż**.


::: zone-end

## <a name="test-the-ropc-flow"></a>Testowanie przepływu ROPC

Użyj ulubionej aplikacji do programowania interfejsów API do wygenerowania wywołania interfejsu API i przejrzyj odpowiedź na Debugowanie zasad. Utwórz wywołanie podobne do tego przykładu, używając następujących informacji jako treści żądania POST:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- Zamień `<tenant-name>` na nazwę dzierżawy Azure AD B2C.
- Zastąp `B2C_1A_ROPC_Auth` pełną nazwą zasad poświadczeń hasła właściciela zasobu.

| Klucz | Wartość |
| --- | ----- |
| nazwa użytkownika | `user-account` |
| hasło | `password1` |
| grant_type | hasło |
| scope | OpenID Connect `application-id` offline_access |
| client_id | `application-id` |
| response_type | id_token tokenu |

- Zamień `user-account` na nazwę konta użytkownika w dzierżawie.
- Zamień `password1` na hasło konta użytkownika.
- Zamień na `application-id` Identyfikator aplikacji z rejestracji *ROPC_Auth_app* .
- *Offline_access* jest opcjonalne, jeśli chcesz otrzymać token odświeżenia.

Rzeczywiste żądanie POST wygląda podobnie do poniższego przykładu:

```https
POST /<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

Pomyślna odpowiedź z dostępem w trybie offline wygląda podobnie do poniższego przykładu:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>Zrealizuj token odświeżania

Utwórz wywołanie POST podobne do pokazanego tutaj. Użyj informacji w poniższej tabeli jako treści żądania:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- Zamień `<tenant-name>` na nazwę dzierżawy Azure AD B2C.
- Zastąp `B2C_1A_ROPC_Auth` pełną nazwą zasad poświadczeń hasła właściciela zasobu.

| Klucz | Wartość |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| zasób | `application-id` |
| refresh_token | `refresh-token` |

- Zamień na `application-id` Identyfikator aplikacji z rejestracji *ROPC_Auth_app* .
- Zamień na `refresh-token` **refresh_token** , który został wysłany z powrotem w poprzedniej odpowiedzi.

Pomyślna odpowiedź wygląda podobnie do poniższego przykładu:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="use-a-native-sdk-or-app-auth"></a>Użyj natywnego zestawu SDK lub App-Auth

Azure AD B2C są zgodne ze standardami OAuth 2,0 dla poświadczeń hasła właściciela publicznego zasobu klienta i powinny być kompatybilne z większością zestawów SDK klienta. Najnowsze informacje znajdują się w temacie [Native App SDK for OAuth 2,0 i OpenID Connect Connect implementujące nowoczesne najlepsze rozwiązania](https://appauth.io/).

## <a name="next-steps"></a>Następne kroki

Pobierz przykłady robocze, które zostały skonfigurowane do użycia z Azure AD B2C z usługi GitHub [dla systemów Android](https://aka.ms/aadb2cappauthropc) i [iOS](https://aka.ms/aadb2ciosappauthropc).
