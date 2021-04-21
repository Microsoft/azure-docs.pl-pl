---
title: Samouczek konfigurowania usługi BioCatch przy użyciu Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Samouczek dotyczący konfigurowania Azure Active Directory B2C biocatch w celu identyfikowania ryzykownych i fałszywych użytkowników
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f15cc294a0d3d930548d7d9bdf1d05b552b60fa5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819445"
---
# <a name="tutorial-configure-biocatch-with-azure-active-directory-b2c"></a>Samouczek: konfigurowanie aplikacji BioCatch przy użyciu Azure Active Directory B2C

Z tego przykładowego samouczka dowiesz się, jak zintegrować uwierzytelnianie usługi Azure Active Directory (AD) B2C z usługą [BioCatch,](https://www.biocatch.com/) aby jeszcze bardziej rozszerzyć poziom zabezpieczeń zarządzania tożsamościami i dostępem klientów (CIAM). Firma BioCatch analizuje fizyczne i poznawcze zachowania cyfrowe użytkownika w celu wygenerowania szczegółowych informacji, które odróżniają uprawnionych klientów od cyberprzestępczych.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).

- [Dzierżawa Azure AD B2C połączona](tutorial-create-tenant.md) z subskrypcją platformy Azure.

- Konto [biocatch.](https://www.biocatch.com/contact-us)

## <a name="scenario-description"></a>Opis scenariusza

Integracja z usługą BioCatch obejmuje następujące składniki:

- **Aplikacja internetowa lub usługa internetowa** — użytkownik najpierw przegląda tę usługę internetową. Ta usługa internetowa iniektuje unikatowy identyfikator sesji klienta, który jest wysyłany do usługi BioCatch. Identyfikator sesji klienta natychmiast rozpoczyna przesyłanie charakterystyk zachowania użytkownika do usługi BioCatch.

- **Metoda —** wysyła unikatowy identyfikator sesji klienta do Azure AD B2C. W tym przykładzie kod JavaScript jest używany do wprowadzania wartości w ukrytym polu HTML.

- **Niestandardowy Azure AD B2C użytkownika** — ukrywa pole HTML dla danych wejściowych identyfikatora sesji klienta z kodu JavaScript, jeśli jest stosowana metoda powyżej

- **Azure AD B2C zasad niestandardowych**

  - Przyjmuje niestandardowy identyfikator sesji klienta z interfejsu użytkownika w postaci oświadczenia. Jest to osiągane za pośrednictwem samodzielnie potwierdzanych profilów technicznych

  - Integruje się z platformą BioCatch za pośrednictwem dostawcy oświadczeń interfejsu API REST i przekazuje identyfikator sesji klienta do platformy BioCatch

  - Z firmy BioCatch jest zwracanych wiele oświadczeń niestandardowych dla logiki zasad niestandardowych, które następnie działają

  - Userjourney, który ocenia zwrócone oświadczenie, na przykład ryzyko sesji, i warunkowo wykonuje akcję, taką jak wywołanie uwierzytelniania wieloskładnikowego (MFA).

![Diagram architektury bio catch.](media/partner-biocatch/biocatch-architecture-diagram.png)

| Krok  | Opis |
|:---|:-----------------------|
|1a     | Użytkownik przegląda usługę internetową. Usługa internetowa zwraca następnie wartości HTML, CSS lub JavaScript i konfiguruje ładowanie zestawu SDK Języka JavaScript dla usługi BioCatch. Kod JavaScript po stronie klienta konfiguruje/ustawia identyfikator sesji klienta dla zestawu SDK usługi BioCatch. Alternatywnie usługa internetowa może wstępnie skonfigurować identyfikator sesji klienta i wysłać go do klienta.        |
|1b    |  Skonfiguruj wystąpienia zestawu SDK Języka JavaScript biocatch na platformie BioCatch. Natychmiast rozpocznij wysyłanie właściwości zachowania użytkownika do usługi BioCatch z urządzenia klienckiego przy użyciu identyfikatora sesji klienta z kroku 1a.    |
|2     |  Użytkownik próbuje się zarejestrować/zalogować i jest przekierowywany do Azure AD B2C.      |
|3a    | Częścią zdarzenia userjourney jest samodzielnie potwierdzany element claimsprovider, który przyjmuje identyfikator sesji klienta jako dane wejściowe. To pole jest ukryte na ekranie. Możesz użyć języka JavaScript, aby wprowadzić identyfikator sesji w polu . Wybierz przycisk *Dalej,* aby kontynuować proces rejestracji/logowania.|
|3b     | Identyfikator sesji klienta jest przesłany do platformy BioCatch w celu określenia oceny ryzyka. |
|3c     | Firma BioCatch zwraca informacje o sesji, takie jak ocena ryzyka i zalecenie dotyczące tego, co należy zrobić — zezwolić lub zablokować |
|3d    |Użytkownik ma krok kontroli warunkowej, który działa na zwracanych oświadczeniach|
| 4   | Na podstawie wyniku kontroli warunkowej wywoływana jest akcja, taka jak *step-up MFA*|
|5    | W dowolnym momencie od pierwszego trafienia strony usługi internetowej usługa internetowa może użyć identyfikatora sesji klienta do odpytowania interfejsu API BioCatch w celu określenia oceny ryzyka i informacji o sesji w czasie rzeczywistym. |

## <a name="onboard-with-biocatch"></a>Dołączanie przy użyciu biocatch

Skontaktuj się [z biocatch](https://www.biocatch.com/contact-us) i utwórz konto.

## <a name="configure-the-custom-ui"></a>Konfigurowanie niestandardowego interfejsu użytkownika

Zaleca się ukrycie pola identyfikatora sesji klienta. Użyj arkuszy CSS, języka JavaScript lub dowolnej innej metody, aby ukryć pole. W celach testowych możesz odkryć to pole. Na przykład kod JavaScript służy do ukrywania pola wejściowego jako:

```
document.getElementById("clientSessionId").style.display = 'none';
```

## <a name="configure--azure-ad-b2c-identity-experience-framework-policies"></a>Konfigurowanie Azure AD B2C Identity Experience Framework konfiguracji

1. Skonfiguruj początkową [konfigurację zasad niestandardowych.](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)

2. Utwórz nowy plik, który dziedziczy z pliku rozszerzeń.

    ```
    <BasePolicy> 

        <TenantId>tenant.onmicrosoft.com</TenantId> 

        <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId> 

      </BasePolicy> 
    ```

3. Utwórz odwołanie do niestandardowego interfejsu użytkownika, aby ukryć pole wejściowe w obszarze zasobu BuildingBlocks.

    ```
    <ContentDefinitions> 

        <ContentDefinition Id="api.selfasserted"> 

            <LoadUri>https://domain.com/path/to/selfAsserted.cshtml</LoadUri> 

            <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri> 

          </ContentDefinition> 

        </ContentDefinitions>
    ```

4. Dodaj następujące oświadczenia w obszarze zasobu BuildingBlocks.

    ```
    <ClaimsSchema> 

          <ClaimType Id="riskLevel"> 

            <DisplayName>Session risk level</DisplayName> 

            <DataType>string</DataType>       

          </ClaimType> 

          <ClaimType Id="score"> 

            <DisplayName>Session risk score</DisplayName> 

            <DataType>int</DataType>       

          </ClaimType> 

          <ClaimType Id="clientSessionId"> 

            <DisplayName>The ID of the client session</DisplayName> 

            <DataType>string</DataType> 

            <UserInputType>TextBox</UserInputType> 

          </ClaimType> 

    <ClaimsSchema> 
    ```

5. Skonfiguruj dostawcę oświadczeń samodzielnie dla pola identyfikatora sesji klienta.

    ```
    <ClaimsProvider> 

          <DisplayName>Client Session ID Claims Provider</DisplayName> 

          <TechnicalProfiles> 

            <TechnicalProfile Id="login-NonInteractive-clientSessionId"> 

              <DisplayName>Client Session ID TP</DisplayName> 

              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

              <Metadata> 

                <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item> 

              </Metadata> 

              <CryptographicKeys> 

                <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" /> 

              </CryptographicKeys> 

            <!—Claim we created earlier --> 

              <OutputClaims> 

                <OutputClaim ClaimTypeReferenceId="clientSessionId" Required="false" DefaultValue="100"/> 

              </OutputClaims> 

            <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" /> 

            </TechnicalProfile> 

          </TechnicalProfiles> 

        </ClaimsProvider> 
    ```

6. Konfigurowanie dostawcy oświadczeń interfejsu API REST dla usługi BioCatch. 

    ```
    <TechnicalProfile Id="BioCatch-API-GETSCORE"> 

          <DisplayName>Technical profile for BioCatch API to return session information</DisplayName> 

          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

          <Metadata> 

            <Item Key="ServiceUrl">https://biocatch-url.com/api/v6/score?customerID=<customerid>&amp;action=getScore&amp;uuid=<uuid>&amp;customerSessionID={clientSessionId}&amp;solution=ATO&amp;activtyType=<activity_type>&amp;brand=<brand></Item> 

            <Item Key="SendClaimsIn">Url</Item> 

            <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item> 

            <!-- Set AuthenticationType to Basic or ClientCertificate in production environments --> 

            <Item Key="AuthenticationType">None</Item> 

            <!-- REMOVE the following line in production environments --> 

            <Item Key="AllowInsecureAuthInProduction">true</Item> 

          </Metadata> 

          <InputClaims> 

            <InputClaim ClaimTypeReferenceId="clientsessionId" /> 

          </InputClaims> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

          </OutputClaims> 

          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" /> 

        </TechnicalProfile> 

      </TechnicalProfiles>
    ```

    > [!Note]
    > Firma BioCatch udostępni adres URL, identyfikator klienta i unikatowy identyfikator użytkownika (uuID) do skonfigurowania. Oświadczenie SessionID klienta jest przekazywane do firmy BioCatch jako parametr querystring. Możesz wybrać typ działania, na przykład *MAKE_PAYMENT*.

7. Skonfiguruj użytkownikajourney; postępuj zgodnie z przykładem

   1. Pobierz clientSessionID jako oświadczenie

   1. Wywołanie interfejsu API usługi BioCatch w celu uzyskania informacji o sesji

   1. Jeśli ryzyko zwrócenia oświadczenia *jest* równe *niskie,* pomiń krok uwierzytelniania wieloskładnikowego, w innym przypadku wymuś uwierzytelniania wieloskładnikowego użytkownika

    ```
    <OrchestrationStep Order="8" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="clientSessionIdInput" TechnicalProfileReferenceId="login-NonInteractive-clientSessionId" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="9" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="BcGetScore" TechnicalProfileReferenceId=" BioCatch-API-GETSCORE" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="10" Type="ClaimsExchange"> 

              <Preconditions> 

                <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 

                  <Value>riskLevel</Value> 

                  <Value>LOW</Value> 

                  <Action>SkipThisOrchestrationStep</Action> 

                </Precondition> 

              </Preconditions> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" /> 

              </ClaimsExchanges>  

    ```

8. Konfigurowanie na podstawie konfiguracji jednostki zależnej (opcjonalnie)

    Warto przekazać zwrócone informacje do aplikacji jako oświadczenia w tokenie, w szczególności *risklevel* i *score*.

    ```
    <RelyingParty> 

        <DefaultUserJourney ReferenceId="SignUpOrSignInMfa" /> 

        <UserJourneyBehaviors> 

          <SingleSignOn Scope="Tenant" KeepAliveInDays="30" /> 

          <SessionExpiryType>Absolute</SessionExpiryType> 

          <SessionExpiryInSeconds>1200</SessionExpiryInSeconds> 

          <ScriptExecution>Allow</ScriptExecution> 

        </UserJourneyBehaviors> 

        <TechnicalProfile Id="PolicyProfile"> 

          <DisplayName>PolicyProfile</DisplayName> 

          <Protocol Name="OpenIdConnect" /> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="displayName" /> 

            <OutputClaim ClaimTypeReferenceId="givenName" /> 

            <OutputClaim ClaimTypeReferenceId="surname" /> 

            <OutputClaim ClaimTypeReferenceId="email" /> 

            <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" /> 

            <OutputClaim ClaimTypeReferenceId="identityProvider" />                 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

            <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" /> 

          </OutputClaims> 

          <SubjectNamingInfo ClaimType="sub" /> 

        </TechnicalProfile> 

      </RelyingParty> 

    ```

## <a name="integrate-with-azure-ad-b2c"></a>Integracja z usługą Azure AD B2C

Wykonaj następujące kroki, aby dodać pliki zasad do Azure AD B2C

1. Zaloguj się do  [**Azure Portal**](https://portal.azure.com/) jako administrator globalny dzierżawy Azure AD B2C dzierżawy.

2. Upewnij się, że używasz katalogu zawierającego dzierżawę Azure AD B2C dzierżawy. Wybierz **pozycję Filtr katalogu** i subskrypcji w górnym menu i wybierz katalog zawierający Twoją   dzierżawę.

3. Wybierz **pozycję Wszystkie** usługi w lewym górnym rogu strony Azure Portal wyszukaj i wybierz   Azure AD B2C.

4. Przejdź do **Azure AD B2C**   >  **Identity Experience Framework**

3. Przekaż wszystkie pliki zasad do dzierżawy.

## <a name="test-the-solution"></a>Testowanie rozwiązania

1. [Rejestrowanie fikcyjnej aplikacji, która przekierowuje do JWT.MS](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=app-reg-ga)  

2. W **obszarze Identity Experience Framework** wybierz utworzone zasady

3. W oknie zasad wybierz fikcyjną JWT.MS, a następnie wybierz **pozycję Uruchom teraz**

4. Przejdź przez przepływ tworzenia konta i utwórz konto. Token zwracany do JWT.MS powinien mieć 2x oświadczenia dla riskLevel i oceny. Postępuj zgodnie z przykładem.  

    ```
    { 

      "typ": "JWT", 

      "alg": "RS256", 

      "kid": "_keyid" 

    }.{ 

      "exp": 1615872580, 

      "nbf": 1615868980, 

      "ver": "1.0", 

      "iss": "https://tenant.b2clogin.com/12345678-1234-1234-1234-123456789012/v2.0/", 

      "sub": "12345678-1234-1234-1234-123456789012", 

      "aud": "12345678-1234-1234-1234-123456789012", 

      "acr": "b2c_1a_signup_signin_biocatch_policy", 

      "nonce": "defaultNonce", 

      "iat": 1615868980, 

      "auth_time": 1615868980, 

      "name": "John Smith", 

      "email": "john.smith@contoso.com", 

      "given_name": "John", 

      "family_name": "Smith", 

      "riskLevel": "LOW", 

      "score": 275, 

      "tid": "12345678-1234-1234-1234-123456789012" 

    }.[Signature]  

    ```

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Zasady niestandardowe w usłudze Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Wprowadzenie do zasad niestandardowych w programie Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
