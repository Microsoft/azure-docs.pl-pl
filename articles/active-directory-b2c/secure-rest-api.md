---
title: Zabezpiecz usługę spokojną w usłudze Azure AD B2C
titleSuffix: Azure AD B2C
description: Zabezpiecz niestandardowe oświadczenia interfejsu API REST w usłudze Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 34ed6d043f713aa55bfe464c48d4332364df805d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680374"
---
# <a name="secure-your-restful-services"></a>Zabezpiecz swoje usługi RESTful 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Podczas integrowania interfejsu API REST w ramach usługi Azure AD B2C podróży użytkownika, należy chronić punkt końcowy interfejsu API REST za pomocą uwierzytelniania. Gwarantuje to, że tylko usługi, które mają odpowiednie poświadczenia, takie jak Usługi Azure AD B2C, można nawiązać połączenia z punktem końcowym interfejsu API REST.

Dowiedz się, jak zintegrować interfejs API REST w środowisku podróży użytkownika usługi Azure AD B2C w [weryfikacji danych wejściowych użytkownika](custom-policy-rest-api-claims-validation.md) i dodawanie oświadczeń interfejsu API REST do artykułów zasad [niestandardowych.](custom-policy-rest-api-claims-exchange.md)

W tym artykule opisano, jak zabezpieczyć interfejs API REST za pomocą podstawowego, certyfikatu klienta HTTP lub uwierzytelniania OAuth2. 

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj czynności opisane w jednym z następujących przewodników "Jak to zrobić":

