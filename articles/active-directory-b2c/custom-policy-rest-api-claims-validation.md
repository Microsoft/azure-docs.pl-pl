---
title: Wymiana oświadczeń interfejsu API REST jako Walidacja
titleSuffix: Azure AD B2C
description: Przewodnik dotyczący tworzenia Azure AD B2Cych podróży użytkowników, które współdziałają z usługami RESTful.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 761bc4db7760ef5e84e3fc3c8a5deea5d4508f51
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951931"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Wskazówki: integruje wymianę oświadczeń interfejsu API REST w ramach kursu Azure AD B2C użytkownika w celu zweryfikowania danych wejściowych użytkownika

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Program Identity Experience Framework (IEF), który podpina Azure Active Directory B2C (Azure AD B2C) umożliwia deweloperom tożsamości integrację interakcji z interfejsem API RESTful w podróży użytkownika.  Po zakończeniu tego instruktażu będziesz mieć możliwość utworzenia Azure AD B2Cej podróży użytkowników, która współdziała z [usługami RESTful](custom-policy-rest-api-intro.md) w celu weryfikacji danych wejściowych użytkownika.

W tym scenariuszu dodamy użytkownikom możliwość wprowadzenia numeru lojalnościowego do strony rejestracji Azure AD B2C. Sprawdzimy, czy ta kombinacja poczty e-mail i numeru lojalnościowego jest zamapowana na kod promocyjny, wysyłając te dane do interfejsu API REST. Jeśli interfejs API REST znajdzie kod promocyjny dla tego użytkownika, zostanie on zwrócony do Azure AD B2C. Na koniec kod promocyjny zostanie wstawiony do oświadczeń tokenu dla aplikacji do użycia.

