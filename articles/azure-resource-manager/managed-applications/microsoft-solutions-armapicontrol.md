---
title: ArmApiControl — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Solutions. ArmApiControl dla Azure Portal. Służy do wywoływania operacji interfejsu API.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: fdc299ef1945e3ee0810f1c314fc07edfb4f4873
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313304"
---
# <a name="microsoftsolutionsarmapicontrol-ui-element"></a>Microsoft. Solutions. ArmApiControl — element interfejsu użytkownika

ArmApiControl umożliwia uzyskanie wyników z Azure Resource Manager operacji interfejsu API. Użyj wyników, aby wypełnić zawartość dynamiczną w innych kontrolkach.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

Brak interfejsu użytkownika dla tej kontrolki.

## <a name="schema"></a>Schemat

W poniższym przykładzie przedstawiono schemat dla tego formantu:

```json
{
    "name": "testApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "{HTTP-method}",
        "path": "{path-for-the-URL}",
        "body": {
            "key1": "val1",
            "key2": "val2"
        }
    }
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

Dane wyjściowe kontrolki nie są wyświetlane użytkownikowi. Zamiast tego wynik operacji jest używany w innych kontrolkach.

## <a name="remarks"></a>Uwagi

- `request.method`Właściwość określa metodę http. `GET` `POST` Dozwolone są tylko lub.
- `request.path`Właściwość określa adres URL, który musi być ścieżką względną do punktu końcowego ARM. Może być ścieżką statyczną lub można ją utworzyć dynamicznie przez odwołując wartości wyjściowe innych kontrolek.

  Na przykład wywołanie usługi ARM do `Microsoft.Network/expressRouteCircuits` dostawcy zasobów:

  ```json
  "path": "<subid>/resourceGroup/<resourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<routecircuitName>/?api-version=2020-05-01"
  ```

- `request.body`Właściwość jest opcjonalna. Służy do określania treści JSON wysyłanej wraz z żądaniem. Treść może być zawartością statyczną lub być generowana dynamicznie przez odwołanie do wartości wyjściowych z innych kontrolek.

## <a name="example"></a>Przykład

W poniższym przykładzie `providersApi` element wywołuje interfejs API, aby uzyskać tablicę obiektów dostawcy.

`allowedValues`Właściwość `providersDropDown` elementu jest skonfigurowana do uzyskiwania nazw dostawców. Jest on wyświetlany na liście rozwijanej.

```json
{
    "name": "providersApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "GET",
        "path": "[concat(subscription().id, '/providers/Microsoft.Network/expressRouteServiceProviders?api-version=2019-02-01')]"
    }
},
{
    "name": "providerDropDown",
    "type": "Microsoft.Common.DropDown",
    "label": "Provider",
    "toolTip": "The provider that offers the express route connection.",
    "constraints": {
        "allowedValues": "[map(steps('settings').providersApi.value, (item) => parse(concat('{\"label\":\"', item.name, '\",\"value\":\"', item.name, '\"}')))]",
        "required": true
    },
    "visible": true
}
```

Przykład użycia ArmApiControl do sprawdzenia dostępności nazwy zasobu można znaleźć w temacie [Microsoft. Common. TextBox](microsoft-common-textbox.md).

## <a name="next-steps"></a>Następne kroki

- Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
- Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
