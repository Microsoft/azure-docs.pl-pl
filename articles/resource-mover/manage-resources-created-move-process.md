---
title: Zarządzanie zasobami utworzonymi podczas procesu przenoszenia maszyny wirtualnej w usłudze Azure Resource Recovery
description: Dowiedz się, jak zarządzać zasobami utworzonymi podczas procesu przenoszenia maszyny wirtualnej w obszarze przenoszenia zasobów platformy Azure
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 4ff1905eb164d004af69ce5b0df3278bf3a46884
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89670516"
---
# <a name="manage-resources-created-for-the-vm-move"></a>Zarządzanie zasobami utworzonymi podczas przenoszenia maszyny wirtualnej

W tym artykule opisano sposób zarządzania zasobami, które są tworzone jawnie przez [usługę Azure Resource przeprowadzki](overview.md) w celu ułatwienia procesu przenoszenia maszyny wirtualnej. 

Po przeniesieniu maszyn wirtualnych między regionami istnieje wiele zasobów utworzonych przez program do przenoszenia zasobów, które powinny zostać oczyszczone ręcznie.

## <a name="delete-resources-created-for-vm-move"></a>Usuwanie zasobów utworzonych do przeniesienia maszyny wirtualnej

Ręcznie usuń kolekcję przenoszenia i Site Recovery zasobów utworzonych dla maszyny wirtualnej.

1. W regionie źródłowym, z którego zostały przeniesione maszyny wirtualne, przejrzyj zasoby w grupie zasobów ```RegionMoveRG-<sourceregion>-<target-region>``` .
2. Sprawdź, czy maszyna wirtualna i wszystkie inne zasoby źródłowe w kolekcji przenoszenia zostały przeniesione/usunięte. Dzięki temu nie ma żadnych oczekujących zasobów, których używają.
2. Usuń te zasoby.

    - Nazwa kolekcji przenoszenia to ```movecollection-<sourceregion>-<target-region>``` .
    - Nazwa konta magazynu pamięci podręcznej to ```resmovecache<guid>```
    - Nazwa magazynu to ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Następne kroki

Spróbuj [przenieść maszynę wirtualną](tutorial-move-region-virtual-machines.md) do innego regionu za pomocą przenoszenia zasobów.