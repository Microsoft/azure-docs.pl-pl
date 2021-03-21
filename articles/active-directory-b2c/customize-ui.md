---
title: Dostosowywanie interfejsu użytkownika
titleSuffix: Azure AD B2C
description: Dowiedz się, jak dostosować interfejs użytkownika dla aplikacji korzystających z Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ecece3a00a788b67f6c831804bf5b00372fef93d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055864"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Dostosowywanie interfejsu użytkownika w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Znakowanie i dostosowywanie interfejsu użytkownika, który Azure Active Directory B2C (Azure AD B2C) są wyświetlane dla klientów, zapewnia bezproblemowe środowisko użytkownika w aplikacji. Do tych środowisk należą: rejestrowanie, logowanie, edytowanie profilów i resetowanie hasła. W tym artykule opisano Dostosowywanie stron Azure AD B2C przy użyciu szablonu strony i znakowania firmowego. 

> [!TIP]
> Aby dostosować inne aspekty stron przepływu użytkownika poza szablonem strony, logo transparentu, obraz tła lub kolor tła, zobacz jak [dostosować interfejs użytkownika przy użyciu szablonu HTML](customize-ui-with-html.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="overview"></a>Omówienie

Azure AD B2C udostępnić kilka wbudowanych szablonów, które można wybrać, aby umożliwić użytkownikom korzystanie ze stron o profesjonalnym wyglądzie. Te szablony stron mogą również stanowić punkt wyjścia do własnych dostosowań przy użyciu funkcji [znakowania firmowego](#company-branding) .

### <a name="ocean-blue"></a>Ocean niebieski

Przykład niebieskiego szablonu oceanu renderowany podczas rejestracji na stronie logowania:

![Niebieski szablon oceanu — zrzut ekranu](media/customize-ui/template-ocean-blue.png)

### <a name="slate-gray"></a>Szare

Przykład nieszarego szablonu renderowanego podczas rejestracji na stronie logowania:

![Zrzut ekranu przedstawiający szary szablon](media/customize-ui/template-slate-gray.png)

### <a name="classic"></a>Klasyczny

Przykładowy szablon klasyczny renderowany na stronie rejestracji rejestracji:

![Klasyczny zrzut ekranu szablonu](media/customize-ui/template-classic.png)

### <a name="company-branding"></a>Znakowanie firmowe

Możesz dostosować Azure AD B2C strony za pomocą logo transparentu, obrazu tła i koloru tła, używając Azure Active Directory [znakowania firmowego](../active-directory/fundamentals/customize-branding.md). Znakowanie firmowe obejmuje rejestrowanie, logowanie, edytowanie profilów i resetowanie hasła. 

W poniższym przykładzie przedstawiono stronę *Tworzenie konta i logowanie* przy użyciu niestandardowego logo, obrazu tła, przy użyciu niebieskiego szablonu oceanu:

![Markowe strony rejestracji/logowania obsługiwane przez Azure AD B2C](media/customize-ui/template-ocean-blue-branded.png)


## <a name="select-a-page-template"></a>Wybierz szablon strony

::: zone pivot="b2c-user-flow"

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **przepływy użytkownika**.
1. Wybierz przepływ użytkownika, który chcesz dostosować.
1. W obszarze **Dostosuj** w menu po lewej stronie wybierz pozycję **układy stron** , a następnie wybierz **szablon**.
    ![Lista rozwijana wybierania szablonu na stronie przepływu użytkownika Azure Portal](./media/customize-ui/select-page-template.png)

Po wybraniu szablonu wybrany szablon zostanie zastosowany do wszystkich stron w przepływie użytkownika. Identyfikator URI dla każdej strony jest widoczny w polu **niestandardowy identyfikator URI strony** .

::: zone-end

::: zone pivot="b2c-custom-policy"

Aby wybrać szablon strony, ustaw `LoadUri` element [definicji zawartości](contentdefinitions.md). Poniższy przykład pokazuje identyfikatory definicji zawartości i odpowiadające im `LoadUri` . 

Ocean niebieski:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Szare:

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/MSA/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/MSA/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/MSA/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/MSA/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/MSA/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```

Motyw 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/default/exception.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/default/idpSelector.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <LoadUri>~/tenant/templates/default/selfAsserted.cshtml</LoadUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/default/multifactor-1.0.0.cshtml</LoadUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end


## <a name="configure-company-branding"></a>Konfigurowanie oznaczenia marką firmy

Aby dostosować strony przepływu użytkownika, należy najpierw skonfigurować znakowanie firmowe w Azure Active Directory, a następnie włączyć je w przepływach użytkownika w Azure AD B2C.

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



## <a name="enable-company-branding-in-user-flow-pages"></a>Włącz znakowanie firmowe na stronach przepływu użytkownika

::: zone pivot="b2c-user-flow"

Po skonfigurowaniu znakowania firmowego włącz je w przepływach użytkownika.

1. W menu po lewej stronie Azure Portal wybierz pozycję **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **przepływy użytkownika (zasady)**.
1. Wybierz przepływ użytkownika, dla którego chcesz włączyć znakowanie firmowe. Znakowanie firmowe **nie jest obsługiwane** w przypadku standardowych typów przepływów *użytkowników* *logowania* standardowego i standardowego.
1. W obszarze **Dostosowywanie** wybierz pozycję **układy stron**, a następnie wybierz stronę, którą chcesz dodać do marki. Na przykład wybierz pozycję **ujednolicone rejestrowanie lub strona logowania**.
1. W polu **wersja układu strony (wersja zapoznawcza)** wybierz pozycję wersja **1.2.0** lub nowsza.
1. Wybierz pozycję **Zapisz**.

Jeśli chcesz oznaczyć wszystkie strony w przepływie użytkownika, ustaw wersję układu strony dla każdego układu strony w przepływie użytkownika.

![Wybór układu strony w Azure AD B2C Azure Portal](media/customize-ui/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Po skonfigurowaniu znakowania firmowego włącz je w zasadach niestandardowych. Skonfiguruj [wersję układu strony](contentdefinitions.md#migrating-to-page-layout) z wersją strony `contract` dla *wszystkich* definicji zawartości w niestandardowych zasadach. Format wartości musi zawierać słowo `contract` : _urn: com: Microsoft: AAD: B2C: elementy:**Contract**:p Age-Name: Version_. Aby określić układ strony w niestandardowych zasadach, które używają starej wartości **DataUri** . Aby uzyskać więcej informacji, zobacz [Migrowanie do układu strony](contentdefinitions.md#migrating-to-page-layout) z wersją strony.

Poniższy przykład pokazuje definicje zawartości z odpowiadającymi im kontraktem strony i szablonem *niebieskiej strony oceanu* : 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <LoadUri>~/tenant/templates/AzureBlue/exception.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <LoadUri>~/tenant/templates/AzureBlue/idpSelector.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <LoadUri>~/tenant/templates/AzureBlue/unified.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
     <LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <LoadUri>~/tenant/templates/AzureBlue/multifactor-1.0.0.cshtml</LoadUri>
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

## <a name="next-steps"></a>Następne kroki

Więcej informacji o sposobach dostosowywania interfejsu użytkownika aplikacji można znaleźć w temacie [Dostosowywanie interfejsu użytkownika aplikacji w Azure Active Directory B2C](customize-ui-with-html.md).
