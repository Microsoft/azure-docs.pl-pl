---
title: JSON twierdzi, że przykłady transformacji dla zasad niestandardowych
titleSuffix: Azure AD B2C
description: JSON twierdzi, że przykłady transformacji dla schematu struktury środowiska tożsamości (IEF) usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b42c2a414333e7ed262441321a808fc45425fc3b
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756758"
---
# <a name="json-claims-transformations"></a>JSON żąda przekształceń

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady użycia przekształceń oświadczeń JSON schematu struktury środowiska tożsamości w usłudze Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="generatejson"></a>Generowanie Json

Użyj wartości oświadczeń lub stałych do wygenerowania ciągu JSON. Ciąg ścieżki po notacji kropki służy do wskazania, gdzie należy wstawić dane do ciągu JSON. Po podzieleniu przez kropki, wszelkie liczby całkowite są interpretowane jako indeks tablicy JSON i liczby inne niż liczby całkowite są interpretowane jako indeks obiektu JSON.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | Dowolny ciąg następujący po notacji kropkowej | ciąg | JsonPath JSON, gdzie wartość oświadczenia zostaną wstawione do. |
| Inputparameter | Dowolny ciąg następujący po notacji kropkowej | ciąg | JsonPath JSON, gdzie stała wartość ciągu zostaną wstawione do. |
| WynikClaim | outputClaim | ciąg | Wygenerowany ciąg JSON. |

Poniższy przykład generuje ciąg JSON na podstawie wartości oświadczenia "e-mail" i "otp", a także ciągów stałych.

```XML
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

### <a name="example"></a>Przykład

Następujące żądania transformacji wyprowadza oświadczenie ciągu JSON, który będzie treści żądania wysyłane do SendGrid (dostawcy poczty e-mail innej firmy). Struktura obiektu JSON jest zdefiniowana przez identyfikatory w notacji punktowej InputParameters i TransformationClaimTypes z InputClaims. Liczby w notacji kropkowej implikują tablice. Wartości pochodzą z InputClaims' wartości i InputParameters "Value" właściwości.

- Oświadczenia wejściowe:
  - **email**, konwersja typu oświadczenia **personalizacji.0.to.0.email**: " "someone@example.com
  - **otp**, personalizacja typu oświadczeń **transformacji.0.dynamic_template_data.otp** "346349"
- Parametr wejściowy:
  - **template_id**: "d-4c56ffb40fa648b1aa6822283df94f60"
  - **from.email**:service@contoso.com" "
  - **personalizations.0.subject** "Kod weryfikacyjny konta Contoso"
- Oświadczenie wyjściowe:
  - **requestBody**: Wartość JSON

```JSON
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

## <a name="getclaimfromjson"></a>GetClaimFromJson ( GetClaimFromJson )

Pobierz określony element z danych JSON.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputJson (wejścieJson) | ciąg | ClaimTypes, które są używane przez transformację oświadczeń, aby uzyskać element. |
| Inputparameter | claimToExtract | ciąg | nazwę elementu JSON, który ma zostać wyodrębniony. |
| WynikClaim | wyodrębnioneClaim | ciąg | ClaimType, który jest produkowany po tym transformacji oświadczeń został wywołany, wartość elementu określona w _claimToExtract_ parametru wejściowego. |

