---
title: Element UI TextBox
description: Opisuje element UI Microsoft. Common. TextBox dla Azure Portal. Służy do dodawania niesformatowanego tekstu.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 547b3ed84c8e4406b65ee8cf51c0db10b6878793
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87063828"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft. Common. TextBox — element interfejsu użytkownika

Kontrolka, która może służyć do edycji niesformatowanego tekstu.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textbox.png)

## <a name="schema"></a>Schemat

```json
{
    "name": "nameInstance",
    "type": "Microsoft.Common.TextBox",
    "label": "Name",
    "defaultValue": "contoso123",
    "toolTip": "Use only allowed characters",
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

- Jeśli `constraints.required` ma wartość **true**, pole tekstowe musi mieć wartość, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **fałsz**.
- `validations`Właściwość jest tablicą, w której można dodać warunki do sprawdzania wartości podanej w polu tekstowym.
- `regex`Właściwość jest wzorcem wyrażenia regularnego JavaScript. Jeśli jest określony, wartość pola tekstowego musi być zgodna ze wzorcem, aby pomyślnie sprawdzić poprawność. Wartość domyślna to **null**.
- `isValid`Właściwość zawiera wyrażenie, którego wynikiem jest wartość true lub false. W wyrażeniu definiujesz warunek określający, czy pole tekstowe jest prawidłowe.
- `message`Właściwość jest ciągiem, który ma być wyświetlany, gdy wartość pola tekstowego nie powiedzie się.
- Możliwe jest określenie wartości parametru `regex` when, `required` która jest ustawiona na **wartość false**. W tym scenariuszu wartość nie jest wymagana, aby pole tekstowe zostało pomyślnie zweryfikowane. Jeśli jest określony, musi być zgodny ze wzorcem wyrażenia regularnego.

## <a name="example"></a>Przykład

Poniższy przykład używa pola tekstowego z kontrolką [Microsoft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md) , aby sprawdzić dostępność nazwy zasobu.

```json
"basics": [
    {
        "name": "nameApi",
        "type": "Microsoft.Solutions.ArmApiControl",
        "request": {
            "method": "POST",
            "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
            "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
        }
    },
    {
        "name": "txtStorageName",
        "type": "Microsoft.Common.TextBox",
        "label": "Storage account name",
        "constraints": {
            "validations": [
                {
                    "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
                    "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
                }
            ]
        }
    }
]
```

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
