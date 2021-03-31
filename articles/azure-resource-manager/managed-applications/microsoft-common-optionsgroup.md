---
title: Options — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Common. options dla Azure Portal. Umożliwia użytkownikom wybór opcji dostępnych podczas wdrażania aplikacji zarządzanej.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: aa73b4cbded98291a14792a7151df9fdfb885b53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87004198"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft. Common. options — element interfejsu użytkownika

Kontrolka w obszarze Opcje umożliwia użytkownikom wybranie jednej opcji z co najmniej dwóch opcji. Użytkownik może wybrać tylko jedną opcję.

> [!NOTE]
> W przeszłości ten formant renderuje opcje w poziomie. Teraz formant przedstawia opcje w pionie jako przyciski radiowe.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

:::image type="content" source="./media/managed-application-elements/microsoft-common-optionsgroup-2.png" alt-text="Microsoft.Common.OptionsGroup":::

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"two"
```

## <a name="remarks"></a>Uwagi

- Etykieta `constraints.allowedValues` jest wyświetlany tekst dla elementu, a jego wartość jest wartością wyjściową elementu, gdy jest zaznaczone.
- Jeśli jest określony, wartość domyślna musi być etykietą obecną w `constraints.allowedValues` . Jeśli nie zostanie określony, `constraints.allowedValues` Domyślnie zaznaczony jest pierwszy element w. Wartość domyślna to **null**.
- `constraints.allowedValues` musi zawierać co najmniej jeden element.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
