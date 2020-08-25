---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "67182944"
---
## <a name="update-properties"></a>Aktualizuj właściwości

Aby zaktualizować właściwości w zakotwiczenia, należy użyć `UpdateAnchorProperties()` metody. Jeśli co najmniej dwa urządzenia próbują zaktualizować właściwości dla tego samego zakotwiczenia w tym samym czasie, użyjemy optymistycznego modelu współbieżności. Oznacza to, że pierwszy zapis zostanie wygrany.  Wszystkie inne operacje zapisu będą mieć charakter "współbieżności": przed ponowieniem próby odświeżenie właściwości będzie wymagało użycia.
