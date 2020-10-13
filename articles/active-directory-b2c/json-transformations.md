---
title: Przykłady transformacji oświadczeń JSON dla zasad niestandardowych
titleSuffix: Azure AD B2C
description: Przykłady transformacji oświadczeń JSON dla schematu programu Identity Experience Framework (IEF) Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 676b6abb28abf58287bfc9036ca907ae6a1ee192
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961293"
---
# <a name="json-claims-transformations"></a>Przekształcenia oświadczeń JSON

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady użycia przekształceń oświadczeń JSON schematu platformy Identity Experience w Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="generatejson"></a>GenerateJson

Aby wygenerować ciąg JSON, użyj wartości lub stałych. Ciąg ścieżki następujący zapis kropki jest używany do wskazania, gdzie wstawić dane do ciągu JSON. Po podzieleniu według kropek wszystkie liczby całkowite są interpretowane jako indeks tablicy JSON, a wartości inne niż liczby całkowite są interpretowane jako indeks obiektu JSON.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Dowolny ciąg następujący po kropce | ciąg | Wykryto pliku JSON, w którym zostanie wstawiona wartość żądania. |
| InputParameter | Dowolny ciąg następujący po kropce | ciąg | Wykryto w formacie JSON, w którym zostanie wstawiona stała wartość ciągu. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | ciąg | Wygenerowany ciąg JSON. |

### <a name="example-1"></a>Przykład 1

Poniższy przykład generuje ciąg JSON na podstawie wartości "e-mail" i "OTP" oraz ciągów stałych.

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-4c56ffb40fa648b1aa6822283df94f60"/>
    <InputParameter Id="from.email" DataType="string" Value="service@contoso.com"/>
    <InputParameter Id="personalizations.0.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

Następujące przekształcanie oświadczeń wyprowadza oświadczenie ciągu JSON, które będzie treścią żądania wysłanego do SendGrid (dostawca poczty e-mail innej firmy). Struktura obiektu JSON jest definiowana przez identyfikatory w notacji kropkowej obiektu InputParameters i TransformationClaimTypes InputClaims. Liczby w zapisie kropkowym oznaczają tablice. Wartości pochodzą z wartości InputClaims i właściwości InputParameters "".

- Oświadczenia wejściowe:
  - **adres e-mail**, personalizacje typów roszczeń przekształceń  **. 0. do. 0. adres e-mail**: " someone@example.com "
  - **uwierzytelnianie OTP**typu **personalizations.0.dynamic_template_data. OTP** "346349"
- Parametr wejściowy:
  - **template_id**: "d-4c56ffb40fa648b1aa6822283df94f60"
  - **z adresu e-mail**: " service@contoso.com "
  - **personalizacje. 0. podmiot** "kod weryfikacyjny E-mail konta Contoso"
- Zgłoszenie wyjściowe:
  - **elemencie requestbody**: wartość JSON

```json
{
  "personalizations": [
    {
      "to": [
        {
          "email": "someone@example.com"
        }
      ],
      "dynamic_template_data": {
        "otp": "346349",
        "verify-email" : "someone@example.com"
      },
      "subject": "Contoso account email verification code"
    }
  ],
  "template_id": "d-989077fbba9746e89f3f6411f596fb96",
  "from": {
    "email": "service@contoso.com"
  }
}
```

### <a name="example-2"></a>Przykład 2

Poniższy przykład generuje ciąg JSON na podstawie wartości, a także ciągów stałych.

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="customerEntity.email" />
    <InputClaim ClaimTypeReferenceId="objectId" TransformationClaimType="customerEntity.userObjectId" />
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="customerEntity.firstName" />
    <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="customerEntity.lastName" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="customerEntity.role.name" DataType="string" Value="Administrator"/>
    <InputParameter Id="customerEntity.role.id" DataType="long" Value="1"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

Następujące przekształcanie oświadczeń wyprowadza oświadczenie ciągu JSON, które będzie treścią żądania wysłanego do interfejsu API REST. Struktura obiektu JSON jest definiowana przez identyfikatory w notacji kropkowej obiektu InputParameters i TransformationClaimTypes InputClaims. Liczby w zapisie kropkowym oznaczają tablice. Wartości pochodzą z wartości InputClaims i właściwości InputParameters "".

- Oświadczenia wejściowe:
  - **adres e-mail**, typ zgłoszenia transformacji  **customerEntity. email**: " john.s@contoso.com "
  - **objectid**, typ **customerEntity. userObjectId** "01234567-89ab-cdef-0123-456789ABCDEF"
  - **objectid**, typ odszkodowania typu **customerEntity. FirstName** "Jan"
  - **objectid**, typ odszkodowania typu **customerEntity. LastName** "Kowalski"
- Parametr wejściowy:
  - **customerEntity.role.Name**: "Administrator"
  - **customerEntity.role.ID** 1
- Zgłoszenie wyjściowe:
  - **elemencie requestbody**: wartość JSON

```json
{
   "customerEntity":{
      "email":"john.s@contoso.com",
      "userObjectId":"01234567-89ab-cdef-0123-456789abcdef",
      "firstName":"John",
      "lastName":"Smith",
      "role":{
         "name":"Administrator",
         "id": 1
      }
   }
}
```

## <a name="getclaimfromjson"></a>GetClaimFromJson

Pobierz określony element z danych JSON.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputJson | ciąg | Elementy Claims, które są używane przez transformację oświadczeń do pobrania elementu. |
| InputParameter | claimToExtract | ciąg | Nazwa elementu JSON, który ma zostać wyodrębniony. |
| Oświadczenie outputclaim | extractedClaim | ciąg | Element ClaimType, który jest generowany po wywołaniu tej transformacji oświadczeń, jest wartością elementu określoną w parametrze wejściowym _claimToExtract_ . |

