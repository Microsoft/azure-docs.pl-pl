---
title: Przykłady skryptów w języku JavaScript
titleSuffix: Azure AD B2C
description: Dowiedz się więcej o używaniu języka JavaScript w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.custom: devx-track-js
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b6b4fff4a58685873e99332b00864ee95f569416
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92628596"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Przykłady kodu JavaScript do użycia w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Możesz dodać własny kod po stronie klienta JavaScript do aplikacji Azure Active Directory B2C (Azure AD B2C).

Aby włączyć język JavaScript dla aplikacji:

* Dodawanie elementu do [zasad niestandardowych](custom-policy-overview.md)
* Wybierz [Układ strony](page-layout.md)
* Używanie [b2clogin.com](b2clogin.md) w żądaniach

W tym artykule opisano sposób zmiany zasad niestandardowych w celu włączenia wykonywania skryptu.

> [!NOTE]
> Jeśli chcesz włączyć język JavaScript dla przepływów użytkownika, zobacz [wersje JavaScript i układ strony w Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="select-a-page-layout"></a>Wybierz układ strony

* Wybierz [Układ strony](contentdefinitions.md#select-a-page-layout) dla elementów interfejsu użytkownika aplikacji.

    Jeśli zamierzasz używać języka JavaScript, musisz [zdefiniować wersję układu strony](contentdefinitions.md#migrating-to-page-layout) z `contract` wersją strony dla *wszystkich* definicji zawartości w niestandardowych zasadach. Dowiedz się, jak przeprowadzić [migrację do układu strony](contentdefinitions.md#migrating-to-page-layout) z wersją strony. 

## <a name="add-the-scriptexecution-element"></a>Dodaj element ScriptExecution

Wykonanie skryptu jest włączane przez dodanie elementu **ScriptExecution** do elementu [RelyingParty](relyingparty.md) .

1. Otwórz niestandardowy plik zasad. Na przykład *SignUpOrSignin.xml* .
2. Dodaj element **ScriptExecution** do elementu **UserJourneyBehaviors** **RelyingParty** :

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Zapisz i Przekaż plik.

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

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

Więcej informacji o sposobach dostosowywania interfejsu użytkownika aplikacji można znaleźć w temacie [Dostosowywanie interfejsu użytkownika aplikacji przy użyciu zasad niestandardowych w Azure Active Directory B2C](custom-policy-ui-customization.md).
