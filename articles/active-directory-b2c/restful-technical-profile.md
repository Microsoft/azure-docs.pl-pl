---
title: Definiowanie profilu technicznego RESTful w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny RESTful w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e5aca04a649dfa5228d12737b21ef2ee2b14013b
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750458"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny RESTful w zasadach niestandardowych Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę integracji własnej usługi RESTful. Azure AD B2C wysyła dane do usługi RESTful w kolekcji oświadczeń wejściowych i odbiera dane z powrotem w kolekcji oświadczeń wyjściowych. Aby uzyskać więcej informacji, zobacz [integrowanie wymiany oświadczeń interfejsu API REST w zasadach niestandardowych Azure AD B2C](custom-policy-rest-api-intro.md).  

## <a name="protocol"></a>Protokół

Atrybut **name** elementu **Protocol** musi być ustawiony na `Proprietary` . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu programu obsługi protokołu, który jest używany przez Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` .

W poniższym przykładzie przedstawiono profil techniczny RESTful:

```xml
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Oświadczenia wejściowe

Element **InputClaims** zawiera listę oświadczeń do wysłania do interfejsu API REST. Możesz również zmapować nazwę swojego zgłoszenia na nazwę zdefiniowaną w interfejsie API REST. Poniższy przykład pokazuje mapowanie między zasadami i interfejsem API REST. **Dana** wartość tego żądania jest wysyłana do interfejsu API REST jako **FirstName**, a **nazwisko** jest wysyłane jako **LastName**. To **ustawienie jest ustawione na wartość** .

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

Element **InputClaimsTransformations** może zawierać kolekcję elementów **InputClaimsTransformation** , które są używane do modyfikowania oświadczeń wejściowych lub generować nowe przed wysłaniem do interfejsu API REST.

## <a name="send-a-json-payload"></a>Wyślij ładunek JSON

Profil techniczny interfejsu API REST umożliwia wysyłanie złożonego ładunku JSON do punktu końcowego.

Aby wysłać złożony ładunek JSON:

1. Kompiluj ładunek JSON przy użyciu transformacji oświadczeń [GenerateJson](json-transformations.md) .
1. W profilu technicznym interfejsu API REST:
    1. Dodaj transformację oświadczeń wejściowych z odwołaniem do `GenerateJson` transformacji oświadczeń.
    1. Ustaw `SendClaimsIn` opcję metadanych na `body`
    1. Ustaw `ClaimUsedForRequestPayload` opcję metadanych na nazwę żądania zawierającego ładunek JSON.
    1. W ramach żądania wejściowego Dodaj odwołanie do żądania wejściowego zawierającego ładunek JSON.

Poniższy przykład `TechnicalProfile` wysyła wiadomość e-mail weryfikacyjną przy użyciu usługi poczty e-mail innej firmy (w tym przypadku SendGrid).

