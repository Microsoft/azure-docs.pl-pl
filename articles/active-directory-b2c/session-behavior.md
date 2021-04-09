---
title: Skonfiguruj zachowanie sesji — Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się, jak skonfigurować zachowanie sesji w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3a3cdb93ee4cbf4a2e15540b9daf78b6c231d393
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579743"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Konfigurowanie zachowania sesji w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Logowanie jednokrotne (SSO) zwiększa bezpieczeństwo i wygodę, gdy użytkownicy logują się w aplikacjach w Azure Active Directory B2C (Azure AD B2C). W tym artykule opisano metody logowania jednokrotnego używane w Azure AD B2C i ułatwiają wybranie najbardziej odpowiedniej metody rejestracji jednokrotnej podczas konfigurowania zasad.

Logowanie jednokrotne umożliwia użytkownikom logowanie jednokrotne przy użyciu jednego konta i uzyskiwanie dostępu do wielu aplikacji. Aplikacja może być aplikacją sieci Web, mobilną lub jednostronicową, niezależnie od nazwy platformy lub domeny.

Gdy użytkownik po raz pierwszy zaloguje się do aplikacji, Azure AD B2C utrzymuje sesję opartą na plikach cookie. Po kolejnych żądaniach uwierzytelniania Azure AD B2C odczytuje i sprawdza poprawność sesji opartej na plikach cookie i wystawia token dostępu bez monitowania użytkownika o ponowne zalogowanie. Jeśli sesja oparta na plikach cookie wygasa lub jest nieprawidłowa, użytkownik zostanie poproszony o ponowne zalogowanie.  

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="azure-ad-b2c-session-overview"></a>Przegląd sesji Azure AD B2C

Integracja z Azure AD B2C obejmuje trzy typy sesji rejestracji jednokrotnej:

- **Azure AD B2C** — sesja zarządzana przez Azure AD B2C
- **Federacyjny dostawca tożsamości** — sesja zarządzana przez dostawcę tożsamości, na przykład Facebook, Salesforce lub konto Microsoft
- **Aplikacja** — sesja zarządzana przez aplikację sieci Web, aplikacji mobilnej lub jednostronicowej

![Sesja logowania jednokrotnego](media/session-behavior/sso-session-types.png)

### <a name="azure-ad-b2c-session"></a>Sesja Azure AD B2C 

W przypadku pomyślnego uwierzytelnienia użytkownika przy użyciu konta lokalnego lub społecznościowego Azure AD B2C w przeglądarce użytkownika jest przechowywana sesja oparta na plikach cookie. Plik cookie jest przechowywany w Azure AD B2C nazwy domeny dzierżawcy, takiej jak `https://contoso.b2clogin.com` .

Jeśli użytkownik początkowo zaloguje się przy użyciu konta federacyjnego, a następnie w oknie czasu sesji (czas wygaśnięcia lub TTL) zaloguje się do tej samej aplikacji lub innej aplikacji, Azure AD B2C próbuje uzyskać nowy token dostępu od dostawcy tożsamości federacyjnych. Jeśli sesja federacyjnego dostawcy tożsamości wygasła lub jest nieprawidłowa, dostawca tożsamości federacyjnych poprosi użytkownika o ich poświadczenia. Jeśli sesja jest nadal aktywna (lub jeśli użytkownik zalogował się przy użyciu konta lokalnego zamiast konta federacyjnego), Azure AD B2C autoryzuje użytkownika i eliminuje dalsze monity.

Można skonfigurować zachowanie sesji, w tym czas wygaśnięcia sesji i sposób, w jaki Azure AD B2C współużytkują sesję dla zasad i aplikacji.

### <a name="federated-identity-provider-session"></a>Sesja federacyjnego dostawcy tożsamości

Dostawca tożsamości społecznościowej lub korporacyjnej zarządza własną sesją. Plik cookie jest przechowywany w nazwie domeny dostawcy tożsamości, na przykład `https://login.salesforce.com` . Azure AD B2C nie steruje sesją federacyjnego dostawcy tożsamości. Zamiast tego zachowanie sesji jest określane przez dostawcę tożsamości federacyjnych. 

