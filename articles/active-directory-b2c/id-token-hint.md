---
title: Zdefiniuj profil techniczny wskazówki tokenu identyfikatora w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny wskazówki tokenu identyfikatora w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d77e145cabcef2931d5fe6e76599da7931e576e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97669163"
---
# <a name="define-an-id-token-hint-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny wskazówki tokenu identyfikatora w zasadach niestandardowych Azure Active Directory B2C

Azure AD B2C umożliwia aplikacjom jednostki uzależnionej wysyłanie przychodzącej JWT w ramach żądania autoryzacji OAuth2. Token JWT może być wystawiony przez aplikację jednostki uzależnionej lub dostawcę tożsamości i może przekazać wskazówkę dotyczącą użytkownika lub żądania autoryzacji. Azure AD B2C sprawdza poprawność podpisu, nazwy wystawcy oraz odbiorców tokenu i wyodrębnia z tokenu przychodzącego.

## <a name="use-cases"></a>Przypadki zastosowań

To rozwiązanie służy do wysyłania danych do Azure AD B2C hermetyzowanych przy użyciu jednego tokenu JWT. [Rozwiązanie z zaproszeniem do zaproszenia e-mail](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md), w którym administrator systemu może wysyłać podpisane zaproszenie do użytkowników, jest oparty na id_token_hint. Tylko użytkownicy z dostępem do wiadomości e-mail z zaproszeniem mogą utworzyć konto w katalogu.

## <a name="token-signing-approach"></a>Podejście do podpisywania tokenu

Za pomocą id_token_hint wystawca tokenów (aplikacja jednostki uzależnionej lub dostawca tożsamości) tworzy token, a następnie podpisuje go przy użyciu klucza podpisywania, aby potwierdzić, że token pochodzi z zaufanego źródła. Klucz podpisywania może być symetryczny lub asymetryczny. Kryptografia symetryczna lub Kryptografia klucza prywatnego używa wspólnego klucza tajnego do podpisywania i weryfikowania podpisu. Kryptografia asymetryczne lub Kryptografia klucza publicznego jest systemem kryptograficznym, który używa zarówno klucza prywatnego, jak i klucza publicznego. Klucz prywatny jest znany tylko wystawcy tokenu i służy do podpisywania tokenu. Klucz publiczny jest udostępniany zasadom Azure AD B2C w celu weryfikacji podpisu tokenu.

## <a name="token-format"></a>Format tokenu

Id_token_hint musi być prawidłowym tokenem JWT. W poniższej tabeli wymieniono oświadczenia, które są obowiązkowe. Dodatkowe oświadczenia są opcjonalne.

| Nazwa | Claim | Przykładowa wartość | Opis |
| ---- | ----- | ------------- | ----------- |
| Grupy odbiorców | `aud` | `a489fc44-3cc0-4a78-92f6-e413cd853eae` | Identyfikuje zamierzony odbiorcę tokenu. Odbiorcy to dowolny ciąg zdefiniowany przez wystawcę tokenu. Azure AD B2C sprawdza poprawność tej wartości i odrzuca token, jeśli nie jest zgodny.  |
| Wystawca | `iss` |`https://localhost` | Identyfikuje usługę tokenu zabezpieczającego (Wystawca tokenu). Wystawca jest umownym identyfikatorem URI zdefiniowanym przez wystawcę tokenu. Azure AD B2C sprawdza poprawność tej wartości i odrzuca token, jeśli nie jest zgodny.  |
| Czas wygaśnięcia | `exp` | `1600087315` | Godzina, o której token stał się nieprawidłowy, reprezentowane w czasie epoki. Azure AD B2C sprawdza poprawność tej wartości i odrzuca token, jeśli token wygasł.|
| Nie przed | `nbf` | `1599482515` | Godzina, o której token stał się ważny, reprezentowane w czasie epoki. Ta godzina jest zwykle taka sama jak godzina wystawienia tokenu. Azure AD B2C sprawdza poprawność tej wartości i odrzuca token, jeśli okres istnienia tokenu jest nieprawidłowy. |

 Następujący token jest przykładem prawidłowego tokenu identyfikatora:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "displayName": " John Smith",
  "userId": "john.s@contoso.com",
  "nbf": 1599482515,
  "exp": 1600087315,
  "iss": "https://localhost",
  "aud": "a489fc44-3cc0-4a78-92f6-e413cd853eae"
}
```

## <a name="protocol"></a>Protokół

Atrybut **name** elementu **Protocol** musi być ustawiony na `None` . Na przykład protokół dla **IdTokenHint_ExtractClaims** profilu technicznego to `None` :

```xml
<TechnicalProfile Id="IdTokenHint_ExtractClaims">
  <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
  <Protocol Name="None" />
  ...
