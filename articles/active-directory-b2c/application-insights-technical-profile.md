---
title: Zdefiniuj profil techniczny Application Insights w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny Application Insights w zasadach niestandardowych w programie Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 77bb53e2605913fcee6999284acb04616efc53af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201416"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Zdefiniuj profil techniczny Application Insights w Azure AD B2C zasadach niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) obsługuje wysyłanie danych zdarzeń bezpośrednio do [Application Insights](../azure-monitor/app/app-insights-overview.md) przy użyciu klucza Instrumentacji dostarczonego do Azure AD B2C.  Profil techniczny Application Insights umożliwia uzyskanie szczegółowych i dostosowanych dzienników zdarzeń dla użytkowników w celu:

* Uzyskaj wgląd w zachowania użytkowników.
* Rozwiązywanie problemów z własnymi zasadami w programowaniu lub w środowisku produkcyjnym.
* Mierzenie wydajności.
* Utwórz powiadomienia z Application Insights.


## <a name="protocol"></a>Protokół

Atrybut **name** elementu **Protocol** musi być ustawiony na `Proprietary` . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu programu obsługi protokołu, który jest używany przez Azure AD B2C dla Application Insights: `Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Poniższy przykład przedstawia wspólny profil techniczny Application Insights. Inne Application Insights profile techniczne obejmują AzureInsights-Common w celu wykorzystania jej konfiguracji.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Oświadczenia wejściowe

Element **InputClaims** zawiera listę oświadczeń do wysłania do Application Insights. Możesz również zmapować nazwę swojego żądania na nazwę, która ma być wyświetlana w Application Insights. Poniższy przykład pokazuje, jak wysyłać telemetrii do Application Insights. Właściwości zdarzenia są dodawane za pomocą składni `{property:NAME}` , gdzie nazwa jest dodawana do zdarzenia. Wartość domyślna może być wartością statyczną lub wartością, która jest rozpoznawana przez jeden z obsługiwanych elementów [rozpoznawania](claim-resolver-overview.md).

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

Element **InputClaimsTransformations** może zawierać kolekcję elementów **InputClaimsTransformation** , które są używane do modyfikowania oświadczeń wejściowych lub generować nowe przed wysłaniem do Application Insights.

## <a name="persist-claims"></a>Utrwalanie oświadczeń

Element PersistedClaims nie jest używany.

## <a name="output-claims"></a>Oświadczenia wyjściowe

Elementy OutputClaims i OutputClaimsTransformations nie są używane.

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Element CryptographicKeys nie jest używany.


## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| InstrumentationKey| Tak | [Klucz instrumentacji](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)Application Insights, który będzie używany do rejestrowania zdarzeń. | 
| Deweloper| Nie | Wartość logiczna wskazująca, czy tryb dewelopera jest włączony. Możliwe wartości: `true` lub `false` (wartość domyślna). Te metadane sterują sposobem buforowania zdarzeń. W środowisku programistycznym z minimalnym woluminem zdarzeń włączenie trybu dewelopera powoduje natychmiastowe wysyłanie zdarzeń do Application Insights.|  
|DisableTelemetry |Nie |Wartość logiczna wskazująca, czy dane telemetryczne powinny być włączone. Możliwe wartości: `true` lub `false` (wartość domyślna).| 


## <a name="next-steps"></a>Następne kroki

- [Tworzenie zasobu Application Insights](../azure-monitor/app/create-new-resource.md)
- Dowiedz się, jak [śledzić zachowanie użytkowników w Azure Active Directory B2C przy użyciu Application Insights](analytics-with-application-insights.md)