Poniżej przedstawiono przykładowy scenariusz:

1. Użytkownik loguje się do usługi Facebook, aby sprawdzić ich źródło.
2. Później użytkownik otwiera aplikację i uruchamia proces logowania. Aplikacja przekierowuje użytkownika do Azure AD B2C w celu ukończenia procesu logowania.
3. Na stronie Rejestracja w Azure AD B2C lub logowanie użytkownik zdecyduje się na zalogowanie się przy użyciu konta w serwisie Facebook. Użytkownik zostanie przekierowany do serwisu Facebook. W przypadku aktywnej sesji w serwisie Facebook użytkownik nie jest monitowany o podanie poświadczeń i zostaje natychmiast przekierowany do Azure AD B2C przy użyciu tokenu w usłudze Facebook.

### <a name="application-session"></a>Sesja aplikacji

Aplikacja sieci Web, mobilna lub jednostronicowa może być chroniona przez dostęp OAuth, tokeny identyfikatorów lub tokeny SAML. Gdy użytkownik próbuje uzyskać dostęp do chronionego zasobu w aplikacji, aplikacja sprawdzi, czy po stronie aplikacji znajduje się aktywna sesja. Jeśli nie ma żadnej sesji aplikacji lub sesja wygasła, aplikacja przeniesie użytkownika do Azure AD B2C stronie logowania.

Sesja aplikacji może być sesją plików cookie przechowywaną w obszarze Nazwa domeny aplikacji, na przykład `https://contoso.com` . Aplikacje mobilne mogą przechowywać sesję w inny sposób, ale przy użyciu podobnego podejścia.

## <a name="configure-azure-ad-b2c-session-behavior"></a>Skonfiguruj zachowanie sesji Azure AD B2C

Można skonfigurować zachowanie sesji Azure AD B2C, w tym:

- **Okres istnienia sesji aplikacji sieci Web (w minutach)** — czas, przez jaki plik cookie sesji Azure AD B2C jest przechowywany w przeglądarce użytkownika po pomyślnym uwierzytelnieniu. Okres istnienia sesji można ustawić maksymalnie przez 24 godziny.

- **Limit czasu sesji aplikacji sieci Web** — wskazuje, w jaki sposób sesja jest rozszerzona za pomocą ustawienia okresu istnienia sesji lub ustawienia nie wylogowuj mnie (KMSI).
  - **Krocząca** — wskazuje, że sesja jest rozszerzana za każdym razem, gdy użytkownik wykonuje uwierzytelnianie na podstawie plików cookie (domyślnie).
  - **Bezwzględna** — wskazuje, że użytkownik jest zmuszony do ponownego uwierzytelnienia po upływie określonego czasu.

- **Konfiguracja logowania** jednokrotnego — sesja Azure AD B2C można skonfigurować przy użyciu następujących zakresów:
  - **Dzierżawca** — to ustawienie jest domyślne. Użycie tego ustawienia umożliwia wielu aplikacjom i przepływom użytkowników w dzierżawie B2C współużytkowanie tej samej sesji użytkownika. Na przykład, gdy użytkownik loguje się do aplikacji, użytkownik może również bezproblemowo zalogować się do innego, aby uzyskać do niego dostęp.
  - **Aplikacja** — to ustawienie umożliwia obsługę sesji użytkownika wyłącznie dla aplikacji, niezależnie od innych aplikacji. Można na przykład użyć tego ustawienia, jeśli chcesz, aby użytkownik zalogował się do usługi contoso apteki, niezależnie od tego, czy użytkownik jest już zalogowany do artykułów spożywczych firmy Contoso.
  - **Zasady** — to ustawienie umożliwia obsługę sesji użytkownika wyłącznie dla przepływu użytkownika niezależnie od aplikacji, z których korzysta. Na przykład jeśli użytkownik został już zalogowany i ukończył krok uwierzytelniania wieloskładnikowego (MFA), użytkownik może uzyskać dostęp do części o wyższym poziomie zabezpieczeń wielu aplikacji, o ile sesja związana z przepływem użytkownika nie wygasa.
  - **Pominięte** — to ustawienie wymusza, aby użytkownik był uruchamiany przez cały przepływ użytkownika przy każdym wykonaniu zasad.
