---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 9f7e2760ef8bf06a2e680dce90c323672ca9d491
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "66416068"
---
* Konwersja wymaga ponownego uruchomienia maszyny wirtualnej, więc należy zaplanować migrację maszyn wirtualnych podczas istniejącego okna obsługi. 

* Konwersja jest nieodwracalna. 

* Należy pamiętać, że wszyscy użytkownicy z rolą [współautor maszyny wirtualnej](../articles/role-based-access-control/built-in-roles.md#virtual-machine-contributor) nie będą mogli zmienić rozmiaru maszyny wirtualnej (ponieważ mogą one zostać przebudowane). Wynika to z faktu, że maszyny wirtualne z dyskami zarządzanymi wymagają, aby użytkownik miał uprawnienie Microsoft. COMPUTE/disks/Write na dyskach systemu operacyjnego.

* Należy przetestować konwersję. Przeprowadź migrację testowej maszyny wirtualnej, zanim przeprowadzisz migrację w środowisku produkcyjnym.

* Podczas konwersji cofany jest przydział maszyny wirtualnej. Maszyna wirtualna otrzyma nowy adres IP przy jej ponownym uruchomieniu po konwersji. W razie potrzeby do maszyny wirtualnej można [przypisać statyczny adres IP](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md).

* Zapoznaj się z minimalną wersją agenta maszyny wirtualnej platformy Azure wymaganą do obsługi procesu konwersji. Aby uzyskać informacje na temat sprawdzania i aktualizowania wersji agenta, zobacz [minimalna wersja wsparcia dla agentów maszyny wirtualnej na platformie Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)