```xml
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
    <Item Key="DefaultUserMessageIfRequestFailed">Cannot process your request right now, please try again later.</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>Oświadczenia wyjściowe

Element **OutputClaims** zawiera listę oświadczeń zwracanych przez interfejs API REST. Może być konieczne zamapowanie nazwy żądania zdefiniowanego w zasadach na nazwę zdefiniowaną w interfejsie API REST. Można również uwzględnić oświadczenia, które nie są zwracane przez dostawcę tożsamości interfejsu API REST, o ile atrybut ten jest ustawiony `DefaultValue` .

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

Poniższy przykład przedstawia zgłoszenie zwrócone przez interfejs API REST:

- **MembershipId** , która jest mapowana na nazwę żądania **loyaltyNumber** .

Profil techniczny zwraca również oświadczenia, które nie są zwracane przez dostawcę tożsamości:

- **LoyaltyNumberIsNew** , które ma wartość domyślną ustawioną na `true` .

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ServiceUrl | Tak | Adres URL punktu końcowego interfejsu API REST. |
| AuthenticationType | Tak | Typ uwierzytelniania wykonywanego przez dostawcę oświadczeń RESTful. Możliwe wartości: `None` , `Basic` , `Bearer` ,  `ClientCertificate` lub `ApiKeyHeader` . <br /><ul><li>`None`Wartość oznacza, że interfejs API REST jest anonimowy. </li><li>`Basic`Wartość oznacza, że interfejs API REST jest zabezpieczony za pomocą uwierzytelniania podstawowego protokołu HTTP. Tylko zweryfikowani użytkownicy, w tym Azure AD B2C, mogą uzyskiwać dostęp do interfejsu API. </li><li>`ClientCertificate`Wartość (zalecane) wskazuje, że interfejs API REST ogranicza dostęp przy użyciu uwierzytelniania za pomocą certyfikatu klienta. Do interfejsu API można uzyskać dostęp tylko do usług, które mają odpowiednie certyfikaty, na przykład Azure AD B2C. </li><li>`Bearer`Wartość wskazuje, że interfejs API REST ogranicza dostęp przy użyciu tokenu okaziciela OAuth2 klienta. </li><li>`ApiKeyHeader`Wartość wskazuje, że interfejs API REST jest zabezpieczony za pomocą nagłówka HTTP Key interfejsu API, takiego jak *x-Functions-Key*. </li></ul> |
| AllowInsecureAuthInProduction| Nie| Wskazuje, czy `AuthenticationType` można ustawić na `none` w środowisku produkcyjnym ( `DeploymentMode` [TrustFrameworkPolicy](trustframeworkpolicy.md) jest ustawiona na `Production` lub nie określono). Możliwe wartości: true lub false (wartość domyślna). |
| SendClaimsIn | Nie | Określa sposób, w jaki oświadczenia wejściowe są wysyłane do dostawcy oświadczeń RESTful. Możliwe wartości: `Body` (wartość domyślna) `Form` , `Header` , `Url` lub `QueryString` . `Body`Wartość jest wartością wejściową, która jest wysyłana w treści żądania w formacie JSON. `Form`Wartość jest wartością wejściową, która jest wysyłana w treści żądania w formacie wartości klucza "&". `Header`Wartość jest wartością wejściową, która jest wysyłana w nagłówku żądania. `Url`Wartość jest wartością wejściową, która jest wysyłana w adresie URL, na przykład https://{claim1}. przykład. com/{claim2}/{claim3}? { claim4} = {claim5}. `QueryString`Wartość jest wartością wejściową, która jest wysyłana w ciągu zapytania żądania. Zlecenia HTTP wywoływane przez poszczególne są następujące:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: Pobierz</li><li>`Url`: Pobierz</li><li>`QueryString`: Pobierz</li></ul> |
| ClaimsFormat | Nie | Obecnie nie są używane, można je zignorować. |
| ClaimUsedForRequestPayload| Nie | Nazwa żądania ciągu zawierającego ładunek do wysłania do interfejsu API REST. |
| Debugujmode | Nie | Uruchamia profil techniczny w trybie debugowania. Możliwe wartości: `true` , lub `false` (wartość domyślna). W trybie debugowania interfejs API REST może zwrócić więcej informacji. Zobacz sekcję [zwracającą komunikat o błędzie](#returning-validation-error-message) . |
| IncludeClaimResolvingInClaimsHandling  | Nie | W przypadku oświadczeń wejściowych i wyjściowych określa, czy w profilu technicznym znajduje się [rozpoznawanie oświadczeń](claim-resolver-overview.md) . Możliwe wartości: `true` , lub `false` (wartość domyślna). Jeśli chcesz użyć programu rozpoznawania oświadczeń w profilu technicznym, ustaw dla tej opcji wartość `true` . |
| ResolveJsonPathsInJsonTokens  | Nie | Wskazuje, czy profil techniczny rozwiązuje ścieżki JSON. Możliwe wartości: `true` , lub `false` (wartość domyślna). Użyj tych metadanych do odczytu danych z zagnieżdżonego elementu JSON. W [oświadczenie outputclaim](technicalprofiles.md#outputclaims)Ustaw `PartnerClaimType` element na ścieżkę JSON, który ma zostać wyprowadzony. Na przykład: `firstName.localized` , lub `data.0.to.0.email` .|
| UseClaimAsBearerToken| Nie| Nazwa żądania zawierającego token okaziciela.|

## <a name="error-handling"></a>Obsługa błędów

Poniższe metadane mogą służyć do konfigurowania komunikatów o błędach wyświetlanych podczas awarii interfejsu API REST. Komunikaty o błędach można [lokalizować](localization-string-ids.md#restful-service-error-messages).

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| DefaultUserMessageIfRequestFailed | Nie | Domyślny dostosowany komunikat o błędzie dla wszystkich wyjątków interfejsu API REST.|
| UserMessageIfCircuitOpen | Nie | Komunikat o błędzie, gdy interfejs API REST jest nieosiągalny. Jeśli nie zostanie określony, zostanie zwrócona wartość DefaultUserMessageIfRequestFailed. |
| UserMessageIfDnsResolutionFailed | Nie | Komunikat o błędzie dotyczący wyjątku rozpoznawania nazw DNS. Jeśli nie zostanie określony, zostanie zwrócona wartość DefaultUserMessageIfRequestFailed. | 
| UserMessageIfRequestTimeout | Nie | Komunikat o błędzie, gdy upłynął limit czasu połączenia. Jeśli nie zostanie określony, zostanie zwrócona wartość DefaultUserMessageIfRequestFailed. | 

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Jeśli typ uwierzytelniania jest ustawiony na `None` , element **CryptographicKeys** nie jest używany.

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Jeśli typ uwierzytelniania jest ustawiony na `Basic` , element **CryptographicKeys** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Tak | Nazwa użytkownika, która jest używana do uwierzytelniania. |
| BasicAuthenticationPassword | Tak | Hasło, które jest używane do uwierzytelniania. |

Poniższy przykład przedstawia profil techniczny z uwierzytelnianiem podstawowym:

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Jeśli typ uwierzytelniania jest ustawiony na `ClientCertificate` , element **CryptographicKeys** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Kolekcja | Tak | Certyfikat x509 (zestaw kluczy RSA) do uwierzytelniania. |

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

Jeśli typ uwierzytelniania jest ustawiony na `Bearer` , element **CryptographicKeys** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | Nie | Token okaziciela OAuth 2,0. |

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

Jeśli typ uwierzytelniania jest ustawiony na `ApiKeyHeader` , element **CryptographicKeys** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Nazwa nagłówka HTTP, na przykład `x-functions-key` , lub `x-api-key` . | Tak | Klucz, który jest używany do uwierzytelniania. |

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ApiKeyHeader</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-validation-error-message"></a>Zwracany komunikat o błędzie walidacji

Interfejs API REST może wymagać zwrócenia komunikatu o błędzie, takiego jak "nie znaleziono użytkownika w systemie CRM". Jeśli wystąpi błąd, interfejs API REST powinien zwrócić komunikat o błędzie HTTP 4xx, taki jak 400 (złe żądanie) lub 409 (konflikt) kod stanu odpowiedzi. Treść odpowiedzi zawiera komunikat o błędzie sformatowany w formacie JSON:

```json
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Wersja | Tak | Wersja interfejsu API REST. Na przykład: 1.0.1 |
| status | Tak | Musi być 409 |
| kod | Nie | Kod błędu od dostawcy punktu końcowego RESTful, który jest wyświetlany, gdy `DebugMode` jest włączony. |
| IdentyfikatorŻądania | Nie | Identyfikator żądania od dostawcy punktu końcowego RESTful, który jest wyświetlany, gdy `DebugMode` jest włączony. |
| userMessage | Tak | Komunikat o błędzie, który jest widoczny dla użytkownika. |
| developerMessage | Nie | Pełny opis problemu i sposób jego naprawy, który jest wyświetlany, gdy `DebugMode` jest włączony. |
| moreInfo | Nie | Identyfikator URI wskazujący na dodatkowe informacje, które są wyświetlane, gdy `DebugMode` jest włączone. |


Poniższy przykład pokazuje klasę języka C#, która zwraca komunikat o błędzie:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby zapoznać się z przykładami dotyczącymi korzystania z profilu technicznego RESTful:

- [Integruj wymianę oświadczeń interfejsu API REST w zasadach niestandardowych Azure AD B2C](custom-policy-rest-api-intro.md)
- [Wskazówki: integruje wymianę oświadczeń interfejsu API REST w trakcie Azure AD B2C użytkownika podczas sprawdzania poprawności danych wejściowych użytkownika](custom-policy-rest-api-claims-validation.md)
- [Przewodnik: Dodawanie wymiany oświadczeń interfejsu API REST do zasad niestandardowych w Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Zabezpieczanie usług interfejsu API REST](secure-rest-api.md)

