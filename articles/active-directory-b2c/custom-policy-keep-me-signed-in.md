---
title: Nie wylogowuj mnie w Azure Active Directory B2C
description: Dowiedz się, jak skonfigurować rejestrowanie nadal zalogowane (KMSI) w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: de5dd051804f3a0a7d1b0d32b998262af13e8926
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85389194"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Włącz opcję Nie wylogowuj mnie (KMSI) w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Możesz włączyć funkcje Zachowaj zalogowany (KMSI) dla użytkowników sieci Web i aplikacji natywnych, które mają konta lokalne w katalogu Azure Active Directory B2C (Azure AD B2C). Ta funkcja udziela dostępu do użytkowników, którzy powracają do aplikacji bez monitowania o wprowadzenie nazwy użytkownika i hasła. Ten dostęp jest odwoływany po wylogowaniu się użytkownika.

Użytkownicy nie powinni włączać tej opcji na komputerach publicznych.

![Przykładowa strona logowania z zalogowaniem, która wyświetla pole wyboru nie wylogowuj mnie](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Wymagania wstępne

- Dzierżawa Azure AD B2C skonfigurowana do zezwalania na logowanie przy użyciu konta lokalnego. KMSI nie jest obsługiwane dla kont zewnętrznych dostawców tożsamości.
- Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych](custom-policy-get-started.md).

## <a name="configure-the-page-identifier"></a>Skonfiguruj identyfikator strony

Aby włączyć KMSI, Ustaw element definicji zawartości `DataUri` na [Identyfikator strony](contentdefinitions.md#datauri) `unifiedssp` i w [wersji strony](page-layout.md) *1.1.0* lub nowszej.

1. Otwórz plik rozszerzenia zasad. Na przykład <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> . Ten plik rozszerzenia jest jednym z plików zasad uwzględnionych w pakiecie początkowym zasad niestandardowych, który powinien zostać uzyskany w wymaganiu wstępnym, [Rozpocznij od zasad niestandardowych](custom-policy-get-started.md).
1. Wyszukaj element **BuildingBlocks** . Jeśli element nie istnieje, Dodaj go.
1. Dodaj element **ContentDefinitions** do elementu **BuildingBlocks** zasad.

    Zasady niestandardowe powinny wyglądać podobnie do następującego fragmentu kodu:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Dodawanie metadanych do profilu technicznego z własnym potwierdzeniem

Aby dodać pole wyboru KMSI do strony rejestracji i logowania, ustaw `setting.enableRememberMe` dla metadanych wartość true. Zastąp profile techniczne SelfAsserted-LocalAccountSignin-email w pliku rozszerzenia.

1. Znajdź element ClaimsProviders. Jeśli element nie istnieje, Dodaj go.
1. Dodaj następującego dostawcę oświadczeń do elementu ClaimsProviders:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Zapisz plik rozszerzeń.

## <a name="configure-a-relying-party-file"></a>Konfigurowanie pliku jednostki uzależnionej

Zaktualizuj plik jednostki uzależnionej (RP), który inicjuje utworzoną przez Ciebie podróż użytkownika.

1. Otwórz niestandardowy plik zasad. Na przykład *SignUpOrSignin.xml*.
1. Jeśli jeszcze nie istnieje, Dodaj `<UserJourneyBehaviors>` węzeł podrzędny do `<RelyingParty>` węzła. Musi ona znajdować się bezpośrednio po `<DefaultUserJourney ReferenceId="User journey Id" />` , na przykład: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` .
1. Dodaj następujący węzeł jako element podrzędny `<UserJourneyBehaviors>` elementu.

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** — wskazuje, jak sesja jest rozszerzona o czas określony w `SessionExpiryInSeconds` i  `KeepAliveInDays` . `Rolling`Wartość (domyślnie) wskazuje, że sesja jest rozszerzana za każdym razem, gdy użytkownik wykonuje uwierzytelnianie. `Absolute`Wartość oznacza, że użytkownik jest zmuszony do ponownego uwierzytelnienia po upływie określonego czasu.

    - **SessionExpiryInSeconds**  — okres istnienia plików cookie sesji, gdy nie włączono *logowania* , nie jest włączony lub jeśli użytkownik nie wybierze opcji nie *wylogowuj mnie*. Sesja wygaśnie po upływie `SessionExpiryInSeconds` Zakończono lub zamknięto przeglądarkę.

    - **KeepAliveInDays** — okres istnienia plików cookie sesji, gdy jest włączone *Logowanie* , a użytkownik wybierze opcję Nie wylogowuj *mnie*.  Wartość `KeepAliveInDays` ma pierwszeństwo przed `SessionExpiryInSeconds` wartością i określa czas wygaśnięcia sesji. Jeśli użytkownik zamknie przeglądarkę i ponownie otworzy ją później, nadal może się zalogować w trybie dyskretnym, o ile jest w okresie KeepAliveInDays czasu.

    Aby uzyskać więcej informacji, zobacz [zachowań podróży użytkownika](relyingparty.md#userjourneybehaviors).

Firma Microsoft zaleca, aby wartość SessionExpiryInSeconds była krótszym okresem (1200 sekund), podczas gdy wartość KeepAliveInDays może być ustawiona na stosunkowo długi okres (30 dni), jak pokazano w następującym przykładzie:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-your-policy"></a>Testowanie zasad

1. Zapisz zmiany, a następnie Przekaż plik.
1. Aby przetestować przekazane zasady niestandardowe, w Azure Portal przejdź do strony zasady, a następnie wybierz pozycję **Uruchom teraz**.
1. Wpisz **nazwę użytkownika** i **hasło**, wybierz opcję nie **wylogowuj mnie**, a następnie kliknij przycisk **Zaloguj**.
1. Wróć do witryny Azure Portal. Przejdź do strony zasady, a następnie wybierz pozycję **Kopiuj** , aby skopiować adres URL logowania.
1. Na pasku adresu przeglądarki usuń `&prompt=login` parametr ciągu zapytania, który wymusza, aby użytkownik wprowadził swoje poświadczenia dla tego żądania.
1. W przeglądarce kliknij pozycję **Przejdź**. Teraz Azure AD B2C wystawia token dostępu bez monitowania o ponowne zalogowanie. 

## <a name="next-steps"></a>Następne kroki

[Tutaj](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)znajdziesz przykładowe zasady.
