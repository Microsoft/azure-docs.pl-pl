---
title: Skonfiguruj Azure Active Directory B2C jako element SAML dostawcy tożsamości dla aplikacji
title-suffix: Azure Active Directory B2C
description: Jak skonfigurować Azure Active Directory B2C, aby zapewnić zatwierdzeń protokołu SAML dla aplikacji (usługodawców). Azure AD B2C będzie pełnić funkcję dostawcy pojedynczej tożsamości (dostawcy tożsamości) do aplikacji SAML.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1035f43642f3884e7cc0f6ab47e9c9afd1f29170
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107516"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Rejestrowanie aplikacji SAML w Azure AD B2C

W tym artykule dowiesz się, jak połączyć aplikacje SAML (SAML) (dostawcy usług) do Azure Active Directory B2C (Azure AD B2C) na potrzeby uwierzytelniania.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>Omówienie

Organizacje używające Azure AD B2C jako rozwiązania do zarządzania tożsamościami i dostępem klientów mogą wymagać integracji z aplikacjami, które uwierzytelniają się przy użyciu protokołu SAML. Na poniższym diagramie przedstawiono sposób, w jaki Azure AD B2C służy jako *dostawca tożsamości* (dostawcy tożsamości) w celu zapewnienia logowania jednokrotnego (SSO) z aplikacjami opartymi na protokole SAML.

![Diagram z B2C jako dostawca tożsamości po lewej i B2C jako dostawca usług po prawej stronie.](media/saml-service-provider/saml-service-provider-integration.png)

1. Aplikacja tworzy żądanie SAML AuthN wysyłane do punktu końcowego logowania do usługi Azure AD B2C's.
2. Użytkownik może użyć konta lokalnego Azure AD B2C lub innego dostawcy tożsamości federacyjnych (jeśli jest skonfigurowany) do uwierzytelniania.
3. Jeśli użytkownik zaloguje się za pomocą federacyjnego dostawcy tożsamości, do Azure AD B2C jest wysyłana odpowiedź dotycząca tokenu.
4. Azure AD B2C generuje potwierdzenie SAML i wysyła je do aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych w Azure AD B2C](custom-policy-get-started.md). Zasady niestandardowe *SocialAndLocalAccounts* są potrzebne w ramach pakietu zasad niestandardowych, który został omówiony w artykule.
* Podstawowa znajomość protokołu SAML i znajomość implementacji języka SAML aplikacji.
* Aplikacja sieci Web skonfigurowana jako aplikacja SAML. Na potrzeby tego samouczka możesz użyć [aplikacji testowej SAML][samltest] , którą udostępniamy.

## <a name="components"></a>Składniki

W tym scenariuszu są wymagane trzy główne składniki:

* **Aplikacja** SAML, która umożliwia wysyłanie żądań SAML AuthN oraz odbieranie, dekodowanie i weryfikowanie odpowiedzi saml z Azure AD B2C. Aplikacja SAML jest również znana jako aplikacja lub Dostawca usługi jednostki uzależnionej.
* Publicznie dostępny **punkt końcowy metadanych** SAML aplikacji SAML lub dokument XML.
* [Dzierżawa Azure AD B2C](tutorial-create-tenant.md)

Jeśli nie masz jeszcze aplikacji SAML i skojarzonego z nią punktu końcowego metadanych, możesz użyć tej przykładowej aplikacji SAML, która została udostępniona do testowania:

[Aplikacja testowa SAML][samltest]

## <a name="set-up-certificates"></a>Konfigurowanie certyfikatów

Aby utworzyć relację zaufania między aplikacją i Azure AD B2C, obie usługi muszą mieć możliwość tworzenia i weryfikowania podpisów innych. Skonfigurujesz certyfikaty x509 w Azure AD B2C i aplikację.

**Certyfikaty aplikacji**

| Użycie | Wymagane | Opis |
| --------- | -------- | ----------- |
| Podpisywanie żądania SAML  | Nie | Certyfikat z kluczem prywatnym przechowywanym w aplikacji sieci Web używanym przez aplikację do podpisywania żądań SAML wysyłanych do Azure AD B2C. Aplikacja sieci Web musi uwidocznić klucz publiczny za pomocą swojego punktu końcowego metadanych SAML. Azure AD B2C sprawdza poprawność podpisu żądania SAML przy użyciu klucza publicznego z metadanych aplikacji.|
| Szyfrowanie potwierdzenia SAML  | Nie | Certyfikat z kluczem prywatnym przechowywanym w aplikacji sieci Web. Aplikacja sieci Web musi uwidocznić klucz publiczny za pomocą swojego punktu końcowego metadanych SAML. Azure AD B2C może zaszyfrować potwierdzenia do aplikacji przy użyciu klucza publicznego. Aplikacja używa klucza prywatnego do odszyfrowania potwierdzenia.|

