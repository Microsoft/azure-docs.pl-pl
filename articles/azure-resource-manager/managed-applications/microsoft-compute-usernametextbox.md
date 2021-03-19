---
title: UserNameTextBox — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. COMPUTE. UserNameTextBox dla Azure Portal. Umożliwia użytkownikom dostarczanie nazw użytkowników systemu Windows lub Linux.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 82478f322e1df22bde50769b90f0424140920e9a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87063573"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft. COMPUTE. UserNameTextBox — element interfejsu użytkownika

Kontrolka pola tekstowego z wbudowaną walidacją nazw użytkowników systemu Windows i Linux.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft-compute-usernametextbox.png)

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
- `osPlatform` musi być określony i może być w **systemie Windows** lub **Linux**.
- `constraints.regex` jest wzorcem wyrażenia regularnego JavaScript. Jeśli jest określony, wartość pola tekstowego musi być zgodna ze wzorcem, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **null**.
- `constraints.validationMessage` jest ciągiem, który ma być wyświetlany, gdy wartość pola tekstowego nie powiedzie się walidacji określonej przez `constraints.regex` . Jeśli nie zostanie określony, będą używane wbudowane komunikaty weryfikacyjne pola tekstowego. Wartość domyślna to **null**.
- Ten element ma wbudowaną weryfikację opartą na wartości określonej dla parametru `osPlatform` . Wbudowanej walidacji można używać razem z niestandardowym wyrażeniem regularnym. Jeśli określono wartość dla parametru `constraints.regex` , wyzwalane są zarówno wbudowane, jak i niestandardowe walidacje.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
