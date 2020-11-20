---
title: Zabezpiecz usługę RESTful w Azure AD B2C
titleSuffix: Azure AD B2C
description: Zabezpiecz swoją niestandardową wymianę oświadczeń interfejsu API REST w Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 18979ba8cbc4e68bf79275059c6c1c976578c407
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953376"
---
# <a name="secure-your-restful-services"></a>Zabezpieczanie usług RESTful Services 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W przypadku integrowania interfejsu API REST w ramach podróży użytkownika Azure AD B2C należy chronić punkt końcowy interfejsu API REST z uwierzytelnianiem. Dzięki temu tylko usługi, które mają odpowiednie poświadczenia, takie jak Azure AD B2C, mogą wykonywać wywołania punktu końcowego interfejsu API REST.

Dowiedz się, jak zintegrować interfejs API REST w trakcie podróży użytkownika Azure AD B2C w ramach funkcji [Weryfikuj dane wejściowe użytkownika](custom-policy-rest-api-claims-validation.md) i [Dodaj wymianę oświadczeń interfejsu API REST do artykułów zasad niestandardowych](custom-policy-rest-api-claims-exchange.md) .

W tym artykule opisano sposób zabezpieczania interfejsu API REST przy użyciu protokołu HTTP Basic, certyfikatu klienta lub uwierzytelniania OAuth2. 

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj czynności opisane w jednej z następujących przewodników:

