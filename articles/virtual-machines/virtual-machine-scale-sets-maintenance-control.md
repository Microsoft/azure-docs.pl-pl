---
title: Omówienie kontroli konserwacji dla uaktualnień obrazów systemu operacyjnego w zestawach skalowania maszyn wirtualnych platformy Azure
description: Dowiedz się, jak sterować automatycznym uaktualnianiem obrazu systemu operacyjnego do zestawów skalowania maszyn wirtualnych platformy Azure przy użyciu funkcji kontroli konserwacji.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 55cbc5db534dd1b05f91a24e0104b1f2dc110547
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532708"
---
# <a name="preview-maintenance-control-for-azure-virtual-machine-scale-sets"></a>Wersja zapoznawcza: sterowanie konserwacją dla zestawów skalowania maszyn wirtualnych platformy Azure 

Zarządzaj [automatycznymi uaktualnieniami obrazu systemu operacyjnego](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) dla zestawów skalowania maszyn wirtualnych za pomocą kontroli konserwacji.

Kontrola konserwacji pozwala określić, kiedy mają być stosowane aktualizacje dysków systemu operacyjnego w zestawach skalowania maszyn wirtualnych przez łatwiejsze i bardziej przewidywalne środowisko. 

Konfiguracje konserwacji działają w ramach subskrypcji i grup zasobów.

Cały przepływ pracy sprowadza się do następujących kroków: 
- Utwórz konfigurację konserwacji.
- Skojarz zestaw skalowania maszyn wirtualnych z konfiguracją konserwacji.
- Włącz automatyczne uaktualnienia systemu operacyjnego.

> [!IMPORTANT]
> Kontrola konserwacji obrazów systemu operacyjnego w zestawach skalowania maszyn wirtualnych platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="limitations"></a>Ograniczenia

- Maszyny wirtualne muszą znajdować się w zestawie skalowania.
- Użytkownik musi mieć dostęp **współautora zasobów** .
- Czas trwania konserwacji musi wynosić 5 godzin lub dłużej w konfiguracji konserwacji.
- Cykl konserwacji musi być ustawiony na wartość "Day" w konfiguracji konserwacji


## <a name="management-options"></a>Opcje zarządzania

Konfiguracje konserwacji można tworzyć i zarządzać nimi przy użyciu dowolnej z następujących opcji:

- [Azure PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak kontrolować, kiedy konserwacja jest stosowana do zestawów skalowania maszyn wirtualnych platformy Azure przy użyciu kontroli konserwacji i programu PowerShell.

> [!div class="nextstepaction"]
> [Kontrola konserwacji zestawu skalowania maszyn wirtualnych przy użyciu programu PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)
