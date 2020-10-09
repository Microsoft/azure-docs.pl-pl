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
ms.openlocfilehash: 29eddbcfb7c0da98e5438f968dd3976b77a44680
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85203099"
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
| UserJourneyRecorderEndpoint | Nie | Punkt końcowy, który jest używany, gdy **DeploymentMode** ma ustawioną wartość `Development` . Wartość musi być `urn:journeyrecorder:applicationinsights` . Aby uzyskać więcej informacji, zobacz [zbieranie dzienników](troubleshoot-with-application-insights.md). |


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

## <a name="inheritance-model"></a>Model dziedziczenia

Te typy plików zasad są zwykle używane w podróży użytkownika:

- Plik **podstawowy** , który zawiera większość definicji. Aby ułatwić rozwiązywanie problemów i długoterminową konserwację zasad, zaleca się dokonanie minimalnej liczby zmian w tym pliku.
- Plik **rozszerzeń** , który przechowuje unikatowe zmiany konfiguracji dla Twojej dzierżawy. Ten plik zasad pochodzi od pliku podstawowego. Użyj tego pliku, aby dodać nowe funkcje lub zastąpić istniejące funkcje. Na przykład użyj tego pliku do sfederować z nowymi dostawcami tożsamości.
- Plik **jednostki uzależnionej (RP)** , który jest pojedynczym plikiem skoncentrowanym na zadaniach, który jest wywoływany bezpośrednio przez aplikację jednostki uzależnionej, taką jak aplikacje internetowe, mobilne lub klasyczne. Każde unikatowe zadanie, takie jak rejestrowanie lub logowanie, Resetowanie hasła lub edytowanie profilu, wymaga własnego pliku zasad RP. Ten plik zasad pochodzi z pliku rozszerzeń.

Aplikacja jednostki uzależnionej wywołuje plik zasad RP, aby wykonać określone zadanie. Na przykład, aby zainicjować przepływ logowania. Struktura środowiska tożsamości w Azure AD B2C dodaje wszystkie elementy najpierw z pliku podstawowego, a następnie z pliku rozszerzeń, a wreszcie z pliku zasad RP, aby utworzyć bieżące zasady. Elementy tego samego typu i nazwy w pliku RP przesłaniają te elementy w rozszerzeniach, a rozszerzenia przesłaniają bazę. Na poniższym diagramie przedstawiono relację między plikami zasad i aplikacjami jednostki uzależnionej.

![Diagram przedstawiający model dziedziczenia zasad struktury zaufania](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Model dziedziczenia jest następujący:

- Nadrzędne zasady i zasady podrzędne są tego samego schematu.
- Zasady podrzędne na dowolnym poziomie mogą dziedziczyć z zasad nadrzędnych i zwiększać je, dodając nowe elementy.
- Nie ma żadnego limitu liczby poziomów.

Aby uzyskać więcej informacji, zobacz Wprowadzenie [do zasad niestandardowych](custom-policy-get-started.md).

## <a name="base-policy"></a>Zasady podstawowe

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

## <a name="policy-execution"></a>Wykonywanie zasad

Aplikacja jednostki uzależnionej, taka jak aplikacja sieci Web, urządzeń przenośnych lub aplikacji klasycznej, wywołuje [zasady jednostki uzależnionej (RP)](relyingparty.md). Plik zasad RP wykonuje określone zadanie, takie jak logowanie, Resetowanie hasła lub edytowanie profilu. Zasady RP konfigurują listę oświadczeń odbieranych przez aplikację jednostki uzależnionej jako część wystawionego tokenu. Wiele aplikacji może korzystać z tych samych zasad. Wszystkie aplikacje otrzymują ten sam token z oświadczeniami, a użytkownik przechodzi przez tę samą podróż użytkownika. Pojedyncza aplikacja może używać wielu zasad.

Wewnątrz pliku zasad RP należy określić element **DefaultUserJourney** , który wskazuje na [UserJourney](userjourneys.md). Podróż użytkownika zwykle jest definiowana w zasadach podstawowych lub opartych na rozszerzeniach.

Zasady B2C_1A_signup_signin:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase lub B2C_1A_TrustFrameworkExtensionPolicy:

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Podróż użytkownika definiuje logikę biznesową, przez którą przechodzi użytkownik. Każda podróż użytkownika to zestaw kroków aranżacji, które wykonują serię akcji, w kolejności pod względem uwierzytelniania i zbierania informacji.

Plik zasad **SocialAndLocalAccounts** w [pakiecie początkowym](custom-policy-get-started.md#custom-policy-starter-pack) zawiera SignUpOrSignIn, ProfileEdit, PasswordReset użytkownika. Możesz dodać więcej podróży użytkowników dla innych scenariuszy, takich jak zmiana adresu e-mail lub łączenie i odłączanie konta społecznościowego.

Kroki aranżacji mogą wywołać [profil techniczny](technicalprofiles.md). Profil techniczny zapewnia platformę z wbudowanym mechanizmem do komunikowania się z różnymi typami stron. Na przykład profil techniczny może wykonywać następujące działania między innymi:

- Renderuj środowisko użytkownika.
- Zezwól użytkownikom na logowanie się przy użyciu konta społecznościowego lub firmowego, takiego jak Facebook, konto Microsoft, Google, Salesforce lub dowolnego innego dostawcy tożsamości.
- Skonfiguruj weryfikację telefonu dla usługi MFA.
- Odczytaj i Zapisz dane do i z magazynu tożsamości Azure AD B2C.
- Wywoływanie niestandardowej usługi interfejsu API RESTful.

![Diagram przedstawiający przepływ wykonywania zasad](./media/trustframeworkpolicy/custom-policy-execution.png)

 Element **TrustFrameworkPolicy** zawiera następujące elementy:

- BasePolicy jak określono powyżej
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
