---
title: TextBlock — element interfejsu użytkownika
description: Opisuje element Microsoft. Common. TextBlock interfejsu użytkownika dla Azure Portal. Służy do dodawania tekstu do interfejsu.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: ba2c4d410891910c29ee1fda1065f8230ab171bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87063880"
---
# <a name="microsoftcommontextblock-ui-element"></a>Microsoft. Common. TextBlock — element interfejsu użytkownika

Kontrolka, która może służyć do dodawania tekstu do interfejsu portalu.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textblock.png)

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