**Azure AD B2C certyfikaty**

| Użycie | Wymagane | Opis |
| --------- | -------- | ----------- |
| Podpisywanie odpowiedzi SAML | Tak | Certyfikat z kluczem prywatnym przechowywanym w Azure AD B2C. Ten certyfikat jest używany przez Azure AD B2C do podpisania odpowiedzi SAML wysyłanej do aplikacji. Aplikacja odczytuje klucz publiczny metadanych Azure AD B2C, aby zweryfikować podpis odpowiedzi SAML. |

W środowisku produkcyjnym zalecamy używanie certyfikatów wystawionych przez publiczny urząd certyfikacji. Można jednak również wykonać tę procedurę przy użyciu certyfikatów z podpisem własnym.

### <a name="prepare-a-self-signed-certificate-for-saml-response-signing"></a>Przygotuj certyfikat z podpisem własnym dla podpisywania odpowiedzi SAML

Należy utworzyć certyfikat podpisywania odpowiedzi SAML, aby aplikacja mogła ufać potwierdzeniu z Azure AD B2C.

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>Włączanie połączenia z aplikacją SAML przy użyciu zasad

Aby nawiązać połączenie z aplikacją SAML, Azure AD B2C musi mieć możliwość tworzenia odpowiedzi SAML.

Otwórz `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** w pakiecie zasad niestandardowych.

Znajdź `<ClaimsProviders>` sekcję i Dodaj następujący fragment kodu XML, aby zaimplementować generatora odpowiedzi SAML.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="configure-the-issueruri-of-the-saml-response"></a>Skonfiguruj IssuerUri odpowiedzi SAML

Można zmienić wartość `IssuerUri` elementu metadanych w profilu technicznym wystawcy tokenów SAML. Ta zmiana zostanie odzwierciedlona w `issuerUri` atrybucie zwracanym w odpowiedzi SAML z Azure AD B2C. Aplikacja powinna być skonfigurowana w taki sposób, aby akceptowała te same `issuerUri` podczas walidacji odpowiedzi SAML.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

#### <a name="sign-the-azure-ad-b2c-idp-saml-metadata-optional"></a>Podpisywanie metadanych SAML Azure AD B2C dostawcy tożsamości (opcjonalnie)

Możesz wydać Azure AD B2C, aby podpisać swój dokument metadanych SAML dostawcy tożsamości, jeśli jest to wymagane przez aplikację. W tym celu należy wygenerować i przekazać klucz zasad podpisywania metadanych SAML dostawcy tożsamości, jak pokazano w temacie [przygotowanie certyfikatu z podpisem własnym na potrzeby podpisywania odpowiedzi SAML](#prepare-a-self-signed-certificate-for-saml-response-signing). Następnie skonfiguruj `MetadataSigning` element metadanych w profilu technicznym wystawcy tokenów SAML. `StorageReferenceId`Musi odwoływać się do nazwy klucza zasad.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlMetadataCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

#### <a name="sign-the-azure-ad-b2c-idp-saml-response-element-optional"></a>Podpisywanie Azure AD B2C dostawcy tożsamości element odpowiedzi SAML (opcjonalnie)

Możesz określić certyfikat, który ma być używany do podpisywania komunikatów SAML. Komunikat jest `<samlp:Response>` elementem w odpowiedzi SAML wysyłanym do aplikacji.

Aby określić certyfikat, wygeneruj i przekaż klucz zasad, jak pokazano w temacie [przygotowanie certyfikatu z podpisem własnym dla podpisywania odpowiedzi SAML](#prepare-a-self-signed-certificate-for-saml-response-signing). Następnie skonfiguruj `SamlMessageSigning` element metadanych w profilu technicznym wystawcy tokenów SAML. `StorageReferenceId`Musi odwoływać się do nazwy klucza zasad.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```
## <a name="configure-your-policy-to-issue-a-saml-response"></a>Konfigurowanie zasad w celu wystawiania odpowiedzi SAML

