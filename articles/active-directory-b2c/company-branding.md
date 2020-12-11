---
title: Dodawanie znakowania do strony logowania w organizacji
titleSuffix: Azure AD B2C
description: Dowiedz się, jak dodać znakowanie organizacji do stron Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: bee81876b066b9fc1ea69c418ee4d0839db27b3c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111495"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-b2c-pages"></a>Dodawanie znakowania do stron Azure Active Directory B2C organizacji

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Możesz dostosować Azure AD B2C strony za pomocą logo transparentu, obrazu tła i koloru tła, używając Azure Active Directory [znakowania firmowego](../active-directory/fundamentals/customize-branding.md). Znakowanie firmowe obejmuje rejestrowanie, logowanie, edytowanie profilów i resetowanie hasła. 

> [!TIP]
> Aby dostosować inne aspekty stron przepływu użytkownika poza logo transparentu, obrazem tła lub kolorem tła, zobacz jak [dostosować interfejs użytkownika przy użyciu szablonu HTML](customize-ui-with-html.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


Aby dostosować strony przepływu użytkownika, należy najpierw skonfigurować znakowanie firmowe w Azure Active Directory, a następnie włączyć je w układach stron przepływów użytkownika w Azure AD B2C.

## <a name="configure-company-branding"></a>Konfigurowanie oznaczenia marką firmy

Zacznij od ustawienia logo transparentu, obrazu tła i koloru tła w obszarze **znakowania firmowego**.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **Zarządzaj** wybierz opcję **znakowanie firmowe**.
1. Wykonaj kroki opisane w sekcji [Dodawanie znakowania do Azure Active Directory stronie logowania w organizacji](../active-directory/fundamentals/customize-branding.md).

Podczas konfigurowania znakowania firmowego w Azure AD B2C należy pamiętać o następujących kwestiach:

* Znakowanie firmowe w Azure AD B2C jest obecnie ograniczone do **obrazu tła**, **logo transparentu** i dostosowania **koloru tła** . Inne właściwości w okienku znakowania firmowego, na przykład **Ustawienia zaawansowane**, *nie są obsługiwane*.
* Na stronach przepływu użytkownika kolor tła jest wyświetlany przed załadowaniem obrazu tła. Zalecamy wybranie koloru tła, który ściśle dopasowuje kolory w obrazie tła w celu wygładzenia środowiska ładowania.
* Logo transparentu pojawia się w wiadomościach e-mail weryfikacyjnych wysyłanych do użytkowników po zainicjowaniu przepływu użytkownika podczas rejestracji.

::: zone pivot="b2c-user-flow"

## <a name="enable-branding-in-user-flow-pages"></a>Włącz znakowanie na stronach przepływu użytkownika

Po skonfigurowaniu znakowania firmowego włącz je w przepływach użytkownika.

1. W menu po lewej stronie Azure Portal wybierz pozycję **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **przepływy użytkownika (zasady)**.
1. Wybierz przepływ użytkownika, dla którego chcesz włączyć znakowanie firmowe. Znakowanie firmowe **nie jest obsługiwane** w przypadku standardowych typów przepływów *użytkowników* *logowania* standardowego i standardowego.
1. W obszarze **Dostosowywanie** wybierz pozycję **układy stron**, a następnie wybierz układ, który chcesz oznaczyć marką. Na przykład wybierz pozycję **ujednolicone rejestrowanie lub strona logowania**.
1. W polu **wersja układu strony (wersja zapoznawcza)** wybierz pozycję wersja **1.2.0** lub nowsza.
1. Wybierz pozycję **Zapisz**.

Jeśli chcesz oznaczyć wszystkie strony w przepływie użytkownika, ustaw wersję układu strony dla każdego układu strony w przepływie użytkownika.

![Wybór układu strony w Azure AD B2C Azure Portal](media/company-branding/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="select-a-page-layout"></a>Wybierz układ strony

Aby włączyć znakowanie firmowe, należy [zdefiniować wersję układu strony](contentdefinitions.md#migrating-to-page-layout) `contract` dla *wszystkich* definicji zawartości w niestandardowych zasadach. Format wartości musi zawierać słowo `contract` : _urn: com: Microsoft: AAD: B2C: elementy:**Contract**:p Age-Name: Version_. Aby określić układ strony w niestandardowych zasadach, które używają starej wartości **DataUri** .

Dowiedz się, jak przeprowadzić [migrację do układu strony](contentdefinitions.md#migrating-to-page-layout) z wersją strony.

W poniższym przykładzie przedstawiono identyfikatory definicji zawartości i odpowiadające im **DataUri** z umową strony: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

W poniższym przykładzie przedstawiono niestandardowe logo transparentu i obraz tła na stronie *rejestracja i logowanie w* przepływie użytkownika, która używa szablonu Ocean Blue:

![Markowe strony rejestracji/logowania obsługiwane przez Azure AD B2C](media/company-branding/template-ocean-blue-branded.png)

## <a name="use-company-branding-assets-in-custom-html"></a>Używanie zasobów znakowania firmowego w niestandardowym kodzie HTML

Aby korzystać z firmowych zasobów znakowania w [niestandardowym kodzie HTML](customize-ui-with-html.md), Dodaj następujące znaczniki poza `<div id="api">` tagiem:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

Źródło obrazu jest zastępowane obrazem tła i logo transparentu.

## <a name="next-steps"></a>Następne kroki

Więcej informacji o sposobach dostosowywania interfejsu użytkownika aplikacji można znaleźć w temacie [Dostosowywanie interfejsu użytkownika aplikacji w Azure Active Directory B2C](customize-ui-with-html.md).