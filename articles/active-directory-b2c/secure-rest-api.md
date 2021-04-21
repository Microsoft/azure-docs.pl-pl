---
title: Zabezpieczanie usługi Restful w usłudze Azure AD B2C
titleSuffix: Azure AD B2C
description: Zabezpieczanie niestandardowych wymian oświadczeń interfejsu API REST w Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/21/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a1c161c28a589e4250fded13cd3d94ccdda97b55
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829822"
---
# <a name="secure-your-restful-services"></a>Zabezpieczanie usług RESTful 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Podczas integrowania interfejsu API REST w ramach Azure AD B2C podróży użytkownika należy chronić punkt końcowy interfejsu API REST za pomocą uwierzytelniania. Gwarantuje to, że tylko usługi, które mają odpowiednie poświadczenia, takie jak Azure AD B2C, mogą wykonać wywołania do punktu końcowego interfejsu API REST.

Dowiedz się, jak zintegrować interfejs API REST w [](custom-policy-rest-api-claims-validation.md) podróży Azure AD B2C w ramach weryfikowania danych wejściowych użytkownika i artykułów Add [REST API claims exchanges to custom policies](custom-policy-rest-api-claims-exchange.md) (Dodawanie wymiany oświadczeń interfejsu API REST do zasad niestandardowych).

W tym artykule opisano sposób zabezpieczania interfejsu API REST przy użyciu podstawowego protokołu HTTP, certyfikatu klienta lub uwierzytelniania OAuth2. 

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki w jednym z następujących przewodników "Instrukcje":