Teraz, gdy zasady mogą tworzyć odpowiedzi SAML, należy skonfigurować zasady w celu wydawania odpowiedzi SAML zamiast domyślnej odpowiedzi JWT do aplikacji.

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>Tworzenie zasad rejestracji lub logowania skonfigurowanych dla protokołu SAML

1. Utwórz kopię pliku *SignUpOrSignin.xml* w katalogu roboczym pakietu Start Pack i Zapisz go przy użyciu nowej nazwy. Na przykład *SignUpOrSigninSAML.xml*. Ten plik jest plikiem zasad jednostki uzależnionej i jest domyślnie skonfigurowany do wystawiania odpowiedzi JWT.

1. Otwórz plik *SignUpOrSigninSAML.xml* w preferowanym edytorze.

1. Zmień wartości `PolicyId` i `PublicPolicyUri` zasad na _B2C_1A_signup_signin_saml_ , `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml` jak pokazano poniżej.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Na koniec podróży użytkownika Azure AD B2C zawiera `SendClaims` krok. Ten krok odwołuje się do profilu technicznego Wystawca tokenu. Aby wysłać odpowiedź SAML zamiast domyślnej odpowiedzi JWT, należy zmodyfikować `SendClaims` krok, aby odwołać się do nowego profilu technicznego wystawcy tokenów SAML `Saml2AssertionIssuer` .

Dodaj następujący fragment kodu XML tuż przed `<RelyingParty>` elementem. Ten kod XML zastępuje krok aranżacji numer 7 w podróży użytkownika _SignUpOrSignIn_ . Jeśli rozpoczęto od innego folderu w pakiecie startowym lub dostosowano drogę użytkownika przez dodanie lub usunięcie kroków aranżacji, upewnij się, że liczba w `order` elemencie odpowiada liczbie określonej w podróży użytkownika dla kroku wystawcy token. Na przykład w pozostałych folderach pakietu startowego odpowiednia liczba kroków to 4 dla `LocalAccounts` , 6 dla `SocialAccounts` i 9 dla `SocialAndLocalAccountsWithMfa` ).

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

Element jednostki uzależnionej określa protokół używany przez aplikację. Wartość domyślna to `OpenId`. `Protocol`Należy zmienić element na `SAML` . Oświadczenia wyjściowe spowodują utworzenie mapowania oświadczeń dla potwierdzenia SAML.

Zastąp cały `<TechnicalProfile>` element w `<RelyingParty>` elemencie następującym kodem XML profilu technicznego. Zaktualizuj `tenant-name` o nazwę dzierżawy Azure AD B2C.

```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
```

Końcowy plik zasad jednostki uzależnionej powinien wyglądać podobnie do następującego kodu XML:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

> [!NOTE]
> Ten sam proces można wykonać w celu zaimplementowania innych typów przepływów użytkowników (na przykład logowania, resetowania hasła lub przepływów edycji profilu).

### <a name="upload-your-policy"></a>Przekazywanie zasad

Zapisz zmiany i przekaż nowe **TrustFrameworkExtensions.xml** i **SignUpOrSigninSAML.xml** plików zasad do Azure Portal.

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>Testowanie metadanych SAML Azure AD B2C dostawcy tożsamości

Po przekazaniu plików zasad Azure AD B2C korzysta z informacji o konfiguracji w celu wygenerowania dokumentu metadanych SAML dostawcy tożsamości, który będzie używany przez aplikację. Dokument metadanych SAML zawiera lokalizacje usług, takich jak logowanie i metody wylogowywania, certyfikaty itd.

Metadane zasad Azure AD B2C są dostępne pod następującym adresem URL:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

Zamień na `<tenant-name>` nazwę dzierżawy Azure AD B2C i `<policy-name>` nazwę (identyfikator) zasad, na przykład:

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>Zarejestruj aplikację SAML w Azure AD B2C

Aby uzyskać Azure AD B2C zaufania do aplikacji, należy utworzyć Azure AD B2C rejestracji aplikacji, która zawiera informacje o konfiguracji, takie jak punkt końcowy metadanych aplikacji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *SAMLApp1*.
1. W obszarze **obsługiwane typy kont** wybierz opcję **konta tylko w tym katalogu organizacji**.
1. W obszarze **Identyfikator URI przekierowania** wybierz pozycję **Sieć Web**, a następnie wprowadź `https://localhost` . Ta wartość zostanie zmodyfikowana później w manifeście rejestracji aplikacji.
1. Wybierz pozycję **Zarejestruj**.

### <a name="configure-your-application-in-azure-ad-b2c"></a>Skonfiguruj aplikację w Azure AD B2C

