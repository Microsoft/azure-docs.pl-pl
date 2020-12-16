---
title: Przekaż token dostępu dostawcy tożsamości do aplikacji
titleSuffix: Azure AD B2C
description: Dowiedz się, jak przekazać token dostępu dla dostawców tożsamości OAuth 2,0 jako rolę w przepływie użytkownika w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a99d41f5f9fc9538aaf563bd3ae56075d269c94a
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584650"
---
# <a name="pass-an-identity-provider-access-token-to-your-application-in-azure-active-directory-b2c"></a>Przekaż token dostępu dostawcy tożsamości do aplikacji w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[Przepływ użytkownika](user-flow-overview.md) w Azure Active Directory B2C (Azure AD B2C) zapewnia użytkownikom aplikacji możliwość rejestrowania się lub logowania za pomocą dostawcy tożsamości. Po rozpoczęciu podróży Azure AD B2C otrzymuje [token dostępu](tokens-overview.md) od dostawcy tożsamości. Azure AD B2C używa tego tokenu do pobierania informacji o użytkowniku. W przepływie użytkownika można włączyć funkcję przekazywania tokenu do aplikacji, które są rejestrowane w Azure AD B2C.

::: zone pivot="b2c-user-flow"

Azure AD B2C obsługuje przekazywanie tokenu dostępu dla dostawców tożsamości [OAuth 2,0](add-identity-provider.md) , w tym [Facebook](identity-provider-facebook.md) i [Google](identity-provider-google.md). W przypadku wszystkich innych dostawców tożsamości, zgłoszenie jest zwracane puste.

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure AD B2C obsługuje przekazywanie tokenu dostępu dla dostawców tożsamości [OAuth 2,0](authorization-code-flow.md) i [OpenID Connect Connect](openid-connect.md) . W przypadku wszystkich innych dostawców tożsamości, zgłoszenie jest zwracane puste.

::: zone-end

Na poniższym diagramie przedstawiono sposób, w jaki token dostawcy tożsamości zwraca do aplikacji: 

![Przepływ przekazywania przez dostawcę tożsamości](./media/idp-pass-through-user-flow/identity-provider-pass-through-flow.png)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="enable-the-claim"></a>Włączanie tego żądania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog, w którym znajduje się Twoja dzierżawa.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **przepływy użytkownika (zasady)**, a następnie wybierz swój przepływ użytkownika. Na przykład **B2C_1_signupsignin1**.
5. Wybierz pozycję **Oświadczenia aplikacji**.
6. Włącz funkcję **token dostępu dostawcy tożsamości** .

    ![Włączanie żądania tokenu dostępu dostawcy tożsamości](./media/idp-pass-through-user-flow/identity-provider-pass-through-app-claim.png)

7. Kliknij przycisk **Zapisz** , aby zapisać przepływ użytkownika.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

W przypadku testowania aplikacji w Azure AD B2C może być przydatne, aby token Azure AD B2C mógł `https://jwt.ms` sprawdzić w nim oświadczenia.

1. Na stronie Przegląd przepływu użytkownika wybierz pozycję **Uruchom przepływ użytkownika**.
2. W przypadku **aplikacji** wybierz wcześniej zarejestrowaną aplikację. Aby wyświetlić token w poniższym przykładzie, **adres URL odpowiedzi** powinien być widoczny `https://jwt.ms` .
3. Kliknij pozycję **Uruchom przepływ użytkownika**, a następnie zaloguj się przy użyciu poświadczeń konta. Powinien zostać wyświetlony token dostępu dostawcy tożsamości w **idp_access_tokenm** .

    Powinieneś zobaczyć coś podobnego do poniższego przykładu:

    ![Zdekodowany token w jwt.ms z wyróżnionym blokiem idp_access_token](./media/idp-pass-through-user-flow/identity-provider-pass-through-token.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-the-claim-elements"></a>Dodaj elementy roszczeń

1. Otwórz plik *TrustframeworkExtensions.xml* i Dodaj następujący element **ClaimType** z identyfikatorem `identityProviderAccessToken` do elementu **ClaimsSchema** :

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Dodaj element **oświadczenie outputclaim** do elementu **profilu technicznym** dla każdego dostawcy tożsamości OAuth 2,0, dla którego chcesz uzyskać token dostępu. Poniższy przykład pokazuje element dodany do profilu technicznego Facebook:

    ```xml
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Zapisz plik *TrustframeworkExtensions.xml* .
4. Otwórz plik zasad jednostki uzależnionej, taki jak *SignUpOrSignIn.xml*, i Dodaj element **oświadczenie outputclaim** do **profilu technicznym**:

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Zapisz plik zasad.

## <a name="test-your-policy"></a>Testowanie zasad

Podczas testowania aplikacji w Azure AD B2C może być przydatne, aby token Azure AD B2C był w `https://jwt.ms` stanie przejrzeć oświadczenia w nim.

### <a name="upload-the-files"></a>Przekazywanie plików

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, klikając filtr **Directory + Subscription** w górnym menu i wybierając katalog zawierający dzierżawcę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **platforma obsługi tożsamości**.
5. Na stronie zasady niestandardowe kliknij pozycję **Przekaż zasady**.
6. Wybierz opcję **Zastąp zasady, jeśli istnieje**, a następnie wyszukaj i wybierz plik *TrustframeworkExtensions.xml* .
7. Wybierz pozycję **Przekaż**.
8. Powtórz kroki od 5 do 7 dla pliku jednostki uzależnionej, takie jak *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Uruchamianie zasad

1. Otwórz zasady, które zostały zmienione. Na przykład *B2C_1A_signup_signin*.
2. W przypadku **aplikacji** wybierz wcześniej zarejestrowaną aplikację. Aby wyświetlić token w poniższym przykładzie, **adres URL odpowiedzi** powinien być widoczny `https://jwt.ms` .
3. Wybierz pozycję **Uruchom teraz**.

    Powinieneś zobaczyć coś podobnego do poniższego przykładu:

    ![Zdekodowany token w jwt.ms z wyróżnionym blokiem idp_access_token](./media/idp-pass-through-user-flow/identity-provider-pass-through-token-custom.png)

::: zone-end

## <a name="next-steps"></a>Następne kroki

Więcej informacji znajduje się w temacie [Omówienie tokenów Azure AD B2C](tokens-overview.md).
