---
title: Zarządzanie zasobami utworzonymi podczas procesu przenoszenia maszyny wirtualnej w usłudze Azure Resource Recovery
description: Dowiedz się, jak zarządzać zasobami utworzonymi podczas procesu przenoszenia maszyny wirtualnej w obszarze przenoszenia zasobów platformy Azure
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 6d6f56fb47bfdaadc6704e2a13ebcf0e893b8b1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90602357"
---
# <a name="manage-resources-created-for-the-vm-move"></a>Zarządzanie zasobami utworzonymi podczas przenoszenia maszyny wirtualnej

W tym artykule opisano sposób zarządzania zasobami, które są tworzone jawnie przez [usługę Azure Resource przeprowadzki](overview.md) w celu ułatwienia procesu przenoszenia maszyny wirtualnej. 

Po przeniesieniu maszyn wirtualnych między regionami istnieje wiele zasobów utworzonych przez program do przenoszenia zasobów, które powinny zostać oczyszczone ręcznie.

## <a name="delete-resources-created-for-vm-move"></a>Usuwanie zasobów utworzonych do przeniesienia maszyny wirtualnej

Ręcznie usuń kolekcję przenoszenia i Site Recovery zasobów utworzonych dla maszyny wirtualnej.

1. Przejrzyj zasoby w grupie zasobów ```ResourceMoverRG-<sourceregion>-<target-region>``` .
2. Sprawdź, czy maszyna wirtualna i wszystkie inne zasoby źródłowe w kolekcji przenoszenia zostały przeniesione/usunięte. Dzięki temu nie ma żadnych oczekujących zasobów, których używają.
2. Usuń te zasoby.

    - Nazwa kolekcji przenoszenia to ```movecollection-<sourceregion>-<target-region>``` .
    - Nazwa konta magazynu pamięci podręcznej to ```resmovecache<guid>```
    - Nazwa magazynu to ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Następne kroki

Spróbuj [przenieść maszynę wirtualną](tutorial-move-region-virtual-machines.md) do innego regionu za pomocą przenoszenia zasobów.