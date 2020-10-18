---
title: Element interfejsu użytkownika listy rozwijanej
description: Opisuje element interfejsu użytkownika Microsoft. Common. DropDown dla Azure Portal. Użyj, aby wybrać dostępne opcje podczas wdrażania aplikacji zarządzanej.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: b9b27a432776635290c7e8e796e84d8c1e0e8675
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168345"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft. Common. DropDown — element interfejsu użytkownika

Kontrolka wyboru z listą rozwijaną. Można zezwolić na wybór tylko jednego elementu lub wielu elementów. Opcjonalnie można również dołączyć opis do elementów.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

Element DropDown zawiera różne opcje, które określają jego wygląd w portalu.

Gdy tylko jeden element jest dozwolony do zaznaczania, formant jest wyświetlany jako:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="Microsoft. Common. DropDown — pojedynczy wybór":::

Po uwzględnieniu opisów kontrolka jest wyświetlana jako:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="Microsoft. Common. DropDown — pojedynczy wybór":::

W przypadku włączenia wyboru wielu elementów kontrolka dodaje opcję **Zaznacz wszystko** i zawiera pola wyboru umożliwiające wybranie więcej niż jednego elementu:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="Microsoft. Common. DropDown — pojedynczy wybór":::

Opisy można dołączać z włączoną opcją wyboru wiele.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="Microsoft. Common. DropDown — pojedynczy wybór":::

Po włączeniu filtrowania kontrolka zawiera pole tekstowe służące do dodawania wartości filtrowania.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="Microsoft. Common. DropDown — pojedynczy wybór":::

## <a name="schema"></a>Schemat

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
    "placeholder": "",
    "defaultValue": "Value two",
    "toolTip": "",
    "multiselect": true,  
    "selectAll": true,  
    "filter": true,  
    "filterPlaceholder": "Filter items ...",  
    "multiLine": true,  
    "defaultDescription": "A value for selection",  
    "constraints": {
        "allowedValues": [
            {
                "label": "Value one",
                "description": "The value to select for option 1.",
                "value": "one"
            },
            {
                "label": "Value two",
                "description": "The value to select for option 2.",
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

- Użyj `multiselect` , aby określić, czy użytkownicy mogą wybrać więcej niż jeden element.
- Domyślnie `selectAll` jest `true` włączona funkcja zaznaczania.
- `filter`Właściwość umożliwia użytkownikom wyszukiwanie w obrębie długiej listy opcji.
- Etykieta `constraints.allowedValues` jest wyświetlany tekst dla elementu, a jego wartość jest wartością wyjściową elementu, gdy jest zaznaczone.
- Jeśli jest określony, wartość domyślna musi być etykietą obecną w `constraints.allowedValues` . Jeśli nie zostanie określony, pierwszy element w `constraints.allowedValues` jest zaznaczony. Wartość domyślna to **null**.
- `constraints.allowedValues` musi zawierać co najmniej jeden element.
- Aby emulować wartość, która nie jest wymagana, Dodaj element z etykietą i wartością `""` (pusty ciąg) do `constraints.allowedValues` .
- `defaultDescription`Właściwość jest używana dla elementów, które nie mają opisu.
- `placeholder`Właściwość jest tekstem pomocy, który znika, gdy użytkownik rozpoczyna edycję. Jeśli `placeholder` i `defaultValue` są zdefiniowane, `defaultValue` ma pierwszeństwo i jest pokazywany.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
