---
title: Dodawanie atrybutów użytkownika i dostosowywanie danych wejściowych użytkownika
titleSuffix: Azure AD B2C
description: Dowiedz się, jak dostosować dane wejściowe użytkownika i dodać atrybuty użytkownika do kursu rejestracji lub logowania w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4e709719d56aacacf61e247a5dbe215f766a891a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102607955"
---
#  <a name="add-user-attributes-and-customize-user-input-in-azure-active-directory-b2c"></a>Dodawanie atrybutów użytkownika i dostosowywanie danych wejściowych użytkownika w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

W tym artykule zebrano nowy atrybut podczas podróży podczas rejestracji w Azure Active Directory B2C (Azure AD B2C). Otrzymasz miasto użytkowników, skonfigurujesz je jako listę rozwijaną i określisz, czy jest wymagane.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="add-user-attributes-your-user-flow"></a>Dodawanie atrybutów użytkownika do przepływu użytkownika

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Wybierz zasady (na przykład "B2C_1_SignupSignin"), aby je otworzyć.
1. Wybierz pozycję **atrybuty użytkownika** , a następnie wybierz atrybut użytkownika (na przykład "miasto"). 
1. Wybierz pozycję **Zapisz**.

## <a name="provide-optional-claims-to-your-app"></a>Podaj opcjonalne oświadczenia do aplikacji

Oświadczenia aplikacji to wartości, które są zwracane do aplikacji. Zaktualizuj przepływ użytkownika w taki sposób, aby zawierał odpowiednie oświadczenia.

1. Wybierz zasady (na przykład "B2C_1_SignupSignin"), aby je otworzyć.
1. Wybierz pozycję **Oświadczenia aplikacji**.
1. Wybierz atrybuty, które mają zostać wysłane z powrotem do aplikacji (na przykład "miasto").
1. Wybierz pozycję **Zapisz**.
 
## <a name="configure-user-attributes-input-type"></a>Konfigurowanie typu danych wejściowych atrybutów użytkownika

1. Wybierz zasady (na przykład "B2C_1_SignupSignin"), aby je otworzyć.
1. Wybierz pozycję **układy strony**.
1. Wybierz **stronę rejestracji konta lokalnego**.
1. W obszarze **atrybuty użytkownika** wybierz pozycję **miasto**.
    1. Z listy rozwijanej **Typ danych wejściowych użytkownika** wybierz pozycję **DropdownSingleSelect**.
    1. Z listy rozwijanej **opcjonalne** wybierz pozycję **nie**.
1. Wybierz pozycję **Zapisz**. 

### <a name="provide-a-list-of-values-by-using-localized-collections"></a>Podaj listę wartości przy użyciu zlokalizowanych kolekcji

Aby podać listę wartości dla atrybutu miasto: 

