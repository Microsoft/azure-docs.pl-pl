---
title: Element interfejsu użytkownika suwaka
description: Opisuje element interfejsu użytkownika Microsoft. Common. Slider dla Azure Portal. Umożliwia użytkownikom ustawianie wartości z zakresu opcji.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: tomfitz
ms.openlocfilehash: bb168a4bff6d2570f539307530232b5063bb535c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87098534"
---
# <a name="microsoftcommonslider-ui-element"></a>Microsoft. Common. Slider — element interfejsu użytkownika

Kontrolka suwaka pozwala użytkownikom wybierać z zakresu dozwolonych wartości.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

:::image type="content" source="./media/managed-application-elements/microsoft-common-slider.png" alt-text="Microsoft. Common. Slider":::

## <a name="schema"></a>Schemat

```json
{
    "name": "memorySize",
    "type": "Microsoft.Common.Slider",
    "min": 1,
    "max": 64,
    "label": "Memory",
    "subLabel": "MB",
    "defaultValue": 24,
    "showStepMarkers": false,
    "toolTip": "Pick the size in MB",
    "constraints": {
        "required": false
    },
    "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
26
```

## <a name="remarks"></a>Uwagi

- `min`Wartości i `max` są wymagane. Ustawili punkty początkowe i końcowe dla suwaka.
- `showStepMarkers`Właściwość domyślna to true. Znaczniki kroku są wyświetlane tylko wtedy, gdy zakres od minimum do maksimum jest 100 lub mniejszy.


## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
