---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 72050ff4887642ba16d52948c1c1253ca01443c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102473732"
---
* Konwersja spowoduje ponowne uruchomienie maszyny wirtualnej, dlatego należy zaplanować migrację maszyn wirtualnych w ramach istniejącego wcześniej okna obsługi. 

* Konwersja jest nieodwracalna. 

* Należy pamiętać, że wszyscy użytkownicy z rolą [współautor maszyny wirtualnej](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) nie będą mogli zmienić rozmiaru maszyny wirtualnej (ponieważ mogą one zostać przebudowane). Wynika to z faktu, że maszyny wirtualne z dyskami zarządzanymi wymagają, aby użytkownik miał uprawnienie Microsoft. COMPUTE/disks/Write na dyskach systemu operacyjnego.

* Należy przetestować konwersję. Przeprowadź migrację testowej maszyny wirtualnej, zanim przeprowadzisz migrację w środowisku produkcyjnym.

* Podczas konwersji cofany jest przydział maszyny wirtualnej. Maszyna wirtualna otrzyma nowy adres IP przy jej ponownym uruchomieniu po konwersji. W razie potrzeby do maszyny wirtualnej można [przypisać statyczny adres IP](../articles/virtual-network/public-ip-addresses.md).

* Zapoznaj się z minimalną wersją agenta maszyny wirtualnej platformy Azure wymaganą do obsługi procesu konwersji. Aby uzyskać informacje na temat sprawdzania i aktualizowania wersji agenta, zobacz [minimalna wersja wsparcia dla agentów maszyny wirtualnej na platformie Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)