- Nie **wylogowuj mnie (KMSI)** — rozszerza okres istnienia sesji przy użyciu trwałego pliku cookie. Jeśli ta funkcja jest włączona, a użytkownik wybierze ją, sesja pozostaje aktywna nawet po zamknięciu i ponownym otwarciu przeglądarki przez użytkownika. Sesja jest odwołana tylko wtedy, gdy użytkownik się wyloguje. Funkcja KMSI ma zastosowanie tylko do logowania z kontami lokalnymi. Funkcja KMSI ma pierwszeństwo przed okresem istnienia sesji.

::: zone pivot="b2c-user-flow"

Aby skonfigurować zachowanie sesji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając w górnym menu pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawcę Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **przepływy użytkownika**.
1. Otwórz wcześniej utworzony przepływ użytkownika.
1. Wybierz pozycję **Właściwości**.
1. Skonfiguruj **okres istnienia sesji aplikacji sieci Web (minuty)**, **limit czasu sesji aplikacji sieci Web**, **konfigurację logowania** jednokrotnego i **Wymagaj tokenu identyfikatora w żądaniach wylogowywania** zgodnie z potrzebami.
1. Kliknij pozycję **Zapisz**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Aby zmienić zachowanie sesji i konfiguracje rejestracji jednokrotnej, należy dodać element **UserJourneyBehaviors** wewnątrz elementu [RelyingParty](relyingparty.md) .  Element **UserJourneyBehaviors** musi występować bezpośrednio po **DefaultUserJourney**. Element **UserJourneyBehavors** powinien wyglądać podobnie do tego przykładu:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
::: zone-end

## <a name="enable-keep-me-signed-in-kmsi"></a>Włącz opcję Nie wylogowuj mnie (KMSI)

Funkcję KMSI można włączyć dla użytkowników sieci Web i aplikacji natywnych, którzy mają konta lokalne w katalogu Azure AD B2C. Po włączeniu tej funkcji użytkownicy mogą zrezygnować z zalogowania się, aby sesja pozostawała aktywna po zamknięciu przeglądarki. Następnie mogą ponownie otworzyć przeglądarkę bez monitowania o ponowne wprowadzenie nazwy użytkownika i hasła. Ten dostęp jest odwoływany po wylogowaniu się użytkownika.

![Przykładowa strona logowania z zalogowaniem, która wyświetla pole wyboru nie wylogowuj mnie](./media/session-behavior/keep-me-signed-in.png)


::: zone pivot="b2c-user-flow"

KMSI można skonfigurować na poziomie przepływu poszczególnych użytkowników. Przed włączeniem KMSI dla przepływów użytkownika należy wziąć pod uwagę następujące kwestie:

- KMSI jest obsługiwana tylko dla **zalecanych** wersji rejestrowania i logowania (Susi), logowania i edytowania profilów użytkowników. Jeśli posiadasz obecnie **standardowe** lub **starsze wersje zapoznawcze-v2** tych przepływów użytkownika i chcesz włączyć KMSI, musisz utworzyć nowe, **zalecane** wersje tych przepływów użytkowników.
- KMSI nie jest obsługiwana w przypadku przepływów użytkownika resetowania hasła ani rejestrowania.
- Jeśli chcesz włączyć KMSI dla wszystkich aplikacji w swojej dzierżawie, zalecamy włączenie KMSI dla wszystkich przepływów użytkowników w dzierżawie. Ze względu na to, że użytkownik może być prezentowany przy użyciu wielu zasad w trakcie sesji, możliwe, że może napotkać taki, który nie ma włączonej KMSI, co spowoduje usunięcie pliku cookie KMSI z sesji.
- KMSI nie powinny być włączone na komputerach publicznych.

### <a name="configure-kmsi-for-a-user-flow"></a>Konfigurowanie KMSI dla przepływu użytkownika