1. [Włącz Dostosowywanie języka w przepływie użytkownika](language-customization.md#support-requested-languages-for-ui_locales)
1. Wybierz zasady (na przykład "B2C_1_SignupSignin"), aby je otworzyć.
1. Na stronie **Języki** dla przepływu użytkownika wybierz język, który chcesz dostosować.
1. W obszarze **pliki zasobów na poziomie strony** wybierz pozycję **rejestracja konta lokalnego**.
1. Wybierz pozycję **Pobierz domyślne** (lub **Pobierz zastąpienia** , jeśli wcześniej edytowano ten język).
1. Utwórz `LocalizedCollections` atrybut.

`LocalizedCollections`Jest tablicą `Name` i `Value` par. Kolejność dla elementów będzie kolejność, w jakiej są wyświetlane. 

* `ElementId` jest atrybutem użytkownika, do którego ten `LocalizedCollections` atrybut jest odpowiedzią.
* `Name` jest wartością pokazywaną użytkownikowi.
* `Value` to co jest zwracane w ramach żądania, gdy ta opcja jest zaznaczona.

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType": "ClaimType",
      "ElementId": "city",
      "TargetCollection": "Restriction",
      "Override": false,
      "Items": [
        {
          "Name": "Berlin",
          "Value": "Berlin"
        },
        {
          "Name": "London",
          "Value": "London"
        },
        {
          "Name": "Seattle",
          "Value": "Seattle"
        }
      ]
    }
  ]
}
```

#### <a name="upload-your-changes"></a>Przekaż zmiany

1. Po wprowadzeniu zmian w pliku JSON Wróć do swojej dzierżawy B2C.
1. Wybierz pozycję **przepływy użytkownika** i wybierz zasady (na przykład "B2C_1_SignupSignin"), aby je otworzyć.
1. Wybierz **Języki**.
1. Wybierz język, w którym chcesz przeprowadzić tłumaczenie.
1. Wybierz **stronę rejestracji konta lokalnego**.
1. Wybierz ikonę folderu i wybierz plik JSON do przekazania. Zmiany są zapisywane automatycznie w przepływie użytkownika.

## <a name="test-your-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz zasady (na przykład "B2C_1_SignupSignin"), aby je otworzyć.
1. Aby przetestować zasady, wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *testapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij pozycję **Uruchom przepływ użytkownika**

::: zone-end

::: zone pivot="b2c-custom-policy"

> [!NOTE]
> Ten przykład używa wbudowanego "miasto". Zamiast tego można wybrać jeden z obsługiwanych [Azure AD B2C atrybuty wbudowane](user-profile-attributes.md) lub atrybut niestandardowy. Aby użyć atrybutu niestandardowego, [Włącz atrybuty niestandardowe](user-flow-custom-attributes.md). Aby użyć innego wbudowanego lub niestandardowego atrybutu, Zastąp wartość "miasto" wybranym atrybutem, na przykład wbudowany atrybut *stanowiska* lub atrybut niestandardowy, taki jak *extension_loyaltyId*.  

Możesz zbierać dane początkowe od użytkowników przy użyciu podróży lub logowania użytkownika. Dodatkowe oświadczenia można zbierać później za pomocą profilu Edytuj podróż użytkownika. W każdej chwili Azure AD B2C gromadzić informacje bezpośrednio od użytkownika, a platforma obsługi tożsamości używa swojego własnego [profilu technicznego](self-asserted-technical-profile.md). W tym przykładzie:

1. Zdefiniuj "miasto". 
1. Poproszenie użytkownika o ich miasto.
1. Utrwalaj miasto w profilu użytkownika w katalogu Azure AD B2C.
1. Przeczytaj wierzytelność z katalogu Azure AD B2C przy każdym logowaniu.
1. Zwróć miasto do aplikacji jednostki uzależnionej po zalogowaniu się lub zarejestrowaniu.  

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
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
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
  <!-- 
  </ClaimsSchema>
</BuildingBlocks>-->
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
```

Aby zebrać wierzytelność po początkowym logowaniu przy użyciu konta federacyjnego, należy dodać je jako zgłoszenie wyjściowe do `SelfAsserted-Social` profilu technicznego. Aby użytkownicy konta lokalnego i federacyjnego mogli edytować swoje dane profilu później, Dodaj do profilu technicznego oświadczenia wejściowe i wyjściowe `SelfAsserted-ProfileUpdate` . Zastąp te profile techniczne w pliku rozszerzenia. Określ całą listę oświadczeń danych wyjściowych do kontrolowania kolejności, w której oświadczenia są prezentowane na ekranie. Znajdź element **ClaimsProviders** . Dodaj nową ClaimsProviders w następujący sposób:

```xml
<ClaimsProvider>
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
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
    <!-- Read data after user resets the password. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
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

::: zone-end

Ekran rejestracji powinien wyglądać podobnie do poniższego zrzutu ekranu:

![Zrzut ekranu przedstawiający zmodyfikowaną opcję tworzenia konta](./media/configure-user-input/signup-with-city-claim-drop-down-example.png)

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

::: zone pivot="b2c-custom-policy"

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o elemencie [ClaimsSchema](claimsschema.md) w odwołaniu IEF.
- Dowiedz się, jak [używać atrybutów niestandardowych w Azure AD B2C](user-flow-custom-attributes.md).

::: zone-end
