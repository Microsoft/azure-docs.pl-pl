---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67182944"
---
## <a name="update-properties"></a>Aktualizuj właściwości

Aby zaktualizować właściwości w zakotwiczenia, należy użyć `UpdateAnchorProperties()` metody. Jeśli co najmniej dwa urządzenia próbują zaktualizować właściwości dla tego samego zakotwiczenia w tym samym czasie, użyjemy optymistycznego modelu współbieżności. Oznacza to, że pierwszy zapis zostanie wygrany.  Wszystkie inne operacje zapisu będą mieć charakter "współbieżności": przed ponowieniem próby odświeżenie właściwości będzie wymagało użycia.
