---
title: TrustFrameworkPolicy — Azure Active Directory B2C | Microsoft Docs
description: Określ element TrustFrameworkPolicy zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a87b4c6b845006a9f9f3cf82815277c67c09bef0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178844"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Zasady niestandardowe są reprezentowane jako jeden lub więcej plików w formacie XML, które odnoszą się do siebie nawzajem w łańcuchu hierarchicznym. Elementy XML definiują elementy zasad, takie jak schemat oświadczeń, przekształcenia oświadczeń, definicje zawartości, dostawcy oświadczeń, profile techniczne, podróż użytkownika i kroki aranżacji. Każdy plik zasad jest zdefiniowany w elemencie **TrustFrameworkPolicy** najwyższego poziomu pliku zasad.

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


Element **TrustFrameworkPolicy** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Tak | Wersja schematu, która ma zostać użyta do wykonania zasad. Wartość musi być równa `0.3.0.0` |
| TenantObjectId | Nie | Unikatowy identyfikator obiektu dzierżawy Azure Active Directory B2C (Azure AD B2C). |
| TenantId | Tak | Unikatowy identyfikator dzierżawy, do której należy ta zasada. |
| PolicyId | Tak | Unikatowy identyfikator zasad. Ten identyfikator musi być poprzedzony *B2C_1A_* |
| PublicPolicyUri | Tak | Identyfikator URI dla zasad, który jest kombinacją identyfikatora dzierżawy i identyfikatora zasad. |
| DeploymentMode | Nie | Możliwe wartości: `Production` , lub `Development` . Wartość domyślna to `Production`. Ta właściwość służy do debugowania zasad. Aby uzyskać więcej informacji, zobacz [zbieranie dzienników](troubleshoot-with-application-insights.md). |
| UserJourneyRecorderEndpoint | Nie | Punkt końcowy, który jest używany do rejestrowania. Wartość musi być ustawiona na `urn:journeyrecorder:applicationinsights` Jeśli atrybut istnieje. Aby uzyskać więcej informacji, zobacz [zbieranie dzienników](troubleshoot-with-application-insights.md). |


Poniższy przykład pokazuje, jak określić element **TrustFrameworkPolicy** :

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Element **TrustFrameworkPolicy** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| BasePolicy| 0:1| Identyfikator zasad podstawowych. |
| [BuildingBlocks](buildingblocks.md) | 0:1 | Bloki konstrukcyjne zasad. |
| [ClaimsProviders](claimsproviders.md) | 0:1 | Kolekcja dostawców oświadczeń. |
| [UserJourneys](userjourneys.md) | 0:1 | Kolekcja podróży użytkownika. |
| [RelyingParty](relyingparty.md) | 0:1 | Definicja zasad jednostki uzależnionej. |

Aby dziedziczyć zasady z innych zasad, element **BasePolicy** musi być zadeklarowany w elemencie **TrustFrameworkPolicy** pliku zasad. Element **BasePolicy** jest odwołaniem do zasad podstawowych, z których pochodzą te zasady.

Element **BasePolicy** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | --------|
| TenantId | 1:1 | Identyfikator dzierżawy Azure AD B2C. |
| PolicyId | 1:1 | Identyfikator zasad nadrzędnych. |


Poniższy przykład pokazuje, jak określić zasady podstawowe. Te zasady **B2C_1A_TrustFrameworkExtensions** pochodzą z zasad **B2C_1A_TrustFrameworkBaseymi** .

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

