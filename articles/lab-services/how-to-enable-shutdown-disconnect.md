---
title: Skonfiguruj automatyczne zamykanie maszyn wirtualnych dla laboratorium w Azure Lab Services
description: Informacje na temat włączania lub wyłączania automatycznego zamykania maszyn wirtualnych po odłączeniu połączenia z pulpitem zdalnym.
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 8f9080f3b7b762d3b9fa448a903a4167cd2cec4a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96433936"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>Konfigurowanie automatycznego zamykania maszyn wirtualnych dla laboratorium

W tym artykule pokazano, jak skonfigurować automatyczne zamykanie maszyn wirtualnych dla laboratorium.

Możesz włączyć kilka funkcji kontroli kosztów automatycznego zamykania, aby aktywnie uniknąć dodatkowych kosztów, gdy maszyny wirtualne nie są aktywnie używane. Kombinacja następujących trzech funkcji automatycznego zamykania i rozłączania przechwytuje większość przypadków, w których użytkownicy przypadkowo opuszczają maszyny wirtualne z systemem:
 
* Automatyczne rozłączanie użytkowników z maszyn wirtualnych uznawanych za bezczynne przez system operacyjny.
* Automatycznie zamykaj maszyny wirtualne podczas odłączania użytkowników.
* Automatycznie Zamykaj uruchomione maszyny wirtualne, ale użytkownicy nie łączą się.

Zapoznaj się z bardziej szczegółowymi informacjami na temat funkcji automatycznego zamykania w sekcji [Maksymalizuj koszt kontroli przy użyciu ustawień automatycznego zamykania](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) .

Administrator konta laboratorium może skonfigurować to ustawienie dla konta laboratorium, w którym tworzysz laboratoria. Aby uzyskać więcej informacji, zobacz [Konfigurowanie automatycznego zamykania maszyn wirtualnych dla konta laboratorium](how-to-configure-lab-accounts.md). Jako właściciel laboratorium możesz zastąpić to ustawienie podczas tworzenia laboratorium lub po utworzeniu laboratorium. 

## <a name="configure-for-the-lab-level"></a>Skonfiguruj dla poziomu laboratorium

Ustawienia automatycznego zamykania można skonfigurować w [Azure Lab Services](https://labs.azure.com/).

* podczas tworzenia laboratorium (w ramach **zasad Lab**) lub
* Po utworzeniu laboratorium (w **ustawieniach**)

> [!div class="mx-imgBorder"]
> ![Konfiguruj podczas tworzenia laboratorium](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

Upewnij się, że szczegółowe informacje o automatycznym zamknięciu znajdują się w sekcji [Maksymalizuj koszt kontroli przy użyciu ustawień automatycznego zamykania](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) .

> [!WARNING]
> W przypadku zamknięcia systemu operacyjnego Linux lub Windows na maszynie wirtualnej przed rozłączeniem sesji RDP z maszyną wirtualną funkcja Autozamykania nie będzie działać prawidłowo.  
## <a name="next-steps"></a>Następne kroki

[Pulpit nawigacyjny dla laboratoriów](use-dashboard.md)