- [Zintegruj wymianę oświadczeń interfejsu API REST w ramach kursu Azure AD B2C użytkownika, aby sprawdzić poprawność danych wejściowych użytkownika](custom-policy-rest-api-claims-validation.md).
- [Dodawanie wymiany oświadczeń interfejsu API REST do zasad niestandardowych](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>Uwierzytelnianie podstawowe HTTP

Uwierzytelnianie podstawowe protokołu HTTP jest zdefiniowane w [dokumencie RFC 2617](https://tools.ietf.org/html/rfc2617). Uwierzytelnianie podstawowe działa w następujący sposób: Azure AD B2C wysyła żądanie HTTP przy użyciu poświadczeń klienta w nagłówku autoryzacji. Poświadczenia są sformatowane jako ciąg szyfrowany algorytmem Base64 "Name: Password".  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Dodaj nazwę użytkownika interfejsu API REST i klucze zasad haseł

Aby skonfigurować profil techniczny interfejsu API REST z uwierzytelnianiem Basic protokołu HTTP, Utwórz następujące klucze kryptograficzne do przechowywania nazwy użytkownika i hasła:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.
1. Wybierz pozycję **klucze zasad**, a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje** wybierz pozycję **Ręczne**.
1. W obszarze **Nazwa** wpisz **RestApiUsername**.
    Prefiks *B2C_1A_* może zostać dodany automatycznie.
1. W polu **wpis tajny** wprowadź nazwę użytkownika interfejsu API REST.
1. W obszarze **użycie klucza** wybierz pozycję **szyfrowanie**.
1. Wybierz pozycję **Utwórz**.
1. Wybierz ponownie **klucze zasad** .
1. Wybierz pozycję **Dodaj**.
1. W obszarze **Opcje** wybierz pozycję **Ręczne**.
1. W obszarze **Nazwa** wpisz **RestApiPassword**.
    Prefiks *B2C_1A_* może zostać dodany automatycznie.
1. W polu **wpis tajny** wprowadź hasło interfejsu API REST.
1. W obszarze **użycie klucza** wybierz pozycję **szyfrowanie**.
1. Wybierz pozycję **Utwórz**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Konfigurowanie profilu technicznego interfejsu API REST do korzystania z uwierzytelniania podstawowego protokołu HTTP

Po utworzeniu niezbędnych kluczy Skonfiguruj metadane profilu technicznego interfejsu API REST, aby odwołać się do poświadczeń.

1. W katalogu roboczym Otwórz plik zasad rozszerzenia (TrustFrameworkExtensions.xml).
1. Wyszukaj profil techniczny interfejsu API REST. Na przykład `REST-ValidateProfile` lub `REST-GetProfile` .
1. Znajdź `<Metadata>` element.
1. Zmień wartość *AuthenticationType* na `Basic` .
1. Zmień *AllowInsecureAuthInProduction* na `false` .
1. Bezpośrednio po elemencie zamykającym `</Metadata>` Dodaj następujący fragment kodu XML:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Poniżej znajduje się przykładowy profil techniczny RESTful skonfigurowany przy użyciu uwierzytelniania podstawowego HTTP:

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

Uwierzytelnianie za pomocą certyfikatu klienta jest wzajemnym uwierzytelnianiem opartym na certyfikatach, w którym klient Azure AD B2C udostępnia certyfikat klienta serwerowi, aby potwierdzić jego tożsamość. Dzieje się tak w ramach uzgadniania protokołu SSL. Do usługi interfejsu API REST można uzyskać dostęp tylko do usług, które mają odpowiednie certyfikaty, takie jak Azure AD B2C. Certyfikat klienta jest certyfikatem cyfrowym X. 509. W środowiskach produkcyjnych musi być podpisane przez urząd certyfikacji.

### <a name="prepare-a-self-signed-certificate-optional"></a>Przygotuj certyfikat z podpisem własnym (opcjonalnie)

Jeśli w środowiskach nieprodukcyjnych nie masz jeszcze certyfikatu, możesz użyć certyfikatu z podpisem własnym. W systemie Windows można użyć polecenia cmdlet [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) programu PowerShell do wygenerowania certyfikatu.

1. Wykonaj to polecenie programu PowerShell, aby wygenerować certyfikat z podpisem własnym. Zmodyfikuj `-Subject` argument jako odpowiedni dla aplikacji i Azure AD B2C nazwę dzierżawcy. Możesz również dostosować datę, `-NotAfter` Aby określić inne wygaśnięcie certyfikatu.
    ```powershell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```    
1. Otwórz przystawkę **Zarządzaj certyfikatami użytkowników**  >  **bieżące**  >  **Personal**  >  **Certyfikaty** osobiste  >  *yourappname.yourtenant.onmicrosoft.com*.
1. Wybierz certyfikat, > **Akcja**  >  **wszystkie zadania**  >  **Eksportuj**.
1. Wybierz pozycję **tak**  >  **dalej**  >  **tak, eksportuj klucz prywatny**  >  **dalej**.
1. Zaakceptuj wartości domyślne w **formacie pliku eksportu**.
1. Podaj hasło do certyfikatu.

### <a name="add-a-client-certificate-policy-key"></a>Dodawanie klucza zasad certyfikatów klienta

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.
1. Wybierz pozycję **klucze zasad**, a następnie wybierz pozycję **Dodaj**.
1. W polu **Opcje** wybierz pozycję **Przekaż**.
1. W polu **Nazwa** wpisz **RestApiClientCertificate**.
    Prefiks *B2C_1A_* jest dodawany automatycznie.
1. W polu **przekazywanie pliku** wybierz plik PFX certyfikatu z kluczem prywatnym.
1. W polu **hasło** wpisz hasło certyfikatu.
1. Wybierz pozycję **Utwórz**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Konfigurowanie profilu technicznego interfejsu API REST w celu korzystania z uwierzytelniania przy użyciu certyfikatu klienta

Po utworzeniu niezbędnego klucza Skonfiguruj metadane profilu technicznego interfejsu API REST, aby odwołać się do certyfikatu klienta.

1. W katalogu roboczym Otwórz plik zasad rozszerzenia (TrustFrameworkExtensions.xml).
1. Wyszukaj profil techniczny interfejsu API REST. Na przykład `REST-ValidateProfile` lub `REST-GetProfile` .
1. Znajdź `<Metadata>` element.
1. Zmień wartość *AuthenticationType* na `ClientCertificate` .
1. Zmień *AllowInsecureAuthInProduction* na `false` .
1. Bezpośrednio po elemencie zamykającym `</Metadata>` Dodaj następujący fragment kodu XML:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Poniżej znajduje się przykładowy profil techniczny RESTful skonfigurowany przy użyciu certyfikatu klienta HTTP:

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

## <a name="oauth2-bearer-authentication"></a>Uwierzytelnianie okaziciela OAuth2 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Uwierzytelnianie tokenu okaziciela jest zdefiniowane w [strukturze autoryzacji OAuth 2.0: użycie tokenu okaziciela (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). W przypadku uwierzytelniania tokenu nośnego Azure AD B2C wysyła żądanie HTTP z tokenem w nagłówku autoryzacji.

```http
Authorization: Bearer <token>
```

Token okaziciela jest nieprzezroczystym ciągiem. Może to być token dostępu JWT lub dowolny ciąg, który interfejs API REST oczekuje Azure AD B2C do wysłania w nagłówku autoryzacji. Azure AD B2C obsługuje następujące typy:

- **Token elementu nośnego**. Aby można było wysłać token okaziciela w profilu technicznym RESTful, zasady muszą najpierw uzyskać token okaziciela, a następnie użyć go w profilu technicznym RESTful.  
- **Statyczny token elementu nośnego**. Użyj tej metody, gdy interfejs API REST wystawia token dostępu długoterminowego. Aby użyć statycznego tokenu okaziciela, Utwórz klucz zasad i wprowadź odwołanie z profilu technicznego RESTful do klucza zasad. 


## <a name="using-oauth2-bearer"></a>Używanie okaziciela OAuth2  

W poniższych krokach pokazano, jak za pomocą poświadczeń klienta uzyskać token okaziciela i przekazać go do nagłówka autoryzacji wywołań interfejsu API REST.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Zdefiniuj zastrzeżenie do przechowywania tokenu okaziciela

W trakcie wykonywania zasad Azure AD B2C, zgłoszenie zapewnia tymczasowy magazyn danych. [Schemat oświadczeń](claimsschema.md) jest miejscem, w którym deklarujesz oświadczenia. Token dostępu musi być przechowywany w ramach żądania do późniejszego użycia. 

1. Otwórz plik rozszerzeń zasad. Na przykład <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Wyszukaj element [BuildingBlocks](buildingblocks.md) . Jeśli element nie istnieje, Dodaj go.
1. Znajdź element [ClaimsSchema](claimsschema.md) . Jeśli element nie istnieje, Dodaj go.
1. Dodaj następujące oświadczenia do elementu **ClaimsSchema** .  

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

### <a name="acquiring-an-access-token"></a>Uzyskiwanie tokenu dostępu 

Token dostępu można uzyskać na jeden z kilku sposobów: uzyskując go [od dostawcy tożsamości federacyjnych](idp-pass-through-custom.md), wywołując interfejs API REST, który zwraca token dostępu, za pomocą [przepływu ROPC](../active-directory/develop/v2-oauth-ropc.md)lub za pomocą [przepływu poświadczeń klienta](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md).  

W poniższym przykładzie użyto profilu technicznego interfejsu API REST, aby wysłać żądanie do punktu końcowego tokenu usługi Azure AD przy użyciu poświadczeń klienta przewidzianych jako podstawowe uwierzytelnianie HTTP. Aby skonfigurować ten program w usłudze Azure AD, zobacz temat [Microsoft Identity platform i klient OAuth 2,0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Może być konieczne zmodyfikowanie tego interfejsu z dostawcą tożsamości. 

W przypadku ServiceUrl Zastąp nazwę dzierżawy nazwą swojej dzierżawy usługi Azure AD. Aby uzyskać dostęp do wszystkich dostępnych opcji, zobacz Dokumentacja [profilu technicznego RESTful](restful-technical-profile.md) .

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token</Item>
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

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Zmień profil techniczny REST, aby korzystać z uwierzytelniania tokenu okaziciela

Aby zapewnić obsługę uwierzytelniania tokenów okaziciela w zasadach niestandardowych, należy zmodyfikować profil techniczny interfejsu API REST, wykonując następujące czynności:

1. W katalogu roboczym Otwórz plik zasad rozszerzenia *TrustFrameworkExtensions.xml* .
1. Wyszukaj `<TechnicalProfile>` węzeł, który zawiera `Id="REST-API-SignUp"` .
1. Znajdź `<Metadata>` element.
1. Zmień wartość *AuthenticationType* na *Bearer* w następujący sposób:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Zmień lub Dodaj *UseClaimAsBearerToken* do *bearerToken* w następujący sposób. *BearerToken* to nazwa żądania, z którego zostanie pobrany token okaziciela (wynikowy wniosek `SecureREST-AccessToken` ).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Upewnij się, że zostało dodane powyższy element w ramach roszczeń wejściowych:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

Po dodaniu powyższych fragmentów Twój profil techniczny powinien wyglądać podobnie do następującego kodu XML:

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

## <a name="using-a-static-oauth2-bearer"></a>Używanie statycznego elementu OAuth2 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Dodaj klucz zasad tokenu okaziciela OAuth2

Aby skonfigurować profil techniczny interfejsu API REST z tokenem okaziciela OAuth2, uzyskaj token dostępu od właściciela interfejsu API REST. Następnie utwórz następujący klucz kryptograficzny do przechowywania tokenu okaziciela.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Struktura środowiska tożsamości**.
1. Wybierz pozycję **klucze zasad**, a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje** wybierz opcję `Manual` .
1. Wprowadź **nazwę** klucza zasad. Na przykład `RestApiBearerToken`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
1. W **kluczu tajnym** wprowadź wcześniej zarejestrowany klucz tajny klienta.
1. W obszarze **użycie klucza** wybierz opcję `Encryption` .
1. Wybierz pozycję **Utwórz**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Skonfiguruj profil techniczny interfejsu API REST, aby użyć klucza zasad tokenu okaziciela

Po utworzeniu niezbędnego klucza Skonfiguruj metadane profilu technicznego interfejsu API REST, aby odwołać się do tokenu okaziciela.

1. W katalogu roboczym Otwórz plik zasad rozszerzenia (TrustFrameworkExtensions.xml).
1. Wyszukaj profil techniczny interfejsu API REST. Na przykład `REST-ValidateProfile` lub `REST-GetProfile` .
1. Znajdź `<Metadata>` element.
1. Zmień wartość *AuthenticationType* na `Bearer` .
1. Zmień *AllowInsecureAuthInProduction* na `false` .
1. Bezpośrednio po elemencie zamykającym `</Metadata>` Dodaj następujący fragment kodu XML:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Poniżej znajduje się przykładowy profil techniczny RESTful skonfigurowany z uwierzytelnianiem tokenu okaziciela:

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

- Więcej informacji na temat elementu [RESTful Technical profile](restful-technical-profile.md) znajduje się w dokumentacji IEF.