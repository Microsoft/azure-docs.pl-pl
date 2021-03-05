---
title: Element UI TextBox
description: Opisuje element UI Microsoft. Common. TextBox dla Azure Portal. Służy do dodawania niesformatowanego tekstu.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
ms.openlocfilehash: 8e4cfcc7fe46c19bf1e58ee5eadf1e0bb8c7bd8e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124333"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft. Common. TextBox — element interfejsu użytkownika

Element interfejsu użytkownika, który może służyć do dodawania niesformatowanego tekstu. `Microsoft.Common.TextBox`Element jest polem wejściowym jednowierszowym, ale obsługuje wielowierszowe dane wejściowe z `multiLine` właściwością.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

`TextBox`Element używa jednowierszowego lub wielowierszowego pola tekstowego.

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox.png" alt-text="Pole tekstowe Microsoft. Common. TextBox.":::

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox-multi-line.png" alt-text="Wielowierszowe pole tekstowe Microsoft. Common. TextBox.":::

## <a name="schema"></a>Schemat

```json
{
  "name": "nameInstance",
  "type": "Microsoft.Common.TextBox",
  "label": "Name",
  "defaultValue": "contoso123",
  "toolTip": "Use only allowed characters",
  "placeholder": "",
  "multiLine": false,
  "constraints": {
    "required": true,
    "validations": [
      {
        "regex": "^[a-z0-9A-Z]{1,30}$",
        "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
      },
      {
        "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
        "message": "Must start with 'contoso'."
      }
    ]
  },
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"contoso123"
```

## <a name="remarks"></a>Uwagi

- Użyj `toolTip` właściwości, aby wyświetlić tekst o elemencie po umieszczeniu kursora myszy nad symbolem informacji.
- `placeholder`Właściwość jest tekstem pomocy, który znika, gdy użytkownik rozpoczyna edycję. Jeśli `placeholder` i `defaultValue` są zdefiniowane, `defaultValue` ma pierwszeństwo i jest pokazywany.
- `multiLine`Właściwość jest wartością logiczną `true` lub `false` . Aby użyć wielowierszowego pola tekstowego, ustaw właściwość na `true` . Jeśli wielowierszowe pole tekstowe nie jest potrzebne, ustaw właściwość na `false` lub Wyklucz właściwość. W przypadku nowych wierszy dane wyjściowe JSON `\n` są wyświetlane dla kanału informacyjnego wiersza. Wielowierszowe pole tekstowe akceptuje `\r` znak powrotu karetki (CR) i `\n` dla wysuwu wiersza (LF). Na przykład wartość domyślna może zawierać `\r\n` do określenia CRLF.
- Jeśli `constraints.required` jest ustawiona na `true` , pole tekstowe musi mieć wartość, aby pomyślnie sprawdzić poprawność. Wartość domyślna to `false`.
- `validations`Właściwość jest tablicą, w której można dodać warunki do sprawdzania wartości podanej w polu tekstowym.
- `regex`Właściwość jest wzorcem wyrażenia regularnego JavaScript. Jeśli jest określony, wartość pola tekstowego musi być zgodna ze wzorcem, aby pomyślnie sprawdzić poprawność. Wartość domyślna to `null`. Aby uzyskać więcej informacji na temat składni wyrażeń regularnych, zobacz [krótkie informacje o wyrażeniach regularnych](/dotnet/standard/base-types/regular-expression-language-quick-reference).
- `isValid`Właściwość zawiera wyrażenie, którego wynikiem jest `true` lub `false` . W wyrażeniu definiujesz warunek określający, czy pole tekstowe jest prawidłowe.
- `message`Właściwość jest ciągiem, który ma być wyświetlany, gdy wartość pola tekstowego nie powiedzie się.
- Można określić wartość parametru `regex` when `required` jest ustawiona na `false` . W tym scenariuszu wartość nie jest wymagana, aby pole tekstowe zostało pomyślnie zweryfikowane. Jeśli jest określony, musi być zgodny ze wzorcem wyrażenia regularnego.

## <a name="examples"></a>Przykłady

W przykładach pokazano, jak używać jednowierszowego pola tekstowego i wielowierszowego pola tekstowego.

### <a name="single-line"></a>Jeden wiersz

Poniższy przykład używa pola tekstowego z kontrolką [Microsoft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md) , aby sprawdzić dostępność nazwy zasobu.

```json
"basics": [
  {
    "name": "nameApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
      "method": "POST",
      "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
      "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
    }
  },
  {
    "name": "txtStorageName",
    "type": "Microsoft.Common.TextBox",
    "label": "Storage account name",
    "constraints": {
      "validations": [
        {
          "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
          "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
        }
      ]
    }
  }
]
```

### <a name="multi-line"></a>Wiele linii

W tym przykładzie `multiLine` właściwość służy do tworzenia wielowierszowego pola tekstowego z tekstem zastępczym.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "demoTextBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Multi-line text box",
        "defaultValue": "",
        "toolTip": "Use 1-60 alphanumeric characters, hyphens, spaces, periods, and colons.",
        "placeholder": "This is a multi-line text box:\nLine 1.\nLine 2.\nLine 3.",
        "multiLine": true,
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z -.:\n]{1,60}$",
              "message": "Only 1-60 alphanumeric characters, hyphens, spaces, periods, and colons are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "textBox": "[basics('demoTextBox')]"
    }
  }
}
```

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [CreateUiDefinition.jsna potrzeby tworzenia środowiska aplikacji zarządzanej przez platformę Azure](create-uidefinition-overview.md).
- Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
