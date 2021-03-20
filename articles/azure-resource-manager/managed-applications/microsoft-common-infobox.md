---
title: InfoBox — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Common. InfoBox dla Azure Portal. Służy do dodawania tekstu lub ostrzeżeń podczas wdrażania aplikacji zarządzanej.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 7580ac0650706d6aee49bbf0e8235e8c5dab33f4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87033358"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Microsoft. Common. InfoBox — element interfejsu użytkownika

Kontrolka, która dodaje pole informacji. Pole zawiera ważny tekst lub ostrzeżenia, które pomagają użytkownikom zrozumieć, jakie wartości zapewniają. Może także połączyć się z identyfikatorem URI, aby uzyskać więcej informacji.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft. Common. InfoBox](./media/managed-application-elements/microsoft-common-infobox.png)


## <a name="schema"></a>Schemat

```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="remarks"></a>Uwagi

* Dla `icon` opcji, użyj wartości **none**, **info**, **Warning** lub **Error**.
* `uri`Właściwość jest opcjonalna.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
