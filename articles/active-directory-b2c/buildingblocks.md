---
title: BuildingBlocks
titleSuffix: Azure AD B2C
description: Określ element BuildingBlocks zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a42c1d06051c283f0e911c4cd166884ddd060f45
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633278"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **BuildingBlocks** jest dodawany wewnątrz elementu [TrustFrameworkPolicy](trustframeworkpolicy.md) .

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
    <DisplayControls>
      ...
    </DisplayControls>
 </BuildingBlocks>
```

Element **BuildingBlocks** zawiera następujące elementy, które muszą być określone w określonej kolejności:

- [ClaimsSchema](claimsschema.md) — definiuje typy roszczeń, do których można odwoływać się w ramach zasad. Schemat oświadczeń jest miejscem, w którym deklarujesz typy oświadczeń. Typ zgłoszenia jest podobny do zmiennej w wielu językach programistycznych. Typ oświadczenia służy do zbierania danych od użytkownika aplikacji, otrzymywania oświadczeń od dostawców tożsamości społecznościowych, wysyłania i odbierania danych z niestandardowego interfejsu API REST lub przechowywania wszelkich danych wewnętrznych używanych przez zasady niestandardowe.

- [Predykaty i PredicateValidationsInput](predicates.md) — umożliwiają wykonanie procesu walidacji w celu zapewnienia, że tylko prawidłowo uformowane dane są wprowadzane do żądania.

- [ClaimsTransformations](claimstransformations.md) — zawiera listę transformacji oświadczeń, które mogą być używane w zasadach.  Transformacja oświadczeń konwertuje jedno oświadczenie na inne. W transformacji oświadczeń należy określić metodę transformacji, taką jak:
  - Zmiana wielkości liter w ramach żądania ciągu na określony. Na przykład zmiana ciągu z małych liter na wielkie litery.
  - Porównanie dwóch oświadczeń i zwrócenie oświadczenia z wartością prawda wskazującą, że oświadczenia są zgodne, w przeciwnym razie false.
  - Tworzenie żądania ciągu z podanego parametru w zasadach.
  - Tworzenie losowego ciągu przy użyciu generatora liczb losowych.
  - Formatowanie roszczeń zgodnie z podanym ciągiem formatu. Ta transformacja używa metody języka C# `String.Format` .

- InputValidation — ten element umożliwia wykonywanie agregacji logicznych, które są podobne do *i* i *lub*.

- [ContentDefinitions](contentdefinitions.md) — zawiera adresy URL dla szablonów HTML5 do użycia w podróży użytkownika. W przypadku zasad niestandardowych definicja zawartości definiuje identyfikator URI strony HTML5, który jest używany przez określony krok w podróży użytkownika. Na przykład strony logowania lub rejestracji, resetowania haseł lub błędów. Możesz zmodyfikować wygląd i działanie, zastępując LoadUri pliku HTML5. Można też tworzyć nowe definicje zawartości zgodnie z potrzebami. Ten element może zawierać zlokalizowane odwołanie do zasobów przy użyciu identyfikatora lokalizacji.

- [Lokalizacja](localization.md) — umożliwia obsługę wielu języków. Obsługa lokalizacji w ramach zasad umożliwia skonfigurowanie listy obsługiwanych języków w zasadach i wybranie języka domyślnego. Obsługiwane są również ciągi i kolekcje charakterystyczne dla języka.

- [DisplayControls](display-controls.md) — definiuje kontrolki, które mają być wyświetlane na stronie. Kontrolki wyświetlania mają specjalne funkcje i współdziałają z profilami technicznymi weryfikacji zaplecza. 
