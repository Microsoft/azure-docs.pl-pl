---
title: Element interfejsu użytkownika sekcji
description: Opisuje element interfejsu użytkownika Microsoft. Common. Section dla Azure Portal. Służy do grupowania elementów w portalu na potrzeby wdrażania aplikacji zarządzanych.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 924aff8f2ba3d796b65f52494845f3b10018065c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87063979"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft. Common. Section — element interfejsu użytkownika

Kontrolka, która grupuje co najmniej jeden element w ramach nagłówka.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Common.Section](./media/managed-application-elements/microsoft-common-section.png)

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

- `elements` musi zawierać co najmniej jeden element i może zawierać wszystkie typy elementów z wyjątkiem `Microsoft.Common.Section` .
- Ten element nie obsługuje `toolTip` właściwości.

## <a name="sample-output"></a>Przykładowe dane wyjściowe
Aby uzyskać dostęp do wartości wyjściowych elementów w `elements` , należy użyć [podstawowych ()](create-ui-definition-referencing-functions.md#basics) lub [czynności ()](create-ui-definition-referencing-functions.md#steps) i notacji kropkowej:

```json
steps('configuration').section1.text1
```

Elementy typu `Microsoft.Common.Section` nie mają samych wartości wyjściowych.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
