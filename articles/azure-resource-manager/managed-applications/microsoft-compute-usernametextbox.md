---
title: UserNameTextBox — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. COMPUTE. UserNameTextBox dla Azure Portal. Umożliwia użytkownikom dostarczanie nazw użytkowników systemu Windows lub Linux.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c7544ae7d872a64547cb6c57ce8af9a09fc6c3d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75651906"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft. COMPUTE. UserNameTextBox — element interfejsu użytkownika

Kontrolka pola tekstowego z wbudowaną walidacją nazw użytkowników systemu Windows i Linux.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"Example name"
```

## <a name="remarks"></a>Uwagi

- Jeśli `constraints.required` ma wartość **true**, pole tekstowe musi mieć wartość, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **true**.
- `osPlatform`musi być określony i może być w **systemie Windows** lub **Linux**.
- `constraints.regex`jest wzorcem wyrażenia regularnego JavaScript. Jeśli jest określony, wartość pola tekstowego musi być zgodna ze wzorcem, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **null**.
- `constraints.validationMessage`jest ciągiem, który ma być wyświetlany, gdy wartość pola tekstowego nie powiedzie się `constraints.regex`walidacji określonej przez. Jeśli nie zostanie określony, będą używane wbudowane komunikaty weryfikacyjne pola tekstowego. Wartość domyślna to **null**.
- Ten element ma wbudowaną weryfikację opartą na wartości określonej dla `osPlatform`parametru. Wbudowanej walidacji można używać razem z niestandardowym wyrażeniem regularnym. Jeśli określono wartość dla `constraints.regex` parametru, wyzwalane są zarówno wbudowane, jak i niestandardowe walidacje.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