- [Zintegruj wymiany oświadczeń interfejsu API REST w podróży użytkownika usługi Azure AD B2C w celu sprawdzenia poprawności danych wejściowych użytkownika.](custom-policy-rest-api-claims-validation.md)
- [Dodawanie wymiany oświadczeń interfejsu API REST do zasad niestandardowych](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>Uwierzytelnianie podstawowe HTTP

Uwierzytelnianie podstawowe HTTP jest zdefiniowane w [RFC 2617](https://tools.ietf.org/html/rfc2617). Uwierzytelnianie podstawowe działa w następujący sposób: usługa Azure AD B2C wysyła żądanie HTTP z poświadczeniami klienta w nagłówku autoryzacji. Poświadczenia są formatowane jako ciąg kodowany base64 "name:password".  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Dodawanie kluczy zasad nazwy użytkownika i zasad interfejsu API REST

Aby skonfigurować profil techniczny interfejsu API REST z podstawowym uwierzytelnianiem HTTP, utwórz następujące klucze kryptograficzne do przechowywania nazwy użytkownika i hasła:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C. Wybierz filtr **subskrypcja Katalogu +** w górnym menu i wybierz katalog usługi Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Identity Experience Framework**.
1. Wybierz pozycję **Klawisze zasad**, a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje**wybierz **pozycję Ręczny**.
1. W obszarze **Nazwa**wpisz **RestApiUsername**.
    Prefiks *B2C_1A_* może zostać dodany automatycznie.
1. W polu **Tajne** wprowadź nazwę użytkownika interfejsu API REST.
1. Aby uzyskać **użycie klucza,** wybierz **opcję Szyfrowanie**.
1. Wybierz pozycję **Utwórz**.
1. Ponownie wybierz **pozycję Klawisze zasad.**
1. Wybierz pozycję **Dodaj**.
1. W obszarze **Opcje**wybierz **pozycję Ręczny**.
1. W obszarze **Nazwa**wpisz **RestApiPassword**.
    Prefiks *B2C_1A_* może zostać dodany automatycznie.
1. W polu **Tajne** wprowadź hasło interfejsu API REST.
1. Aby uzyskać **użycie klucza,** wybierz **opcję Szyfrowanie**.
1. Wybierz pozycję **Utwórz**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Konfigurowanie profilu technicznego interfejsu API REST do używania uwierzytelniania podstawowego HTTP

Po utworzeniu niezbędnych kluczy skonfiguruj metadane profilu technicznego interfejsu API REST, aby odwoływać się do poświadczeń.

1. W katalogu roboczym otwórz plik zasad rozszerzenia (TrustFrameworkExtensions.xml).
1. Wyszukaj profil techniczny interfejsu API REST. Na `REST-ValidateProfile`przykład `REST-GetProfile`, lub .
1. Zlokalizuj `<Metadata>` element.
1. Zmień *typ uwierzytelniania* na `Basic`.
1. Zmień *produkcję AllowInsecureAuthInProduction* na `false`.
1. Natychmiast po `</Metadata>` zakończeniu elementu dodaj następujący fragment kodu XML:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Poniżej przedstawiono przykład profilu technicznego RESTful skonfigurowanego z podstawowym uwierzytelnianiem HTTP:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>Uwierzytelnianie certyfikatu klienta HTTPS

Uwierzytelnianie certyfikatu klienta jest uwierzytelnianiem opartym na wzajemnym certyfikatie, w którym klient usługi Azure AD B2C udostępnia serwerowi certyfikat klienta w celu udowodnienia jego tożsamości. Dzieje się tak jako część uzgadniania SSL. Tylko usługi, które mają odpowiednie certyfikaty, takie jak Usługa Azure AD B2C, mogą uzyskiwać dostęp do usługi interfejsu API REST. Certyfikat klienta jest certyfikatem cyfrowym X.509. W środowiskach produkcyjnych musi być podpisany przez urząd certyfikacji.

### <a name="prepare-a-self-signed-certificate-optional"></a>Przygotowywanie certyfikatu z podpisem własnym (opcjonalnie)

W środowiskach nieprodukcyjnych, jeśli nie masz jeszcze certyfikatu, możesz użyć certyfikatu z podpisem własnym. W systemie Windows można użyć polecenia cmdlet [New-SelfSignedCertificate programu](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) PowerShell do wygenerowania certyfikatu.

1. Wykonaj to polecenie programu PowerShell, aby wygenerować certyfikat z podpisem własnym. Zmodyfikuj `-Subject` argument odpowiednio dla aplikacji i nazwy dzierżawy usługi Azure AD B2C. Można również dostosować `-NotAfter` datę, aby określić inny wygaśnięcie certyfikatu.
    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```    
1. Otwórz **pozycję Zarządzaj certyfikatami** > użytkowników**Bieżące certyfikaty** > **osobiste** > **użytkowników** > *yourappname.yourtenant.onmicrosoft.com*.
1. Wybierz certyfikat > **akcja** > **eksportu****wszystkich zadań** > .
1. Wybierz **pozycję Tak** > **Następny** > **Tak, wyeksportuj klucz** > prywatny**Dalej**.
1. Zaakceptuj ustawienia domyślne **dla formatu pliku eksportu**.
1. Podaj hasło certyfikatu.

### <a name="add-a-client-certificate-policy-key"></a>Dodawanie klucza zasad certyfikatu klienta

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C. Wybierz filtr **subskrypcja Katalogu +** w górnym menu i wybierz katalog usługi Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Identity Experience Framework**.
1. Wybierz pozycję **Klawisze zasad**, a następnie wybierz pozycję **Dodaj**.
1. W polu **Opcje** wybierz pozycję **Przekaż**.
1. W polu **Nazwa** wpisz **Polecenie RestApiClientCertificate**.
    Prefiks *B2C_1A_* zostanie dodany automatycznie.
1. W polu **Przekazywanie pliku** wybierz plik .pfx certyfikatu z kluczem prywatnym.
1. W polu **Hasło** wpisz hasło certyfikatu.
1. Wybierz pozycję **Utwórz**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Konfigurowanie profilu technicznego interfejsu API REST do używania uwierzytelniania certyfikatów klienta

Po utworzeniu niezbędnego klucza skonfiguruj metadane profilu technicznego interfejsu API REST, aby odwoływać się do certyfikatu klienta.

1. W katalogu roboczym otwórz plik zasad rozszerzenia (TrustFrameworkExtensions.xml).
1. Wyszukaj profil techniczny interfejsu API REST. Na `REST-ValidateProfile`przykład `REST-GetProfile`, lub .
1. Zlokalizuj `<Metadata>` element.
1. Zmień *typ uwierzytelniania* na `ClientCertificate`.
1. Zmień *produkcję AllowInsecureAuthInProduction* na `false`.
1. Natychmiast po `</Metadata>` zakończeniu elementu dodaj następujący fragment kodu XML:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Poniżej przedstawiono przykład profilu technicznego RESTful skonfigurowanego z certyfikatem klienta HTTP:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>Uwierzytelnianie na okaziciela OAuth2 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Uwierzytelnianie tokena na okaziciela jest zdefiniowane w [ramach autoryzacji OAuth2.0: Użycie tokena nośnika (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). W uwierzytelnianiu tokenu okaziciela usługa Azure AD B2C wysyła żądanie HTTP z tokenem w nagłówku autoryzacji.

```http
Authorization: Bearer <token>
```

Token nośnika jest nieprzezroczystym ciągiem. Może to być token dostępu JWT lub dowolny ciąg, który interfejs API REST oczekuje, że usługa Azure AD B2C zostanie wysłana w nagłówku autoryzacji. Usługa Azure AD B2C obsługuje następujące typy:

- **Token na okaziciela**. Aby móc wysłać token okaziciela w profilu technicznym Restful, twoje zasady muszą najpierw nabyć token okaziciela, a następnie użyć go w profilu technicznym RESTful.  
- **Statyczny token na okaziciela**. Użyj tej metody, gdy interfejs API REST wystawia token dostępu długoterminowego. Aby użyć statycznego tokenu nośnika, utwórz klucz zasad i nawiązuj odwołanie z profilu technicznego RESTful do klucza zasad. 


## <a name="using-oauth2-bearer"></a>Korzystanie z nośnika OAuth2  

Poniższe kroki pokazują, jak używać poświadczeń klienta, aby uzyskać token nośnika i przekazać go do nagłówka autoryzacji wywołań interfejsu API REST.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Zdefiniuj oświadczenie do przechowywania tokenu okaziciela

Oświadczenie zapewnia tymczasowe przechowywanie danych podczas wykonywania zasad usługi Azure AD B2C. [Schemat oświadczeń](claimsschema.md) jest miejscem, w którym deklarujesz swoje oświadczenia. Token dostępu musi być przechowywany w oświadczeniu, które ma zostać użyte później. 

1. Otwórz plik rozszerzeń zasad. Na przykład <em> `SocialAndLocalAccounts/` </em>.
1. Wyszukaj [element BuildingBlocks.](buildingblocks.md) Jeśli element nie istnieje, dodaj go.
1. Znajdź [ClaimsSchema](claimsschema.md) element. Jeśli element nie istnieje, dodaj go.
1. Dodaj następujące oświadczenia do **claimsschema** elementu.  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Pobieranie tokenu dostępu 

Token dostępu można uzyskać na jeden z kilku sposobów: uzyskując go [od dostawcy tożsamości federacyjnej,](idp-pass-through-custom.md)wywołując interfejs API REST, który zwraca token dostępu, przy użyciu [przepływu ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)lub przy użyciu [przepływu poświadczeń klienta.](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)  

W poniższym przykładzie użyto profilu technicznego interfejsu API REST, aby złożyć żądanie do punktu końcowego tokenu usługi Azure AD przy użyciu poświadczeń klienta przekazanych jako uwierzytelnianie podstawowe HTTP. Aby skonfigurować to w usłudze Azure AD, zobacz [platformę tożsamości firmy Microsoft i przepływ poświadczeń klienta OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow). Może być konieczne zmodyfikowanie tego do interfejsu z dostawcą tożsamości. 

W przypadku serviceurl należy zastąpić nazwę dzierżawy nazwą dzierżawy usługi Azure AD. Wszystkie dostępne opcje można znaleźć w [opisie profilu technicznego RESTful.](restful-technical-profile.md)

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.microsoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Zmienianie profilu technicznego REST na uwierzytelnianie tokenu okaziciela

Aby obsługiwać uwierzytelnianie tokenu okaziciela w zasadach niestandardowych, zmodyfikuj profil techniczny interfejsu API REST, wykonując następujące czynności:

1. W katalogu roboczym otwórz plik zasad rozszerzenia *TrustFrameworkExtensions.xml.*
1. Wyszukaj węzeł, `<TechnicalProfile>` który zawiera `Id="REST-API-SignUp"`plik .
1. Zlokalizuj `<Metadata>` element.
1. Zmień *rodzaj uwierzytelniania* na *nosiciel*, w następujący sposób:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Zmień lub dodaj *UseClaimAsBearerToken* do *bearerToken*, w następujący sposób. *BearerToken* jest nazwą oświadczenia, z których zostanie pobrany token na okaziciela (oświadczenie wyjściowe od `SecureREST-AccessToken`).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Upewnij się, że dodasz oświadczenie użyte powyżej jako oświadczenie wejściowe:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

Po dodaniu powyższych fragmentów profil techniczny powinien wyglądać następująco:

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Używanie statycznego okaziciela OAuth2 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Dodawanie klucza zasad tokenu nośnika OAuth2

Utwórz klucz zasad do przechowywania wartości tokenu nośnika.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C. Wybierz filtr **subskrypcja Katalogu +** w górnym menu i wybierz katalog usługi Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Identity Experience Framework**.
1. Wybierz pozycję **Klawisze zasad**, a następnie wybierz pozycję **Dodaj**.
1. W **Options**przypadku `Manual`opcji wybierz opcję .
1. Wprowadź **nazwę** klucza zasad. Na przykład `RestApiBearerToken`. Prefiks `B2C_1A_` zostanie automatycznie dodany do nazwy klucza.
1. W **pliku Secret**wprowadź wcześniej zarejestrowany klucz tajny klienta.
1. Dla **użycia klucza**wybierz opcję `Encryption`.
1. Wybierz pozycję **Utwórz**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Skonfiguruj profil techniczny interfejsu API REST tak, aby używał klucza zasad tokenu nośnika

Po utworzeniu niezbędnego klucza skonfiguruj metadane profilu technicznego interfejsu API REST, aby odwoływać się do tokenu nośnika.

1. W katalogu roboczym otwórz plik zasad rozszerzenia (TrustFrameworkExtensions.xml).
1. Wyszukaj profil techniczny interfejsu API REST. Na `REST-ValidateProfile`przykład `REST-GetProfile`, lub .
1. Zlokalizuj `<Metadata>` element.
1. Zmień *typ uwierzytelniania* na `Bearer`.
1. Zmień *produkcję AllowInsecureAuthInProduction* na `false`.
1. Natychmiast po `</Metadata>` zakończeniu elementu dodaj następujący fragment kodu XML:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Poniżej przedstawiono przykład profilu technicznego RESTful skonfigurowanego z uwierzytelnianiem tokenu okaziciela:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [elementu profilu technicznego Restful](restful-technical-profile.md) w odwołaniu do IEF. 
