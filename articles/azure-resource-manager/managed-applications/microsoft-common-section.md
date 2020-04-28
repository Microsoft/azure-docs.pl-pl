---
title: Element interfejsu użytkownika sekcji
description: Opisuje element interfejsu użytkownika Microsoft. Common. Section dla Azure Portal. Służy do grupowania elementów w portalu na potrzeby wdrażania aplikacji zarządzanych.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 999a7386ee7c5b3ef76f87280cc2d0cd45f143cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652257"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft. Common. Section — element interfejsu użytkownika

Kontrolka, która grupuje co najmniej jeden element w ramach nagłówka.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Uwagi

- `elements`musi zawierać co najmniej jeden element i może zawierać wszystkie typy elementów z wyjątkiem `Microsoft.Common.Section`.
- Ten element nie obsługuje `toolTip` właściwości.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
Aby uzyskać dostęp do wartości wyjściowych elementów `elements`w, należy użyć [podstawowych ()](create-uidefinition-functions.md#basics) lub [czynności ()](create-uidefinition-functions.md#steps) i notacji kropkowej:

```json
steps('configuration').section1.text1
```

Elementy typu `Microsoft.Common.Section` nie mają samych wartości wyjściowych.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