W przypadku aplikacji SAML należy skonfigurować kilka właściwości w manifeście rejestracji aplikacji.

1. W [Azure Portal](https://portal.azure.com)przejdź do rejestracji aplikacji utworzonej w poprzedniej sekcji.
1. W obszarze **Zarządzaj** wybierz pozycję **manifest** , aby otworzyć Edytor manifestu, a następnie zmodyfikuj właściwości opisane w poniższych sekcjach.

#### <a name="add-the-identifier"></a>Dodaj identyfikator

Gdy aplikacja SAML wysyła żądanie do Azure AD B2C, żądanie SAML AuthN zawiera `Issuer` atrybut, który zazwyczaj jest taka sama jak wartość metadanych aplikacji `entityID` . Azure AD B2C używa tej wartości, aby wyszukać rejestrację aplikacji w katalogu i odczytać konfigurację. Aby to wyszukiwanie powiodło się, `identifierUri` w rejestracji aplikacji musi zostać wypełniona wartość, która pasuje do `Issuer` atrybutu.

W manifeście rejestracji Znajdź `identifierURIs` parametr i Dodaj odpowiednią wartość. Ta wartość będzie taka sama jak wartość skonfigurowana w żądaniach SAML AuthN dla EntityId w aplikacji oraz `entityID` wartość w metadanych aplikacji.

Poniższy przykład przedstawia `entityID` w metadanych SAML:

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

`identifierUris`Właściwość akceptuje tylko adresy URL w domenie `tenant-name.onmicrosoft.com` .

```json
"identifierUris":"https://samltestapp2.azurewebsites.net",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>Udostępnianie metadanych aplikacji za pomocą Azure AD B2C

Po załadowaniu aplikacji przez `identifierUri` program Azure AD B2C używa metadanych aplikacji do walidacji żądania SAML AuthN i określania sposobu reagowania.

Zaleca się, aby aplikacja uwidacznia dostęp do dostępnego publicznie punktu końcowego metadanych.

Jeśli istnieją właściwości określone *zarówno* w adresie URL metadanych SAML, jak i w manifeście rejestracji aplikacji, zostaną one *scalone*. Właściwości określone w adresie URL metadanych są przetwarzane jako pierwsze i mają pierwszeństwo.

Użycie aplikacji testowej SAML jako przykładu w manifeście aplikacji należy użyć następującej wartości `samlMetadataUrl` :

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>Przesłoń lub ustaw adres URL odbiorcy potwierdzenia (opcjonalnie)

Można skonfigurować adres URL odpowiedzi, na który Azure AD B2C wysyła odpowiedzi SAML. Adresy URL odpowiedzi można skonfigurować w manifeście aplikacji. Ta konfiguracja jest przydatna, gdy aplikacja nie ujawnia publicznie dostępnego punktu końcowego metadanych.

Adres URL odpowiedzi dla aplikacji SAML to punkt końcowy, w którym aplikacja oczekuje odpowiedzi SAML. Aplikacja zwykle udostępnia ten adres URL w dokumencie metadanych pod `AssertionConsumerServiceUrl` atrybut, jak pokazano poniżej:

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

Jeśli chcesz zastąpić metadane podane w `AssertionConsumerServiceUrl` atrybucie lub adres URL nie znajduje się w dokumencie metadanych, możesz skonfigurować adres URL w manifeście pod `replyUrlsWithType` właściwością. `BindingType`Zostanie ustawiony na `HTTP POST` .

Przy użyciu aplikacji testowej SAML jako przykładu należy ustawić `url` Właściwość `replyUrlsWithType` na wartość pokazaną w poniższym FRAGMENCIE kodu JSON.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>Przesłoń lub ustaw adres URL wylogowywania (opcjonalnie)

Można skonfigurować adres URL wylogowania, na który Azure AD B2C wyśle użytkownika po otrzymaniu żądania wylogowania. Adresy URL odpowiedzi można skonfigurować w manifeście aplikacji.

Jeśli chcesz zastąpić metadane podane w `SingleLogoutService` atrybucie lub adres URL nie znajduje się w dokumencie metadanych, możesz go skonfigurować w manifeście pod `Logout` właściwością. `BindingType`Zostanie ustawiony na `Http-Redirect` .

Aplikacja zwykle udostępnia ten adres URL w dokumencie metadanych pod `AssertionConsumerServiceUrl` atrybut, jak pokazano poniżej:

```xml
<IDPSSODescriptor WantAuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</IDPSSODescriptor>
```

Korzystając z aplikacji testowej SAML jako przykładu, pozostaw `logoutUrl` ustawiony na `https://samltestapp2.azurewebsites.net/logout` :

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> Jeśli zdecydujesz się skonfigurować adres URL odpowiedzi i adres URL wylogowywania w manifeście aplikacji bez wypełniania punktu końcowego metadanych aplikacji za pomocą `samlMetadataUrl` właściwości, Azure AD B2C nie zweryfikuje podpisu żądania SAML ani nie zaszyfruje odpowiedzi SAML.

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>Konfigurowanie Azure AD B2C jako elementu SAML dostawcy tożsamości w aplikacji SAML

Ostatnim krokiem jest włączenie Azure AD B2C jako elementu SAML dostawcy tożsamości w aplikacji SAML. Poszczególne aplikacje różnią się od siebie. Aby uzyskać szczegółowe informacje, zajrzyj do dokumentacji aplikacji.

Metadane można skonfigurować w aplikacji jako *metadane statyczne* lub *metadane dynamiczne*. W trybie statycznym Skopiuj wszystkie lub części metadanych z metadanych zasad Azure AD B2C. W obszarze Tryb dynamiczny Podaj adres URL metadanych i zezwól aplikacji na dynamiczne odczytywanie metadanych.

Niektóre lub wszystkie następujące elementy są zwykle wymagane:

* **Metadane**: Użyj formatu `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata` .
* **Wystawca**: wartość żądania SAML `issuer` musi być zgodna z jednym z identyfikatorów URI skonfigurowanych w `identifierUris` elemencie manifestu rejestracji aplikacji. Jeśli nazwa żądania SAML `issuer` nie istnieje w `identifierUris` elemencie, [Dodaj ją do manifestu rejestracji aplikacji](#add-the-identifier). Na przykład `https://contoso.onmicrosoft.com/app-name`. 
* **Adres URL logowania/punkt końcowy SAML/adres URL SAML**: Sprawdź wartość w Azure AD B2C pliku metadanych zasad SAML dla `<SingleSignOnService>` elementu XML.
* **Certyfikat**: ten certyfikat jest *B2C_1A_SamlIdpCert*, ale bez klucza prywatnego. Aby uzyskać klucz publiczny certyfikatu:

    1. Przejdź do adresu URL metadanych określonego powyżej.
    1. Skopiuj wartość z `<X509Certificate>` elementu.
    1. Wklej ją do pliku tekstowego.
    1. Zapisz plik tekstowy jako plik *CER* .

### <a name="test-with-the-saml-test-app"></a>Testowanie przy użyciu aplikacji testowej SAML

Aby przetestować konfigurację, możesz użyć naszej [aplikacji testowej SAML][samltest] :

* Zaktualizuj nazwę dzierżawy.
* Zaktualizuj nazwę zasad, na przykład *B2C_1A_signup_signin_saml*.
* Określ ten identyfikator URI wystawcy. Użyj jednego z identyfikatorów URI znalezionych w `identifierUris` elemencie w manifeście rejestracji aplikacji, na przykład `https://contoso.onmicrosoft.com/app-name` .

Wybierz pozycję **Zaloguj** , a następnie Wyświetl ekran logowania użytkownika. Po zalogowaniu zostanie wystawiona odpowiedź SAML z powrotem do przykładowej aplikacji.

## <a name="supported-and-unsupported-saml-modalities"></a>Obsługiwane i nieobsługiwane procedury SAML

Następujące scenariusze aplikacji SAML są obsługiwane za pośrednictwem własnego punktu końcowego metadanych:

* Wiele adresów URL wylogowywania lub adres URL wylogowania w obiekcie głównym aplikacji/usługi.
* Określ klucz podpisywania, aby zweryfikować żądania jednostki uzależnionej (RP) w obiekcie głównym aplikacji/usługi.
* Określ klucz szyfrowania tokenu w obiekcie głównym aplikacji/usługi.
* Logowanie zainicjowane przez dostawcę tożsamości, w którym dostawca tożsamości jest Azure AD B2C.

## <a name="next-steps"></a>Następne kroki

- Pobierz aplikację sieci Web test SAML z [Azure AD B2C repozytorium społeczności usługi GitHub](https://github.com/azure-ad-b2c/saml-sp-tester).
- Zobacz [Opcje rejestrowania aplikacji SAML w Azure AD B2C](saml-service-provider-options.md)

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