Aby włączyć KMSI dla przepływu użytkownika:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C.  ****   W górnym menu wybierz pozycję katalog i subskrypcja, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
3. Wybierz pozycję **wszystkie usługi**   w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **Azure AD B2C**.
4. Wybierz pozycję **przepływy użytkownika (zasady)**.
5. Otwórz wcześniej utworzony przepływ użytkownika.
6. Wybierz pozycję **Właściwości**.

7. W obszarze  **zachowanie sesji** zaznacz pole wyboru **Włącz logowanie nie wylogowuj mnie**. Obok pozycji nie **wylogowuj mnie (dni)** wprowadź wartość z przenoszącą od 1 do 90, aby określić liczbę dni, przez które sesja może pozostać otwarta.


   ![Włącz opcję Nie wylogowuj mnie](media/session-behavior/enable-keep-me-signed-in.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Użytkownicy nie powinni włączać tej opcji na komputerach publicznych.

### <a name="configure-the-page-identifier"></a>Skonfiguruj identyfikator strony

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

### <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Dodawanie metadanych do profilu technicznego z własnym potwierdzeniem

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

### <a name="configure-a-relying-party-file"></a>Konfigurowanie pliku jednostki uzależnionej

Zaktualizuj plik jednostki uzależnionej (RP), który inicjuje utworzoną przez Ciebie podróż użytkownika. Parametr keepAliveInDays umożliwia skonfigurowanie sposobu, w jaki długi plik cookie dotyczący rejestrowania (KMSI) powinien być trwały. Jeśli na przykład ustawisz wartość 30, plik cookie sesji KMSI będzie trwały przez 30 dni. Zakres dla wartości wynosi od 1 do 90 dni.

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

::: zone-end

## <a name="sign-out"></a>Wylogowywanie

Jeśli chcesz podpisać użytkownika poza aplikacją, nie wystarczy wyczyścić plików cookie aplikacji lub zakończyć sesję z użytkownikiem. Musisz przekierować użytkownika do Azure AD B2C, aby się wylogować. W przeciwnym razie użytkownik może być w stanie ponownie uwierzytelnić się w aplikacjach bez konieczności ponownego wprowadzania poświadczeń.

W przypadku żądania wylogowania Azure AD B2C:

1. Unieważnia Azure AD B2C sesji opartej na plikach cookie.
::: zone pivot="b2c-user-flow"
2. Próbuje wylogować się z dostawców tożsamości federacyjnych
::: zone-end
::: zone pivot="b2c-custom-policy"
3. Próbuje wylogować się z dostawców tożsamości federacyjnych:
   - OpenID Connect Connect — Jeśli dostawca tożsamości dobrze znanego punktu końcowego konfiguracji określa `end_session_endpoint` lokalizację. Żądanie wylogowania nie przeszedł do `id_token_hint` parametru. Jeśli federacyjny dostawca tożsamości wymaga tego parametru, żądanie wylogowania zakończy się niepowodzeniem.
   - OAuth2 — Jeśli [metadane dostawcy tożsamości](oauth2-technical-profile.md#metadata) zawierają `end_session_endpoint` lokalizację.
   - SAML — Jeśli [metadane dostawcy tożsamości](identity-provider-generic-saml.md) zawierają `SingleLogoutService` lokalizację.
4. Opcjonalnie możesz wylogować się z innych aplikacji. Aby uzyskać więcej informacji, zobacz sekcję [Logowanie](#single-sign-out) jednokrotne.

> [!NOTE]
> Można wyłączyć Wylogowywanie z dostawców tożsamości federacyjnych, ustawiając metadane profilu technicznego dostawcy tożsamości `SingleLogoutEnabled` `false` .
::: zone-end

Wylogowanie powoduje wyczyszczenie stanu logowania jednokrotnego użytkownika przy użyciu Azure AD B2C, ale może nie podpisać użytkownika z sesji dostawcy tożsamości społecznościowej. Jeśli użytkownik wybierze tego samego dostawcę tożsamości podczas kolejnego logowania, może ponownie uwierzytelnić się bez wprowadzania poświadczeń. Jeśli użytkownik chce wylogować się z aplikacji, nie musi to oznaczać, że chce się wylogować z konta w serwisie Facebook. Jeśli jednak są używane konta lokalne, sesja użytkownika zostanie zakończona prawidłowo.

::: zone pivot="b2c-custom-policy"

### <a name="single-sign-out"></a>Wylogowanie jednokrotne 

Po przekierowaniu użytkownika do punktu końcowego wylogowania Azure AD B2C (dla protokołów OAuth2 i SAML) Azure AD B2C czyści sesję użytkownika z przeglądarki. Jednak użytkownik może nadal być zalogowany do innych aplikacji, które używają Azure AD B2C do uwierzytelniania. Aby umożliwić tym aplikacjom jednokrotne podpisywanie użytkownika, Azure AD B2C wysyła żądanie HTTP GET do zarejestrowanych `LogoutUrl` wszystkich aplikacji, do których użytkownik jest aktualnie zalogowany.

Aplikacje muszą odpowiedzieć na to żądanie przez wyczyszczenie każdej sesji identyfikującej użytkownika i zwracającej `200` odpowiedź. Jeśli chcesz obsługiwać Logowanie jednokrotne w aplikacji, musisz zaimplementować `LogoutUrl` w kodzie aplikacji. 

Aby zapewnić obsługę logowania jednokrotnego, profile techniczne wystawców tokenów dla tokenu JWT i SAML muszą określać:

- Nazwa protokołu, taka jak `<Protocol Name="OpenIdConnect" />`
- Odwołanie do profilu technicznego sesji, na przykład `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

Poniższy przykład ilustruje wystawców tokenów JWT i SAML z logowaniem jednokrotnym:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

### <a name="secure-your-logout-redirect"></a>Zabezpiecz przekierowanie wylogowania

Po wylogowaniu użytkownik zostanie przekierowany na identyfikator URI określony w `post_logout_redirect_uri` parametrze, bez względu na adresy URL odpowiedzi określone dla aplikacji. Jeśli jednak zostanie `id_token_hint` przekierowany prawidłowy, a **token Wymagaj tokenu w żądaniach wylogowania** jest włączony, Azure AD B2C sprawdza, czy wartość jest `post_logout_redirect_uri` zgodna z skonfigurowanymi identyfikatorami URI przekierowań aplikacji przed przekazaniem. Jeśli nie skonfigurowano zgodnego adresu URL odpowiedzi dla aplikacji, zostanie wyświetlony komunikat o błędzie i użytkownik nie zostanie przekierowany. 

::: zone pivot="b2c-user-flow"

Aby wymagać tokenu identyfikatora w żądaniach wylogowania:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając w górnym menu pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawcę Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **przepływy użytkownika**.
1. Otwórz wcześniej utworzony przepływ użytkownika.
1. Wybierz pozycję **Właściwości**.
1. Włącz **token Wymagaj identyfikatora w żądaniach wylogowania**.
1. Wróć do  **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz aplikację.
1. Wybierz pozycję **Uwierzytelnianie**.
1. W polu tekstowym **adres URL wylogowywania** wpisz identyfikator URI przekierowania wylogowania, a następnie wybierz pozycję **Zapisz**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Aby wymagać tokenu identyfikatora w żądaniach wylogowania, Dodaj element **UserJourneyBehaviors** wewnątrz elementu [RelyingParty](relyingparty.md) . Następnie ustaw **EnforceIdTokenHintOnLogout** elementu **SingleSignon** na `true` . Element **UserJourneyBehaviors** powinien wyglądać podobnie do tego przykładu:

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

::: zone-end

Aby skonfigurować adres URL wylogowywania aplikacji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając w górnym menu pozycję **katalog i subskrypcja** , a następnie wybierz katalog zawierający dzierżawcę Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz aplikację.
1. Wybierz pozycję **Uwierzytelnianie**.
1. W polu tekstowym **adres URL wylogowywania** wpisz identyfikator URI przekierowania wylogowania, a następnie wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [skonfigurować tokeny w Azure AD B2C](configure-tokens.md).
