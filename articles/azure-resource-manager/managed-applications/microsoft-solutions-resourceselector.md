---
title: ResourceSelector — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Solutions. ResourceSelector dla Azure Portal. Służy do pobierania listy istniejących zasobów.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: d4b21f092733f6ebb3c2bf5aa06c1d2782323730
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87099057"
---
# <a name="microsoftsolutionsresourceselector-ui-element"></a>Microsoft. Solutions. ResourceSelector — element interfejsu użytkownika

ResourceSelector umożliwia użytkownikom wybranie istniejącego zasobu z subskrypcji.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft. Solutions. ResourceSelector](./media/managed-application-elements/microsoft-solutions-resourceselector.png)

## <a name="schema"></a>Schemat

```json
{
    "name": "storageSelector",
    "type": "Microsoft.Solutions.ResourceSelector",
    "label": "Select storage accounts",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "options": {
        "filter": {
            "subscription": "onBasics",
            "location": "onBasics"
        }
    }
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"name": "{resource-name}",
"id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{resource-provider-namespace}/{resource-type}/{resource-name}",
"location": "{deployed-location}"
```

## <a name="remarks"></a>Uwagi

We `resourceType` Właściwości Podaj przestrzeń nazw dostawcy zasobów i nazwę typu zasobu dla zasobu, który ma zostać wyświetlony na liście.

`filter`Właściwość ogranicza dostępne opcje dla zasobów. Możesz ograniczyć wyniki według lokalizacji lub subskrypcji. Aby wyświetlić tylko te zasoby, które pasują do wyboru w obszarze podstawy, użyj `onBasics` . Aby wyświetlić wszystkie zasoby, użyj `all` . Wartość domyślna to `all`.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
