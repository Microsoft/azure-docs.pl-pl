---
title: Przekazywanie tokenu dostępu za pomocą zasad niestandardowych do aplikacji
titleSuffix: Azure AD B2C
description: Dowiedz się, jak przekazać token dostępu dla dostawców tożsamości OAuth 2,0 jako rolę w ramach niestandardowych zasad do aplikacji w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c434ad6a724ba513caf7923916997600097b43f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387868"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Przekaż token dostępu za pomocą zasad niestandardowych do aplikacji w Azure Active Directory B2C

[Zasady niestandardowe](custom-policy-get-started.md) w Azure Active Directory B2C (Azure AD B2C) umożliwiają użytkownikom aplikacji rejestrowanie się lub logowanie przy użyciu dostawcy tożsamości. W takim przypadku Azure AD B2C odbiera [token dostępu](tokens-overview.md) od dostawcy tożsamości. Azure AD B2C używa tego tokenu do pobierania informacji o użytkowniku. Aby przekazać token do aplikacji rejestrowanych w Azure AD B2C, należy dodać do zasad niestandardowych typ i zastrzeżenie wyjściowe.

Azure AD B2C obsługuje przekazywanie tokenu dostępu dla dostawców tożsamości [OAuth 2,0](authorization-code-flow.md) i [OpenID Connect Connect](openid-connect.md) . W przypadku wszystkich innych dostawców tożsamości, zgłoszenie jest zwracane puste.

## <a name="prerequisites"></a>Wymagania wstępne

* Zasady niestandardowe są konfigurowane za pomocą dostawcy tożsamości protokołu OAuth 2,0 lub OpenID Connect Connect.

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
2. W przypadku **aplikacji**wybierz wcześniej zarejestrowaną aplikację. Aby wyświetlić token w poniższym przykładzie, **adres URL odpowiedzi** powinien być widoczny `https://jwt.ms` .
3. Wybierz pozycję **Uruchom teraz**.

    Powinieneś zobaczyć coś podobnego do poniższego przykładu:

    ![Zdekodowany token w jwt.ms z wyróżnionym blokiem idp_access_token](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tokenach w [odniesieniu do tokenów Azure Active Directory B2C](tokens-overview.md).
