---
title: Wersje języka JavaScript i układu strony
titleSuffix: Azure AD B2C
description: Informacje o sposobie włączania języka JavaScript i używania wersji układu strony w programie Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6bb478038d398226db38dc20e49ed7a14e5d5d0a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592810"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Wersje języka JavaScript i układu strony w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

Azure AD B2C zawiera zestaw spakowanych zawartości zawierający kod HTML, CSS i JavaScript dla elementów interfejsu użytkownika w przepływach użytkownika i zasadach niestandardowych. Aby włączyć język JavaScript dla aplikacji:

::: zone pivot="b2c-user-flow"

* Wybierz [Układ strony](page-layout.md)
* Włącz ją w przepływie użytkownika przy użyciu Azure Portal
* Używanie [b2clogin.com](b2clogin.md) w żądaniach

::: zone-end

::: zone pivot="b2c-custom-policy"

* Wybierz [Układ strony](page-layout.md)
* Dodawanie elementu do [zasad niestandardowych](custom-policy-overview.md)
* Używanie [b2clogin.com](b2clogin.md) w żądaniach

::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="select-a-page-layout-version"></a>Wybierz wersję układu strony

Jeśli zamierzasz włączyć kod po stronie klienta JavaScript, elementy, które opierają się na języku JavaScript, muszą być niezmienne. Jeśli nie są one niezmienne, wszelkie zmiany mogą spowodować nieoczekiwane zachowanie na stronach użytkownika. Aby uniknąć tych problemów, Wymuś użycie układu strony i określ wersję układu strony, aby upewnić się, że definicje zawartości opartej na języku JavaScript są niezmienne. Nawet jeśli nie zamierzasz włączać języka JavaScript, możesz określić wersję układu strony dla stron.

::: zone pivot="b2c-user-flow"

Aby określić wersję układu strony dla stron przepływu użytkownika: 

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Wybierz zasady (na przykład "B2C_1_SignupSignin"), aby je otworzyć.
1. Wybierz pozycję **układy strony**. Wybierz **nazwę układu**, a następnie wybierz **wersję układu strony (wersja zapoznawcza)**.

Informacje o różnych wersjach układów stron można znaleźć w [dzienniku zmian wersji układu strony](page-layout.md).

![Ustawienia układu strony w portalu pokazujące listę rozwijaną wersji układu strony](media/javascript-and-page-layout/page-layout-version.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Wybierz [Układ strony](contentdefinitions.md#select-a-page-layout) dla elementów interfejsu użytkownika aplikacji.

Zdefiniuj [wersję układu strony](contentdefinitions.md#migrating-to-page-layout) z wersją strony `contract` dla *wszystkich* definicji zawartości w niestandardowych zasadach. Format wartości musi zawierać słowo `contract` : _urn: com: Microsoft: AAD: B2C: elementy:**Contract**:p Age-Name: Version_. Dowiedz się, jak przeprowadzić [migrację do układu strony](contentdefinitions.md#migrating-to-page-layout) z wersją strony.

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

## <a name="enable-javascript"></a>Włączanie języka JavaScript

::: zone pivot="b2c-user-flow"

We **właściwościach** przepływu użytkownika można włączyć język JavaScript. Włączenie języka JavaScript wymusza również użycie układu strony. Następnie można ustawić wersję układu strony dla przepływu użytkownika zgodnie z opisem w następnej sekcji.

![Strona właściwości przepływu użytkownika z wyróżnionym ustawieniem Włącz język JavaScript](media/javascript-and-page-layout/javascript-settings.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Wykonanie skryptu jest włączane przez dodanie elementu **ScriptExecution** do elementu [RelyingParty](relyingparty.md) .

1. Otwórz niestandardowy plik zasad. Na przykład *SignUpOrSignin.xml*.
1. Dodaj element **ScriptExecution** do elementu **RelyingParty** :

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
1. Zapisz i Przekaż plik.

::: zone-end

## <a name="guidelines-for-using-javascript"></a>Wskazówki dotyczące korzystania z języka JavaScript

Postępuj zgodnie z poniższymi wskazówkami, gdy dostosowujesz interfejs aplikacji przy użyciu języka JavaScript:

- Nie należy wiązać zdarzenia kliknięcia dla `<a>` elementów HTML.
- Nie przyjmujej zależności od kodu Azure AD B2C ani komentarzy.
- Nie zmieniaj kolejności ani hierarchii Azure AD B2C elementów HTML. Użyj zasad Azure AD B2C, aby kontrolować kolejność elementów interfejsu użytkownika.
- Można wywołać dowolną usługę RESTful z następującymi kwestiami:
    - Może być konieczne ustawienie CORS usługi RESTful, aby zezwalać na wywołania HTTP po stronie klienta.
    - Upewnij się, że usługa RESTful jest zabezpieczona i używa tylko protokołu HTTPS.
    - Nie używaj kodu JavaScript bezpośrednio, aby wywoływać Azure AD B2C punkty końcowe.
- Możesz osadzić kod JavaScript lub połączyć się z zewnętrznymi plikami JavaScript. W przypadku korzystania z zewnętrznego pliku JavaScript upewnij się, że używasz bezwzględnego adresu URL, a nie względnego adresu URL.
- Struktury JavaScript:
    - Azure AD B2C używa określonej wersji platformy jQuery. Nie dołączaj innej wersji jQuery. Użycie więcej niż jednej wersji na tej samej stronie powoduje problemy.
    - Korzystanie z RequireJS nie jest obsługiwane.
    - Większość platform języka JavaScript nie jest obsługiwana przez Azure AD B2C.
- Ustawienia Azure AD B2C mogą być odczytywane przez wywoływanie `window.SETTINGS` , `window.CONTENT` obiektów, takich jak bieżący język interfejsu użytkownika. Nie zmieniaj wartości tych obiektów.
- Aby dostosować Azure AD B2C komunikat o błędzie, należy użyć lokalizacji w zasadzie.
- Jeśli coś można osiągnąć przy użyciu zasad, zwykle jest to zalecany sposób.

## <a name="javascript-samples"></a>Przykłady skryptów w języku JavaScript

### <a name="show-or-hide-a-password"></a>Pokaż lub Ukryj hasło

Typowym sposobem, aby pomóc klientom przy rejestrowaniu się po sobie, jest umożliwienie im, aby mogli zobaczyć, co zostało wprowadzone jako hasło. Ta opcja pomaga użytkownikom w rejestrowaniu się, umożliwiając im łatwe wyświetlanie i poprawianie hasła w razie potrzeby. Każde pole typu hasło ma pole wyboru z etykietą **Pokaż hasło** .  Dzięki temu użytkownik może zobaczyć hasło w postaci zwykłego tekstu. Dołącz ten fragment kodu do szablonu rejestracji lub logowania dla strony z własną stroną:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Dodaj warunki użytkowania

Dołącz następujący kod do strony, na której chcesz dołączyć pole wyboru **warunki użytkowania** . To pole wyboru jest zwykle zbędne na stronach rejestracji konta lokalnego i konta społecznościowego.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

W kodzie Zastąp `termsOfUseUrl` link do umowy dotyczącej warunków użytkowania. Dla katalogu Utwórz nowy atrybut użytkownika o nazwie **termsOfUse** , a następnie Dołącz **termsOfUse** jako atrybut użytkownika.

## <a name="next-steps"></a>Następne kroki

Więcej informacji o sposobach dostosowywania interfejsu użytkownika aplikacji można znaleźć w temacie [Dostosowywanie interfejsu użytkownika aplikacji w Azure Active Directory B2C](customize-ui-with-html.md).
