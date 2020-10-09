---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67182944"
---
## <a name="update-properties"></a>Aktualizuj właściwości

Aby zaktualizować właściwości w zakotwiczenia, należy użyć `UpdateAnchorProperties()` metody. Jeśli co najmniej dwa urządzenia próbują zaktualizować właściwości dla tego samego zakotwiczenia w tym samym czasie, użyjemy optymistycznego modelu współbieżności. Oznacza to, że pierwszy zapis zostanie wygrany.  Wszystkie inne operacje zapisu będą mieć charakter "współbieżności": przed ponowieniem próby odświeżenie właściwości będzie wymagało użycia.