W poniższym przykładzie transformacja `emailAddress` oświadczeń wyodrębniła element z danych JSON:`{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
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
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- Parametr wejściowy:
    - **claimToExtract**: emailAddress
- Oświadczenia wyjściowe:
  - **wyodrębnioneKlam:**someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Pobierz listę określonych elementów z danych Json.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | jsonSourceClaim | ciąg | ClaimTypes, które są używane przez transformację oświadczeń, aby uzyskać oświadczenia. |
| Inputparameter | błądOnMissingClaims | wartość logiczna | Określa, czy błąd ma być zgłaszany, jeśli brakuje jednego z oświadczeń. |
| Inputparameter | obejmująmemptyClaims | ciąg | Określ, czy mają być uwzględniane puste oświadczenia. |
| Inputparameter | jsonSourceKeyName | ciąg | Nazwa klucza elementu |
| Inputparameter | jsonSourceValueName | ciąg | Nazwa wartości elementu |
| WynikClaim | Collection | ciąg, int, logiczny i datetime |Lista roszczeń do wyodrębnienia. Nazwa oświadczenia powinna być równa nazwa określona w _jsonSourceClaim_ oświadczenia wejściowego. |

W poniższym przykładzie transformacja oświadczeń wyodrębnia następujące oświadczenia: e-mail (ciąg), displayName (ciąg), membershipNum (int), active (boolean) i data urodzenia (datetime) z danych JSON.

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
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
  - **jsonSourceClaim**: [{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value": true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
- Parametry wejściowe:
    - **errorOnMissingClaims**: false
    - **obejmująEmptyClaims**: false
    - **jsonSourceKeyName**: klucz
    - **jsonSourceValueName**: wartość
- Oświadczenia wyjściowe:
  - **adres e-mail**: " "someone@example.com
  - **nazwa displayName**: "Ktoś"
  - **członkostwoNum**: 6353399
  - **aktywny**: prawda
  - **data urodzenia**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Pobiera określony element numeryczny (długi) z danych JSON.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputJson (wejścieJson) | ciąg | ClaimTypes, które są używane przez transformację oświadczeń, aby uzyskać oświadczenie. |
| Inputparameter | claimToExtract | ciąg | Nazwa elementu JSON do wyodrębnienia. |
| WynikClaim | wyodrębnioneClaim | long | ClaimType, który jest produkowany po tym ClaimsTransformation został wywołany, wartość elementu określony w _claimToExtract_ parametrów wejściowych. |

W poniższym przykładzie transformacja `id` oświadczeń wyodrębnia element z danych JSON.

```JSON
{
    "emailAddress": "someone@example.com",
    "displayName": "Someone",
    "id" : 6353399
}
```

```XML
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
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone", "id" : 6353399}
- Parametry wejściowe
    - **claimToExtract**: id
- Oświadczenia wyjściowe:
    - **wyodrębnioneClaim**: 6353399

## <a name="getsingleitemfromjson"></a>GetSingleItemFromjson ( GetSingleItemFromjson )

Pobiera pierwszy element z danych JSON.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputJson (wejścieJson) | ciąg | ClaimTypes, które są używane przez transformację oświadczeń, aby uzyskać element z danych JSON. |
| WynikClaim | key | ciąg | Pierwszy klucz elementu w JSON. |
| WynikClaim | value | ciąg | Pierwsza wartość elementu w JSON. |

W poniższym przykładzie transformacja oświadczeń wyodrębnia pierwszy element (podana nazwa) z danych JSON.

```XML
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
  - **inputJson**: {"givenName": "Emilty", "lastName": "Smith"}
- Oświadczenia wyjściowe:
  - **klucz:** givenName
  - **wartość**: Emilty


## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Pobiera pierwszy element z tablicy danych JSON.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputJsonClaim | ciąg | ClaimTypes, które są używane przez transformację oświadczeń, aby uzyskać element z tablicy JSON. |
| WynikClaim | wyodrębnioneClaim | ciąg | ClaimType, który jest produkowany po wywołaniu tego ClaimsTransformation, pierwszy element w tablicy JSON. |

W poniższym przykładzie transformacja oświadczeń wyodrębnia pierwszy element (adres `["someone@example.com", "Someone", 6353399]`e-mail) z tablicy JSON .

```XML
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
  - **inputJsonClaim**:someone@example.com[" ", "Ktoś", 6353399]
- Oświadczenia wyjściowe:
  - **wyodrębnioneKlam:**someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Konwertuje dane XML na format JSON.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | xml | ciąg | ClaimTypes, które są używane przez transformację oświadczeń do konwersji danych z XML do formatu JSON. |
| WynikClaim | json | ciąg | ClaimType, który jest produkowany po wywołaniu tego ClaimsTransformation, dane w formacie JSON. |

```XML
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
Oświadczenie wejściowe:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Oświadczenie wyjściowe:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```


