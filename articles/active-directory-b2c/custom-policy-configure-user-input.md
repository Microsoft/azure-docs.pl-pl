---
title: Dodawanie oświadczeń i dostosowywanie danych wejściowych użytkownika w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się, jak dostosować dane wejściowe użytkownika i dodać oświadczenia do kursu rejestracji lub logowania w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 47fdf445fa11693dd3a998b8c73ac0c3ed8452a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85389364"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Dodawanie oświadczeń i dostosowywanie danych wejściowych użytkownika przy użyciu zasad niestandardowych w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule zebrano nowy atrybut podczas podróży podczas rejestracji w Azure Active Directory B2C (Azure AD B2C). Otrzymasz miasto użytkowników, skonfigurujesz je jako listę rozwijaną i określisz, czy jest wymagane.

> [!NOTE]
> Ten przykład używa wbudowanego "miasto". Zamiast tego można wybrać jeden z obsługiwanych [Azure AD B2C atrybuty wbudowane](user-profile-attributes.md) lub atrybut niestandardowy. Aby użyć atrybutu niestandardowego, [Włącz atrybuty niestandardowe w zasadach](custom-policy-custom-attributes.md). Aby użyć innego wbudowanego lub niestandardowego atrybutu, Zastąp wartość "miasto" wybranym atrybutem, na przykład wbudowany atrybut *stanowiska* lub atrybut niestandardowy, taki jak *extension_loyaltyId*.  

Możesz zbierać dane początkowe od użytkowników przy użyciu podróży lub logowania użytkownika. Dodatkowe oświadczenia można zbierać później za pomocą profilu Edytuj podróż użytkownika. W każdej chwili Azure AD B2C gromadzić informacje bezpośrednio od użytkownika, a platforma obsługi tożsamości używa swojego własnego [profilu technicznego](self-asserted-technical-profile.md). W tym przykładzie:

1. Zdefiniuj "miasto". 
1. Poproszenie użytkownika o ich miasto.
1. Utrwalaj miasto w profilu użytkownika w katalogu Azure AD B2C.
1. Przeczytaj wierzytelność z katalogu Azure AD B2C przy każdym logowaniu.
1. Zwróć miasto do aplikacji jednostki uzależnionej po zalogowaniu się lub zarejestrowaniu.  

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych](custom-policy-get-started.md). Należy mieć działającą niestandardową zasadę do rejestracji i logowania przy użyciu kont społecznościowych i lokalnych.

## <a name="define-a-claim"></a>Definiowanie odszkodowania

W trakcie wykonywania zasad Azure AD B2C, zgłoszenie zapewnia tymczasowy magazyn danych. [Schemat oświadczeń](claimsschema.md) jest miejscem, w którym deklarujesz oświadczenia. Następujące elementy są używane do definiowania roszczeń:

- **DisplayName** — ciąg definiujący etykietę dodaną do użytkownika.
- [DataType](claimsschema.md#datatype) — typ żądania.
- **UserHelpText** — pomaga użytkownikowi zrozumieć, co jest wymagane.
- [UserInputType](claimsschema.md#userinputtype) — typ kontrolki wprowadzania, taki jak pole tekstowe, wybór radiowy, lista rozwijana lub wiele zaznaczeń.

Otwórz plik rozszerzeń zasad. Na przykład <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Wyszukaj element [BuildingBlocks](buildingblocks.md) . Jeśli element nie istnieje, Dodaj go.
1. Znajdź element [ClaimsSchema](claimsschema.md) . Jeśli element nie istnieje, Dodaj go.
1. Dodaj wierzytelność do elementu **ClaimsSchema** .  

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-a-claim-to-the-user-interface"></a>Dodawanie roszczeń do interfejsu użytkownika

Następujące profile techniczne są [samodzielne](self-asserted-technical-profile.md), wywoływane, gdy użytkownik oczekuje na wprowadzenie:

- **LocalAccountSignUpWithLogonEmail** — przepływ rejestracji w ramach konta lokalnego.
- Logowanie za pierwszym razem z kontem **SelfAsserted-Social** -Federation User.
- **SelfAsserted-ProfileUpdate** — Edytuj przepływ profilu.

W celu zebrania roszczeń miasto podczas tworzenia konta należy dodać je jako zgłoszenie wyjściowe do `LocalAccountSignUpWithLogonEmail` profilu technicznego. Zastąp ten profil techniczny w pliku rozszerzenia. Określ całą listę oświadczeń danych wyjściowych do kontrolowania kolejności, w której oświadczenia są prezentowane na ekranie. Znajdź element **ClaimsProviders** . Dodaj nową ClaimsProviders w następujący sposób:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
<ClaimsProvider>
```

Aby zebrać wierzytelność po początkowym logowaniu przy użyciu konta federacyjnego, należy dodać je jako zgłoszenie wyjściowe do `SelfAsserted-Social` profilu technicznego. Aby użytkownicy konta lokalnego i federacyjnego mogli edytować swoje dane profilu później, należy dodać zgłoszenie danych wyjściowych do `SelfAsserted-ProfileUpdate` profilu technicznego. Zastąp te profile techniczne w pliku rozszerzenia. Określ całą listę oświadczeń danych wyjściowych do kontrolowania kolejności, w której oświadczenia są prezentowane na ekranie. Znajdź element **ClaimsProviders** . Dodaj nową ClaimsProviders w następujący sposób:

```xml
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Odczytuj i zapisuj roszczeń

Następujące profile techniczne są [Active Directory profile techniczne](active-directory-technical-profile.md), które odczytują i zapisują dane do Azure Active Directory.  
Służy `PersistedClaims` do zapisywania danych w profilu użytkownika i `OutputClaims` odczytywania danych z profilu użytkownika w ramach odpowiednich Active Directory profilów technicznych.

Zastąp te profile techniczne w pliku rozszerzenia. Znajdź element **ClaimsProviders** .  Dodaj nową ClaimsProviders w następujący sposób:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Uwzględnianie roszczeń w tokenie 

Aby zwrócić miasto z powrotem do aplikacji jednostki uzależnionej, Dodaj do pliku zgłoszenie wyjściowe <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> . Zgłoszenie danych wyjściowych zostanie dodane do tokenu po pomyślnym przejściu użytkownika i zostanie wysłane do aplikacji. Zmodyfikuj element profil techniczny w sekcji jednostki uzależnionej, aby dodać miasto jako zgłoszenie wyjściowe.
 
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
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **katalog + subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
3. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **rejestracje aplikacji**.
4. Wybierz pozycję **platforma obsługi tożsamości**.
5. Wybierz pozycję **Przekaż zasady niestandardowe**, a następnie Przekaż dwa zmienione pliki zasad.
2. Wybierz przekazane zasady rejestracji lub logowania, a następnie kliknij przycisk **Uruchom teraz** .
3. Należy mieć możliwość rejestrowania się przy użyciu adresu e-mail.

Ekran rejestracji powinien wyglądać podobnie do poniższego zrzutu ekranu:

![Zrzut ekranu przedstawiający zmodyfikowaną opcję tworzenia konta](./media/custom-policy-configure-user-input/signup-with-city-claim-dropdown-example.png)

Token wysłany z powrotem do aplikacji zawiera `city` zastrzeżenie.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o elemencie [ClaimsSchema](claimsschema.md) w odwołaniu IEF.
- Dowiedz się [, jak używać atrybutów niestandardowych w niestandardowych zasadach edytowania profilu](custom-policy-custom-attributes.md).
