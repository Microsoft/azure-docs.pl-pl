---
title: MultiStorageAccountCombo — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Storage. MultiStorageAccountCombo dla Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 06412a1f08f1f242a3f3bd9be17b795ee09fcf9d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75651880"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Element interfejsu użytkownika Microsoft. Storage. MultiStorageAccountCombo

Grupa kontrolek do tworzenia kilku kont magazynu z nazwami, które zaczynają się od wspólnego prefiksu.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="remarks"></a>Uwagi

- Wartość dla `defaultValue.prefix` jest łączona z co najmniej jedną liczbą całkowitą w celu wygenerowania sekwencji nazw kont magazynu. Na `defaultValue.prefix` przykład jeśli jest to **sa** i `count` ma **2**, generowane są nazwy kont magazynu **SA1** i **SA2** . Nazwy wygenerowanego konta magazynu są sprawdzane automatycznie w celu zapewnienia unikatowości.
- Nazwy kont magazynu są generowane na podstawie lexicographically `count`. Na przykład, jeśli `count` wynosi 10, wówczas nazwy kont magazynu kończą się dwiema cyframi liczb całkowitych (01, 02, 03).
- Wartość domyślna dla `defaultValue.prefix` jest **równa null**, a `defaultValue.type` w przypadku **Premium_LRS**.
- Dowolny typ, który nie `constraints.allowedTypes` jest określony w elemencie jest ukryty, i dowolny `constraints.excludedTypes` typ, który nie jest określony w, jest pokazywany. `constraints.allowedTypes`i `constraints.excludedTypes` są opcjonalne, ale nie mogą być używane jednocześnie.
- Oprócz generowania nazw kont magazynu, `count` służy do ustawiania odpowiedniego mnożnika dla elementu. Obsługuje ona wartość statyczną, taką jak **2**, lub wartość dynamiczną z innego elementu, `[steps('step1').storageAccountCount]`na przykład. Wartość domyślna to **1**.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
