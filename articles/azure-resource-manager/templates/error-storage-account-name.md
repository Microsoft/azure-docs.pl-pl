---
title: Błędy nazw kont magazynu
description: Opisuje błędy, które można napotkać podczas określania nazwy konta magazynu.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 5b2706d8540ea38ef08bf7ca0f804e6811a93085
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76153976"
---
# <a name="resolve-errors-for-storage-account-names"></a>Rozwiązywanie problemów z nazwami kont magazynu

W tym artykule opisano błędy nazewnictwa, które mogą wystąpić podczas wdrażania konta magazynu.

## <a name="symptom"></a>Objaw

Jeśli nazwa konta magazynu zawiera niedozwolone znaki, zostanie wyświetlony komunikat o błędzie podobny do:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

W przypadku kont magazynu należy podać nazwę zasobu, który jest unikatowy w ramach platformy Azure. Jeśli nie podasz unikatowej nazwy, zostanie zwrócony błąd podobny do tego:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

W przypadku wdrożenia konta magazynu o takiej samej nazwie jak istniejące konto magazynu w ramach subskrypcji, ale podania innej lokalizacji zostanie wyświetlony komunikat o błędzie z informacją o tym, że konto magazynu już istnieje w innej lokalizacji. Usuń istniejące konto magazynu lub podaj tę samą lokalizację co istniejące konto magazynu.

## <a name="cause"></a>Przyczyna

Nazwy kont magazynu muszą mieć długość od 3 do 24 znaków i używać tylko cyfr i małych liter. Nazwa musi być unikatowa.

## <a name="solution"></a>Rozwiązanie

Upewnij się, że nazwa konta magazynu jest unikatowa. Można utworzyć unikatową nazwę, łącząc konwencję nazewnictwa z wynikiem funkcji [uniqueString](template-functions-string.md#uniquestring) .

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Upewnij się, że nazwa konta magazynu nie przekracza 24 znaków. Funkcja [uniqueString](template-functions-string.md#uniquestring) zwraca 13 znaków. Jeśli połączysz prefiks lub przyrostek z wynikiem **uniqueString** , podaj wartość zawierającą co najmniej 11 znaków.

```json
"parameters": {
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
    "description": "The value to use for starting the storage account name."
    }
  }
}
```

Upewnij się, że nazwa konta magazynu nie zawiera żadnych wielkich liter ani znaków specjalnych.