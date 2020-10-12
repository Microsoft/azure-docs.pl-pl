---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183040"
---
### <a name="cacheskuname"></a>cacheSKUName

Warstwa cenowa nowej pamięci podręcznej platformy Azure dla Redis.

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

Szablon definiuje wartości, które są dozwolone dla tego parametru (podstawowa, standardowa lub Premium) i przypisuje wartość domyślną (podstawowa), jeśli nie określono żadnej wartości. Podstawowa oferuje pojedynczy węzeł z wieloma rozmiarami dostępnymi do 53 GB. Standard oferuje dwa węzły podstawowe/repliki o wielu rozmiarach dostępnych do 53 GB i 99,9% umowy SLA.

### <a name="cacheskufamily"></a>cacheSKUFamily

Rodzina dla jednostki SKU.

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

Rozmiar nowego wystąpienia usługi Azure cache for Redis.

W przypadku rodzin podstawowych i standardowych:

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

Pojemność pamięci podręcznej wartości Premium jest zdefiniowana tak samo, z tą różnicą, że dozwolone wartości są uruchamiane z przenoszącą od 1 do 5 zamiast od 0 do 6.

Szablon definiuje wartości całkowite dozwolone dla tego parametru (od 0 do 6 dla rodzin podstawowych i standardowych; od 1 do 5 dla rodziny Premium). Jeśli żadna wartość nie zostanie określona, szablon przypisze wartość domyślną 0 dla warstwy Podstawowa i standardowa, 1 dla warstwy Premium.

Wartości odpowiadają następującym rozmiarom pamięci podręcznej:

| Wartość | Basic i Standard<br>rozmiar pamięci podręcznej | Premium<br>rozmiar pamięci podręcznej |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250 MB (wartość domyślna)                 | nie dotyczy                   |
| 1     | 1 GB                             | 6 GB (wartość domyślna)        |
| 2     | 2,5 GB                           | 13 GB                 |
| 3     | 6 GB                             | 26 GB                 |
| 4     | 13 GB                            | 53 GB                 |
| 5     | 26 GB                            | 120 GB                |
| 6     | 53 GB                            | nie dotyczy                   |
