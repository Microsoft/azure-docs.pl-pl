---
title: Usługa Azure automanage dla systemu Linux
description: Zapoznaj się z najlepszymi rozwiązaniami dotyczącymi platformy Azure dla maszyn wirtualnych dla usług, które są automatycznie dołączane i konfigurowane dla maszyn z systemem Linux.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 3aab43be49cb98fbe136e1f0216590785d650392
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953253"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---linux"></a>Azure automanage dla najlepszych rozwiązań dotyczących maszyn wirtualnych — system Linux

Te usługi platformy Azure są automatycznie dołączane w przypadku używania autozarządzania dla maszyn wirtualnych na maszynie wirtualnej z systemem Linux. Są one istotne dla naszych najlepszych rozwiązań, które można znaleźć w naszej [strukturze wdrożenia chmury](/azure/cloud-adoption-framework/manage/azure-server-management).

W przypadku wszystkich tych usług będziemy automatycznie dołączać, automatycznie konfigurować, monitorować do dryfowania i korygować w przypadku wykrycia dryfu. Aby dowiedzieć się więcej o tym procesie, zobacz [Azure automanage for Virtual Machines](automanage-virtual-machines.md).

## <a name="supported-linux-distributions-and-versions"></a>Obsługiwane dystrybucje i wersje systemu Linux

Autozarządzanie obsługuje następujące dystrybucje i wersje systemu Linux:

- CentOS 7.3 +
- RHEL 7.4 +
- Ubuntu 16,04 i 18,04
- SLES 12 (tylko SP3 – SP5)

## <a name="participating-services"></a>Usługi uczestniczące

>[!NOTE]
> Oprogramowanie chroniące przed złośliwym oprogramowaniem firmy Microsoft nie jest teraz obsługiwane na maszynach wirtualnych z systemem Linux.

|Usługa    |Opis    |Obsługiwane środowiska<sup>1</sup>    |Preferencje obsługiwane<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|Monitorowanie usługi VM Insights    |Azure Monitor dla maszyn wirtualnych monitoruje wydajność i kondycję maszyn wirtualnych, w tym ich uruchomione procesy i zależności od innych zasobów. Dowiedz się [więcej](../azure-monitor/vm/vminsights-overview.md).    |Produkcja    |Nie    |
|Backup    |Usługa Azure Backup zapewnia niezależne i odizolowane kopie zapasowe zapewniające ochronę przed niezamierzonym zniszczeniem danych na maszynach wirtualnych. Dowiedz się [więcej](../backup/backup-azure-vms-introduction.md). Opłaty są naliczane na podstawie liczby i rozmiaru chronionych maszyn wirtualnych. Dowiedz się [więcej](https://azure.microsoft.com/pricing/details/backup/).    |Produkcja    |Tak    |
|Azure Security Center    |Azure Security Center to ujednolicony system zarządzania zabezpieczeniami infrastruktury, który wzmacnia stan zabezpieczeń centrów danych i zapewnia zaawansowaną ochronę przed zagrożeniami w ramach obciążeń hybrydowych w chmurze. Dowiedz się [więcej](../security-center/security-center-introduction.md).  Autozarządzanie skonfiguruje subskrypcję, w której znajduje się Twoja maszyna wirtualna, do oferty w warstwie Bezpłatna Azure Security Center. Jeśli Twoja subskrypcja została już dołączona do Azure Security Center, wówczas Autozarządzanie nie skonfiguruje jej ponownie.    |Produkcja, tworzenie i testowanie    |Nie    |
|Zarządzanie aktualizacjami    |Update Management w Azure Automation służy do zarządzania aktualizacjami systemu operacyjnego dla maszyn wirtualnych. Można szybko ocenić stan dostępnych aktualizacji na wszystkich komputerach agentów i zarządzać procesem instalowania wymaganych aktualizacji dla serwerów. Dowiedz się [więcej](../automation/update-management/overview.md).    |Produkcja, tworzenie i testowanie    |Nie    |
|Change Tracking & spis    |Change Tracking i spis łączy funkcje śledzenia zmian i spisu, aby umożliwić śledzenie zmian infrastruktury maszyny wirtualnej i serwera. Usługa obsługuje śledzenie zmian między usługami, przedemonuje oprogramowanie, rejestr i pliki w swoim środowisku, aby ułatwić diagnozowanie niepożądanych zmian i zgłaszanie alertów. Obsługa spisu umożliwia wykonywanie zapytań dotyczących zasobów gościa w celu wglądu w zainstalowane aplikacje i inne elementy konfiguracji.  Dowiedz się [więcej](../automation/change-tracking/overview.md).    |Produkcja, tworzenie i testowanie    |Nie    |
|Konfiguracja gościa platformy Azure    | Zasady konfiguracji gościa służą do monitorowania konfiguracji i raportu dotyczącego zgodności maszyny. Usługa autozarządzania zainstaluje linię bazową systemu Linux platformy Azure przy użyciu rozszerzenia konfiguracji gościa. W przypadku maszyn z systemem Linux usługa konfiguracji gościa zainstaluje linię bazową w trybie tylko Inspekcja. Będzie można zobaczyć, gdzie maszyna wirtualna jest niezgodna z linią bazową, ale niezgodność nie będzie automatycznie korygowana. Dowiedz się [więcej](../governance/policy/concepts/guest-configuration.md).    |Produkcja, tworzenie i testowanie    |Nie    |
|Konto usługi Azure Automation    |Azure Automation obsługuje zarządzanie przez cały cykl życia infrastruktury i aplikacji. Dowiedz się [więcej](../automation/automation-intro.md).    |Produkcja, tworzenie i testowanie    |Nie    |
|Obszar roboczy usługi Log Analytics    |Azure Monitor przechowuje dane dzienników w obszarze roboczym Log Analytics, który jest zasobem platformy Azure i kontenerem, w którym zbierane są dane, agregowane i obsługiwane jako granica administracyjna. Dowiedz się [więcej](../azure-monitor/logs/design-logs-deployment.md).    |Produkcja, tworzenie i testowanie    |Nie    |


<sup>1</sup> wybór środowiska jest dostępny po włączeniu autozarządzania. Dowiedz się [więcej](automanage-virtual-machines.md#environment-configuration). Możesz również dostosować ustawienia domyślne środowiska i ustawić własne preferencje w ramach ograniczeń najlepszych rozwiązań.


## <a name="next-steps"></a>Następne kroki

Spróbuj włączyć Autozarządzanie dla maszyn wirtualnych w Azure Portal.

> [!div class="nextstepaction"]
> [Włącz Autozarządzanie maszynami wirtualnymi w Azure Portal](quick-create-virtual-machines-portal.md)