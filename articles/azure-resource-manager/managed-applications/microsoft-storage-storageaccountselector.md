---
title: StorageAccountSelector — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Storage. StorageAccountSelector dla Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 4fcaf0e5842ce8a65175d2fc1dfa2483a1203b2f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75651893"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Element interfejsu użytkownika Microsoft. Storage. StorageAccountSelector

Kontrolka służąca do wybierania nowego lub istniejącego konta magazynu.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

Kontrolka wyświetla wartość domyślną.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

Kontrolka umożliwia użytkownikowi utworzenie nowego konta magazynu lub wybranie istniejącego konta magazynu.

![Microsoft. Storage. StorageAccountSelector New](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="remarks"></a>Uwagi

- Jeśli jest określony, `defaultValue.name` jest automatycznie weryfikowany pod kątem unikatowości. Jeśli nazwa konta magazynu nie jest unikatowa, użytkownik musi określić inną nazwę lub wybrać istniejące konto magazynu.
- Wartość domyślna dla `defaultValue.type` jest **Premium_LRS**.
- Dowolny typ, który nie jest określony w elemencie `constraints.allowedTypes` jest ukryty, i dowolny typ, który nie jest określony w, `constraints.excludedTypes` jest pokazywany. `constraints.allowedTypes`i `constraints.excludedTypes` są opcjonalne, ale nie mogą być używane jednocześnie.
- Jeśli `options.hideExisting` ma **wartość true**, użytkownik nie może wybrać istniejącego konta magazynu. Wartość domyślna to **fałsz**.

## <a name="next-steps"></a>Następne kroki
* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