- [Zintegruj wymiany oświadczeń interfejsu API REST w Azure AD B2C użytkownika w celu zweryfikowania danych wejściowych użytkownika.](custom-policy-rest-api-claims-validation.md)
- [Dodawanie wymiany oświadczeń interfejsu API REST do zasad niestandardowych](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>Uwierzytelnianie podstawowe PROTOKOŁU HTTP

Podstawowe uwierzytelnianie HTTP jest zdefiniowane w [dokumencie RFC 2617.](https://tools.ietf.org/html/rfc2617) Uwierzytelnianie podstawowe działa w następujący sposób: Azure AD B2C wysyła żądanie HTTP z poświadczeniami klienta w nagłówku autoryzacji. Poświadczenia są sformatowane jako ciąg "name:password" zakodowany w formacie base64.  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Dodawanie kluczy zasad nazwy użytkownika i hasła interfejsu API REST

Aby skonfigurować profil techniczny interfejsu API REST przy użyciu podstawowego uwierzytelniania protokołu HTTP, utwórz następujące klucze kryptograficzne do przechowywania nazwy użytkownika i hasła:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu zawierającego dzierżawę Azure AD B2C dzierżawy. Wybierz filtr **Katalog i subskrypcja** w górnym menu, a następnie wybierz Azure AD B2C katalog.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Identity Experience Framework**.
1. Wybierz **pozycję Klucze zasad,** a następnie wybierz **pozycję Dodaj**.
1. W **przypadku opcji** wybierz opcję **Ręcznie.**
1. W **obszarze Nazwa** wpisz **RestApiUsername**.
    Prefiks *B2C_1A_* zostać dodany automatycznie.
1. W polu **Wpis tajny** wprowadź nazwę użytkownika interfejsu API REST.
1. W **przypadku opcji Użycie klucza** wybierz pozycję **Szyfrowanie.**
1. Wybierz przycisk **Utwórz**.
1. Ponownie **wybierz pozycję Klucze** zasad.
1. Wybierz pozycję **Dodaj**.
1. W **przypadku opcji** wybierz opcję **Ręcznie.**
1. W **obszarze Nazwa** wpisz **RestApiPassword.**
    Prefiks *B2C_1A_* zostać dodany automatycznie.
1. W polu **Wpis tajny** wprowadź hasło interfejsu API REST.
1. W **przypadku opcji Użycie klucza** wybierz pozycję **Szyfrowanie.**
1. Wybierz przycisk **Utwórz**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Konfigurowanie profilu technicznego interfejsu API REST do korzystania z uwierzytelniania podstawowego PROTOKOŁU HTTP

Po utworzeniu niezbędnych kluczy skonfiguruj metadane profilu technicznego interfejsu API REST, aby odwoływać się do poświadczeń.

1. W katalogu roboczy otwórz plik zasad rozszerzenia (TrustFrameworkExtensions.xml).
1. Wyszukaj profil techniczny interfejsu API REST. Na przykład `REST-ValidateProfile` , lub `REST-GetProfile` .
1. Znajdź `<Metadata>` element .
1. Zmień typ *AuthenticationType na* `Basic` .
1. Zmień ciąg *AllowInsecureAuthInProduction na* `false` .
1. Bezpośrednio po elemencie `</Metadata>` zamykającym dodaj następujący fragment kodu XML:
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

Uwierzytelnianie certyfikatu klienta to wzajemne uwierzytelnianie oparte na certyfikatach, w którym klient, Azure AD B2C, dostarcza serwerowi swój certyfikat klienta, aby potwierdzić swoją tożsamość. Dzieje się to w ramach uściśniania protokołu SSL. Tylko usługi, które mają odpowiednie certyfikaty, takie jak Azure AD B2C, mogą uzyskać dostęp do usługi interfejsu API REST. Certyfikat klienta jest certyfikatem cyfrowym X.509. W środowiskach produkcyjnych musi być podpisany przez urząd certyfikacji.

### <a name="prepare-a-self-signed-certificate-optional"></a>Przygotowywanie certyfikatu z podpisem własnym (opcjonalnie)

W środowiskach nieprodukcji, jeśli nie masz jeszcze certyfikatu, możesz użyć certyfikatu z podpisem własnym. W systemie Windows do wygenerowania certyfikatu można użyć polecenia cmdlet [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) programu PowerShell.

1. Wykonaj to polecenie programu PowerShell, aby wygenerować certyfikat z podpisem własnym. `-Subject`Zmodyfikuj argument zgodnie z potrzebami aplikacji i Azure AD B2C dzierżawy. Można również dostosować `-NotAfter` datę, aby określić inną datę wygaśnięcia certyfikatu.
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
1. Otwórz **okno Zarządzanie certyfikatami użytkowników** Certyfikaty osobiste  >    >    >  **bieżącego użytkownika**  >  *yourappname.yourtenant.onmicrosoft.com*.
1. Wybierz certyfikat i >  >  **Eksportuj wszystkie**  >  **zadania.**
1. Wybierz **pozycję Tak**  >  **Dalej**  >  **Tak, wyeksportuj klucz prywatny**  >  **Dalej.**
1. Zaakceptuj wartości domyślne dla formatu **eksportu pliku**.
1. Podaj hasło certyfikatu.

### <a name="add-a-client-certificate-policy-key"></a>Dodawanie klucza zasad certyfikatu klienta

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu zawierającego dzierżawę Azure AD B2C dzierżawy. Wybierz filtr **Katalog i subskrypcja** w górnym menu, a następnie wybierz Azure AD B2C katalog.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Identity Experience Framework**.
1. Wybierz **pozycję Klucze zasad,** a następnie wybierz **pozycję Dodaj.**
1. W polu **Opcje** wybierz pozycję **Przekaż**.
1. W **polu Nazwa** wpisz **RestApiClientCertificate**.
    Prefiks *B2C_1A_* jest dodawany automatycznie.
1. W polu **Przekazywanie pliku** wybierz plik pfx certyfikatu z kluczem prywatnym.
1. W **polu** Hasło wpisz hasło certyfikatu.
1. Wybierz przycisk **Utwórz**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Konfigurowanie profilu technicznego interfejsu API REST do korzystania z uwierzytelniania certyfikatu klienta

Po utworzeniu niezbędnego klucza skonfiguruj metadane profilu technicznego interfejsu API REST, aby odwoływać się do certyfikatu klienta.

1. W katalogu roboczy otwórz plik zasad rozszerzenia (TrustFrameworkExtensions.xml).
1. Wyszukaj profil techniczny interfejsu API REST. Na przykład `REST-ValidateProfile` , lub `REST-GetProfile` .
1. Znajdź `<Metadata>` element .
1. Zmień typ *AuthenticationType na* `ClientCertificate` .
1. Zmień ciąg *AllowInsecureAuthInProduction na* `false` .
1. Bezpośrednio po elemencie `</Metadata>` zamykającym dodaj następujący fragment kodu XML:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Poniżej przedstawiono przykład profilu technicznego RESTful skonfigurowanego przy użyciu certyfikatu klienta HTTP:

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

## <a name="oauth2-bearer-authentication"></a>Uwierzytelnianie użytkownika OAuth2 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Uwierzytelnianie tokenu bearer jest zdefiniowane w [dokumencie OAuth2.0 Authorization Framework: Bearer Token Usage (RFC 6750) .](https://www.rfc-editor.org/rfc/rfc6750.txt) W przypadku uwierzytelniania tokenu Azure AD B2C wysyła żądanie HTTP z tokenem w nagłówku autoryzacji.

```http
Authorization: Bearer <token>
```

Token bearer jest nieprzezroczystym ciągiem. Może to być token dostępu JWT lub dowolny ciąg, który interfejs API REST Azure AD B2C wysłać w nagłówku autoryzacji. Azure AD B2C obsługuje następujące typy:

- **Token bearer**. Aby móc wysłać token okaziciela w profilu technicznym Restful, zasady muszą najpierw uzyskać token okaziciela, a następnie użyć go w profilu technicznym RESTful.  
- **Token statycznego okaziciela**. Użyj tej metody, gdy interfejs API REST wydaje token dostępu długoterminowego. Aby użyć statycznego tokenu bearer, utwórz klucz zasad i utwórz odwołanie z profilu technicznego RESTful do klucza zasad. 


## <a name="using-oauth2-bearer"></a>Korzystanie z programu Bearer OAuth2  

W poniższych krokach pokazano, jak używać poświadczeń klienta w celu uzyskania tokenu okaziciela i przekazania go do nagłówka autoryzacji wywołań interfejsu API REST.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Definiowanie oświadczenia do przechowywania tokenu bearer

Oświadczenie zapewnia tymczasowy magazyn danych podczas wykonywania Azure AD B2C zasad. Schemat [oświadczeń to](claimsschema.md) miejsce, w którym deklaruje się oświadczenia. Token dostępu musi być przechowywany w oświadczenie, aby można było go użyć później. 

1. Otwórz plik rozszerzeń zasad. Na przykład <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Wyszukaj element [BuildingBlocks.](buildingblocks.md) Jeśli element nie istnieje, dodaj go.
1. Znajdź [claimsSchema](claimsschema.md) element. Jeśli element nie istnieje, dodaj go.
1. Dodaj następujące oświadczenia do **elementu ClaimsSchema.**  

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

Token dostępu można uzyskać na jeden z kilku sposobów: przez uzyskanie go od dostawcy tożsamości [federacji](idp-pass-through-user-flow.md), przez wywołanie interfejsu API REST, który zwraca token dostępu, przy użyciu przepływu [PRZEPŁYWC](../active-directory/develop/v2-oauth-ropc.md)lub przy użyciu przepływu poświadczeń [klienta](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Przepływ poświadczeń klienta jest często używany w przypadku interakcji między serwerami, które muszą być uruchamiane w tle, bez bezpośredniej interakcji z użytkownikiem.

#### <a name="acquiring-an-azure-ad-access-token"></a>Uzyskiwanie tokenu dostępu usługi Azure AD 

W poniższym przykładzie użyto profilu technicznego interfejsu API REST, aby wykonać żądanie do punktu końcowego tokenu usługi Azure AD przy użyciu poświadczeń klienta przekazanych jako podstawowe uwierzytelnianie HTTP. Aby uzyskać więcej informacji, zobacz Platforma tożsamości firmy Microsoft i przepływ poświadczeń klienta [OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). 

Aby profil techniczny współdziałał z usługą Azure AD w celu uzyskania tokenu dostępu, należy zarejestrować aplikację. Azure AD B2C korzysta z platformy Azure AD. Aplikację można utworzyć w dzierżawie Azure AD B2C dzierżawie lub w dowolnej dzierżawie usługi Azure AD, która jest zarządzana. Aby zarejestrować aplikację:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **Katalog i subskrypcja** w górnym menu, a następnie wybierz katalog zawierający usługę Azure AD lub Azure AD B2C dzierżawy.
1. W menu po lewej stronie wybierz **pozycję Azure Active Directory**. Możesz też wybrać **pozycję Wszystkie usługi,** wyszukać i wybrać pozycję **Azure Active Directory**.
1. Wybierz **Rejestracje aplikacji**, a następnie wybierz pozycję **Nowa rejestracja.**
1. Wprowadź **nazwę** aplikacji. Na przykład *Client_Credentials_Auth_app*.
1. W **obszarze Obsługiwane typy kont** wybierz pozycję Konta tylko w tym katalogu **organizacyjnym.**
1. Wybierz pozycję **Zarejestruj**.
2. **Zanotuj identyfikator aplikacji (klienta).** 


W przypadku przepływu poświadczeń klienta należy utworzyć klucz tajny aplikacji. Klucz tajny klienta jest również nazywany hasłem aplikacji. Klucz tajny będzie używany przez aplikację do uzyskania tokenu dostępu.

1. Na stronie **Azure AD — Rejestracje aplikacji** wybierz utworzoną aplikację, na przykład *Client_Credentials_Auth_app*.
1. W menu po lewej stronie w **obszarze Zarządzanie** wybierz pozycję Certyfikaty i **& tajne.**
1. Wybierz pozycję **Nowy wpis tajny klienta**.
1. Wprowadź opis wpisów tajnych klienta w **polu** Opis. Na przykład *clientsecret1*.
1. W **obszarze Wygasa** wybierz czas trwania, dla którego klucz tajny jest prawidłowy, a następnie wybierz pozycję **Dodaj**.
1. Zanotuj wartość **tajnego do** użycia w kodzie aplikacji klienckiej. Ta wartość tajnego nigdy nie jest wyświetlana ponownie po opuszczeniu tej strony. Ta wartość jest używana jako klucz tajny aplikacji w kodzie aplikacji.

#### <a name="create-azure-ad-b2c-policy-keys"></a>Tworzenie Azure AD B2C zasad zabezpieczeń

Należy przechowywać identyfikator klienta i klucz tajny klienta, które zostały wcześniej zarejestrowane w dzierżawie Azure AD B2C dzierżawy.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu zawierającego dzierżawę Azure AD B2C dzierżawy. Wybierz filtr **Katalog i subskrypcja** w górnym menu i wybierz katalog zawierający Twoją dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Na stronie Przegląd wybierz pozycję **Identity Experience Framework**.
5. Wybierz **pozycję Klucze zasad,** a następnie wybierz **pozycję Dodaj**.
6. W **przypadku opcji** wybierz pozycję `Manual` .
7. W **polach Nazwa** wprowadź nazwę klucza `SecureRESTClientId` zasad. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
8. W **kluczu** tajnym wprowadź wcześniej zarejestrowany identyfikator klienta.
9. W **przypadku opcji Użycie klucza** wybierz pozycję `Signature` .
10. Wybierz przycisk **Utwórz**.
11. Utwórz inny klucz zasad z następującymi ustawieniami:
    -   **Nazwa:** `SecureRESTClientSecret` .
    -   **Wpis** tajny: wprowadź wcześniej zarejestrowany klucz tajny klienta

W przypadku nazwy ServiceUrl zastąp nazwę your-tenant-name nazwą swojej dzierżawy usługi Azure AD. Zobacz [dokumentację profilu technicznego RESTful,](restful-technical-profile.md) aby uzyskać wszystkie dostępne opcje.

```xml
<TechnicalProfile Id="REST-AcquireAccessToken">
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
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://graph.microsoft.com/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Zmienianie profilu technicznego REST w celu używania uwierzytelniania tokenu okaziciela

Aby obsługiwać uwierzytelnianie tokenu okaziciela w zasadach niestandardowych, zmodyfikuj profil techniczny interfejsu API REST przy użyciu następujących opcji:

1. W katalogu roboczy otwórz plik *zasadTrustFrameworkExtensions.xml* rozszerzenia.
1. Wyszukaj `<TechnicalProfile>` węzeł, który zawiera `Id="REST-API-SignUp"` .
1. Znajdź `<Metadata>` element .
1. Zmień typ *AuthenticationType na* *Bearer* w następujący sposób:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Zmień lub dodaj *pozycję UseClaimAsBearerToken na* *bearerToken* w następujący sposób. *BearerToken* to nazwa oświadczenia, z których zostanie pobrany token bearer (oświadczenie wyjściowe z `REST-AcquireAccessToken` ).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Upewnij się, że dodano oświadczenie użyte powyżej jako oświadczenie wejściowe:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

Po dodaniu powyższych fragmentów kodu Twój profil techniczny powinien wyglądać podobnie do następującego kodu XML:

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

## <a name="using-a-static-oauth2-bearer"></a>Używanie statycznego okaziciela OAuth2 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Dodawanie klucza zasad tokenu okaziciela OAuth2

Aby skonfigurować profil techniczny interfejsu API REST przy użyciu tokenu okaziciela OAuth2, uzyskaj token dostępu od właściciela interfejsu API REST. Następnie utwórz następujący klucz kryptograficzny do przechowywania tokenu bearer.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu zawierającego dzierżawę Azure AD B2C dzierżawy. Wybierz filtr **Katalog i subskrypcja** w górnym menu, a następnie wybierz Azure AD B2C katalog.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Identity Experience Framework**.
1. Wybierz **pozycję Klucze zasad,** a następnie wybierz **pozycję Dodaj**.
1. W **przypadku opcji** wybierz pozycję `Manual` .
1. Wprowadź **nazwę** klucza zasad. Na przykład `RestApiBearerToken`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
1. W **kluczu** tajnym wprowadź wcześniej zarejestrowany klucz tajny klienta.
1. W **przypadku opcji Użycie klucza** wybierz pozycję `Encryption` .
1. Wybierz przycisk **Utwórz**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Konfigurowanie profilu technicznego interfejsu API REST w celu używania klucza zasad tokenu okaziciela

Po utworzeniu niezbędnego klucza skonfiguruj metadane profilu technicznego interfejsu API REST, aby odwoływać się do tokenu elementu bearer.

1. W katalogu pracy otwórz plik zasad rozszerzenia (TrustFrameworkExtensions.xml).
1. Wyszukaj profil techniczny interfejsu API REST. Na przykład `REST-ValidateProfile` , lub `REST-GetProfile` .
1. Znajdź `<Metadata>` element .
1. Zmień typ *AuthenticationType na* `Bearer` .
1. Zmień ciąg *AllowInsecureAuthInProduction na* `false` .
1. Bezpośrednio po elemencie `</Metadata>` zamykającym dodaj następujący fragment kodu XML:
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

## <a name="api-key-authentication"></a>Uwierzytelnianie klucza interfejsu API

Klucz interfejsu API to unikatowy identyfikator używany do uwierzytelniania użytkownika w celu uzyskania dostępu do punktu końcowego interfejsu API REST. Klucz jest wysyłany w niestandardowym nagłówku HTTP. Na przykład wyzwalacz [Azure Functions HTTP](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) używa nagłówka HTTP do identyfikowania `x-functions-key` osoby żądacej.  

### <a name="add-api-key-policy-keys"></a>Dodawanie kluczy zasad klucza interfejsu API

Aby skonfigurować profil techniczny interfejsu API REST z uwierzytelnianiem klucza interfejsu API, utwórz następujący klucz kryptograficzny do przechowywania klucza interfejsu API:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu zawierającego dzierżawę Azure AD B2C dzierżawy. Wybierz filtr **Katalog i subskrypcja** w górnym menu, a następnie wybierz Azure AD B2C katalog.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Identity Experience Framework**.
1. Wybierz **pozycję Klucze zasad,** a następnie wybierz **pozycję Dodaj.**
1. W **przypadku opcji** wybierz opcję **Ręcznie.**
1. W **obszarze Nazwa** wpisz **RestApiKey.**
    Prefiks *B2C_1A_* zostać dodany automatycznie.
1. W polu **Wpis tajny** wprowadź klucz interfejsu API REST.
1. W **przypadku opcji Użycie klucza** wybierz pozycję **Szyfrowanie.**
1. Wybierz przycisk **Utwórz**.


### <a name="configure-your-rest-api-technical-profile-to-use-api-key-authentication"></a>Konfigurowanie profilu technicznego interfejsu API REST do korzystania z uwierzytelniania za pomocą klucza interfejsu API

Po utworzeniu niezbędnego klucza skonfiguruj metadane profilu technicznego interfejsu API REST, aby odwoływać się do poświadczeń.

1. W katalogu roboczy otwórz plik zasad rozszerzenia (TrustFrameworkExtensions.xml).
1. Wyszukaj profil techniczny interfejsu API REST. Na przykład `REST-ValidateProfile` , lub `REST-GetProfile` .
1. Znajdź `<Metadata>` element .
1. Zmień typ *AuthenticationType na* `ApiKeyHeader` .
1. Zmień ciąg *AllowInsecureAuthInProduction na* `false` .
1. Bezpośrednio po elemencie `</Metadata>` zamykającym dodaj następujący fragment kodu XML:
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

Identyfikator **klucza** kryptograficznego definiuje nagłówek HTTP. W tym przykładzie klucz interfejsu API jest wysyłany jako **x-functions-key**.

Poniżej przedstawiono przykład profilu technicznego RESTful skonfigurowanego do wywołania funkcji platformy Azure z uwierzytelnianiem klucza interfejsu API:

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
        <Item Key="AuthenticationType">ApiKeyHeader</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [elemencie profilu technicznego Restful](restful-technical-profile.md) w dokumentacji IEF.