Interakcję można także zaprojektować jako krok aranżacji. Jest to odpowiednie, gdy interfejs API REST nie będzie sprawdzać poprawności danych na ekranie i zawsze zwraca oświadczenia. Aby uzyskać więcej informacji, zobacz [Przewodnik: Integrowanie wymiany oświadczeń interfejsu API REST w ramach kursu Azure AD B2C użytkownika jako kroku aranżacji](custom-policy-rest-api-claims-exchange.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych](custom-policy-get-started.md). Należy mieć działającą niestandardową zasadę do rejestracji i logowania przy użyciu kont lokalnych.
- Dowiedz się, jak [zintegrować wymianę oświadczeń interfejsu API REST w zasadach niestandardowych Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Przygotowywanie punktu końcowego interfejsu API REST

W tym instruktażu należy mieć interfejs API REST, który sprawdza, czy adres e-mail jest zarejestrowany w systemie zaplecza przy użyciu identyfikatora lojalnościowego. W przypadku zarejestrowania interfejs API REST powinien zwrócić kod promocji rejestracji, którego klient może użyć do zakupu towarów w aplikacji. W przeciwnym razie interfejs API REST powinien zwrócić komunikat o błędzie HTTP 409: "Identyfikator lojalnościowy" {lojalny identyfikator} "nie jest skojarzony z adresem e-mail" {email} ".

Poniższy kod JSON ilustruje Azure AD B2C dane zostaną wysłane do punktu końcowego interfejsu API REST. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Gdy interfejs API REST zweryfikuje dane, musi zwrócić HTTP 200 (ok), z następującymi danymi JSON:

```json
{
    "promoCode": "24534"
}
```

Jeśli walidacja nie powiodła się, interfejs API REST musi zwrócić HTTP 409 (konflikt) z `userMessage` elementem JSON. IEF oczekuje `userMessage` żądania, które zwraca interfejs API REST. Jeśli sprawdzanie poprawności zakończy się niepowodzeniem, to zgłoszenie zostanie przedstawione jako ciąg dla użytkownika.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

Konfiguracja punktu końcowego interfejsu API REST jest poza zakresem tego artykułu. Utworzyliśmy przykład [Azure Functions](../azure-functions/functions-reference.md) . Możesz uzyskać dostęp do kompletnego kodu funkcji platformy Azure w serwisie [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Definiowanie oświadczeń

W trakcie wykonywania zasad Azure AD B2C, zgłoszenie zapewnia tymczasowy magazyn danych. Oświadczenia można zadeklarować w sekcji [schematu oświadczeń](claimsschema.md) . 

1. Otwórz plik rozszerzeń zasad. Na przykład <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Wyszukaj element [BuildingBlocks](buildingblocks.md) . Jeśli element nie istnieje, Dodaj go.
1. Znajdź element [ClaimsSchema](claimsschema.md) . Jeśli element nie istnieje, Dodaj go.
1. Dodaj następujące oświadczenia do elementu **ClaimsSchema** .  

```xml
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Dodaj profil techniczny interfejsu API RESTful 

[Profil techniczny RESTful](restful-technical-profile.md) zapewnia pomoc techniczną dla usługi RESTful. Azure AD B2C wysyła dane do usługi RESTful w `InputClaims` kolekcji i odbiera dane z powrotem w `OutputClaims` kolekcji. Znajdź element **ClaimsProviders** i Dodaj nowego dostawcę oświadczeń w następujący sposób:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

W tym przykładzie `userLanguage` zostanie on wysłany do usługi REST, jak `lang` w ramach ładunku JSON. Wartość tego `userLanguage` żądania zawiera identyfikator języka bieżącego użytkownika. Aby uzyskać więcej informacji, zobacz temat [Rozwiązywanie konfliktów](claim-resolver-overview.md).

### <a name="configure-the-restful-api-technical-profile"></a>Konfigurowanie profilu technicznego interfejsu API RESTful 

Po wdrożeniu interfejsu API REST Ustaw metadane `REST-ValidateProfile` profilu technicznego w celu odzwierciedlenia własnego interfejsu API REST, w tym:

- **ServiceUrl**. Ustaw adres URL punktu końcowego interfejsu API REST.
- **SendClaimsIn**. Określ, w jaki sposób oświadczenia wejściowe są wysyłane do dostawcy oświadczeń RESTful.
- **AuthenticationType**. Ustaw typ uwierzytelniania wykonywanego przez dostawcę oświadczeń RESTful. 
- **AllowInsecureAuthInProduction**. Upewnij się, że w środowisku produkcyjnym ustawiono następujące metadane `true`
    
Więcej konfiguracji można znaleźć w temacie [metadane profilu technicznego RESTful](restful-technical-profile.md#metadata) .

Powyższe Komentarze `AuthenticationType` i `AllowInsecureAuthInProduction` określają zmiany, które należy wykonać po przejściu do środowiska produkcyjnego. Aby dowiedzieć się, jak zabezpieczyć interfejsy API usługi RESTful w środowisku produkcyjnym, zobacz [Secure RESTful API](secure-rest-api.md).

## <a name="validate-the-user-input"></a>Weryfikowanie danych wejściowych użytkownika

Aby uzyskać numer lojalnościowy użytkownika podczas rejestracji, musisz zezwolić użytkownikowi na wprowadzanie tych danych na ekranie. Dodaj **loyaltyIde** dane wyjściowe do strony rejestracji, dodając ją do istniejącej sekcji profilu technicznego rejestracji `OutputClaims` . Określ całą listę oświadczeń danych wyjściowych do kontrolowania kolejności, w której oświadczenia są prezentowane na ekranie.  

Dodaj do profilu technicznego rejestracji numer referencyjny profilu weryfikacji, który wywołuje `REST-ValidateProfile` . Nowy profil techniczny weryfikacji zostanie dodany na początku `<ValidationTechnicalProfiles>` kolekcji zdefiniowanej w zasadach podstawowych. To zachowanie oznacza, że po pomyślnym sprawdzeniu Azure AD B2C przejdzie w celu utworzenia konta w katalogu.   

1. Znajdź element **ClaimsProviders** . Dodaj nowego dostawcę oświadczeń w następujący sposób:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Uwzględnianie roszczeń w tokenie 

Aby zwrócić z powrotem do aplikacji jednostki uzależnionej, należy dodać do pliku zgłoszenie wyjściowe <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> . Zgłoszenie danych wyjściowych zezwoli na dodanie żądania do tokenu po pomyślnym przejściu użytkownika i zostanie wysłane do aplikacji. Zmodyfikuj element profil techniczny w sekcji jednostki uzależnionej, aby dodać jako zgłoszenie `promoCode` wyjściowe.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **katalog + subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **rejestracje aplikacji**.
1. Wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **Przekaż zasady niestandardowe**, a następnie Przekaż zmienione pliki zasad: *TrustFrameworkExtensions.xml* i *SignUpOrSignin.xml*. 
1. Wybierz przekazane zasady rejestracji lub logowania, a następnie kliknij przycisk **Uruchom teraz** .
1. Należy mieć możliwość rejestrowania się przy użyciu adresu e-mail.
1. Kliknij link **Utwórz konto teraz** .
1. W polu **Identyfikator lojalności** wpisz 1234, a następnie kliknij przycisk **Kontynuuj**. W tym momencie powinien zostać wyświetlony komunikat o błędzie walidacji.
1. Zmień na inną wartość, a następnie kliknij przycisk **Kontynuuj**.
1. Token wysłany z powrotem do aplikacji zawiera `promoCode` zastrzeżenie.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zabezpieczyć interfejsy API, zobacz następujące artykuły:

- [Przewodnik: integruje wymianę oświadczeń interfejsu API REST w ramach przejazdu Azure AD B2C użytkownika w ramach kroku aranżacji](custom-policy-rest-api-claims-exchange.md)
- [Zabezpieczanie interfejsu API usługi RESTful](secure-rest-api.md)
- [Dokumentacja: profil techniczny RESTful](restful-technical-profile.md)