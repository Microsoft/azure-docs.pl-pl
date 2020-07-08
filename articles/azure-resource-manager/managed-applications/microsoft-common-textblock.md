---
title: TextBlock — element interfejsu użytkownika
description: Opisuje element Microsoft. Common. TextBlock interfejsu użytkownika dla Azure Portal. Służy do dodawania tekstu do interfejsu.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 418056cb149f4441bac49db839a0dba40c2bb42d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75652218"
---
# <a name="microsoftcommontextblock-ui-element"></a>Microsoft. Common. TextBlock — element interfejsu użytkownika

Kontrolka, która może służyć do dodawania tekstu do interfejsu portalu.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textblock.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Please provide the configuration values for your application.",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"Please provide the configuration values for your application. Learn more"
```

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