```

Profil techniczny jest wywoływany z kroku aranżacji z typem `GetClaims` .

```xml
<OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
``` 

## <a name="output-claims"></a>Oświadczenia wyjściowe

Element **OutputClaims** zawiera listę oświadczeń, które mają zostać wyodrębnione z tokenu JWT. Może być konieczne zamapowanie nazwy żądania zdefiniowanego w zasadach na nazwę zdefiniowaną w tokenie JWT. Można również uwzględnić oświadczenia, które nie są zwracane przez token JWT, o ile atrybut jest ustawiony `DefaultValue` .

## <a name="metadata"></a>Metadane

Następujące metadane są istotne w przypadku korzystania z klucza symetrycznego. 

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| issuer | Tak | Identyfikuje usługę tokenu zabezpieczającego (Wystawca tokenu). Ta wartość musi być taka sama jak w przypadku żądania `iss` tokenu JWT. | 
| IdTokenAudience | Tak | Identyfikuje zamierzony odbiorcę tokenu. Musi być taka sama jak w przypadku żądania `aud` tokenu JWT. | 

Poniższe metadane są istotne w przypadku korzystania z klucza asymetrycznego. 

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| METADANE| Tak | Adres URL wskazujący na dokument konfiguracji wystawcy tokenów, który jest również znany jako OpenID Connect dobrze znany punkt końcowy konfiguracji.   |
| issuer | Nie | Identyfikuje usługę tokenu zabezpieczającego (Wystawca tokenu). Ta wartość może służyć do zastępowania wartości skonfigurowanej w metadanych i musi być taka sama jak w przypadku żądania `iss` tokenu JWT. |  
| IdTokenAudience | Nie | Identyfikuje zamierzony odbiorcę tokenu. Musi być taka sama jak w przypadku żądania `aud` tokenu JWT. |  

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Przy użyciu klucza symetrycznego element **CryptographicKeys** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| client_secret | Tak | Klucz kryptograficzny używany do weryfikowania podpisu tokenu JWT.|


## <a name="how-to-guide"></a>Przewodnik z instrukcjami

### <a name="issue-a-token-with-symmetric-keys"></a>Wystawianie tokenu przy użyciu kluczy symetrycznych

#### <a name="step-1-create-a-shared-key"></a>Krok 1. Utwórz klucz współużytkowany 

Utwórz klucz, którego można użyć do podpisania tokenu. Na przykład użyj poniższego kodu programu PowerShell do wygenerowania klucza.

```powershell
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$newClientSecret
```

Ten kod tworzy tajny ciąg, taki jak `VK62QTn0m1hMcn0DQ3RPYDAr6yIiSvYgdRwjZtU5QhI=` .

#### <a name="step-2-add-the-signing-key-to-azure-ad-b2c"></a>Krok 2. Dodaj klucz podpisywania do Azure AD B2C

Ten sam klucz, który jest używany przez wystawcę tokenu, musi zostać utworzony w kluczach zasad Azure AD B2C.  

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Na stronie Przegląd w obszarze **zasady** wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz **klucze zasad** 
1. Wybierz pozycję **ręcznie**.
1. W obszarze **Nazwa** Użyj `IdTokenHintKey` .  
   Prefiks `B2C_1A_` może zostać dodany automatycznie.
1. W polu **wpis tajny** wprowadź wcześniej wygenerowany klucz logowania.
1. W celu **użycia klucza** Użyj **szyfrowania**.
1. Wybierz przycisk **Utwórz**.
1. Upewnij się, że utworzono klucz `B2C_1A_IdTokenHintKey` .


#### <a name="step-3-add-the-id-token-hint-technical-profile"></a>Krok 3. Dodaj profil techniczny wskazówki tokenu identyfikatora

Poniższy profil techniczny sprawdza poprawność tokenu i wyodrębnia oświadczenia. 

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <Item Key="IdTokenAudience">a489fc44-3cc0-4a78-92f6-e413cd853eae</Item>
        <Item Key="issuer">https://localhost</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_IdTokenHintKey" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-4-prepare-your-policy"></a>Krok 4. Przygotowywanie zasad

Ukończ krok [konfigurowania zasad](#configure-your-policy) .

#### <a name="step-5-prepare-the-code"></a>Krok 5. Przygotuj kod  

[Przykład usługi GitHub](https://github.com/azure-ad-b2c/id_token_hint/tree/master/dotnet_core_symmetric_key) to aplikacja sieci Web ASP.NET i Aplikacja konsolowa, która GENERUJE token identyfikatora, który jest podpisany przy użyciu klucza symetrycznego. 


### <a name="issue-a-token-with-asymmetric-keys"></a>Wystawianie tokenu z kluczami asymetrycznymi

Za pomocą klucza asymetrycznego token jest podpisywany przy użyciu certyfikatów RSA. Ta aplikacja obsługuje punkt końcowy metadanych połączenia typu Open ID i klucz sieci Web JSON (JWKs), który jest używany przez Azure AD B2C do weryfikowania podpisu tokenu identyfikatora.

Wystawca tokenu musi podawać następujące punkty końcowe:

* `/.well-known/openid-configuration` -Dobrze znany punkt końcowy konfiguracji z odpowiednimi informacjami o tokenie, takimi jak nazwa wystawcy tokenu i link do punktu końcowego JWK. 
* `/.well-known/keys` -punkt końcowy JSON (JWK) z kluczem publicznym używanym do podpisywania klucza (z częściowym kluczem prywatnym certyfikatu).

Zobacz przykład [TokenMetadataController. cs](https://github.com/azure-ad-b2c/id-token-builder/blob/master/source-code/B2CIdTokenBuilder/Controllers/TokenMetadataController.cs) .NET MVC Controller.

#### <a name="step-1-prepare-a-self-signed-certificate"></a>Krok 1. Przygotuj certyfikat z podpisem własnym

Jeśli nie masz jeszcze certyfikatu, możesz użyć certyfikatu z podpisem własnym, aby zapoznać się z tym przewodnikiem. W systemie Windows można użyć polecenia cmdlet [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) programu PowerShell do wygenerowania certyfikatu.

Uruchom to polecenie programu PowerShell, aby wygenerować certyfikat z podpisem własnym. Zmodyfikuj `-Subject` argument jako odpowiedni dla aplikacji i Azure AD B2C nazwę dzierżawcy. Możesz również dostosować datę, `-NotAfter` Aby określić inne wygaśnięcie certyfikatu.

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


#### <a name="step-2-add-the-id-token-hint-technical-profile"></a>Krok 2. Dodaj profil techniczny wskazówki tokenu identyfikatora 

Poniższy profil techniczny sprawdza poprawność tokenu i wyodrębnia oświadczenia. Zmień identyfikator URI metadanych w dobrze znanym punkcie końcowym konfiguracji wystawcy tokenu.  

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <!-- Replace with your endpoint location -->
        <Item Key="METADATA">https://your-app.azurewebsites.net/.well-known/openid-configuration</Item>
        <Item Key="IdTokenAudience">your_optional_audience</Item> -->
        <!-- <Item Key="issuer">your_optional_token_issuer_override</Item> -->
      </Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-3-prepare-your-policy"></a>Krok 3. Przygotowywanie zasad

Ukończ krok [konfigurowania zasad](#configure-your-policy) .

#### <a name="step-4-prepare-the-code"></a>Krok 4. Przygotuj kod 

Ta aplikacja sieci Web usługi [GitHub sample](https://github.com/azure-ad-b2c/id-token-builder) ASP.NET generuje tokeny identyfikatorów i hostuje punkty końcowe metadanych wymagane do użycia parametru "id_token_hint" w Azure AD B2C.


### <a name="configure-your-policy"></a>Konfigurowanie zasad

W przypadku podejścia symetrycznego i asymetrycznego `id_token_hint` profil techniczny jest wywoływany z kroku aranżacji z typem `GetClaims` i musi określać oświadczenia wejściowe zasad jednostki uzależnionej.

1. Dodaj profil techniczny IdTokenHint_ExtractClaims do zasad rozszerzenia.
1. Dodaj następujący krok aranżacji do podróży użytkownika jako pierwszy element.  

    ```xml
    <OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
    ``` 
1. W zasadach jednostki uzależnionej Powtórz te same oświadczenia wejściowe, które zostały skonfigurowane w profilu technicznym IdTokenHint_ExtractClaims. Na przykład:
    ```xml
   <RelyingParty>
     <DefaultUserJourney ReferenceId="SignUp" />
     <TechnicalProfile Id="PolicyProfile">
       <DisplayName>PolicyProfile</DisplayName>
       <Protocol Name="OpenIdConnect" />
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
        </InputClaims>
       <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
       </OutputClaims>
       <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

