---
title: Skonfiguruj automatyczne zamykanie maszyn wirtualnych dla laboratorium w Azure Lab Services
description: Informacje na temat włączania lub wyłączania automatycznego zamykania maszyn wirtualnych po odłączeniu połączenia z pulpitem zdalnym.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 7941a3eed502f2329f5d2acf244eec7cef322615
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589773"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>Konfigurowanie automatycznego zamykania maszyn wirtualnych dla laboratorium

W tym artykule pokazano, jak skonfigurować automatyczne zamykanie maszyn wirtualnych dla laboratorium.

Możesz włączyć kilka funkcji kontroli kosztów automatycznego zamykania, aby aktywnie uniknąć dodatkowych kosztów, gdy maszyny wirtualne nie są aktywnie używane. Kombinacja następujących trzech funkcji automatycznego zamykania i rozłączania przechwytuje większość przypadków, w których użytkownicy przypadkowo opuszczają maszyny wirtualne z systemem:
 
* Automatyczne rozłączanie użytkowników z maszyn wirtualnych, które system operacyjny uzna za bezczynne (tylko system Windows).
* Automatycznie zamykaj maszyny wirtualne podczas odłączania użytkowników (Windows & Linux).
* Automatycznie Zamykaj uruchomione maszyny wirtualne, ale użytkownicy nie łączą się.

Zapoznaj się z bardziej szczegółowymi informacjami na temat funkcji automatycznego zamykania w sekcji [Maksymalizuj koszt kontroli przy użyciu ustawień automatycznego zamykania](cost-management-guide.md#maximize-cost-control-with-auto-shutdown-settings) .

Administrator konta laboratorium może skonfigurować to ustawienie dla konta laboratorium, w którym tworzysz laboratoria. Aby uzyskać więcej informacji, zobacz [Konfigurowanie automatycznego zamykania maszyn wirtualnych przy rozłączaniu dla konta laboratorium](how-to-configure-lab-accounts.md). Jako właściciel laboratorium możesz zastąpić to ustawienie podczas tworzenia laboratorium lub po utworzeniu laboratorium. 

## <a name="configure-for-the-lab-level"></a>Skonfiguruj dla poziomu laboratorium

Ustawienia automatycznego zamykania można skonfigurować w [Azure Lab Services](https://labs.azure.com/).

* podczas tworzenia laboratorium (w ramach **zasad Lab**) lub
* Po utworzeniu laboratorium (w **ustawieniach**)

> [!div class="mx-imgBorder"]
> ![Konfiguruj podczas tworzenia laboratorium](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

Upewnij się, że szczegółowe informacje o automatycznym zamknięciu znajdują się w sekcji [Maksymalizuj koszt kontroli przy użyciu ustawień automatycznego zamykania](cost-management-guide.md#maximize-cost-control-with-auto-shutdown-settings) .

> [!WARNING]
> Jeśli system operacyjny Windows zostanie zamknięty na maszynie wirtualnej przed rozłączeniem sesji RDP z maszyną wirtualną, funkcja Autozamykania nie będzie działać prawidłowo.  

## <a name="next-steps"></a>Następne kroki

[Pulpit nawigacyjny dla laboratoriów zajęć](use-dashboard.md)