W poniższym przykładzie transformacja oświadczeń wyekstrahowano `emailAddress` element z danych JSON: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```xml
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **inputJson**: {"EmailAddress": " someone@example.com ", "DisplayName": "ktoś"}
- Parametr wejściowy:
    - **claimToExtract**: EmailAddress
- Oświadczenia wyjściowe:
  - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Pobierz listę określonych elementów z danych JSON.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | jsonSourceClaim | ciąg | Oświadczenia są używane przez transformację oświadczeń do pobierania oświadczeń. |
| InputParameter | errorOnMissingClaims | boolean | Określa, czy zgłosić błąd, jeśli brakuje jednego z oświadczeń. |
| InputParameter | includeEmptyClaims | ciąg | Określ, czy mają być dołączane puste oświadczenia. |
| InputParameter | jsonSourceKeyName | ciąg | Nazwa klucza elementu |
| InputParameter | jsonSourceValueName | ciąg | Nazwa wartości elementu |
| Oświadczenie outputclaim | Kolekcja | String, int, Boolean i DateTime |Lista oświadczeń do wyodrębnienia. Nazwa żądania powinna być taka sama jak określona w _jsonSourceClaim_ wejściowym. |

W poniższym przykładzie transformacja oświadczeń wyodrębnia następujące oświadczenia: adres e-mail (ciąg), displayName (String), membershipNum (int), Active (Boolean) i DataUrodzenia (DateTime) z danych JSON.

```json
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```xml
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```

- Oświadczenia wejściowe:
  - **jsonSourceClaim**: [{"Key": "e-mail", "value": " someone@example.com "}, {"Key": "DisplayName", "value": "ktoś"}, {"Key": "membershipNum", "value": 6353399}, {"Key": "Active", "value": true}, {"Key": "DataUrodzenia", "value": "1980-09-23T00:00:00Z"}]
- Parametry wejściowe:
    - **errorOnMissingClaims**: FAŁSZ
    - **includeEmptyClaims**: FAŁSZ
    - **jsonSourceKeyName**: klucz
    - **jsonSourceValueName**: wartość
- Oświadczenia wyjściowe:
  - **poczta e-mail**: " someone@example.com "
  - **DisplayName**: "ktoś"
  - **membershipNum**: 6353399
  - **aktywne**: prawda
  - **dataurodzenie**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Pobiera określony liczbowy (Long) element z danych JSON.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputJson | ciąg | Oświadczenia są używane przez transformację oświadczeń do uzyskania oświadczenia. |
| InputParameter | claimToExtract | ciąg | Nazwa elementu JSON do wyodrębnienia. |
| Oświadczenie outputclaim | extractedClaim | długi | Element ClaimType wygenerowany po tym ClaimsTransformation został wywołany, wartość elementu określona w parametrach wejściowych _claimToExtract_ . |

W poniższym przykładzie transformacja oświadczeń wyodrębnia `id` element z danych JSON.

```json
{
    "emailAddress": "someone@example.com",
    "displayName": "Someone",
    "id" : 6353399
}
```

```xml
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **inputJson**: {"EmailAddress": " someone@example.com ", "DisplayName": "ktoś", "id": 6353399}
- Parametry wejściowe
    - **claimToExtract**: Identyfikator
- Oświadczenia wyjściowe:
    - **extractedClaim**: 6353399

## <a name="getsingleitemfromjson"></a>GetSingleItemFromJson

Pobiera pierwszy element z danych JSON.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputJson | ciąg | Elementy Claims, które są używane przez transformację oświadczeń do pobierania elementu z danych JSON. |
| Oświadczenie outputclaim | key | ciąg | Pierwszy klucz elementu w formacie JSON. |
| Oświadczenie outputclaim | wartość | ciąg | Wartość pierwszego elementu w formacie JSON. |

W poniższym przykładzie transformacja oświadczeń wyodrębnia pierwszy element (imię i nazwisko) z danych JSON.

```xml
<ClaimsTransformation Id="GetGivenNameFromResponse" TransformationMethod="GetSingleItemFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="json" TransformationClaimType="inputJson" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="givenNameKey" TransformationClaimType="key" />
    <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **inputJson**: {"imięname": "Emilty", "LastName": "Kowalski"}
- Oświadczenia wyjściowe:
  - **klucz**: imię
  - **wartość**: Emilty


## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Pobiera pierwszy element z tablicy danych JSON.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputJsonClaim | ciąg | Elementy Claims, które są używane przez transformację oświadczeń do pobierania elementu z tablicy JSON. |
| Oświadczenie outputclaim | extractedClaim | ciąg | Element ClaimType, który jest generowany po wywołaniu tego ClaimsTransformation, jest pierwszym elementem w tablicy JSON. |

W poniższym przykładzie transformacja oświadczeń wyodrębnia pierwszy element (adres e-mail) z tablicy JSON  `["someone@example.com", "Someone", 6353399]` .

```xml
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **inputJsonClaim**: [" someone@example.com ", "ktoś", 6353399]
- Oświadczenia wyjściowe:
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Konwertuje dane XML do formatu JSON.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | xml | ciąg | Oświadczenia są używane przez transformację oświadczeń do konwersji danych z formatu XML na format JSON. |
| Oświadczenie outputclaim | json | ciąg | W formacie JSON jest wywoływana wartość oświadczenia, która jest generowana po wywołaniu tej ClaimsTransformation. |

```xml
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

W poniższym przykładzie transformacja oświadczeń konwertuje następujące dane XML do formatu JSON.

#### <a name="example"></a>Przykład
Dane wejściowe:

```xml
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Zgłoszenie wyjściowe:

```json
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```


