---
title: Zarządzanie zasobami utworzonymi podczas procesu przenoszenia maszyny wirtualnej w usłudze Azure Resource Recovery
description: Dowiedz się, jak zarządzać zasobami utworzonymi podczas procesu przenoszenia maszyny wirtualnej w obszarze przenoszenia zasobów platformy Azure
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 7ad0e73a90e733af0dd752100ebc71908f68181a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388485"
---
# <a name="manage-resources-created-for-the-vm-move"></a>Zarządzanie zasobami utworzonymi podczas przenoszenia maszyny wirtualnej

W tym artykule opisano sposób zarządzania zasobami, które są tworzone jawnie przez [usługę Azure Resource przeprowadzki](overview.md) w celu ułatwienia procesu przenoszenia maszyny wirtualnej. 

Po przeniesieniu maszyn wirtualnych między regionami istnieje wiele zasobów utworzonych przez program do przenoszenia zasobów, które powinny zostać oczyszczone ręcznie.

## <a name="delete-resources-created-for-vm-move"></a>Usuwanie zasobów utworzonych do przeniesienia maszyny wirtualnej

Ręcznie usuń kolekcję przenoszenia i Site Recovery zasobów utworzonych dla maszyny wirtualnej.

1. Przejrzyj zasoby w grupie zasobów ```ResourceMoverRG-<sourceregion>-<target-region>``` .
2. Sprawdź, czy maszyna wirtualna i wszystkie inne zasoby źródłowe w kolekcji przenoszenia zostały przeniesione/usunięte. Dzięki temu nie ma żadnych oczekujących zasobów, których używają.
2. Usuń te zasoby.

    - Nazwa kolekcji przenoszenia to ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` .
    - Nazwa konta magazynu pamięci podręcznej to ```resmovecache<guid>```
    - Nazwa magazynu to ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Następne kroki

Spróbuj [przenieść maszynę wirtualną](tutorial-move-region-virtual-machines.md) do innego regionu za pomocą przenoszenia zasobów.
