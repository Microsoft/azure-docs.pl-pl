---
title: Wymiana oświadczeń interfejsu API REST — Azure Active Directory B2C
description: Dodaj wymianę oświadczeń interfejsu API REST do zasad niestandardowych w Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84053df34ffda0d4686ad80a9e5f3af00ac53d72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94949500"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Przewodnik: Dodawanie wymiany oświadczeń interfejsu API REST do zasad niestandardowych w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) umożliwia deweloperom tożsamości integrację interakcji z interfejsem API RESTful w podróży użytkownika. Po zakończeniu tego instruktażu będziesz mieć możliwość utworzenia Azure AD B2Cej podróży użytkowników, która współdziała z [usługami RESTful](custom-policy-rest-api-intro.md).

W tym scenariuszu wzbogacamy dane tokenów użytkownika przez integrację z firmowym przepływem pracy. Podczas rejestracji lub logowania przy użyciu konta lokalnego lub federacyjnego Azure AD B2C wywołuje interfejs API REST, aby uzyskać rozszerzone dane profilu użytkownika ze zdalnego źródła danych. W tym przykładzie Azure AD B2C wysyła unikatowy identyfikator użytkownika, objectId. Interfejs API REST zwraca saldo konta użytkownika (liczbę losową). Tego przykładu można użyć jako punktu wyjścia do integracji z własnym systemem CRM, marketingową bazą danych lub dowolnym przepływem pracy biznesowym.

Interakcję można także zaprojektować jako profil techniczny weryfikacji. Jest to odpowiednie, gdy interfejs API REST będzie sprawdzać poprawność danych na ekranie i zwracające oświadczenia. Aby uzyskać więcej informacji, zobacz [Przewodnik: Integrowanie wymiany interfejsów API REST w ramach kursu Azure AD B2C użytkownika w celu zweryfikowania danych wejściowych użytkownika](custom-policy-rest-api-claims-validation.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych](custom-policy-get-started.md). Należy mieć działającą niestandardową zasadę do rejestracji i logowania przy użyciu kont lokalnych.
- Dowiedz się, jak [zintegrować wymianę oświadczeń interfejsu API REST w zasadach niestandardowych Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Przygotowywanie punktu końcowego interfejsu API REST

W tym instruktażu należy mieć interfejs API REST, który sprawdza, czy identyfikator objectId Azure AD B2C użytkownika jest zarejestrowany w systemie zaplecza. W przypadku zarejestrowania interfejs API REST zwraca saldo konta użytkownika. W przeciwnym razie interfejs API REST rejestruje nowe konto w katalogu i zwraca saldo początkowe `50.00` .

Poniższy kod JSON ilustruje Azure AD B2C dane zostaną wysłane do punktu końcowego interfejsu API REST. 

```json
{
    "objectId": "User objectId",
    "lang": "Current UI language"
}
```

Gdy interfejs API REST zweryfikuje dane, musi zwrócić HTTP 200 (ok), z następującymi danymi JSON:

```json
{
    "balance": "760.50"
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
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Dodaj profil techniczny interfejsu API RESTful 

[Profil techniczny RESTful](restful-technical-profile.md) zapewnia pomoc techniczną dla usługi RESTful. Azure AD B2C wysyła dane do usługi RESTful w `InputClaims` kolekcji i odbiera dane z powrotem w `OutputClaims` kolekcji. Znajdź element **ClaimsProviders** w <em>**`TrustFrameworkExtensions.xml`**</em> pliku i Dodaj nowego dostawcę oświadczeń w następujący sposób:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
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

## <a name="add-an-orchestration-step"></a>Dodaj krok aranżacji

[Podróże użytkownika](userjourneys.md) określają jawne ścieżki, za pośrednictwem których zasady umożliwiają aplikacji jednostki zależnej uzyskanie żądanych oświadczeń dla użytkownika. Podróż użytkownika jest reprezentowana jako sekwencja aranżacji, która musi być stosowana w przypadku pomyślnej transakcji. Możesz dodawać lub odejmować kroki aranżacji. W takim przypadku dodasz nowy krok aranżacji, który służy do rozszerzania informacji dostarczonych do aplikacji po zarejestrowaniu lub zalogowaniu się użytkownika za pośrednictwem wywołania interfejsu API REST.

1. Otwórz podstawowy plik zasad. Na przykład <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> .
1. Wyszukaj `<UserJourneys>` element. Skopiuj cały element, a następnie usuń go.
1. Otwórz plik rozszerzeń zasad. Na przykład <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Wklej `<UserJourneys>` do pliku rozszerzeń po zamknięciu `<ClaimsProviders>` elementu.
1. Znajdź `<UserJourney Id="SignUpOrSignIn">` i Dodaj następujący krok aranżacji przed ostatnim.

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Refaktoryzacja ostatniego kroku aranżacji przez zmianę `Order` do `8` . Ostatnie dwa kroki aranżacji powinny wyglądać następująco:

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Powtórz ostatnie dwa kroki dla podróży użytkownika **ProfileEdit** i **PasswordReset** .


## <a name="include-a-claim-in-the-token"></a>Uwzględnianie roszczeń w tokenie 

Aby zwrócić `balance` wniosek z powrotem do aplikacji jednostki uzależnionej, Dodaj do pliku zgłoszenie wyjściowe <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> . Dodanie zgłoszenia wyjściowego spowoduje wydanie tego żądania do tokenu po pomyślnym przejściu użytkownika i zostanie wysłane do aplikacji. Zmodyfikuj element profil techniczny w sekcji jednostki uzależnionej, aby dodać `balance` jako zgłoszenie wyjściowe.
 
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
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Powtórz ten krok dla **ProfileEdit.xml** i **PasswordReset.xml** podróży użytkownika.

Zapisz zmienione pliki: *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* i *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **katalog + subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **rejestracje aplikacji**.
1. Wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **Przekaż zasady niestandardowe**, a następnie Przekaż zmienione pliki zasad: *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* i *PasswordReset.xml*. 
1. Wybierz przekazane zasady rejestracji lub logowania, a następnie kliknij przycisk **Uruchom teraz** .
1. Użytkownik powinien mieć możliwość rejestrowania się przy użyciu adresu e-mail lub konta w serwisie Facebook.
1. Token wysłany z powrotem do aplikacji zawiera `balance` zastrzeżenie.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zabezpieczyć interfejsy API, zobacz następujące artykuły:

- [Przewodnik: integruje wymianę oświadczeń interfejsu API REST w ramach przejazdu Azure AD B2C użytkownika w ramach kroku aranżacji](custom-policy-rest-api-claims-exchange.md)
- [Zabezpieczanie interfejsu API usługi RESTful](secure-rest-api.md)
- [Dokumentacja: profil techniczny RESTful](restful-technical-profile.md)