---
title: TagsByResource — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Common. TagsByResource dla Azure Portal. Użyj, aby zastosować znaczniki do zasobu podczas wdrażania.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: e730201812005a9b469a964e4acd081ebe86b100
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87063950"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Microsoft. Common. TagsByResource — element interfejsu użytkownika

Kontrolka służąca do kojarzenia [tagów](../management/tag-resources.md) z zasobami w ramach wdrożenia.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft-common-tagsbyresource.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>Uwagi

- Należy określić co najmniej jeden element w `resources` tablicy.
- Każdy element w `resources` musi być w pełni kwalifikowanym typem zasobu. Te elementy pojawiają się na liście rozwijanej **zasobów** i są taggable przez użytkownika.
- Dane wyjściowe kontrolki są formatowane w celu łatwego przypisywania wartości tagów w szablonie Azure Resource Manager. Aby uzyskać dane wyjściowe kontrolki w szablonie, należy dołączyć parametr w szablonie, jak pokazano w następującym przykładzie:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Dla każdego zasobu, który może być otagowany, przypisz Właściwość Tags do wartości parametru tego typu zasobu:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Użyj funkcji [if](../templates/template-functions-logical.md#if) podczas uzyskiwania dostępu do parametru tagsByResource. Umożliwia przypisanie pustego obiektu, gdy nie są przypisane żadne Tagi do danego typu zasobu.

## <a name="next-steps"></a>Następne kroki

- Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
- Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