W zależności od wymagań firmy może być konieczne dodanie walidacji tokenów, na przykład sprawdź format adresu e-mail. Aby to zrobić, Dodaj kroki aranżacji, które wywołują [profil techniczny przekształcenia oświadczeń](claims-transformation-technical-profile.md). Należy również dodać [profil techniczny z własnym potwierdzeniem](self-asserted-technical-profile.md) , aby przedstawić komunikat o błędzie. 

### <a name="create-and-sign-a-token"></a>Tworzenie i podpisywanie tokenu

Przykłady w witrynie GitHub przedstawiają sposób tworzenia takiego tokenu, który wystawił token JWT, który później został wysłany jako `id_token_hint` parametr ciągu zapytania. Poniżej znajduje się przykład żądania autoryzacji z parametrem id_token_hint
 
```html
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&id_token_hint=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkaXNwbGF5TmFtZSI6IiBKb2huIFNtaXRoIiwidXNlcklkIjoiam9obi5zQGNvbnRvc28uY29tIiwibmJmIjoxNTk5NDgyNTE1LCJleHAiOjE2MDAwODczMTUsImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0IiwiYXVkIjoiYTQ4OWZjNDQtM2NjMC00YTc4LTkyZjYtZTQxM2NkODUzZWFlIn0.nPmLXydI83PQCk5lRBYUZRu_aX58pL1khahHyQuupig
```

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z rozwiązaniem do [rejestracji przy użyciu zaproszenia do obsługi poczty e-mail](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md) w Azure AD B2C repozytorium GitHub społeczności.
